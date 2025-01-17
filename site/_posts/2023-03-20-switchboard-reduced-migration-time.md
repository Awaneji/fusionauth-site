---
layout: blog-post
title: Switchboard.app reduced their estimated migration time by 66% using FusionAuth
description: Switchboard, a collaborative digital workspace where people meet to work on any project, with any application or file, uses FusionAuth for OAuth and creating custom JWTs.
author: Dan Moore
image: blogs/switchboard-fusionauth/switchboard-migrated-fusionauth-header.png
category: community-story
tags: topic-community-story topic-upgrade upgrade community-story saas
excerpt_separator: "<!--more-->"
---

Theo Gravity is a FusionAuth community member and senior engineer at Switchboard. He chatted with us over email about how he and his team are using FusionAuth to meet their auth needs. 

<!--more-->

*This interview has been lightly edited for clarity and length.*

-------

**Dan:** Can you tell me a bit about Switchboard? What is the company's mission?

**Theo:** Switchboard is a collaborative digital workspace where people meet to work on any project, with any application or file. The workspace is divided into rooms that companies can use for team meetings, projects, or even work with external clients. It recreates the feeling of being in the same room, having all of the resources you need, and jamming on something together. 

Our mission is to make teamwork the best part of work.

> In a world where it’s difficult to estimate development time to production, we were surprised we were able to launch [our FusionAuth migration] according to the four month estimation with just one full time engineer and partial support from an infrastructure engineer and another full stack engineer.

**Dan:** Tell me about your work as a senior engineer at Switchboard.

**Theo:** I’m a generalist and work on all sorts of projects and initiatives at Switchboard. I work on architecture and build out frameworks and services for things like logging, monitoring, API development and testing. One of my goals is to provide a level of consistency for other Switchboard engineers to work from. 

Other times, I’m developing new product features in our frontend (such as workspaces and auth) while eliminating tech debt on the backend and cleaning up outdated database schemas. 

I’m also responsible for performing in-depth research around a problem (such as migrating our auth platform), scoping out timelines for a solution, and the execution of it all the way to production.

**Dan:** How do you use FusionAuth? OAuth? User management? Social sign-on? Something else?

**Theo:** We use FusionAuth for OAuth and issuing custom JWTs.

> [Lambdas have] been a boon for us when it came to adding custom logic around auth lifecycles.

**Dan:** What problems did we solve for you?

**Theo:** As a SaaS company, we want to be able to support any kind of auth that a user might be using along with integrating SSO when working with enterprise customers. 

FusionAuth’s list of native support for 3rd party identity providers is really extensive compared to competitors we’ve evaluated, and getting up and running with a supported provider was simple to do. In the rare case that there isn’t any native support, we’ve found that FusionAuth supports the provider with a little additional work on our end as long as the identity provider we’re integrating with supports OAuth2 + OIDC or SAML.

Originally we were using password-based sign-in for email accounts, and wanted to switch to a passwordless system (via email link verification). FusionAuth had passwordless auth APIs that were designed for this use-case that made the transition to this style of login trivial.

A Switchboard user can have multiple workspaces on separate accounts. We wanted our users to be able to sign into multiple accounts from various providers, and to be able to switch between them. We had a big issue with our prior auth provider around Google account switching, where we’ve had to add hacks in our codebase through additional redirects and cookie manipulation to allow a user to select an alternative Google account when accessing other workspaces. Using Google as an identity provider under FusionAuth solved this use case without requiring hacks in our codebase.

Additionally, we needed to support different user access rights to the product depending on whether they are members or one-time guests. 

We opted to use the FusionAuth Vend JWT APIs to create temporary access tokens for guest users. In my experience using competitor products, it is difficult to customize a JWT exactly how you need it, but I really appreciated how flexible the API was in allowing us to include all of our requirements into a custom JWT. Not only that, we were able to easily generate and have separate signing keys for each within FusionAuth.

One of our core features on Switchboard is integration with your Google calendar. Users can schedule and see upcoming Switchboard meetings. The 3rd party provider we use to perform calendar synchronization requires the original 3rd party identity provider tokens in order to link the user’s calendar.  Competitors generally don’t provide these tokens, but FusionAuth does, which means we will be able to provide a seamless calendar integration experience for users in the future without an additional sign-in.

We have multiple environments for Switchboard, which allow us to test and develop new product features isolated from production data. This also includes any changes to our auth systems. We used the FusionAuth tenant feature to create segmented environments in the same FusionAuth instance, which meant less infrastructure for us to maintain. Rather than having a separate server for each environment, we only have two servers (prod and other environments) for FusionAuth instead.

For the multiple FusionAuth tenants we maintain, we might have differences between them with respect to how they are configured, and we needed a way to easily make changes outside of the FusionAuth administration UI, but also keep track of the current state of the system. Fortunately, the FusionAuth community created a Terraform module for configuration management. We actually use Pulumi for our configuration management, and I was able to create a Pulumi integration that bridged the Terraform module for us to easily maintain the configuration of our FusionAuth instances.

**Dan:** How were you solving them before FusionAuth?

**Theo:** We were using another high-profile competitor. The problems around their SDK and APIs, along with the expanding use-cases we had that would be natively unsupported is what led to our evaluation of other auth providers.

**Dan:** Why did you choose FusionAuth over the alternatives?

**Theo:** I had experience migrating away from proprietary auth systems in the past to other solutions and had a specific vendor in mind at the time due to that experience, thinking it would also serve our use-cases. 

However, our use-cases at Switchboard are quite different from my prior experience, which led me to build out a matrix of our use-cases and competitors in the space. I ended up evaluating 13 competitors against various dimensions like the level of support for 3rd party identity providers, SAML support, machine tokens, hooks / lambda support, 2FA types, self-hosting, token revocation, signing key rotation, and compliance such as SOC2 / HIPAA / GDPR / CCPA.

The FusionAuth developer documentation is top-notch with detailed usage examples, and the provided SDKs were intuitive to use and easy to implement using Typescript. Other competitors either lacked appropriate documentation or had an unintuitive SDK or development workflow.

I appreciated that FusionAuth provided a few avenues for support: the community forums, github issues, and paid support. I’ve had success getting answers to questions while I was prototyping a FusionAuth integration, and was surprised to see the level of FusionAuth engineering responses to issues that were reported. I really liked that I can interact directly with a FusionAuth engineer for any deep technical issues.

Other competitors had a lot of complexity when working with their SDK or overall solutions. Some would require multiple products to fit together like pieces of a puzzle that required dedicated infrastructure or hide complex use-cases behind abstractions that were difficult to follow or customize. 

What I appreciated with FusionAuth is its flexibility. While it handles simple use cases around auth with an out-of-box experience by using the hosted UI for auth sign-in and registration, you can also roll your own customized auth workflows and user experience through the use of their SDKs.

In our case, we went for a customized approach as we wanted total control over our auth experience and workflows by utilizing the SDKs. The development experience was exceptional. We never felt like we were fighting against the FusionAuth platform to get what we wanted it to do.

Feature-wise, lambda / hooks that support modern Javascript (ES6+) is something I haven’t seen with other competitors (or rather, lambda support in general), and has been a boon for us when it came to adding custom logic around auth lifecycles.

Finally, we chose FusionAuth because of the options to self-host or have FusionAuth host for you. We did not want to maintain infrastructure for FusionAuth at the present, and the hosted option cost was very reasonable and removed a lot of the time to bootstrap. Having the option to self-host if we had significant growth in the future is really nice.

**Dan:** How much time and money would you say FusionAuth has saved you?

**Theo:** Originally, we had planned that it may take up to a year to migrate to another auth provider. We were able to reduce that estimate down to four months once we decided on FusionAuth and started prototyping the  migration.

In a world where it’s difficult to estimate development time to production, we were surprised we were able to launch according to the four month estimation with just one full time engineer and partial support from an infrastructure engineer and another full stack engineer.

> FusionAuth’s list of native support for 3rd party identity providers is really extensive compared to competitors we’ve evaluated, and getting up and running with a supported provider was simple to do.  

**Dan:** How do you run FusionAuth (kubernetes, standalone server, behind a proxy, etc)?

**Theo:** Our instances of FusionAuth are hosted and maintained via FusionAuth Cloud, and we’ve built a service that has its own business logic for auth that acts as a proxy to it.

**Dan:** Any general feedback/areas to improve?

**Theo:** My feedback isn’t specific to FusionAuth in general, and is more around building your own auth solution using something like FusionAuth. One of the biggest problems I’ve encountered is around refreshing tokens when not using an auth provider’s hosted UI / SDK. I wish there was a simple-to-use library / abstraction that can properly handle token lifetimes and refresh in the background when you do things like switch between tabs or wake from sleep on your laptop. 

**Dan:** Thanks for your feedback!

-------

We love sharing community stories. You can check out [Switchboard's website](https://www.switchboard.app/) if you'd like to learn more about them. 

