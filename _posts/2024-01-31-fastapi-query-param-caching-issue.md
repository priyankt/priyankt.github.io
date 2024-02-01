---
layout: post
title: Minor issue that haunted me for 3 days
tags: tech python FastAPI
category: tech
date: 2024-01-31 18:16:00 +0530
---

While building the [options expiry calendar](https://priyankt.com/posts/building-expiry-calendar/), I came across a weird issue that haunted me for 3 days. The following blog post outlines the issue and how I was able to track and fix it. Following is an excerpt from the buggy code for reference. See if you can spot the bug.

```python
@app.get(path="/", response_class=HTMLResponse)
async def get_expiry_calendar(
    request: Request,
    today: date = datetime.now(tz=ZoneInfo(key="Asia/Calcutta")).date(),
):
    market: Market = get_market()
    end_date: date = today + timedelta(days=7)
    expiry_sections: List[ExpirySection] = []
    is_selected = True
```

For the [options expiry calendar](https://expiry-calendar.priyankt.com), I decided to try the [Digital Ocean App platform](https://www.digitalocean.com/products/app-platform) for hosting and deployment. In my opinion, it is much simpler, cheaper and predictable as compared to AWS.

## On 27th Jan, 2024

I followed the above listed procedure and deployed the app on the resource that would cost me $5 per month to run the app. The app also seem to be running perfectly fine so all good. Time to sleep. 

## Next morning (28th Jan, 2024)

I decided to access the app again to check what options are expiring today i.e. on 28th Jan, 2024. To my surprise, I see the app showing me the options that have already expired yesterday i.e. on 27th Jan, 2024. First thought that came to my mind - Probably I forgot to take the timezone into the account. The server date is still probably 27th Jan, 2024. A quick `date` command on the server displayed that the server time is set in UTC timezone and the date on the server is 28th Jan, 2024. Hmmm, it's a bit confusing. Why the app is displaying 27th Jan expiries when the server date is also 28th Jan? Nevertheless, I decided to use the timezone aware python function to get today's date. Previously, I was using `date.today()` to get the today's date but now modified it to `datetime.now(tz=ZoneInfo(key="Asia/Calcutta")).date()` so that timezone is taken into account. As soon as I pushed my code to main branch, the App platform detected the change and re-deployed the app. I accessed the app and it was now displaying the correct expiries i.e. for 28th Jan, 2024. Since 28th Jan 2024 is a Sunday, no expiries were expected. I believed it was probably me not using the timezone aware funtion which led to the issue. But all good now. The day went by with me feeling a sense of accomplishment after fixing the issue.

## Next morning (29th Jan, 2024)

I accessed the web app again, to check the expiring options. To my surprise, the app is still displaying stale data i.e. the expiries from 28th Jan. I thought I have fixed the issue. The issue was not reproducible on the local dev environment as well. Could it be that the code didn't deploy properly? I decided to take a quick look at the code on the server but the changes seem to be deployed properly. I am now wondering why the app is not picking up the today's date correctly. Looks like the same web page from yesterday is being displayed again. Could it be related to caching? I didn't implement any caching in the code but I know App platform has integration with the Cloudflare. Could it be that Cloudflare or App platform is caching the page due to some misconfiguration from my end? Sounds plausible since clearing the browser cache doesn't seem to have any effect and the app is still displaying the stale data. It is probably being cached on the server or somehwere in the middle. I quickly try to analyze the request & response headers using developer tools. The `cache-control` header is set to private. The `Last-Modified:` header & `If-Modified-Since:` header also doesn't create any suspicion. I decide to write to Digital Ocean support to check if they know where the page might be cached. They replied with the documentation related to app platform caching and nothing seems to be wrong there. I am now out of ideas and decide to explicitly set the `cache-control` header to `no-store` in response and re-deploy the app. As soon as the app is deployed, the app started displaying today's data. I thought probably explicitly setting the `cache-control` header has fixed it. But this time I was only 50% sure that I've fixed the issue.

## Next morning (30th Jan, 2024)

I reopen the app and once again the app is displaying expiries for 29th Jan. I start to think how I can track the bug and improve the testing cycle for the issue. Right now, I have to wait an entire day to check if my changes have fixed the issue. I need a way to test the changes as soon as I deploy them. Whole day, at the back of my mind, I kept thinking about how I can reduce the testing time.

## Next morning (31st Jan, 2024)

As soon as I wake up, somehow I have a flash of thought. Could it be that the FastAPI is caching the today's date? I vaguely remember reading that the FastAPI caches the dependencies provided to the routing function. Could it be that the FastAPI is caching the default value that I have set for the today query string parameter? To validate my hypothesis, I quickly wrote a small FastAPI app which would accept a query parameter with random value as default.

```python
import random

from fastapi import FastAPI, Request

app = FastAPI()


@app.get(path="/")
async def test(
    request: Request,
    rnd: float = random.random(),
):
    return {"rnd": rnd}
```

My understanding was that every time a request is made for test path ("/"), I would see a different random number. Unfortunately, I was wrong. I tried reloading the web page for test path ("/") multiple times and found that the same random number is returned every time. My hypothesis, that the FastAPI is caching the query string turns out to be true and which was the source of the issue I was encountering.

I quickly updated the code to obtain today's date inside the request function instead of passing it as default value.

```python
@app.get(path="/", response_class=HTMLResponse)
async def get_expiry_calendar(
    request: Request,
    today: Optional[date] = None,
):
	if today is None:
        today = datetime.now(tz=ZoneInfo(key="Asia/Calcutta")).date()
    market: Market = get_market()
    end_date: date = today + timedelta(days=7)
    expiry_sections: List[ExpirySection] = []
    is_selected = True
```

## Next morning (1st Feb, 2024)

I was pretty confident this time that the bug has been fixed. So confident that I didn't even wait to verify it and wrote the blog post. I wasn't disappointed. The calendar is not displaying the correct expiries. You can check out the expiry calendar yourself [here](https://expiry-calendar.priyankt.com/).
