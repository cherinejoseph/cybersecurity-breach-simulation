# Cybersecurity Breach Investigation – Analysis Report

## 1. Can a hacker access Daikibo's manufacturing status dashboard directly from the internet?

### Key Finding:
No. The dashboard is hosted on Daikibo's internal network (intranet) and is only accessible through VPN. Therefore, an attacker without VPN access cannot directly reach the dashboard from the internet.

### Detailed Explanation:
The `web_activity.log` file contains only IP addresses from Daikibo's internal network, confirming that all activity comes from within the company's private network. There are no logs showing access from external or public IP addresses.

This means the dashboard is protected by network boundaries that prevent direct external access. The only way to remotely connect is via VPN tunneling, which requires valid credentials and additional security controls.

In the real world, this limits the attack surface and means that any breach would likely involve compromised internal credentials or VPN access, not a direct internet attack.

> *“The dashboard lives in Daikibo's intranet. The only remote access to it would be through VPN tunnelling.”* — Forage Virtual Experience

---

## Dashboard Accessibility Overview

Understanding whether the manufacturing status dashboard can be accessed directly from the internet is crucial for assessing the security risk.

### When Direct Internet Access is NOT Possible
- The dashboard is hosted exclusively on Daikibo's private intranet.
- Access requires VPN or other secure tunneling methods.
- Strong firewall and network perimeter controls block external access.
- No public-facing servers or portals expose the dashboard.

### When Direct Internet Access is Possible
- The dashboard is hosted on a public-facing server accessible without VPN.
- Weak or missing authentication allows unauthorized access.
- Compromised credentials are used to log in remotely.
- Network misconfigurations expose internal services to the internet.
- Security vulnerabilities allow attackers to bypass access controls.

In our investigation, the dashboard lives solely on the internal network, accessible only via VPN, so direct internet access by attackers is not possible.

---

## Hints and Methodology

During the investigation, the following hints and guidelines were essential for analyzing the `web_requests.log` file and identifying suspicious activity:

- **Log File Structure:**  
  - The log is divided into blocks separated by empty lines.  
  - Each block represents activity from a unique internal IP address (no duplicates).  
  - Each block starts with the IP address followed by a time-sorted table of requests to Daikibo’s telemetry dashboard.  
  - IP addresses are static and belong to Daikibo’s internal network.  
  - One block may represent multiple browsing sessions; sessions on different dates require new logins.  
  - There is no continuous polling or data pushing; users must refresh the page to get updates.

- **Analyzing Requests:**  
  - Look for longer sequences of requests per user to identify behavior patterns.  
  - Observe the typical request flow:  
    **Login → dashboard page resources (styles, scripts, images) → API requests for machine statuses.**  
  - Identify automated activity by spotting API requests occurring at exact intervals (e.g., once per hour), which the dashboard does not support natively.

- **Tools and Tips:**  
  - For easier inspection, open the log file in a code editor (like Sublime Text or Visual Studio Code).  
  - Expand the editor window to full width and reduce the font size so that no text wraps onto a new line, making it easier to read.

These guidelines helped pinpoint suspicious user behavior indicative of automated or unauthorized access.

---

## Log Analysis Approach

To identify suspicious user activity in the `web_requests.log` file, I followed these steps:

1. **Examined request timestamps:**  
   Looked for requests occurring at exact, regular intervals which may indicate automated behavior.

2. **Reviewed request sequences:**  
   Verified that normal user activity follows the pattern:  
   *Login → dashboard page resources (styles, scripts, images) → API requests for machine statuses.*

3. **Identified irregular patterns:**  
   Flagged users with requests that skipped expected steps or had unusually long sequences of API calls without refreshing or logging in again.

By applying these methods, I was able to pinpoint the user ID exhibiting the most suspicious activity.

---

## Understanding the API Requests

The manufacturing status dashboard updates machine data by making API requests in the background. These requests fetch real-time information without reloading the whole page.

Normal user behavior involves:
1. Logging in  
2. Loading the dashboard page (which loads necessary styles, scripts, and images)  
3. Occasionally refreshing or interacting, triggering API requests.

In this investigation, the suspicious user made API requests at exact, regular intervals (once per hour) without reloading the dashboard page resources. This pattern indicates automated polling — likely a script or bot — rather than human interaction.

Repeated, precisely timed API calls without page reloads are strong signs of unauthorized or automated activity.

---

## 2. User ID with the most suspicious activity

**User ID:** mdB7yD2dp1BFZPontHBQ1Z

**Reasoning:**  
This user showed suspicious behavior with requests at exact time intervals and unusual request patterns, indicating possible automated activity.

**Note:**  
Although these requests were successful, the timing and pattern strongly suggest unauthorized or automated activity. Successful responses do not guarantee legitimacy when behavior deviates from typical user interaction.

> *“It starts off with a regular login → browsing of the dashboard. But then it turns into a regular, once-per-hour automated check of the statuses in all 4 factories with no page resources being loaded and with an obviously non-human punctuality.”*  
> — Forage Virtual Experience Feedback

