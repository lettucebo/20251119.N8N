# n8n Cloud Free Trial Guide

> This document provides an English-only walkthrough for provisioning and operating an n8n Cloud free-trial workspace.

## Table of Contents

- [n8n Cloud Free Trial Guide](#n8n-cloud-free-trial-guide)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
    - [Highlights](#highlights)
  - [Signup Steps](#signup-steps)
  - [Verification \& Activation](#verification--activation)
  - [Trial Limits](#trial-limits)
  - [Administration \& Maintenance](#administration--maintenance)
  - [FAQ \& Troubleshooting](#faq--troubleshooting)
    - [How do I cancel the trial?](#how-do-i-cancel-the-trial)
    - [What if I need more than 14 days or 1,000 executions?](#what-if-i-need-more-than-14-days-or-1000-executions)
    - [Can’t reach the Admin Dashboard?](#cant-reach-the-admin-dashboard)
    - [Can I recover workflows after expiration?](#can-i-recover-workflows-after-expiration)

## Introduction

The n8n Cloud free trial grants 14 days of Pro-plan capabilities—Global Variables, the Insights dashboard, Execution Search, and five days of workflow history—running on Starter-plan compute with a 1,000-execution cap so you can evaluate automation safely in the managed cloud. (Source: [n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

### Highlights

- Auto-expiring 14-day window with no manual cancellation required.
- Pro-grade features paired with Starter-level compute and a 1,000-execution cap.
- 90-day grace period after expiration to download workflow backups. (Source: [n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

## Signup Steps

![Screenshot Placeholder – Step 1](./images/placeholder-step1.png)

Visit [n8n Cloud](https://n8n.io/pricing/) and click "Get started for free". Provide your name plus work email, then submit to receive the workspace-creation link in your inbox.

![Screenshot Placeholder – Step 2](./images/placeholder-step2.png)

Inside the creation wizard, define your workspace name, hosting region, and first admin credentials, choose a strong password, accept the policies, and press "Create workspace" to provision the trial.

![Screenshot Placeholder – Step 3](./images/placeholder-step3.png)

Open the confirmation email, click the embedded magic link to activate the account, and you’ll land in your workspace; on the first login you can load an official template or import your own workflow JSON.

## Verification & Activation

If the app appears offline after provisioning, log in again and request a "Send magic link" email to access the Admin Dashboard securely; once inside, set the workspace timezone, notification preferences, and enable 2FA/SSO in Settings to harden the trial environment. (Source: [Cloud admin dashboard](https://docs.n8n.io/manage-cloud/cloud-admin-dashboard/))

## Trial Limits

- Duration: 14 days; the workspace auto-expires and is deleted afterward.
- Features: Full Pro-plan set (Global Variables, Insights Dashboard, Execution Search, five-day workflow history).
- Resources: Starter-level compute with a 1,000-execution ceiling; data is purged after expiration.
- Export: Download workflows for up to 90 days post-expiration to continue in self-hosted or paid plans.
- Payment: Click the upper-right "Upgrade" button any time to choose annual or monthly billing. (Source: [n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

## Administration & Maintenance

Use the Admin Dashboard to review versions, toggle workflows, and configure the timezone; if the UI is unreachable, requesting another magic link still grants direct dashboard access. Monitor the 1,000-execution quota via the Executions view, upgrade before you run out, and leverage [Download workflows](https://docs.n8n.io/manage-cloud/download-workflows/) to back up automations before the trial wipes the workspace. (Source: [Cloud admin dashboard](https://docs.n8n.io/manage-cloud/cloud-admin-dashboard/))

## FAQ & Troubleshooting

### How do I cancel the trial?

You don’t have to cancel—once the 14-day window ends, the workspace expires automatically with no charge; just download any workflows you want to keep. (Source: [n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

### What if I need more than 14 days or 1,000 executions?

Click the "Upgrade" button in the top-right corner to switch to a paid plan instantly; if you need Enterprise-only perks (environments, SSO, external secret stores), use the "Contact" button on the pricing page to request an Enterprise trial. (Source: [n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

### Can’t reach the Admin Dashboard?

On the login screen choose "Send magic link"; the email link lets you jump straight into the dashboard even if the main app is down or under maintenance. (Source: [Cloud admin dashboard](https://docs.n8n.io/manage-cloud/cloud-admin-dashboard/))

### Can I recover workflows after expiration?

You have a 90-day grace period to export workflow JSONs; after that, the workspace and its data are removed, so back up via the Download workflows tool ahead of time. (Source: [n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))
