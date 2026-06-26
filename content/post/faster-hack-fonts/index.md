---
title: "Faster Hack Fonts"
date: 2025-10-01
draft: false
categories:
    - Development
    - Tools
---

I rage created a new repo on my Github, all because I couldn't remember how the heck to install a machine-wide font.

Picture this: it's 2 AM, I'm setting up a new development environment, and I just want to install the Hack font because—let's be honest—it's the only monospace font that doesn't make my ADHD brain want to set everything on fire. Simple request, right?

## The Font Installation Dance of Death

Wrong. So very wrong.

First, I have to go to the official Hack repository on GitHub. Then I need to navigate through their releases, find the right version, download a ZIP file that's named something like "hack-v3.003-ttf.zip" (which, by the way, tells me nothing about whether this includes all the variants I need). Then I extract it, and now I have a folder full of individual TTF files that I need to manually copy to `/usr/share/fonts/truetype/` or `~/.local/share/fonts/` depending on whether I want system-wide or user-local installation.

But wait, there's more! Now I need to run `fc-cache -f -v` to update the font cache. And if I'm on macOS, it's a different dance entirely—drag files to Font Book, or copy them to `/Library/Fonts/`, and pray that the system actually picks them up.

By the time I finally had Hack installed and working, I was wide awake, frustrated, and thinking: "There has to be a better way."

## The Git Clone Epiphany

See, here's the thing about my ADHD brain—it loves patterns. And I realized I was doing this same font installation dance every time I set up a new machine, every time I spun up a new Docker container for development, every time I helped a colleague get their environment configured.

And then it hit me: what if installing fonts could be as simple as cloning a repository?

What if, instead of all that manual downloading and extracting and copying, you could just open MacOS Terminal and run:

```bash
cd /Library/Fonts && git clone https://github.com/SamPlaysKeys/hack-fonts.git
```

Or, if it was on a Linux machine like my RHEL10 dev instance:

```bash
sudo git clone https://github.com/SamPlaysKeys/hack-fonts.git /usr/share/fonts/hack-fonts && sudo fc-cache -f -v
```

That's it. One command. Boom. Fonts installed.

## Building the Solution (Because I Apparently Hate Myself)

So naturally, instead of going to bed like a normal person, I built exactly that.

I created [https://github.com/SamPlaysKeys/hack-fonts](https://github.com/SamPlaysKeys/hack-fonts) This is a repo that's literally just the Hack font family organized in a way that makes installation brain-dead simple.

The structure is pretty straightforward:

```
hack-fonts/
├── ttf/               # Original Hack TTF files
│   ├── Hack-Regular.ttf
│   ├── Hack-Bold.ttf
│   ├── Hack-Italic.ttf
│   └── Hack-BoldItalic.ttf
├── otf/               # Nerd Font variants with extra glyphs
│   ├── HackNerdFont-Regular.otf
│   ├── HackNerdFont-Bold.otf
│   ├── HackNerdFont-Italic.otf
│   └── HackNerdFont-BoldItalic.otf
└── web/               # Web font files for projects
    ├── demo.html
    ├── stylesheet.css
    └── *.woff/woff2 files
```

The beautiful thing is that when you clone this directly into your system's font directory, everything just works. The OS font system picks up the files automatically, and you're done.

## The "But Wait, There's More" Problem

Of course, because I have ADHD and apparently can't do anything halfway, I didn't stop at just the basic TTF files. I also included the Nerd Font variants—you know, the ones with all the extra glyphs for terminal icons and PowerLine symbols and all that jazz that makes your terminal look like something from the future.

And then I thought, "You know what developers also need? Web fonts." So I threw in WOFF and WOFF2 versions with a ready-to-use CSS file. Because why solve one problem when you can solve three?

I even built a demo page at [samplayskeys.com/shared/2025/hack_font_demo.html](https://samplayskeys.com/shared/2025/hack_font_demo.html) so you can see all the variants in action before installing anything.

## The Attribution Thing (Because I'm Not a Monster)

Now, here's where the responsible adult in me had to step in. 
Christopher Simpkins created the Hack font based on Bitstream Vera Sans Mono. It's got proper open source licensing, and I wanted to make absolutely sure I wasn't stepping on anyone's toes.

As a way to do this properly, I included all the original license files, attribution documents, and links back to the official repository at [source-foundry/Hack](https://github.com/source-foundry/Hack). This isn't about taking credit for someone else's work—it's about making that excellent work more accessible.

Think of it like... a font delivery service. The original creators did the hard work of designing beautiful, readable monospace characters. I just figured out a more convenient way to get them onto your machine.

## The Broader Point About Developer Experience

The thing is, this isn't really about fonts, is it? It's about all the tiny friction points in our development workflows that I just... accept. Because that's how it's always been done.

I will spend hours configuring development environments, manually downloading and installing tools, copying config files around, and act like this is normal. Like it's just the cost of doing business as a developer.

But my ADHD brain refuses to accept "that's just how it is" as an answer. Every manual step is a place where I'm going to get distracted, make a mistake, or just give up and use a suboptimal default because the proper setup is too much friction.

Font installation is just one example. How many times have you needed to set up a new machine and spent half a day just getting your terminal to look and feel the way you want it? How many times have you helped a new team member and watched them struggle through the same setup steps you've done dozens of times?

## The Philosophy of Small Improvements

I'm not claiming this repository is going to change the world. It's not revolutionary technology. It's just... slightly better than the status quo.

But you know what? Sometimes that's enough. Sometimes making something go from "annoying and manual" to "one command and done" is a meaningful improvement, even if it's not going to win any innovation awards.

The idealist in me believes that if we can eliminate enough of these small friction points, we can spend more time on the work that actually matters. The jaded industry veteran in me knows that for every problem we solve, three new ones will appear.

But I'd rather be solving problems than just complaining about them.

## The Simple Install Commands

So here's how it works in practice:

**macOS (system-wide):**
```bash
sudo git clone https://github.com/SamPlaysKeys/hack-fonts.git /Library/Fonts/hack-fonts
```

**macOS (user-only):**
```bash
git clone https://github.com/SamPlaysKeys/hack-fonts.git ~/Library/Fonts/hack-fonts
```

**Linux (system-wide):**
```bash
sudo git clone https://github.com/SamPlaysKeys/hack-fonts.git /usr/share/fonts/hack-fonts
sudo fc-cache -f -v
```

**Linux (user-only):**
```bash
mkdir -p ~/.local/share/fonts
git clone https://github.com/SamPlaysKeys/hack-fonts.git ~/.local/share/fonts/hack-fonts
fc-cache -f -v
```

That's it. No ZIP files to download. No manual extraction. No hunting through folder structures to find the right files. Just clone and go.

## Try It Out (Or Don't, I'm Not Your Boss)

If any of this resonates with you, go ahead and try the hack-fonts repository. The worst-case scenario is that you waste thirty seconds cloning a repository. The best-case scenario is that you never have to manually install fonts again.

And if you find issues, or have ideas for improvement, or want to extend this approach to other tools—well, that's what GitHub is for, right?

Because here's the thing: I built this for me, to solve my own font installation frustrations. But if it helps other developers avoid the same 2 AM font configuration rage, then maybe we've made the world slightly less annoying.

And in a world full of complex problems, sometimes making things slightly less annoying is a pretty good day's work.

---

*Check out the [hack-fonts repository](https://github.com/SamPlaysKeys/hack-fonts) on GitHub, or try the [live font demo](https://samplayskeys.com/shared/2025/hack_font_demo.html) to see all the variants in action.*
