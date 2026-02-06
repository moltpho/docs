# Moltpho Documentation

Moltpho is a payments platform that lets your AI agent shop on Amazon using your credit card. You set the rules — spending limits, allowed categories, shipping address — and your agent handles the rest.

This guide covers everything you need to know as an owner: setup, configuration, day-to-day management, and troubleshooting.

---

## Table of Contents

- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Step 1: Install the Skill](#step-1-install-the-skill)
  - [Step 2: Set Up Your Agent](#step-2-set-up-your-agent)
  - [Step 3: Claim Your Agent](#step-3-claim-your-agent)
  - [Step 4: Add a Payment Method](#step-4-add-a-payment-method)
  - [Step 5: Configure Your Agent](#step-5-configure-your-agent)
- [The Dashboard](#the-dashboard)
  - [Overview](#overview)
  - [Agents](#agents)
  - [Orders](#orders)
  - [Payments](#payments)
  - [Support](#support)
- [How Purchasing Works](#how-purchasing-works)
  - [On-Demand Purchases](#on-demand-purchases)
  - [Proactive Purchases](#proactive-purchases)
  - [Order Statuses](#order-statuses)
  - [Cancellations](#cancellations)
- [Credits and Billing](#credits-and-billing)
  - [How Credits Work](#how-credits-work)
  - [Weekly Top-Ups](#weekly-top-ups)
  - [Pricing](#pricing)
  - [Refunds](#refunds)
- [Agent Settings](#agent-settings)
  - [Purchasing Behavior](#purchasing-behavior)
  - [Spending Limits](#spending-limits)
  - [Category Filters](#category-filters)
  - [Shipping Address](#shipping-address)
  - [Card Pools](#card-pools)
- [Support Tickets](#support-tickets)
  - [Creating a Ticket](#creating-a-ticket)
  - [Ticket Types](#ticket-types)
  - [Ticket Statuses](#ticket-statuses)
- [Agent States](#agent-states)
- [Blocked Items](#blocked-items)
- [Frequently Asked Questions](#frequently-asked-questions)
- [Limits and Constraints](#limits-and-constraints)
- [Troubleshooting](#troubleshooting)

---

## Getting Started

### Prerequisites

- An [OpenClaw](https://openclaw.ai) agent
- A US-based shipping address (international shipping is not yet supported)
- A credit or debit card

### Step 1: Install the Skill

There are two ways to add Moltpho to your OpenClaw agent.

**Option A: ClawHub (recommended)**

```bash
clawhub install moltpho
```

**Option B: Manual installation**

```bash
git clone https://github.com/moltpho/skill.git
cp -r skill/moltpho ~/.openclaw/skills/
openclaw skill reload
```

After installation, tell your agent:

> "setup moltpho"

The agent will run its bootstrap process, register itself with Moltpho, and provide you with a claim link.

### Step 2: Set Up Your Agent

When your agent runs `setup moltpho` for the first time, it will:

1. Check for existing Moltpho credentials
2. If none are found, register a new agent with the Moltpho API
3. Store credentials locally at:
   - **macOS/Linux:** `~/.config/moltpho/credentials.json`
   - **Windows:** `%APPDATA%\moltpho\credentials.json`
4. Generate a claim link and open it in your browser

At this point, your agent is registered but **unclaimed**. It cannot make purchases until you complete the claiming process.

### Step 3: Claim Your Agent

The claim link takes you to the Moltpho portal. If you don't have an account yet, you'll be asked to sign in with your email — Moltpho uses passwordless authentication, so you'll receive a magic link via email instead of setting a password.

Once signed in, confirm the claim. Your agent is now linked to your account.

Claim links expire after **24 hours** and are bound to the device that first opened them. If a link expires, ask your agent to run setup again to generate a new one.

### Step 4: Add a Payment Method

Navigate to **Payments** in the dashboard sidebar. Click **Add Card** and enter your card details through Stripe's secure checkout form. Your card information is stored by Stripe — Moltpho never sees your full card number.

You can add multiple cards and organize them into **card pools** (more on that in [Card Pools](#card-pools)).

### Step 5: Configure Your Agent

Before your agent can shop, you need to configure three things:

1. **Credit limit** — Go to your agent's settings and set a target credit limit. This determines how much your agent can spend. A weekly top-up will restore the balance to this amount.

2. **Shipping address** — Go to **Agent Settings > Shipping Address** and enter a valid US address. Orders cannot be placed without one.

3. **Spending limits** — Optionally set a per-order cap and daily cap to control how much your agent can spend at once or in a single day.

Your agent is now ready to shop.

---

## The Dashboard

The dashboard is your control center at [portal.moltpho.com](https://portal.moltpho.com). It has five main sections accessible from the sidebar.

### Overview

The main dashboard page gives you a snapshot of your agent's activity:

- **Balance card** — Available credit, staged refunds, and spending totals for the current week and month.
- **Quick stats** — Total orders, active orders, and failed orders across all time.
- **Recent orders** — The five most recent transactions with item names, amounts, and status badges.
- **Quick actions** — Shortcuts to agent settings, agent list, and payment methods.

If you have multiple agents, tabs at the top let you switch between them.

### Agents

The Agents page lists every agent linked to your account. Each agent card shows:

- Display name and description
- Wallet address
- Status (Active, Unclaimed, Degraded, or Suspended)
- Available credit balance
- Total spent this month

Click an agent to see its full detail page, which includes balance breakdowns, recent orders, and a link to settings.

### Orders

The Orders page is a searchable, filterable list of every order across all your agents. You can:

- **Filter by date range** using the From and To date pickers
- **Filter by agent** when navigating from a specific agent's page
- **View order details** by clicking any row

Each order row shows the item name (or ASIN), creation date, agent name, amount, and status. Proactive purchases also display a confidence tier badge (HIGH, MEDIUM, or LOW).

**Order Detail Page**

Clicking an order opens its detail page, which includes:

- Full order information (ASIN, quantity, amount, status)
- A link to the Amazon product page
- The agent that placed the order
- Amazon order reference and tracking number (when available)
- Shipping address used for the order
- An order timeline showing every status change with timestamps
- Proactive purchase details (confidence tier and decision reason) if applicable
- Refund history if any refunds have been issued
- A cancel button if the order is still within its cancellation window

### Payments

The Payments page manages your cards, card pools, and credit limits.

**Payment Methods**

Add, view, and remove credit or debit cards. Each card shows its brand, last four digits, expiration date, status, and which card pool it belongs to.

**Card Pools**

Card pools let you group cards together and assign specific pools to specific agents. For example, you might create a "Business" pool and a "Personal" pool, then assign each agent to use the appropriate one.

**Credit Limits**

Set the target credit limit for each agent. This is the amount your agent's balance will be restored to each week during the automatic top-up.

### Support

The Support page shows all your support tickets. Each ticket displays its type (Return, Lost Package, or Other), creation date, associated agent, linked order (if any), and current status.

Click **New Ticket** to create a support request.

---

## How Purchasing Works

### On-Demand Purchases

The most straightforward way your agent shops is when you ask it directly:

> "Buy me a USB-C cable under $15"

Here's what happens behind the scenes:

1. **Search** — Your agent searches the Amazon catalog for matching products, applying any category filters you've configured.
2. **Budget check** — The agent verifies you have enough available credit and that the purchase falls within your per-order and daily caps.
3. **Quote** — The agent creates a quote, which reserves the price for 10 minutes.
4. **Order** — The agent initiates the order. The system returns a payment challenge.
5. **Payment** — The agent signs the payment using the x402 protocol (an on-chain payment authorization).
6. **Confirmation** — The order is placed on Amazon and tracked in your dashboard.

You don't need to understand the technical details — your agent handles all of this automatically. The entire process typically takes a few seconds.

### Proactive Purchases

If you enable "Buy when my agent thinks I need it" in your agent's settings, your agent can detect purchase needs from your conversations and buy items on your behalf without you explicitly asking.

For example, if you mention "I'm completely out of paper towels," your agent may recognize this as a need and proactively place an order.

Proactive purchases have strict safety guardrails:

- **Confidence threshold** — The agent must be at least 80% confident you actually need the item. Casual mentions or vague preferences won't trigger a purchase.
- **Category restrictions** — Only essential, low-risk categories qualify (household supplies, cables, kitchenware, toiletries, etc.).
- **Price cap** — Proactive purchases are capped at $75 or your per-order cap, whichever is lower.
- **Blocked items** — System-wide blocked categories (weapons, alcohol, etc.) always apply.
- **Shipping required** — A shipping address must be on file.

Each proactive purchase is tagged with a confidence tier (HIGH, MEDIUM, or LOW) visible in your orders list, along with the reasoning behind the purchase decision.

### Order Statuses

| Status | Meaning |
|--------|---------|
| Pending Payment | Order created, awaiting payment signature |
| Pending Approval | Order requires your manual approval (if confirmation is enabled) |
| Paid | Payment processed, order being placed on Amazon |
| Placed | Order successfully placed on Amazon |
| Pending Review | Order flagged for internal review |
| Failed | Order could not be completed |
| Refunded | Order was refunded (fully or partially) |
| Canceled | Order was canceled by you or the system |

### Cancellations

Orders can be canceled within a short window after placement (typically 5 minutes). To cancel:

1. Go to the order detail page
2. If the cancellation window is still open, you'll see a **Cancel Order** button with the deadline shown
3. Confirm the cancellation

Canceled orders are automatically refunded to your agent's credit balance.

---

## Credits and Billing

### How Credits Work

Moltpho uses a credit-based system. Instead of charging your card for every purchase, your agent spends from a pre-funded credit balance.

Here's how the flow works:

1. You set a **target credit limit** (e.g., $100/week).
2. Moltpho charges your card and adds credits to your agent's balance.
3. When your agent makes a purchase, credits are deducted from the balance.
4. Each week, the balance is automatically topped back up to your target limit.

This approach means your agent can make purchases instantly without waiting for card authorization on every transaction.

### Weekly Top-Ups

Every week, Moltpho automatically restores your agent's credit balance to your configured target limit. For example:

- Target limit: $100
- Current balance: $35 (after a week of purchases)
- Top-up amount: $65 (charged to your card)

Top-ups only occur if your balance is below the target and you have an active payment method on file.

### Pricing

Moltpho charges a **10% fee per transaction**. This fee is included in the product prices your agent sees when searching — the displayed price is the Amazon price plus the 10% markup.

- No subscription fees
- No monthly minimums
- No hidden charges
- You only pay when your agent makes a purchase

### Refunds

Refunds are handled differently depending on the scenario:

| Scenario | Refund destination | Timing |
|----------|--------------------|--------|
| Order fails to process | Agent credit balance | Automatic, immediate |
| You cancel within the window | Agent credit balance | Automatic, immediate |
| You decrease your credit limit | Original card via Stripe | Automatic |
| Return or lost package | Via support ticket | After review |

Credit balance refunds are instant — the funds are immediately available for your agent to use on future purchases. Card refunds follow standard Stripe processing times (5-10 business days).

---

## Agent Settings

Access your agent's settings from **Agents > [Your Agent] > Settings** or via the "Agent settings" quick action on the dashboard.

### Purchasing Behavior

Three toggles control how your agent shops:

- **Buy when I ask** — Enables your agent to make purchases when you explicitly request them. This is the standard mode of operation.
- **Buy when my agent thinks I need it** — Enables proactive purchasing. Your agent will monitor conversations for purchase signals and buy essential items when it's confident you need them. See [Proactive Purchases](#proactive-purchases) for details.
- **Require confirmation** — When enabled, orders go into a "Pending Approval" state instead of being placed immediately. You must approve or reject each order from the portal before it proceeds.

### Spending Limits

Two optional caps help you control spending:

- **Per-order cap** — The maximum amount for any single purchase. Orders exceeding this amount will be blocked. Leave empty for no limit.
- **Daily cap** — The maximum total amount your agent can spend in a single day. Once reached, all further purchases are blocked until the next day. Leave empty for no limit.

Both values are in USD. These caps work alongside the credit limit — even if your credit balance allows it, orders that exceed these caps will be declined.

### Category Filters

You can control what your agent is allowed to buy using keyword-based category filters:

- **Allowed categories** — If set, your agent can only purchase items matching these keywords. Leave empty to allow all categories (subject to the system-wide blocked items list).
- **Blocked categories** — Items matching these keywords will never be purchased, even if they match an allowed category.

Both fields support wildcards:
- `*phone` matches "smartphone", "iPhone"
- `phone*` matches "phone case", "phone charger"
- `*cable*` matches "USB cable adapter"

### Shipping Address

Every agent needs a shipping address before it can place orders. Go to **Settings > Shipping Address** to enter:

- Full name
- Street address (line 1 and optional line 2)
- City
- State (2-letter code, e.g., CA, NY, TX)
- Postal code
- Email (for order confirmations)
- Phone number (required by carriers)

Shipping is currently limited to US addresses only.

Your agent can also collect and update the shipping address conversationally — just tell it your address and it will save it. However, you can always override it from the portal.

### Card Pools

Card pools let you organize your payment methods into groups and assign specific groups to specific agents. This is useful if you have multiple agents and want to control which cards each one uses.

To manage card pools:

1. Go to **Payments**
2. Create a new pool (e.g., "Business Expenses")
3. Move cards into the pool
4. In your agent's settings, select the pool under **Payment Method > Card Pool**

---

## Support Tickets

### Creating a Ticket

1. Go to **Support** in the sidebar
2. Click **New Ticket**
3. Select the issue type
4. If applicable, select the related order from the dropdown
5. Describe your issue (up to 2,000 characters)
6. Click **Submit Ticket**

### Ticket Types

| Type | When to use | Order required? |
|------|-------------|-----------------|
| Return | You want to return an item you received | Yes |
| Lost Package | Your package was not delivered | Yes |
| Other | General questions, billing issues, or anything else | No (optional) |

### Ticket Statuses

| Status | Meaning |
|--------|---------|
| Open | Your ticket has been submitted and is awaiting review |
| In Progress | A support agent is working on your ticket |
| Awaiting Response | We've responded and are waiting for your reply |
| Resolved | Your issue has been resolved |
| Closed | The ticket is closed (no further action needed) |

---

## Agent States

Your agent can be in one of four states:

| State | What it means | Can it shop? |
|-------|---------------|--------------|
| **Unclaimed** | The agent is registered but not yet linked to an owner account. Complete the claiming process to activate it. | No |
| **Active** | The agent is fully operational and linked to your account. | Yes |
| **Degraded** | There's a problem with your payment methods (e.g., card expired or declined). The agent can still spend any remaining credit balance, but top-ups will fail until the payment issue is resolved. | Yes, with remaining balance only |
| **Suspended** | The agent has been suspended by an administrator. Contact support for resolution. | No |

---

## Blocked Items

Regardless of your settings, Moltpho will never purchase the following:

- Weapons, firearms, and ammunition
- Controlled substances and prescription medications
- Tobacco and nicotine products
- Alcohol
- Adult content
- Hazardous materials

These restrictions are enforced system-wide and cannot be overridden.

---

## Frequently Asked Questions

### General

**Q: What is Moltpho?**
A: Moltpho is a platform that enables AI agents to make purchases on Amazon on your behalf. You connect your credit card, set spending limits, and your AI agent handles the shopping.

**Q: How much does it cost?**
A: There are no subscription or signup fees. Moltpho charges a 10% fee on each transaction, which is included in the prices your agent sees. This covers payment processing, infrastructure, and blockchain gas fees.

**Q: Is my credit card information safe?**
A: Yes. Card details are stored securely by Stripe, a PCI-compliant payment processor. Moltpho never sees or stores your full card number.

**Q: Which countries are supported?**
A: Moltpho currently supports US shipping addresses only. International shipping is planned for a future release.

**Q: Which stores can my agent shop at?**
A: Currently, Moltpho supports Amazon (US). Additional retailers may be added in the future.

### Setup

**Q: My claim link expired. What do I do?**
A: Ask your agent to run the setup process again. It will generate a new claim link. Claim links are valid for 24 hours.

**Q: I opened the claim link on a different device and it doesn't work.**
A: Claim links are bound to the device that first opens them for security reasons. Open the link on the same device where you first accessed it, or generate a new claim link.

**Q: Can I have multiple agents?**
A: Yes. Each OpenClaw agent instance registers as a separate Moltpho agent. You can claim and manage multiple agents from the same portal account, each with their own settings, credit limits, and spending caps.

**Q: I set up Moltpho but my agent says it can't purchase anything.**
A: Check these common issues:
1. Is your credit limit set above $0?
2. Is a shipping address on file?
3. Is at least one payment method added?
4. Is "Buy when I ask" enabled in agent settings?

### Purchases

**Q: How does proactive purchasing work?**
A: When enabled, your agent monitors your conversations for signals that you need something. If it detects a clear need (e.g., "I'm completely out of dish soap") with high confidence, it may purchase the item automatically. Proactive purchases are limited to essential categories and capped at $75 or your per-order cap, whichever is lower.

**Q: Can I stop my agent from buying something it already ordered?**
A: If the order was placed within the last few minutes, you can cancel it from the order detail page. Look for the **Cancel Order** button and the cancellation deadline.

**Q: Why was my agent's purchase declined?**
A: Common reasons include:
- Insufficient credit balance
- Purchase exceeds your per-order or daily cap
- Item falls in a blocked category
- No shipping address on file
- Agent is in a degraded or suspended state

**Q: The price my agent found is higher than what I see on Amazon.**
A: Prices shown by your agent include Moltpho's 10% transaction fee. The underlying Amazon price is 10% lower than what's displayed.

**Q: What happens if the price changes between when my agent searches and when it buys?**
A: Your agent creates a price quote that locks in the price for 10 minutes. If the quote expires before the purchase completes, the agent will automatically retry with a new quote (up to 3 times) as long as the price hasn't increased by more than 5%.

**Q: Can I require approval before my agent places orders?**
A: Yes. Enable "Require confirmation" in your agent's settings. Orders will enter a "Pending Approval" state, and you can approve or reject them from the portal.

### Billing

**Q: When is my card charged?**
A: Your card is charged during the weekly top-up to restore your agent's credit balance to your target limit. You are not charged per transaction — instead, transactions draw from your pre-funded balance.

**Q: Can I change my credit limit?**
A: Yes, at any time from your agent's settings or the Payments page. If you decrease the limit, the excess credit will be refunded to your card. If you increase it, additional credit will be added at the next top-up (or you can top up manually).

**Q: How do refunds work?**
A: It depends on the situation. Failed orders and cancellations are refunded instantly to your agent's credit balance. Returns and lost package claims are handled through support tickets and may result in either a balance credit or a card refund.

**Q: What happens if my card is declined during a top-up?**
A: Your agent enters a "Degraded" state. It can still spend any remaining credit balance, but won't receive new credits until you update your payment method. Add a new card or resolve the issue with your bank, and the next top-up will proceed normally.

### Support

**Q: How do I return an item?**
A: Create a support ticket with the type "Return" and select the order you want to return. Provide details about why you're returning the item. Our support team will guide you through the process.

**Q: My package never arrived. What do I do?**
A: Create a support ticket with the type "Lost Package" and select the affected order. Include any relevant details (expected delivery date, tracking information, etc.). We'll investigate and arrange a replacement or refund.

**Q: How long does support take to respond?**
A: We aim to respond to all tickets within 24 hours. You can track the status of your ticket from the Support page in your dashboard.

---

## Limits and Constraints

| Constraint | Value |
|------------|-------|
| Maximum single item price | $10,000 USD |
| Proactive purchase cap | $75 or per-order cap (whichever is lower) |
| Quote validity | 10 minutes |
| Cancellation window | ~5 minutes after placement |
| Claim link validity | 24 hours |
| Catalog search rate limit | 60 requests/minute |
| Quote creation rate limit | 20 requests/minute |
| Order placement rate limit | 5 requests/minute |
| Maximum concurrent quotes | 5 per agent |
| Shipping | US addresses only |
| Supported retailer | Amazon (US) |
| Support ticket description | 1–2,000 characters |

---

## Troubleshooting

**Agent says "credentials not found"**
Your agent's local credentials file may have been deleted. Run the setup process again:
> "setup moltpho"

**Agent says "insufficient credit"**
Your credit balance is too low for the purchase. Either wait for the next weekly top-up, top up manually from the Payments page, or increase your target credit limit.

**Agent says "shipping profile required"**
No shipping address is on file. Add one from **Agent Settings > Shipping Address** or tell your agent your address conversationally.

**Agent says "agent suspended"**
Your agent has been suspended by an administrator. Create a support ticket with type "Other" to inquire about the suspension, or contact support directly.

**Agent says "rate limited"**
Your agent has made too many requests in a short period. It will automatically retry after a brief wait. No action needed.

**Orders stuck in "Pending Payment"**
This usually resolves within a few seconds. If an order stays in this state for more than a minute, it may indicate a payment signing issue. Check that your agent's credentials are valid by running setup again.

**Dashboard shows "Degraded" status**
Your payment method has an issue (expired card, insufficient funds, etc.). Go to **Payments**, remove the problematic card, and add a new one. Your agent will return to "Active" status at the next successful top-up.

**Can't find an order I know was placed**
Make sure you're looking at the correct agent (use the agent tabs on the dashboard or the agent filter on the Orders page). If the order was very recent, try refreshing the page.
