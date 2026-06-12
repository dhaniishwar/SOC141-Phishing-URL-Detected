**Platform:** LetsDefend

**Module:** SOC141 - Phishing URL

**Difficulty:** Easy

**Target IP:** 172.16.17.49

**Target Username:** EmilyComp

**Date:** 2026-06-11

---

<h4 align =center> Walkthrough </h4>

***Alert Review:***

<br>
&nbsp;&nbsp;&nbsp;&nbsp; Opening the alert in the Monitoring tab under the Investigation Channel reveals all the critical details. 
<br>
<br>

<img width="2534" height="950" alt="p-1" src="https://github.com/user-attachments/assets/fb9537eb-3543-41bc-a458-f5a4824598c9" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The alert showed that a user named ellie on a machine called EmilyComp (172.16.17.49) had made a request to
<br>
<br>

```bash
 http://mogagrocol.ru/wp-content/plugins/akismet/fv/index.php?email=ellie@letsdefend.io
```

<br>
&nbsp;&nbsp;&nbsp;&nbsp; Which hitting destination IP 91.189.114.8. The thing that immediately caught my attention was the Device Action field — it said Allowed. That means the firewall didn't block anything, the connection actually went through. That's not great.
<br>

---
***Starting the Playbook:***

