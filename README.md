**Platform:** LetsDefend

**Module:** SOC141 - Phishing URL

**Difficulty:** Easy

**Target IP:** 172.16.17.49

**Target Username:** EmilyComp

**Date:** 2026-06-11

---

<h3 align =center> Walkthrough </h3>

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

<br>
&nbsp;&nbsp;&nbsp;&nbsp; Once you've reviewed the alert, head to Case Management and start the playbook. Think of the playbook as your structured checklist — it makes sure you don't skip steps under pressure.
<br>
<br>

<img width="2545" height="589" alt="p-2" src="https://github.com/user-attachments/assets/df7322f2-39aa-4458-bac4-d8ca6508905c" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp;The first step is Collection Data, 
<br>
<br>

<img width="1171" height="546" alt="p-3" src="https://github.com/user-attachments/assets/bc257afe-68f3-4dea-b47e-953ae9a0c21e" />

<br>

- Source Address = 172.16.17.49

- destination Address = 91.189.114.8
 
- User-Agent = Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.88 Safari/537.36

&nbsp;&nbsp;&nbsp;&nbsp;The next step is Log Investigation,
<br>

<img width="1183" height="547" alt="p-4" src="https://github.com/user-attachments/assets/4ba5e09b-8176-455d-875b-d189e9cea91e" />


---
***Log Investigation:***

<br>
&nbsp;&nbsp;&nbsp;&nbsp;Now this is where you start building your evidence. What you're trying to answer here simple : did this connection actually happen and what did it look like? Search for source_address contains "172.16.17.49".
<br>
<br>

<img width="2510" height="995" alt="p-5" src="https://github.com/user-attachments/assets/2096433b-5eef-4d8e-a6ee-fd38e6feacee" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; You'll get 7 events, but forcus on thr two from March 22, 2021 at 09:23pm.
<br>
<br>

<img width="1720" height="578" alt="p-6" src="https://github.com/user-attachments/assets/f80e8177-8f69-491f-bdf3-72d14923babd" />

<img width="1584" height="529" alt="p-7" src="https://github.com/user-attachments/assets/a06aec26-c810-4550-ab88-5eef19493fcf" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; One is a Proxy log showing the full request URL going out to mogagrocol.ru, and the other is a Firewall log for the same source and destination with an empty raw body. What does that tell us? It tells us the request was made at the proxy level and the firewall pass request. Both logs together confirm the traffic was real.
<br>

---

***URL Analysis:***

<br>
&nbsp;&nbsp;&nbsp;&nbsp; At this point you have a suspicious URL and you need to know if it's actually malicious or false positive.
<br>
<br>

<img width="1200" height="681" alt="p-8" src="https://github.com/user-attachments/assets/39fb6f08-118c-4d2f-8904-547d1a5231a2" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Take the full request URL and submit it to VirusTotal,
<br>
<br>

<img width="2560" height="1414" alt="p-10" src="https://github.com/user-attachments/assets/789bd610-e3f5-4aa3-b3f6-a6fbab12fe65" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; What you're looking for is the detection ratio and what category vendors are assigning it.
<br>
<br>

<img width="2560" height="1464" alt="p-11" src="https://github.com/user-attachments/assets/263da8fc-7388-4430-ade2-70d9658d0fc4" />


<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; A 14/92 detection ratio on a URL with ellie's email literally embedded as a parameter is more than enough to confirm this is a credential harvesting phishing site. At this stage you can confidently say this is a True Positive.
<br>
<br>

---
***Endpoint Containment:***

<br>
&nbsp;&nbsp;&nbsp;&nbsp; Once you've confirmed a True Positive, we need to containment the target host.Go to Endpoint Security, search for EmilyComp, and turn Containment on.
<br>
<br>

<img width="2012" height="1138" alt="p-12" src="https://github.com/user-attachments/assets/1c6ae266-b5bb-4c5e-8ade-5d43c0ea8e5f" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; This isolates the host from the rest of the network immediately. The reason this matters is that if credentials were harvested, an attacker could already be trying to use that access to move laterally across the network. Containment cuts that path off.
<br>
<br>

---
***Documertation:***

<br>
&nbsp;&nbsp;&nbsp;&nbsp; After containment, go back to the playbook and add your artifacts. This step is about formally logging your IOCs so they're tied to the case and searchable for future investigations. Add the full phishing URL and IP address.
<br>
<br>

<img width="1191" height="739" alt="p-13" src="https://github.com/user-attachments/assets/331d1bb0-133d-4d47-a68f-0f895afd3fda" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The reason you document both the URL and the IP separately is that the domain could change while the IP stays the same. These both gives future analysts more options to detect related activity. The Analyst Note is your chance to tell the full story of what happened in plain language.
<br>

<img width="1198" height="741" alt="p-14" src="https://github.com/user-attachments/assets/ff2500c6-11fc-4248-a3f3-660e1e016d55" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; A good analyst note leaves no questions unanswered.
<br>
<br>

---
***Close The Alert:***

<br>
&nbsp;&nbsp;&nbsp;&nbsp; The final step is closing the alert in Case Management.
<br>
<br>

<img width="913" height="654" alt="p-15" src="https://github.com/user-attachments/assets/c3819ead-9d88-431c-9f52-a9ce02465ecb" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp;"Never close an alert as a false positive just because you want to move on."
<br>

---
<h3 align =center> Summary </h3>

<br>
&nbsp;&nbsp;&nbsp;&nbsp; This alert was a confirmed True Positive phishing incident. User ellie on EmilyComp (172.16.17.49) clicked a malicious link that the firewall allowed through, successfully connecting her machine to a credential-harvesting site at mogagrocol.ru (91.189.114.8). VirusTotal confirmed the URL as malicious with 14/92 vendor detections. The host was contained, IOCs were documented, and the case was closed as a True Positive.
<br>

