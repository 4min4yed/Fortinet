# FortiGate Email Alerts & Automation Setup Guide

## Part 1: Setting Email Alerts in FortiGate

### Step 1: Configure SMTP Server

Before setting up AlertMail, you must configure an SMTP server. Navigate to:
**System > Email Service**

#### Configure SMTP:

```bash
> config system email-server
    > set server "smtp.office365.com"
    > set source-ip 0.0.0.0    # IP used to request the mail server
> end
```

**Verify Connectivity:**

Test the SMTP server connectivity (ping smtp.office365.com):

```bash
> diagnose test email-server smtp.office365.com
```

---

### Step 2: Configure AlertMail Settings

Display the default **AlertMail** configuration:

```bash
> config alertmail setting
    > show full configuration
```

**Example Output:**

The default AlertMail configuration includes various alert settings and log collection options.

### Step 3: Set AlertMail Credentials

Configure the AlertMail user and recipient:

```bash
> config alertmail setting
    > set username "user@name.com"
    > set mailto1 "<your_email>"
> end
```

> **Important**: Username should match the SMTP account credentials (Office365/Gmail)

---

### Step 4: Enable Alert Types

Enable the specific alerts you want to receive:

```bash
> config alertmail setting
    set HA-logs enable
    set FDS-license-expiring-warning enable
    set FDS-license-expiring-days 15
> end
```

**Available Alert Types:**

Common alerts include:
- **HA logs** - High Availability related events
- **FDS license expiration** - FortiGuard service license warnings
- **System logs** - Critical system events
- **Security logs** - Security-related events

---

### Step 5: Test AlertMail Configuration

Test the alertmail settings:

```bash
> diagnose log alertmail setting
```

### Step 6: Monitor Email Sending

After 1 minute, check if emails were sent:

```bash
> diagnose debug reset
> diagnose debug application alertmail -1
> diagnose debug enable
> diagnose log alertmail test
```

---

## Part 2: Setting Automation in FortiGate

Automation allows you to automate responses to specific events via **Triggers** → **Stitches** → **Actions**.

### Architecture Overview

**Three Components:**

| Component | Purpose |
|-----------|---------|
| **TRIGGER** | Detects an event (e.g., internet loss, system reboot) |
| **STITCH** | Links triggers to actions |
| **ACTION** | Performs an action (e.g., send email alert) |

**Flow**: `TRIGGER` → activates via `STITCH` → executes `ACTION`

---

### Example: Email Alert on Internet Loss

**Objective**: Send email alert when internet connectivity is lost

**Components Needed:**

1. **Trigger**: Internet link goes down
2. **Action**: Send email notification
3. **Stitch**: Links the internet down event to email action

---

### Step 1: Create a Trigger (Event Log ID)

Navigate to **System > Automation > Trigger**

Create a trigger for **System Reboot**:

- **Event Type**: Log ID
- **Log ID**: 32009 (`LOG_ID_SYSTEM_START`)
- **Name**: "Reboot"

>  Note: Reboot triggers are better set as event log IDs because the system restarts before FortiGate can send the mail during a reboot.

---

### Step 2: Create an Action

Navigate to **System > Automation > Action**

Create an action to send email:

- **Action Type**: Send Email
- **From-Email**: Should match the `AlertMail` configuration email
- **Subject**: "FortiGate Alert: [Device Name] Restarted"
- **Message**: "Your FortiGate [Device Name] has restarted at [timestamp]"

---

### Step 3: Create a Stitch (Link Trigger to Action)

Navigate to **System > Automation > Stitch**

Create a stitch named "Reboot":

- **Trigger**: Select the "Reboot" trigger created in Step 1
- **Action**: Select the email action created in Step 2

---

### Step 4: Test the Automation

Test the automation trigger:

```bash
> diagnose automation test Reboot "logid=32009 type=event subtype=system level=notice vd=root msg=\"FortiGate started\""
```

**Expected Output:**

```
automation test is done. stitch:Reboot
```

---

## Important: Office365/Gmail 2FA Requirements

### Enable 2FA First

To send emails via Office365 or Gmail, you **MUST** enable Two-Factor Authentication (2FA) on your account first.

### Create App Password

Only after 2FA is enabled, you can create an **App Password**:

1. Enable 2FA on your Office365/Gmail account
2. Visit the [Account Security Page](https://myaccount.microsoft.com/security-info) for Microsoft accounts
3. Create and manage app passwords from this link
4. Use the generated **app password** in FortiGate (not your regular password)

---

## Available FortiGate Event Log IDs

Common event log IDs for automations:

| Event | Log ID | Description |
|-------|--------|-------------|
| System Start | 32009 | `LOG_ID_SYSTEM_START` - FortiGate started |
| System Shutdown | 32008 | `LOG_ID_SYSTEM_SHUTDOWN` - FortiGate shutting down |
| HA Failover | 33100+ | High Availability failover events |
| License Expiring | 26001+ | FortiGuard license expiration warnings |
| WAN Link Down | 40001+ | Internet/Link down events |

---

## Troubleshooting Email Alerts

### Enable Debug Logging

Debug the alertmail mechanism:

```bash
> diagnose debug reset
> diagnose debug application alertmail -1
> diagnose debug enable
> diagnose log alertmail test
```

### Check System Email Configuration

Verify email server settings:

```bash
> config system email-server
    > show
> end
```

### Check AlertMail Status

View current AlertMail configuration:

```bash
> config alertmail setting
    > show full configuration
> end
```

### Verify SMTP Connectivity

Test SMTP server connection:

```bash
> execute email-server-test
```

---

## Best Practices

###  Do's

- Enable 2FA before setting up O365/Gmail email alerts
- Use **event log IDs** for triggers instead of immediate actions (more reliable)
- Test alerts before deploying to production
- Set reasonable alert intervals (batch every 5 minutes vs instant)
- Document which automations are active and why

### Don'ts

- Don't use regular passwords with Office365/Gmail - always use app passwords
- Don't create overlapping automations that trigger simultaneously
- Don't disable logging needed for automation triggers
- Don't forget to enable the specific alerts you want in AlertMail

---

## Common Automation Examples

### Alert on License Expiration

**Trigger**: FDS license expiring in 15 days
**Action**: Send warning email
**Stitch**: Link license expiry trigger to email action

### Alert on High Availability Failover

**Trigger**: HA failover event occurs
**Action**: Send alert email to security team
**Stitch**: Link HA failover to email action

### Alert on System Reboot

**Trigger**: System reboot detected (log ID 32009)
**Action**: Send email notification
**Stitch**: Link system start to email action

---

## Quick Reference

### Configuration Paths

- Email Server: `System > Email Service`
- AlertMail: `System > AlertMail Setting`
- Automation: `System > Automation > Trigger/Action/Stitch`

### Key Commands

| Command | Purpose |
|---------|---------|
| `config system email-server` | Configure SMTP |
| `config alertmail setting` | Configure AlertMail |
| `execute email-server-test` | Test SMTP connectivity |
| `diagnose automation test` | Test automation triggers |
| `diagnose debug application alertmail -1` | Debug email sending |

---

## Summary

1. Configure SMTP server (Email Service)
2. Set up AlertMail credentials and enable desired alerts
3. Test email connectivity
4. Create automation triggers for specific events
5. Create actions to respond to those events (send email)
6. Create stitches to link triggers with actions
7. Test automations before production deployment
8. Monitor logs for successful execution

With proper configuration, you'll receive timely email alerts for critical FortiGate events and automations.
