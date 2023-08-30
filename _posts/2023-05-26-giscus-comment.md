---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://www.thebigredgroup.com/wp-content/uploads/2021/06/top-six.jpg"> Comment system with GISCUS
date: 2023-05-26 00:00:02 +730
categories: [Resources, general]
tags: [comment-system, jekyl,giscus] # TAG names should always be lowercase


---

# Adding a Comment System to Your Static Site with Giscus

Do you have a static site and want to add a comment system to engage with your readers? Look no further! In this guide, we'll explore how you can incorporate Giscus, a fantastic tool that leverages the GitHub discussion API, to seamlessly integrate a comment system into your website.

## The Challenge

Static generated sites often lack built-in support for comment systems, making it difficult to foster engagement and interaction with your audience. Fortunately, there are third-party solutions available, such as Giscus, that can fill this gap.

## Introducing Giscus

Giscus is an excellent option for adding a comment system to your static site. It harnesses the power of the GitHub discussion API, making it easy for readers to leave comments, ask questions, and interact with your content.

## How to Get Started

To begin, follow these steps:

### Step 1: Enable GitHub Discussions

- Go to your repository on GitHub.com.
- Click on "Settings" ⚙️
<img width="568" alt="select-settings" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/0d9dc68d-b9ef-4b0b-962d-a0863cfd06f2"> <br>
- Under "Features," select "Set up discussions."
- Customize the discussion template to suit your community's needs.
- Finally, click on "Start discussion" to activate GitHub discussions.
- <img width="484" alt="setup-discussion" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/8532c316-32f6-426f-a389-a8eec65cef16">

### Step 2: Enable Giscus

Visit [https://github.com/apps/giscus](https://github.com/apps/giscus) and enable Giscus for your repository.

### Step 3: Obtain Your Repository API Key

You'll need your repository's API key to integrate Giscus effectively. Here's how you can retrieve it:

- Access the GitHub GraphQL API [explorer](https://docs.github.com/en/graphql/overview/explorer) and log in with your GitHub account.
- Use the GraphQL query provided in the code block below to fetch your repository ID and the list of discussion categories.

```graphql
query { 
  repository(owner: "your_username", name:"your_repository"){
    id
    discussionCategories(first:10) {
      edges {
        node {
          id
          name
        }
      }
    }
  }
}
```

- Save the repository ID and the discussion categories for future reference.

### Step 4: Install the @giscus/react Package

Using your preferred package manager, install the `@giscus/react` package:

```bash
yarn add @giscus/react
```

or

```bash
npm install @giscus/react
```

### Step 5: Integrate the Giscus Component

Now, import and use the `Giscus` component in your code:

```javascript
import { Giscus } from "@giscus/react";

export default function CommentSection() {
  return (
    <Giscus
      repo="your_username/your_repository"
      repoId="your_repository_id"
      category="General"
      categoryId="general_category_id"
      mapping="pathname"
      reactionsEnabled="0"
      emitMetadata="0"
      theme="dark"
    />
  );
}
```

Customize the props based on your repository and desired configuration.


<img width="555" alt="comment" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/7756f184-74aa-44ee-85b7-9607e9b6f50d">

It will render a GitHub comment widget where other developer can sign in using their GitHub account to comment through GitHub Discussion API.

That's it folks! Hope this tutorial help, and happy hacking!


**Reference:**

[https://giscus.app/](https://giscus.app/)  
[https://graphql.org/](https://graphql.org/)  


## Conclusion

By following these steps, you can effortlessly enhance your static site with a fully functional comment system using Giscus. Engage with your readers, foster discussions, and create a vibrant community around your content. Happy coding!


![gif](https://media.giphy.com/media/DAtJCG1t3im1G/giphy.gif)
