---
title: "Skype for Business blurry during screenshare"
date: 2019-02-16T09:43:56-09:00
tags: ["O365", "SkypeForBusiness"]
---

Recently I've encountered an issue when screensharing via Skype for Business with some users. The remotely presented screen will be blurry, but if the remote viewer requests control the screenshare will suddenly become clear for them and stay that way even after they release control.

I'm guessing there's probably something going wrong with the VbSS (Video based Screen Sharing) mechanism that Microsoft uses now in Skype for Business for screen sharing.

Forcing Skype to revert back to using RDP (which perhaps is also what happens when a user requests control) resolved the issue for me.

Run the following reg file, or manually create the below entries, to disable VbSS and make sure to completely restart Skype.
(Note that this is assuming you're using the Office 2016 / ver 16.* version of Skype for Business ) :

<script src="https://gist.github.com/CodeAndLoathing/df329d47b66a9e0a2938439bf4bd77f7.js"></script>

To re-enable VbSS simple remove the above created reg entries or run the below reg file :

<script src="https://gist.github.com/CodeAndLoathing/f19f90ba0e07a77e557884cb008904df.js"></script>
