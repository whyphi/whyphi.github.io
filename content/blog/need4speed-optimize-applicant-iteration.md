---
title: Need4Speed - Optimizing Applicant Iteration
date: 2024-06-08
authors:
  - name: William De Rocco
    link: https://github.com/wderocco8
    image: https://github.com/wderocco8.png
tags:
  - optimization
excludeSearch: true
---

After many months of hard work, the [WhyPhi](https://why-phi.com/) team successfuly deployed the first iteration of our web-app this past February 2024. Overall, the launch was quite successfuly, but as PCT members began to review applications, the need for *efficient* applicant iteration became evident.
<!--more-->

## What happened?

On February 2nd of this year, the official WhyPhy application was opened to the public, and successfully accepted the first 50 applications to our fraternity, PCT. Despite a couple load-time issues relating to our AWS instances, the application process was quite smooth. However, when PCT brothers began to review each application, load-times increased exponentially and it became almost impossible to view applicants in an efficient manner.

So, what caused this inefficency? Upon hearing feedback from WhyPhi users and diving deeper into the codebase archietcture, it became apparent that there were some systemic issues with the way we were fetching `applicantData` from DynamoDB. Specifically, the issue was the **duplication** of database requests.

In the original approach, there were **two sub-components** of a `listing`:

1. `admin/listing/${params.listingId}` returned a list `applicantData` with information for **every** applicant
2. `admin/listing/${params.listingId}/${params.applicantId}` **re-fetched** data but for a specified `params.applicantId`.

As you might guess, this approach is noticeably ineffecient, mainly because the *first* sub-component had already fetched **all** of the requisite data to render every applicant page. So by clicking on an applicant and rendering the *second* sub-component, we re-fetch the data that was just fetched.

Now imagine we want to iterate over the first 3 applicants, the flow would look something like this:

- load all applicants (1 API call)
- click on first applicant (1 API call)
- go back to all applicants (1 API call)
- click on second applicant (1 API call)
- go back... (1 API call)
- click on third applicant (1 API call)

So to view just 3 applicants, we had already made 6 API calls. Further, we were executing API calls twice as many times as there are applicants. Expanding this to 50 applicants, that is roughly **100** API calls (now imagine each of our 50+ brothers are trying to access this information simultaneously... 😅).

## The Fix

There were **three** main options we considered to solve this efficiency problem:

1. pass the `applicantData[index]` into the `admin/listing/${params.listingId}/${params.applicantId}` sub-component
2. integrate `useContext` to maintain the `applicantData` across *both* sub-components
3. use **conditional rendering** to display either all applicants or single-applicant within `admin/listing/${params.listingId}`

The first option was quickly rules out as our Next.js framework does not render components within each other (so using props would involve significant overhead with something like [Redux](https://redux.js.org/) for global state management). A similar approach using `params` would pose security risks given that the `applicantData` holds sensitive information (e.g. name, email, GPA, phone number... etc.).

The second option did have more promise and took more consideration before ruling out. React's [`useContext`](https://react.dev/reference/react/useContext) can be a highly effective and secure way to share data across multiple components. However, the fatal flaw was that once rendering the `admin/listing/${params.listingId}/${params.applicantId}` page, there would be no way to re-fetch more updated data when the user **reloads** the page. Given that it is possible for applications to be deleted by our team, this could pose the risk of seeing stale data even after a page reload.

So, the final **solution** was to use **conditional rendering**. [Conditional rendering](https://react.dev/learn/conditional-rendering) is a fairly simple practice in React which, as the name suggests, allows the frontend to render two (or more) versions the UI depending on some boolean conditions. We used this alongside localStorage to maintain a `selectedApplicantIndex` state variable that would alter the UI depending on its value to either display all applicants or a selected applicant. 

This approach fixed both issues above: 1) minimized security risk of passing sensitive data via params and 2) the page would re-fetch *all* applicants on reload. This meant also that in order to view all applicants, we would only require **1 API call** (I'd argue this is much better than the previous 100). This refactor was implemented and can be referenced in detail in the following pull request: https://github.com/whyphi/portal/pull/128

## Results

After conducting some preliminary testing, it was evident that the fix was quite successful in improving the application's efficiency. Below are two clips showing the workflow from before and after the fix.

In the first clip, we can see that each API request takes roughly 230 ms to load. If we recall from above, in order to load all of our 50 applicants, this would mean executing ~100 API calls, for a total of 230,000 ms or **230 s** of wait time. For an average user, this is simply unacceptable, and most individuals would likely give up before making it through every single applicant.

However, when reviewing the second clip, we see that only **one** API call (taking **310 ms**) is executed. This means that to view all 50 applicants, we only need to wait for 310 ms of I/O, a **99.87%** reduction in wait time.

<br />

<figure>
  <video width="600" controls>
    <source src="/videos/blog/old-approach.mov" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption>Old iteration approach</figcaption>
</figure>

<br />

<figure>
  <video width="600" controls>
    <source src="/videos/blog/new-approach.mov" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption>New iteration approach</figcaption>
</figure>

## How can we prevent this from happening again?

- Careful consideration for UX when developing API calls and program architecture.
- Taking feedback from users and implementing directly into the application.
- Make use of `network` tab in developer tools to get a better understanding of the application's efficiency.
