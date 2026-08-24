# Disco: Post-Purchase Offer Widget
**Disco take-home, Option B**

A mobile-first, interactive prototype for a consumer-facing post-purchase offer embedded on an order-confirmation page.

**Live prototype:** https://post-purchase-offer-widget.netlify.app/

**Prototype/Walkthrough controls:** 
- The sidebar lets a reviewer switch purchase scenario and interaction state. 
- These are dev-only scaffolding, not part of the consumer experience. 
- The **Empty** variant is labeled *"Empty, reviewer only"* for the same reason: a real shopper should never see it (more on why below).

---

## What I built

A **single, contextual offer** that appears directly beneath the confirmed order.

Four purchase scenarios show that the offer changes with purchase context:

- Fleece Quarter-Zip → coffee cashback
- Trail Runner 2 → trail-sock cashback
- Camp Enamel Mug → coffee cashback
- Multiple items → order-level cashback

And the full set of interaction states the brief calls for: default, claiming/loading, claimed/success, claim error, no-match/empty, and dismissal via "Not interested?"

> **One offer. Clear relevance. One action.**

**Reviewer-only rationale** can be toggled on to surface the key product decisions behind each state. It's off by default and never alters the consumer experience.

---

## What the data changed

### 1. I removed the 3-offer carousel

The supplied A/B test was the clearest signal:

- **3-offer carousel:** 2.9% CTR · 12% claim rate · $8.40 revenue/1K
- **1-offer static:** 3.6% CTR · 17% claim rate · $11.80 revenue/1K

Roughly **+24% CTR, +42% claim rate, +40% revenue/1K**. Qualitative feedback pointed at the same issue: people missed the swipe interaction, ignored the module when the first offer felt irrelevant, and wanted the recommendation connected to what they'd just bought.

So I didn't redesign the carousel. I removed it.

### 2. I kept the offer immediately visible

Position 1 materially outperforms lower positions (4.8% CTR vs. 1.9% vs. 0.7%). The offer sits directly below the order summary, with no additional browsing step.

### 3. I designed mobile first

Mobile is 72% of traffic but underperforms desktop (2.6% vs. 3.8% CTR, 10% vs. 16% claim). That made mobile the primary design surface, not a reduced desktop layout.

### 4. I made the reason for the offer visible

The recommendation is framed around the completed purchase, not generic personalization language: *"Picked because you just bought a Fleece Quarter-Zip."* The intent is a useful continuation of the purchase, not an unrelated ad.

---

## Product decisions

**Order confirmation stays primary.** The shopper's first job is confirming the purchase succeeded. Order status, item details, price, and delivery information stay visually dominant before any commercial content appears.

**No-match is a system state, not a consumer message.** In production, if no sufficiently relevant offer exists, the module shouldn't render at all. An unsolicited recommendation has to earn its place; a visibly empty box doesn't. The reviewer-only state demonstrates the suppression behavior; in production, no offer UI would render.

**Loading is quiet.** If an offer is still resolving, the space reserves briefly with a neutral skeleton to avoid layout shift. If nothing eligible returns, that reserved space collapses.

**Claim errors only appear after user intent.** A backend failure before an offer is shown should fail silently. A visible error only earns its place after the shopper has tapped Claim, because that's the moment they have an expectation that needs recovery.

**Multi-item orders show one offer, not one per item.** The order summary supports any number of items, but the offer logic doesn't change: still exactly one offer, never one per SKU. What changes is the reasoning, from item-specific to order-level, since no single item is the obvious anchor once there's more than one.

**"Not interested?" dismisses, it does not replace.** Swapping in another offer would recreate the carousel in disguise and undercut the single-best-match thesis this whole build is arguing for.

**The visual system stays out of the way of the decision.** Black/white hierarchy, one semantic color per meaning (green for value and success, red for error), one icon language, minimal motion, quiet surfaces. Every color pairing was checked against WCAG contrast minimums rather than eyeballed, and it caught real failures before they shipped.

---

## What I deliberately did not build

Given the 6-8 hour constraint, I focused on the core consumer decision moment.

I did not build: a recommendation or ranking engine, a multi-offer browser, preference controls, account creation or authentication, production analytics infrastructure, a full merchant theming system, or a separate desktop experience.

The four purchase scenarios are hardcoded to demonstrate how content, merchant, value, and reasoning adapt to purchase context.

---

## What I would measure after launch

**Primary outcome: revenue per 1,000 impressions.** CTR alone can reward curiosity without improving commercial value; revenue/1K is the number that actually reflects whether the offer worked.

**Diagnostic metrics** (explain *why* the primary number moves, not success on their own): offer CTR, click-to-claim conversion, claim completion rate, claim error/retry rate, "Not interested" rate, no-match/suppressed-offer rate.

**Guardrails:** order-confirmation task completion, interaction with core order information, publisher-page abandonment, repeat dismissal behavior.

**Next experiment.** I'd test this as a third variant, **C: Contextual single best-match**, against the existing single-best-match treatment, isolating the explicit connection between the purchase and the recommendation. Hypothesis: making that relationship clear improves perceived relevance and increases both CTR and claim conversion without the page feeling more promotional.

---

## AI-native workflow

- **GPT:** image generation exploration for the prototype
- **Claude:** context management and QA across the full assignment, used throughout product reasoning, implementation, and iteration. Contrast was computed against WCAG minimums rather than eyeballed, and every state, in every scenario, was screenshot-verified rather than assumed correct. I overrode AI suggestions when they conflicted with the evidence, especially around keeping multiple offers, exposing empty-state UI, and over-explaining personalization.
- **Claude Code:** implementation of the lightweight prototype
- **Wispr Flow:** voice dictation for fast prompting throughout

---

## Implementation

HTML, CSS, and vanilla JavaScript. Product and brand images are embedded directly as base64 data URIs rather than hotlinked, so the file has zero external dependencies and renders identically regardless of where it's hosted. No framework: the brief asks for a deployed, clickable prototype, not production architecture, and a lightweight build put more of the time budget into product judgment and interaction quality.
