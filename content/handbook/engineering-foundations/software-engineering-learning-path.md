---
title: "A Software-Engineering Learning Path"
weight: 50
description: "How I'd learn (and keep learning) software engineering — Julia Evans' zines, the people worth following, and a staged, curated book list with the why behind each pick."
lastUpdated: 2026-06-25
goDeeper:
  - group: Tools
    title: "Wizardzines — Julia Evans"
    url: "https://wizardzines.com/"
    why: "Bite-size, illustrated zines that make hard systems topics (HTTP, DNS, debugging, containers) finally click."
  - group: Courses
    title: "Teach Yourself Computer Science"
    url: "https://teachyourselfcs.com/"
    why: "A no-nonsense map of the nine CS subjects that matter and the best book or course for each — the structure under a self-taught path."
  - group: Books
    title: "Designing Data-Intensive Applications — Martin Kleppmann"
    url: "https://dataintensive.net/"
    why: "The single best book on how real systems store and move data at scale. If you read one technical book this year, this is it."
  - group: Books
    title: "The Pragmatic Programmer — Hunt & Thomas"
    url: "https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/"
    why: "Timeless habits for becoming a more adaptive engineer — the best starting point on this whole list."
---

The hardest thing about learning software engineering isn't any single topic — it's knowing what to learn next and who to trust. This is how I'd approach it: keep a great teacher in rotation, follow a few people who think clearly in public, and work through a staged reading list instead of grabbing books at random.

## Start with a great teacher: Julia Evans

[Julia Evans](https://jvns.ca/) is an inspiring tech leader who excels at teaching topics in a bite-size way using zines. She's also written a ton of [blog posts](https://jvns.ca/) and given [talks](https://jvns.ca/talks/) worth your time.

Her [Wizardzines](https://wizardzines.com/) site is an extensive, genuinely fun library of zines on technical topics — HTTP, DNS, [debugging]({{< relref "/handbook/engineering-foundations/debugging-craft.md" >}}), containers, the command line, and more. If a foundational concept hasn't clicked from the official docs, there's a good chance a Wizardzine will get you there in twenty minutes.

{{< protip >}}
When I onboard an engineer who's strong but has gaps in the fundamentals, I don't hand them a 600-page reference. I point them at the relevant Wizardzine first. Getting the shape of an idea fast — then going deep where it matters — beats slogging through a spec you don't yet have the hooks to understand.
{{< /protip >}}

## Follow people who think in public

Beyond the foundational thinkers — Martin Fowler, Kent Beck, Robert C. Martin, Fred Brooks — a few developer-centric voices are worth keeping in your feed for how they reason, not just what they know:

| Name | Known for | Why follow |
|------|-----------|------------|
| [**Scott Hanselman**](https://www.hanselman.com/) | .NET, developer advocacy | Educator and host of *Hanselminutes*; generous, accessible explanations. |
| [**Jeff Atwood**](https://blog.codinghorror.com/) | Stack Overflow, Coding Horror | Co-founder of Stack Overflow and Discourse; a strong voice for human-friendly software. |
| [**Jon Skeet**](https://codeblog.jonskeet.uk/) | Stack Overflow, C# | Legendary contributor; clear technical writing and deep dives. |
| [**Dan North**](https://dannorth.net/) | BDD, delivery thinking | Creator of Behavior-Driven Development; focused on communication and delivery. |
| [**Charity Majors**](https://charity.wtf/) | Observability, DevOps culture | Co-founder of Honeycomb.io; outspoken on operability and on management. |

## A staged book list

This is a structured reading list rather than a pile of recommendations — each stage assumes the one before it, and each book comes with the reason it earns a spot.

### 1. Start here — engineering mindset & Agile principles

| Book | Why |
|------|-----|
| [**The Pragmatic Programmer**](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/) | Timeless guidance on becoming a better, more adaptive engineer — habits that scale with you. |
| [**Clean Code**](https://www.oreilly.com/library/view/clean-code-a/9780136083238/) | A reference point for writing maintainable, readable, testable code (take its rules as prompts, not law). |
| [**Extreme Programming Explained**](https://www.amazon.com/Extreme-Programming-Explained-Embrace-Change/dp/0321278658) | How Agile really works in practice, and the developer's role in feedback-driven teams. |
| [**Accelerate**](https://itrevolution.com/product/accelerate/) | The evidence behind what makes software teams elite — and the home of the [DORA metrics]({{< relref "/handbook/engineering-foundations/delivery-metrics-agile.md" >}}). |

### 2. Next steps — architecture, DDD & microservices

| Book | Why |
|------|-----|
| [**Domain-Driven Design**](https://www.oreilly.com/library/view/domain-driven-design-tackling/0321125215/) — Eric Evans | The philosophy of building systems around the business domain. Foundational. |
| [**Implementing Domain-Driven Design**](https://www.oreilly.com/library/view/implementing-domain-driven-design/9780133039900/) — Vaughn Vernon | A more practical, code-heavy take on DDD, great for developers. |
| [**Building Microservices**](https://samnewman.io/books/building_microservices/) — Sam Newman | How to break down monoliths and handle real distributed-system challenges. |
| [**Software Architecture: The Hard Parts**](https://www.oreilly.com/library/view/software-architecture-the/9781492086888/) | A deep dive into architectural trade-offs — essential for scaling decisions. |

### 3. Deepen & scale — DevOps, observability & production

| Book | Why |
|------|-----|
| [**Designing Data-Intensive Applications**](https://dataintensive.net/) — Martin Kleppmann | The clearest book on how real systems store and move data at scale. The standout pick on this list. |
| [**The Phoenix Project**](https://itrevolution.com/product/the-phoenix-project/) | A novel that brings DevOps concepts to life in a business-friendly way. |
| [**The Unicorn Project**](https://itrevolution.com/product/the-unicorn-project/) | A follow-up from the developer's point of view — flow, feedback, and simplicity. |
| [**Site Reliability Engineering**](https://sre.google/books/) | How high-performing companies operate production systems at scale. |
| [**Team Topologies**](https://teamtopologies.com/book/) | Designing teams to scale software delivery — ideal for leadership and architecture roles. |

### 4. Specialized & platform topics

| Book | Why |
|------|-----|
| [**Pro ASP.NET Core**](https://www.apress.com/book/9781484279565) | One of the best practical guides for ASP.NET Core, from fundamentals to advanced. |
| [**Hands-On RESTful APIs with ASP.NET Core**](https://www.packtpub.com/product/hands-on-restful-api-design-patterns-and-best-practices/9781803230476) | Designing clean, maintainable APIs with modern .NET and async patterns. |
| [**Cloud Native .NET**](https://www.manning.com/books/cloud-native-net) | Building cloud-native apps in .NET with DAPR, containers, and MassTransit. |
| [**Azure for Architects**](https://www.packtpub.com/product/azure-for-architects-third-edition/9781800208294) | When to use App Services vs. AKS, hybrid deployments, and the security trade-offs. |

## A few more starting points

- Read Coding Horror, especially [Falling into the pit of success](https://blog.codinghorror.com/falling-into-the-pit-of-success/) — make the right thing the easy thing.
- Go deep with [How to learn APIs](https://newsletter.techworld-with-milan.com/p/how-to-learn-api).
- [The Odin Project](https://www.theodinproject.com/) is a strong, free full-stack curriculum if you (or someone you mentor) want hands-on practice.

{{< protip >}}
Don't read this list front to back like a syllabus. Read stage one properly, then let the work pull you into the rest — pick the book that matches the problem the team is hitting right now. A book you read because you needed it sticks; a book you read out of obligation evaporates.
{{< /protip >}}
