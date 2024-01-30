---
layout: post
title:  Building options expiry calendar
tags: tech finance
categories: finance options trading
---

# Building options expiry calendar
Simple web page for tracking current and upcoming index options expiry for the Indian Market

### The Problem
I trade index options in the Indian stock market to generate a passive income on the side. The goal is to generate passive income with minimum time investment. I usually sell [far OTM index options](https://www.avatrade.com/education/market-terms/what-is-otm) on the expiry day which has high risk to reward ratio. The general idea is to make ₹2 if market is in the favor of the strategy and loose ₹1 if the market is not in the favor of the strategy. Usually, by selling the far OTM options, the win rate is more than 70%.

Since there is an index option expiring every day of the week now, the strategy works really well. The below table outlines the day of the week and the corresponding expiring index option.

| Day of week | Expiries |
|--|--|
| Monday | MIDCAPNIFTY, BANKEX |
| Tuesday | FINNIFTY |
| Wednesday | BANKNIFTY |
| Thursday | NIFTY |
| Friday | SENSEX |

MIDCAPNIFTY & BANKEX has poor liquidity right now due to which I avoid trading those expiries on Mondays. But if there is a holiday on Tuesday, the FINNIFTY expiry will fall on Monday and then it makes sense to take a trade on Monday. If the FINNIFTY trade is not taken on Monday, an expiry is missed along with the opportunity to generate better overall profit.

To make sure I don't miss such expiries that fall outside their designated day of week due to holiday (or other reason), it is important to keep track of the exchange holidays. But since the goal is to make profit with minimum time investment, its an additional burden for me.

### The Idea

I always wanted a tool that would tell me what options are expiring today taking holidays into the consideration. With such a tool, I simply need to check the tool in the morning and decide which expiry to trade. Thus, the idea of expiry calendar is born.

### Why design the UI first?

In my experience, if you are building a tool with UI, it always makes sense to start with a good prototype. While creating a prototype, you will be forced to answer questions that users will have while using your tool. You will be forced to prioritize user needs and functionality of the tool over the look and feel of the tool.

Since I believe that the UI design is a crucial step in the process of building UI based tools, I have learned the basics of [Figma](https://www.figma.com/) that helps me quickly sketch out my ideas.

### Design Process - Assumptions, Questions & Answers

The first step in the design process is to ask various questions about the tool and either try to answer them or make reasonable assumptions about them. The goal is to simplify the design as much as possible. For example,

> What should be the color theme for the tool?

There are various color palette generators online that we can use to finalize a color theme. But mixing different colors together on a small screen in a harmonious way is an art and not everyone can pull it off. Due to this reason, I am more inclined towards monochromatic color themes that uses shades of a single color to highlight various parts of a UI. I find it simpler to work with since not many colors are involved.

> Do we need a title at the top that would explain what the tool is about? Can we get away without a title at the top?

I think its a good practice to have a title at the top that would explain what the tool does and who the target audience for the tool is? No one likes to spend time looking at something and eventually realizing that it does not solve any of their problems.

> What is the most important piece of information that the user would be interested in?

This question will guide us in prioritizing the information on the screen. In our case, the most important piece of information would be the Index options that are expiring today. This should be highlighted. People might view the page during the night, early morning or from another timezone. Thus, it makes sense to display today's date explicitly. Various options have different lot size and that value also keeps on changing. It makes sense to display that as well.

> Shall we list only today's expiry or expiries on the next few days as well?

On most days, the traders would know the expiries. There will be very few days when there would be unexpected expiries. In such a case, its always good to know beforehand if there are any upcoming unexpected expiries. To me, it feels like it would be helpful if we highlight the today's expiries and display expiries on the next few days.

> Shall we display holidays in the list? What about weekends?

If we don't display holidays and weekends, then the user has to focus on the 2 things in the UI,

-   Upcoming expiries
    
-   Date of upcoming expiry
    

If we display holidays & weekends, then user is assured that the next expiries listed are for tomorrow. That reduces a bit of cognitive load I feel.

### Figma Design

After making reasonable assumptions, I decided to build the design in Figma. I created a component set and a component with 2 states - Default & Today. The Today state will be highlighted with dark border and text color. The Default state will have light border & text color as shown in below screenshot.
![Figma Design]({{ "/assets/expiry-calendar-figma.png" | relative_url }})


Next, we will try to implement this design using tailwind and python.
