---
layout: post
title:  Enhancing your GitHub public profile
tag: tech
category: tech
date: 2023-02-13 18:00:00 +0530
---

GitHub profiles are increasingly becoming important in the tech consulting and hiring world. One of the most significant benefits of having a GitHub profile is the ability to showcase your work. By creating a profile and sharing your repositories, you can demonstrate your coding skills and highlight the projects you’ve worked on. This can be particularly valuable when looking for new job opportunities or when trying to build a personal brand in the tech industry.

Many employers and recruiters use GitHub to search for potential job candidates. By having a well-maintained profile with high-quality code and contributions to open source projects, you can make yourself more visible to potential employers and increase your chances of finding job opportunities.

I recently discovered that you can enhance your GitHub public profile by creating a special repository with README.md file. Here is how my public profile looks now.

![My GitHub Profile](/assets/img/posts/2024-02-13/github-profile.png){: w="700" h="400" }


To enhance your GitHub public profile follow these steps:

1. Create a public repo with the name same as your GitHub username. Since my username is priyankt, my repo name is priyankt. View my repo for reference.
2. Initialize the repo with a README.md file.
3. Browse all the different types of profiles available on (awesome github profile)[https://zzetao.github.io/awesome-github-profile/] website. This will give you an idea of what you can include in your profile. Pick the ones that you like.
4. Build your profile using markdown content.

I also maintain a blog on my website priyankt.com and decided to include my recent blog posts in the profile section. The recent blog posts should automatically update after I have added a blog on my website. We can achieve this, using GitHub actions. You should be familiar with how to use different actions from GitHub marketplace. Refer the .github/workflows/main.yaml and feed.py files in the repo to get an idea of how everything is organised.
