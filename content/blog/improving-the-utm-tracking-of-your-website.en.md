---
title: Improving the UTM tracking of your website
description: Learn how to quickly improve the tracking from the UTM tags on your landing pages with utmkeeper
publishDate: 2018-09-14
images:
  - /img/utmkeeper/utm.png
tags: ["javascript", "utm"]
draft: false

---

Recently, I had the opportunity to face a frontend challenge at [Configr](https://configr.com/?utm_source=humberto&utm_campaign=post-utm&utm_content=link-intro). It consists of improving the tracking rate of the new users who come to the system (a.k.a. leads). More specifically, the tracking rate from the [UTM parameters](https://en.wikipedia.org/wiki/UTM_parameters) of the marketing campaigns.

{{< tip class="info" >}}
**TLDR**: I created a **javascript** library called **utmkeeper** that do the job for you, and it's available at  [https://github.com/humrochagf/utmkeeper](https://github.com/humrochagf/utmkeeper).
{{< /tip >}}

{{< tip class="info" >}}
**UPDATE (September 21, 2018)** Post updated to cover the  **0.3** version of **utmkeeper**.
{{< /tip >}}

## UTM

The acronym **UTM** came from **Urchin** which was an analytics software made to measure how effective a marketing campaign was, by using the parameters of the **U**rchin **T**raffic **M**odule to accomplish that. Later, **Google** started to support these parameters, and right after **Facebook**, and eventually, these parameters became famous as a standard tool to measure the effectiveness of marketing campaigns across the internet.

It works with a simple mechanic, you add them as **URL** parameters (these variables that comes after the "?" on a link) using the `utm_` prefix followed by its type, that by default are five:

- `utm_source`: indicates the lead origin
- `utm_medium`: indicates the type of the link, like "cpc" in case it comes from a "cost per click" campaign
- `utm_campaign`: indicates from which campaign it came
- `utm_term`: indicates the search term used to get to the page
- `utm_content`: indicates which element of the page the lead clicked, a banner for example

These UTM parameters measure the effectiveness of a marketing campaign, and you can use it on every famous analytic tool, but if you have your tracking implementation, you can create another `utm_something` parameter.

The fact that we had an internal tracking system and an implementation to catch new UTM parameters was the reason that motivated this challenge.

## The challenge

The **UTMs** arrives at the landing page on the first access coming from a campaign link. At this moment, the analytics tools track them, and they are lost during the user navigation leaving the metrics only for the third party analytics tool. Unfortunately, that's not good for our internal tracking system, a good part of the data doesn't propagate until login/signup and do not persist at the system.

Thinking on how to solve this problem, I made a few considerations:

- The solution must ensure the **UTM** propagation from the landing page until the login/signup
- The propagation need to happen across different domains, and that made me discard the possibility to use [cookies](https://developer.mozilla.org/en/docs/Web/HTTP/Cookies)
- We don't want to consider the previous access to the landing page because the only access that matters is the one that turned into a login/signup
- It should be something simple to integrate with different platforms like Django, Ghost, WordPress, and even static HTML.

With these considerations in mind, I came with the **utmkeeper** (very descriptive name right?).

## utmkeeper

The **utmkeeper** is a javascript library with the single purpose of capture the **UTMs** and reapply them on every link and form of the page without affecting its navigability. 

With it, I can propagate the UTMs through the user navigation even if it happens across domains always keeping the UTM group that started that particular flow of navigation. It also is easy to apply on different platforms.

To use the **utmkeeper** you can download the package from its repository [https://github.com/humrochagf/utmkeeper/releases](https://github.com/humrochagf/utmkeeper/releases) or installs it from npm:

{{< highlight console >}}
npm install utmkeeper
{{< / highlight >}}

After that, add the following script at the end of the HTML before the `</body>` closing tag:

{{< highlight html >}}
<script src="path/to/utmkeeper.js" charset="utf-8"></script>
<script charset="utf-8">
  utmkeeper.load();
</script>
{{< / highlight >}}

The function `load` is responsible for loading the library and persisting the UTM through navigation. At the function we can pass a configuration **object**:

{{< highlight javascript >}}
{
  forceOriginUTM: true,
  fillForms: true,
  utmObject: {},
  postLoad: null,
}
{{< / highlight >}}

- **forceOriginUTM**: receives a **boolean** that indicates if you want to override any UTM that can be at some link inside the page. Its default value is `true`
- **fillForms**: receives a **boolean** to indicate if you want to add the UTMs at the page forms. In case the UTM fields already exist in the form, it only fills it. Otherwise, it creates the fields as `hidden`. Its default value is `true`
- **utmObject**: receives an **object** with pre-set UTMs. It can be used to set default values for UTMs
- **postLoad**: receives a single-parameter function that will run at the end of the `load` function. This single parameter is an object with the extracted UTMs in it, so you can use them on a custom logic like sending leads to [mautic](https://www.mautic.org/)

Check the following example with all the parameters passed to the `load` function:

{{< highlight javascript >}}
utmkeeper.load({
  forceOriginUTM: true,
  fillForms: true,
  utmObject: {
    utm_test: 'test'
  },
  postLoad: function(utms){console.log(utms);}
});
{{< / highlight >}}

## Result

The following graph is a slice of the transition period of the **utmkeeper** configuration at  [Configr](https://configr.com/?utm_source=humberto&utm_campaign=post-utm&utm_content=link-resultado) landing pages. The **blue** color is the total of leads coming to the login/signup, the **red** color is the number of leads **without UTM**, and the **green** is the number of leads **with UTM** :

![utm graph](/img/utmkeeper/utm-graph.png)

The easy to integrate feature from **utmkeeper** helped a lot to put the solution to production, bringing a quick result to the tracking metrics.
