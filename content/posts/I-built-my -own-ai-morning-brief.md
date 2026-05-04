---
title: "I Built My Own AI Morning Brief on a Raspberry Pi"
date: 2026-05-04
tags: [homelab, automation, n8n, claude, raspberry-pi]
description: "How I replaced my morning routine of checking 5 different apps with a single email — built on n8n, Claude, and a $35 computer."
---

# I Built My Own AI Morning Brief on a Raspberry Pi

I wake up at 4:45 AM. By 5:00 I'm downstairs making pre-workout, by 5:30 I'm at the gym. Somewhere in that 45-minute window I used to check my phone five times — calendar, weather, todo list, macro tracker, work email — just to figure out what the day actually looked like.

That's a stupid amount of friction at 4:45 in the morning. So I built a thing.

## What it does

Every weekday at 4:45 AM, an email shows up in my inbox with everything I need for the day in one place:

- Today's weather and what to wear for the commute
- Every meeting and event from my Google Calendar
- My nutrition targets (calories, protein, carbs, fat)
- A short motivational line — not the cheesy kind, more stoic
- Anything I flagged as a top priority the night before

It's clean HTML, properly formatted, and it lands before my alarm even goes off. I open my phone, read one email, and I'm done. No app-hopping.

## The stack

The whole thing runs on a Raspberry Pi 4 sitting on a shelf in my room. Total cost: a Pi I already had and zero dollars in monthly subscriptions.

The pieces:

- **n8n** — the automation engine that ties everything together. Self-hosted in Docker behind Nginx Proxy Manager and a Cloudflare Tunnel, so I can hit the UI from anywhere
- **Google Calendar API** — pulls today's events
- **Claude API** — generates the actual content of the brief, including the personalized line and weather commentary
- **Gmail API** — sends the final email
- **Cron trigger** — fires the workflow at 4:45 AM Monday through Friday

The n8n workflow itself is maybe 8 nodes. Calendar in, weather in, macros pulled from a static config, all of it gets stuffed into a Claude prompt, Claude returns clean HTML, Gmail ships it out.

## The painful parts

It wasn't quite plug-and-play.

Getting the Cloudflare Tunnel routing right took a couple of attempts — n8n needed a specific subdomain config and I had a port collision with Vaultwarden running on the same Pi. Once I sorted out the routing it was fine.

Google OAuth2 was the usual annoying dance. Create the project, configure the consent screen, add the scopes, generate credentials, paste them into n8n, hope it works. It worked on the second try.

The biggest gotcha was the Claude API integration. n8n has a quirk where you need to switch certain fields to "expression mode" for them to actually parse variables. I spent way too long debugging why my prompt wasn't pulling in calendar data before I figured that out. Also had to wrestle with HTML rendering — Claude was returning markdown by default and Gmail was showing it raw until I tightened the system prompt.

## Why bother?

A lot of people would say "just use Notion AI" or "Google has Gemini for this now." Sure. But I wanted to actually own the thing. I wanted to know exactly what data goes where, exactly what model is being called, and exactly what shows up in my inbox. Self-hosted means I'm not waiting for someone else's product roadmap.

It also doubles as practice. n8n, OAuth flows, API integrations, Docker, reverse proxies, Cloudflare Tunnels — that's a ton of skills reinforced just by building something I actually use.

## What's next

Eventually I want to add a couple more data sources — pull from my Obsidian daily notes, surface anything I flagged the night before, maybe wire in a Pomodoro counter that resets daily. But the current version already saves me real friction every morning, which is the whole point.

If you're sitting on a homelab and you've been looking for a reason to dig into n8n, build something like this. It's small enough to finish in a weekend and useful enough that you'll never turn it off.
