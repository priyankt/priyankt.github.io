---
layout: post
title:  Real time currency formatting with Alpine.js
tag: tech
category: tech
date: 2024-01-19 05:30:00 +0530
---
At KOFFi, we recently decided to build a returns calculator to highlight how KOFFi-recommended debt mutual funds generate superior returns in short term as compared to the bank fixed deposits.

![KOFFi Calculator](/assets/koffi-calculator.png){: w="400" h="700" }
_KOFFi Returns Calculator_

The calculator takes the amount and duration in weeks and generates the returns for KOFFi-recommended mutual fund vs fixed deposits. The calculator is a simple utility built using [Tailwind CSS](https://tailwindcss.com/) & [Alpine.js](https://alpinejs.dev/) in the frontend supported by python FastAPI in the backend.

Initially the *Amount* input box was a plain HTML input tag with `type=”number”` attribute. This allowed us to supply min, max & step values so that the amount can be validated at the client side with minimum efforts.

Later, we received feedback from the users that while typing large amount, due to absence of formatting, it was sometimes confusing to understand if they have typed 100000 or 1000000 especially on the mobile devices. Acknowledging the feedback, we decided to format the *Amount* input on the fly in the Indian currency format such that 100000 will look 1,00,000 and 1000000 will look 10,00,000. This means that we can no longer use `type=”number”` since comma will not be allowed.

We were already using [Alpine.js](https://alpinejs.dev/) in the frontend for basic interactivity and decided to dynamically format the amount using Alpine.js. A quick search led us to `$money()` prebuilt [Mask Plugin](https://alpinejs.dev/plugins/mask) that is built for this exact same purpose. Only cavet is that it works well for US and EU (100,000) currency formats and not INR(1,00,000) format out of the box. Fortunately, mask plugin allows us to provide custom expression to format the input and we decided to make use of it. Below is the simplified version of our code for reference.

```javascript
<script>
    const formatter = new Intl.NumberFormat("en-IN", {
        style: "currency",
        currency: "INR",
        // We don't need fractions in our case
        minimumFractionDigits: 0,
        maximumFractionDigits: 0
    });
    function formatAmount(input) {
        // Default to 1000000 if failed to parse input to Int
        const amountInt = parseFloat(input.replace(/,/g, '')) || 1000000;
        // Remove leading ₹ symbol since it is already present in the UI
        return formatter.format(amountInt).slice(1);
    }
</script>

<div x-data="{'amount': '10,00,000'}">
<input id="id_amount" name="amount" placeholder="10,00,000" x-model.lazy="amount" x-mask:dynamic="formatAmount" x-ref="amount" @keyup="$refs.amount.blur(); $refs.amount.focus()">
```

For some reason, the x-mask plugin works fine when you enter values in the input but fails to update the input when you delete the digits. [HERE](https://github.com/alpinejs/alpine/discussions/2872) is the related discussion on the GitHub. To resolve this issue, we have to blur and focus the input again for changes to take effect. You can also refer to [this codepen](https://codepen.io/Priyank-Tiwari/pen/xxBrKOX) for code snippet.
