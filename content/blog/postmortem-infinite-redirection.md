---
title: Postmortem - Infinite Redirection on Onboarding
date: 2024-04-18
authors:
  - name: Jin Young Bang
    link: https://github.com/jinyoungbang
    image: https://github.com/jinyoungbang.png
tags:
  - Postmortem
  - Admin
  - Onboarding
excludeSearch: true
---

On April 17th, tech team attempted to demo new features of WhyPhi (QR Code, accountability, onboarding, etc). However, as we got members to signup to WhyPhi, `/admin/onboarding` was refreshing forever.
<!--more-->

## Infinite Redirection: What happened?

In `/app/contexts/AuthContext.tsx`, we have a functionality where when loading any admin related routes, it attempts to get the user's session. Before the fix, our `useEffect` hook was constructed like this:

```js 
/app/contexts/AuthContext.tsx

useEffect(() => {
  getSession().then((session: Session | null) => {
    if (session) {
      // Use type assertion to add the 'token' property
      const sessionWithToken = session as CustomSession;

      // Check if user is a newUser
      if (sessionWithToken.token?.isNewUser) {
        router.push("/admin/onboarding");
      } 

...
};
```

The initial idea of this implementation was that any user that had `isNewUser = true` would be redirected to `/admin/onboarding` where they could fill out their information and the `isNewUser` flag would reset to false.

The issue with this approach was that when a user would be loaded to the onboarding route, this `useEffect` hook would be triggered again. As the user's `isNewUser` flag is still true, the router would push to the onboarding route again and cause an infinite redirection to the same page. Thus, a new fix was implemented (https://github.com/whyphi/portal/pull/113/files):

```js
/app/contexts/AuthContext.tsx

useEffect(() => {
  getSession().then((session: Session | null) => {
    if (session) {
      // Use type assertion to add the 'token' property
      const sessionWithToken = session as CustomSession;

      // Check if user is a newUser
      if (sessionWithToken.token?.isNewUser === undefined || sessionWithToken.token?.isNewUser) {
        if (pathname !== "/admin/onboarding") {
          router.push("/admin/onboarding");
        }
      }
  ...
});
```

During testing on local, the onboarding redirection feature was working as expected. While deploying to both the staging and production environment, another issue appeared. After a user finishes filling out onboarding information, they should be redirected to the admin homepage. However, what happened was a user would get redirected to the start of the onboarding page.

## Stale User Session: What happened again?

In `/app/admin/onboarding/page.tsx`, we have a `handleUserOnboarding` function that makes an API call to save onboarded information into our database:

```js
/app/admin/onboarding/page.tsx

const handleUserOnboarding = async () => {
  try {
    const response = await fetch(`${process.env.NEXT_PUBLIC_API_BASE_URL}/members/onboard/${userId}`, {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${token}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify(userInfo)
    });
    if (response.ok) {
      console.log('User info updated successfully');
      update();
      router.push("/admin");
    } else {
      console.error('Failed to update user info');
    }
  } catch (error) {
    console.error('Error updating user info:', error);
  }
}
```

`update()` is a [method](https://next-auth.js.org/getting-started/client#updating-the-session) of the `useSession` hook from nextAuth. So the logic behind this function is that after submitting a user's information, the `update()` method would the clear the user's current state, and then fetch the user's new token with the `isNewUser` flag set to false. With that new session and flag set, ideally, the user should not be redirected to the onboarding page.

In the local environment, the functionality above worked without any issues as mentioned previously. However, loading time in the local environment tends to be much faster since there's less overall network I/Os. In the local environment, before `router.push("/admin")` gets executed, `update()` sets the new session on the client-side. 

As Javascript is asynchronous, it's possible that before `update()` finishes, `router.push("/admin")` can get executed, meaning that the user could get redirected before the session updates on the client-side. This case happened for the staging and production environment as there could be more background network I/Os happening, allowing update() to be much slower. After deep-diving into how the `update()`method works, I was able to find out that this method is an asynchronous method as well so adding an `await` keyword would fix this [issue](https://github.com/whyphi/portal/pull/117).

## Why did this break in production?

Lack of testing on the staging environment. Under the assumption that things worked fine in the local environment, we thought that things would not break on production. Additionally, we don't have a development version of our `users` collection and all of our testing has been performed on production data, which is not good practice.

## How can we prevent this from happening again?

- Proper testing in the staging environment and promoting to production when testing has been done thoroughly.
- Understanding code external library code properly
- Separating testing and production-related environment + data
