---
layout: post
title: "How I Used AI to Hack My CrossFit Waitlist Problem (and Why It Changed My View on AI Development)"
subtitle: 
categories:
- blog
catalog: true
date:       2024-11-28
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1541534741688-6078c6bfb5c5?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=2000&q=80
tags: ["AI", "Development", "Personal Project", "CrossFit", "Technology"]
---

As a CTO who barely codes anymore, I never thought I'd build another app from scratch - until my CrossFit addiction and AI joined forces. Here's how I used generative AI to increase my chances of getting into popular CrossFit classes from 20% to 90%.

## The Problem: CrossFit Class Booking Blues

For two years, I've been battling with my CrossFit box's booking system. The classes are incredibly popular, requiring reservations up to 14 days in advance. Miss that window? Welcome to waitlist hell, often 15 people deep for the coveted after-work slots.

Here's the kicker: I'm terrible at planning ahead. My only hope was to pounce on cancellation notifications, but even my lightning-fast thumb wasn't enough. Why? The push notifications arrived with up to a minute delay between users. Even when I clicked within a second, I'd still miss out 80% of the time.

## The Light Bulb Moment

As someone who coded for a decade before getting more hands-off, I knew there had to be a better way. The solution was clear: why wait for delayed push notifications when I could query the API directly (which is not public by the way) ? But there was a catch - while the concept was simple, the modern development landscape had evolved significantly since my coding days.

## Enter AI: My Unexpected Coding Buddy

Last week, I decided to test all the "AI coding hype" I'd been seeing on social media. Armed with Cursor and Claude, I embarked on my diabolical plan to enhance my fitness journey.

The process was fascinating:

1. I started small, asking AI to create an interface displaying available classes, participant counts, and waitlist numbers
2. I extracted network calls (.HAR) from the official web app and fed them to Claude
3. Within an hour, I had my first "wow" moment - what would have taken me 4 hours in my familiar tech stack, I accomplished in unfamiliar territory (Astro & React)

## The Reality Check: AI Isn't Magic, It's a Partnership

Working with AI wasn't all smooth sailing:
- Sometimes it was like working with a brilliant but distracted child
- It would forget context ("You generated pure React, but we're in an Astro project, remember?")
- Adding features could break existing functionality (turns out AI shares some human coding traits 😅)

The key was learning to provide context effectively, whether sharing specific files or the entire project scope to ensure coherent solutions.

## The CTO Perspective: A Game-Changing Realization

This experience transformed my view on AI tools in development. While some of my team already uses GitHub Copilot or Cursor, this personal project revealed the true potential of AI-assisted development. It's not about replacing developers - it's about forming an effective partnership with AI tools.

## Key Takeaways

1. **Clear Requirements Matter**: AI needs well-defined scope and expectations
2. **Context is King**: The limiting factor is often human communication - vague requests yield vague results
3. **Embrace Evolution**: Tech constantly changes; while core concepts remain, tools and frameworks evolve
4. **AI as an Enabler**: It can help experienced developers work with unfamiliar technologies more effectively

P.S. Did my evil plan work? Absolutely. My success rate for getting into cancelled CrossFit slots jumped from 20% to 90%. Sometimes the best coding projects start with a selfish need! 

---

*This post was written by Tristan Bessoussa, CTO at Wamiz (Nestlé), where I am exploring ways to blend technology with practical solutions - even if it means gaming the system for a better workout! 💪* 

*This post was then enhanced by AI, and If you read this it until the very end, it means the AI was better at writing than me*
