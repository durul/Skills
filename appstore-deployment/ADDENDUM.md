# Addendum: Overlooked Rejection Vectors, Platform-Specific Rules, and Breaking Enforcement

Material discovered during second-pass audit. These are real rejection causes and submission blockers that the primary skill files did not cover.

---

## BREAKING: October 16, 2026 — App Review Guidelines Revision (bait-and-switch, NCII, creative assets)

**Sources:** <https://developer.apple.com/news/?id=92th7e5i> (change list) · <https://developer.apple.com/news/?id=vvzx6cul> ("Strengthening App Review Guidelines against nonconsensual intimate imagery and bait-and-switch apps")  
**Verified:** diffed against the June 8, 2026 text (Wayback Machine snapshot of October 15, 2026). Apple publishes translations within a month.

### What changed

| Guideline | Change | Skill gate |
| --- | --- | --- |
| Before You Submit | New item: *"Provide required documentation for apps and features that require regulatory approval."* Links added to new App Review Help pages (testing on compatible devices, configuring IAPs for review, providing information for a complete review); App Review Help added to the documentation list | B |
| 1.2 | Scope rewritten to apps that let users *"create, generate, upload, share, or otherwise contribute content"* — explicitly **social media, UGC platforms, and AI content creation apps**; **nonconsensual intimate imagery** named; failing to implement safeguards or to remediate after notice may violate the **Developer Code of Conduct** | G, L |
| 2.3.1(a) | Opening sentence on hidden/dormant/undocumented features removed (moved to 2.5.19); Notes-for-Review specificity and misleading-marketing rules unchanged | E |
| 2.3.3 | Links to new screenshot guidance (more than half of each device's screenshots must show the app in use) | A, E |
| **2.3.14 (new)** | Creative assets (product page header, search results) | E |
| **2.5.19 (new)** | Hidden, dormant, or undocumented features; hiding features in the binary *"such as through code obfuscation or other methods"* → app removal + developer account termination | F, L |
| 3.1.1(a) | Links go to a **"permitted destination"** (approved destinations vary by region); Music Streaming Services Entitlement bullet removed and folded into the StoreKit External Purchase Link bullet ("where permitted"); entitlements limited to specific storefronts **and certain platforms** in each | D |
| 4.1 | Text unchanged; now links to Apple's copycat & impersonation guidance | E, L |
| 5 (intro) | Apps found to facilitate human trafficking and/or child exploitation: authorities notified **and** the app is removed from the App Store **and blocked from alternative distribution** | K, L |
| 5.1.1(ii) | Links to new purpose-string guidance | C |
| After You Submit | Links to review-status, expedite, contact, appeal, guideline-suggestion, and Bug Fix Submission help pages | I |
| Throughout | Editorial: "e.g." → "such as" / "for example"; no change in meaning | — |

### New text (verbatim)

**2.5.19:**
> *"Don't include any hidden, dormant, or undocumented features in your app; your app's functionality should be clear to end users and App Review. Attempting to hide undocumented features in your app binary, such as through code obfuscation or other methods, is grounds for removal of your app from the App Store and termination of your developer account."*

**1.2 (opening):**
> *"Apps that enable users to create, generate, upload, share, or otherwise contribute content present particular challenges, including intellectual property infringement, nonconsensual intimate imagery, and anonymous bullying. This includes social media, user-generated content platforms, and AI content creation apps. To prevent abuse, such apps must include: [filtering, reporting with timely responses, blocking, published contact information]"*

**1.2 (sentence added to the June 2026 enforcement paragraph):**
> *"Developers who fail to implement appropriate safeguards or fail to remediate identified violations after notice may be considered in violation of the Developer Code of Conduct."*

**2.3.14:**
> *"Creative assets allow you to highlight the value of your app in new ways compared to app-in-use screenshots and preview videos, and can be used across the App Store, including the product page header and in search results. Creative assets must pertain to your app's content and functionality or overarching brand, follow standard metadata guidelines, and be appropriate for all audiences. They should not include inappropriate content, references to other apps with an intent to mislead, unverifiable product claims, or specific or discounted pricing information."*

**3.1.1(a) (StoreKit External Purchase Link Entitlements):**
> *"apps on the App Store in specific regions may offer in-app purchases and also use a StoreKit External Purchase Link Entitlement or, where permitted, a Music Streaming Services Entitlement to include a link to a permitted destination that informs users of other ways to purchase digital goods or services. Approved destinations may vary by region. [...] The entitlements are limited to use only in specific storefronts of the App Store, and only on certain platforms in each of those storefronts."*

**5 (Legal intro, last sentence):**
> *"In extreme cases, such as apps that are found to facilitate human trafficking and/or the exploitation of children, appropriate authorities will be notified and the apps will be removed from the App Store and blocked from alternative distribution."*

### Why Apple did it

From the announcement: apps that *"misrepresent their purpose during review and only reveal harmful capabilities — such as 'nudification' — after approval"* (bait-and-switch) were already banned as hidden features; §2.5.19 makes that an explicit rule with account termination. Apple says it *"prevented over 150K apps with safety issues from reaching customers and terminated over 193K developer accounts for engaging in fraud and abuse"* last year, tracing the violations to malicious actors — *"in some cases coordinated fraud operations — working to conceal harmful content that only becomes active once an app is approved"* — and that it keeps improving detection. Expect more post-approval checks of server-driven behavior. Apple's [removal page](https://developer.apple.com/help/app-review/safe-and-trusted-store/app-removals) already lists *"modified after approval, such as through a server-side change"* as a removal trigger.

### "Things that can get you banned" — what is actually new

Summaries of this update circulating among developers list three ban triggers. Only the first changed in October 2026; the other two are long-standing rules whose text is unchanged:

| Summary | Rule | New in October 2026? |
| --- | --- | --- |
| Tricking the review process or hiding dormant code | Introduction ("cheat the system ... trick the review process" → apps removed, developer expelled); **§2.5.19**; ADPLA §3.2(3) post-review modifications | **Yes** — §2.5.19 (Oct 16) + ADPLA §3.2(3) (Oct 13) |
| Buying fake reviews or manipulating chart rankings | §3 Business intro (*"paid, incentivized, filtered, or fake feedback"*, including via third-party services); §5.6.3 Discovery Fraud; ADPLA §3.2 fraudulent reviews | No — unchanged |
| Copying other developers' UI, icons, or brand names | §4.1(a) copycats, (b) impersonation → Code of Conduct, (c) no other developer's icon/brand/product name in your icon or name; §5.2.1; ADPLA §3.2 confusingly similar names / name squatting | No — unchanged; new help page linked from 4.1 |

Full trigger map and pre-submission integrity audit: [SKILL.md — Gate L](SKILL.md#gate-l--review-integrity--account-termination-triggers-intro-2519-3-41-56).

### Practical impact

- **Feature flags / remote config:** list every flag-controlled capability in Notes for Review with its state in the review build. A staged rollout of a reviewed feature is fine; enabling something App Review never saw is a §2.5.19 violation and, under ADPLA §3.2(3), a contract breach.
- **Obfuscation:** allowed for IP protection, but it can't hide functionality. Say in Notes for Review that you use it and why.
- **UGC & AI generation apps:** NCII prevention (real-person likeness filters, prompt/output moderation), fast takedowns, and a compliance-plan template are now baseline; gaps can escalate to account termination.
- **Creative assets:** new rejection surface — brand art is fine, prices/discounts, competitor references, and unverifiable claims are not.
- **External purchase links:** check that each entitlement covers the **platform** you ship (not only the storefront) and that the link target is an approved destination for that region.

---

## BREAKING: October 13, 2026 — ADPLA Revision (integrity terms, new APIs, HomeView, multiseat)

**Source:** <https://developer.apple.com/news/?id=17mwd36z> · §3.2 verified by diffing the agreement against the October 12, 2026 snapshot. The Account Holder must accept the updated terms.

- **§3.2 — new prohibited-code items.** You may not create any product, code, or program that would: *"(3) deceive regarding the identity, purpose, or behavior of the product, code, or program, including through post-review modifications that would cause harm to others, including end users or Apple; (4) impersonate Apple or any third party to solicit credentials or other sensitive information; (5) implement security or privacy protections in a manner that is intentionally weakened, or inconsistent with what is described; (6) whether through Your own code or someone else's, access, collect, or transmit user data beyond what was disclosed when consent was requested; or (7) use the end user's device or network resources to provide services to third parties without the user's explicit prior consent."* Items (1)–(2) (defeating Apple security; harming system security) were already there.
  - (3) is the contract twin of guideline §2.5.19 — bait-and-switch is now a guideline **and** a license violation.
  - (6) makes you liable for SDK over-collection ("someone else's" code).
  - (7) targets proxy / bandwidth-sharing / background-compute SDKs that resell users' connections or CPU.
- **§3.3.8(J) Live Assistance API** — connects an app to the user's FaceTime call; allowed only at the user's invitation and solely so an accessibility service provider can join to provide services such as captioning or translation.
- **§3.3.8(K) App Replacement Entitlement** — first launch of your replacement app shows a system sheet offering to move data and settings from your original app and delete it. Only allowed when both apps are under your membership and have *"substantively the same primary purpose and functionality."*
- **§6.4, 6.4.1, 6.4.2 — CarPlay and Apple HomeView:** your iOS app widgets may be shown on CarPlay **and Apple HomeView**; you decide whether a widget is suitable for CarPlay and use `disfavoredLocations` if not (Apple can also disfavor it). Apps with a CarPlay entitlement in an Apple-identified category may be offered on Apple HomeView while running on a paired iOS device.
- **Schedule 2 — Group Purchases:** a "Group Organizer" buys an auto-renewable subscription for a group; each "Group Member" is an End-User. Refunds may be full or for unused seats; no partial refund for a seat an active Group Member is using.
- **Schedule 3 — Volume Purchasing:** subscriptions bought by organizations through Apple Business / Apple School Manager; opting out of multiseat or changing availability can stop renewals and remove the subscription from those portals.
- **Schedules 2 & 3 §3.3:** Apple reports legally required tax, sales, and contact information to tax authorities; customer tax information Apple shares with you may be used only for purposes mandated by law.

---

## EU Unified Business Terms — ADPLA Attachment 14 (effective October 1, 2026)

**Sources:** <https://developer.apple.com/news/?id=gmws0jgp> · <https://developer.apple.com/news/?id=0cgo95n6> · <https://developer.apple.com/support/apps-in-the-eu/>

Attachment 14 (added August 18, 2026) moves every developer distributing in the EU to one set of terms and, from October 1, 2026, supersedes the **Alternative Terms Addendum for Apps in the EU** and the **StoreKit External Purchase Link Entitlement (EU) Addendum**. Fees already accrued under the old agreements are still owed.

**Payment options in EU storefronts (iOS, iPadOS, macOS, tvOS, visionOS, watchOS apps):**

- Apple IAP keeps working with no changes.
- You may **also** offer in-app **alternative payment processing** and/or **out-of-app offers** to a destination of your choice — website, alternative app marketplace, or another app — with or without an actionable link. The old rule that an EU app could not offer IAP and alternative payments at the same time is gone.
- Whatever combination you choose must be kept for **12 months**.
- Reader apps may promote out-of-app offers **without** an actionable link, with or without the External Link Account Entitlement (placement rules apply).

**Commissions (App Store, EU):**

| What | Standard | Small Business / Mini Apps Partner / Video Partner programs, subscriptions after year one |
| --- | --- | --- |
| Sales through Apple IAP | 26% | 15% |
| In-app alternative payment processing | 20% | 10% |
| Store services commission — out-of-app offer with actionable link (sales within 7 days of the tap) | 15% | 10% |
| Core Technology Commission — alternative marketplaces, their apps, Web Distribution | 5% | — |

The Core Technology Fee (per-install), the Initial Acquisition Fee, and the old Store Services Fee tiers are **eliminated**. Alternative-payment transactions must be reported **monthly**, within 15 days after month end; you collect and remit taxes on them.

**Child safety for alternative payments on the App Store:**

- **Kids category:** alternative payment flows behind a parental gate; no out-of-app offers to buy on a website.
- **Under 13** (or the storefront's higher age of parental consent, e.g. 16): alternative payments behind a parental gate; **no** out-of-app offers.
- **13–17** (or 16–17): both alternative payments and out-of-app offers behind a parental gate.

**Alternative distribution:** eligibility to run an alternative marketplace or use Web Distribution now needs only **one** of: a moderate Dun & Bradstreet financial-stability score, being publicly traded (or owned by a public company), established venture funding, a licensed financial audit, approved government/education/nonprofit fee-waiver status, a USD 1,000,000 stand-by letter of credit, or one million first annual installs worldwide — and **no EU legal entity**. Small marketplace operators (under €10M global revenue in the last 12 months and under €1M lifetime EU marketplace revenue) can have the CTC waived on marketplace download/subscription fees. With iOS/iPadOS 27.2: a new install experience, an API for starting web-distributed app downloads from your own app, and installs allowed for 90 days while EU users travel abroad.

This replaces the January 2026 "single business model" summary under [Commission Rate Changes](#commission-rate-changes-2026).

---

## Fall 2026 Platform & App Store Connect Changes (August–October 2026)

### iPhone Duo & screenshot specifications (October 5, 2026)

**Sources:** <https://developer.apple.com/news/?id=kkphp5qo> · <https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications>

- iPhone Duo ships **October 23, 2026** on iOS 27.1. Recompile with **Xcode 27.1**; check every pose and orientation with Device Hub.
- Screenshots: **1398x2034 / 2034x1398** (outer display) and **2007x2853 / 2853x2007** (inner display). Providing them adds an "optimized for iPhone Duo" badge; from **April 2027**, apps built with the **iOS 27.1 SDK or later** must include them.
- Updated app preview specs and a product page **preview tool** in App Store Connect show how assets look on each device and orientation.

### Creative assets, Asset Library & product page preview (August 5 / October 5, 2026)

**Sources:** <https://developer.apple.com/news/?id=ljpl7kyn> · <https://developer.apple.com/app-store/asset-best-practices/> · <https://developer.apple.com/help/app-store-connect/reference/app-information/creative-assets-specifications>

- New **creative assets** for the product page header and search results (iOS/iPadOS 27+), reviewed under new guideline **§2.3.14**. Can also be used on custom product pages and tested with product page optimization.
- Specs (no alpha/transparency): header **21:9 3840x1646** (JPEG/PNG) or **16:9 5244x2950** (PNG); search results **3:2** from 1920x1280 to 3840x2560 (JPEG/PNG) or 16:9 5244x2950 (PNG). Videos 30 or 60 fps, **5–30 s**, muted by default, looping. In-App Event media: 16:9 card + 9:16 details page (video 15–30 s).
- Best-practice rules App Review applies: no specific prices, discounts, website URLs, or copyright symbols; no unverifiable claims (awards you didn't win); no logos of other platforms or marketplaces; no Apple recognitions (Editor's Choice, App of the Day, Game of the Day, Apple Design Award); 4+ appropriate, no gore or weapons pointed at a person or the viewer.
- The **Asset Library** stores approved assets (submit seasonal art ahead of time, use it when ready); assets can also be uploaded with the App Store Connect API.

### SDK & deployment-target minimums (September 9, 2026)

**Sources:** <https://developer.apple.com/news/?id=k1mtkt1k> · <https://developer.apple.com/app-store/submitting/> · <https://developer.apple.com/news/upcoming-requirements/>

- **Since September 9, 2026:** iOS and iPadOS uploads must **target iOS 13 or later**.
- **From April 2027:** iOS/iPadOS apps built with the **iOS & iPadOS 27 SDK** and targeting **iOS 15 or later**; tvOS, visionOS, watchOS apps with their **27 SDKs**.
- macOS 27 is **Apple silicon only** (macOS 26 was the last release for Intel Macs). To drop Intel support, build arm64 only and resubmit.

### Subscriptions: Bundles & Suites, multiseat (September 16, 2026)

**Sources:** <https://developer.apple.com/news/?id=likeohx4> · <https://developer.apple.com/app-store/subscriptions/bundles-and-suites/>

- **Multiseat purchases are enabled by default** for subscriptions: **Volume Purchasing** for Apple Business / Apple School Manager launches **October 22, 2026**; **Group Purchases** (a subscriber buys seats and invites others; Apple handles invitations) launch this winter. Manage or disable per subscription in App Store Connect. Contract terms: ADPLA Schedules 2 & 3 (October 13, 2026).
- **Bundles** (request-based): up to **5** subscriptions in one IAP, from one developer or up to **5** developers (each signs the Multi-Developer Bundle Program Legal Addendum). **Suites**: one subscription across up to **15** of your apps. Requires **StoreKit 2** and iOS/iPadOS/macOS/tvOS 27+; subscriptions in a Bundle must share the same duration. Buying a Bundle that contains an existing standalone subscription upgrades it immediately with a prorated refund.

### ATT alternative prompt in the EU (September 16, 2026)

**Sources:** <https://developer.apple.com/news/?id=idsft9ai> · <https://developer.apple.com/app-store/user-privacy-and-data-use/>

- From **iOS/iPadOS 27.2**, apps in the EU may use an **alternative ATT system prompt** with changed wording and an optional **"Additional Information"** text button that opens your own layered-consent screen (GDPR/ePrivacy). In **Germany, France, Italy, Poland, and Romania** only the alternative prompt is available.
- EU users can be **re-prompted one year** after their last ATT choice (accept or reject), unless they turned off the system setting (renamed in the EU to "Allow Apps to Request to Link Your Activity Across Companies").
- Rules on **when** tracking needs permission are unchanged: no IDFA access or tracking until the user allows it in the ATT prompt, and no gating or incentives (§5.1.2(i)). New docs: *Requesting Authorization with Expanded Interface*, *Tracking Markdown Usage Description*.

### Sign in with Apple relay domain — correction (August 24, 2026)

**Source:** <https://developer.apple.com/news/?id=1ptvdtcm>

Apple revised its June plan: **new Sign in with Apple** relay addresses will be issued on **`private.icloud.com`** (later in 2026), existing **`privaterelay.appleid.com`** addresses keep working, and **iCloud+ Hide My Email stays on `icloud.com`** — there is no shared-domain unification. Allowlists and email validation must accept **both** relay domains.

### macOS: Rosetta wind-down, Full Disk Access, Developer ID Sub-CA

**Sources:** <https://developer.apple.com/news/?id=w5ngl9k2> · <https://developer.apple.com/news/?id=p6zjojqw> · <https://developer.apple.com/news/?id=w4atic4c>

- **Rosetta:** macOS 26.4+ may warn users when launching Rosetta-dependent apps; Apple describes macOS 27 as the final Rosetta release, after which Intel-only apps stop running on Apple silicon (older, unmaintained games excepted). Ship a universal binary now.
- **Full Disk Access:** Apple will add controls so FDA can only be granted with very explicit user action, citing apps (including AI agents and communication apps) that expose mail, messages, and browsing history. Design around scoped access.
- **Developer ID Sub-CA expires February 1, 2027:** certificates from the original Developer ID CA stop working; `.pkg` files signed with them will no longer install. Create a replacement from the **G2** Sub-CA (pick "G2 Sub-CA" when prompted; update Xcode 11.4 or earlier first), re-sign packages, and sign future app updates with the new certificate and a secure timestamp. Already signed and notarized apps keep working.

### Republic of Korea age ratings (August 12, 2026)

**Source:** <https://developer.apple.com/news/?id=oj3r9pvw>

- Apps with an official **GRAC** rating can override their Korea rating (All, 12+, 15+, 19+) by giving the GRAC Rating Classification Number with the next version submission.
- From **October 2026**, two descriptors move from **All → 12+** for Games and Entertainment apps in Korea: infrequent profanity and crude humor; infrequent mature or suggestive themes.

---

## BREAKING: Age Rating Questionnaire Adds Social Media Questions (July 9, 2026)

**Source:** <https://developer.apple.com/news/?id=tlur8uvi>  
**Deadline:** Responses **mandatory beginning September 2026**

The App Store Connect age rating questionnaire now includes questions about your app's **social media capabilities** — defined as *"the ability to redistribute, amplify, or interact with user-generated content through a social feed or similar discovery method."*

- Apps with social media capabilities receive a new **content descriptor** on the product page.
- The answers place apps in the **Social Media** category of the new **Time Allowances** parental controls (iOS 27, iPadOS 27, macOS 27+) — **regardless** of the App Store category selected in App Store Connect ([Time Allowances announcement](https://developer.apple.com/news/?id=0d2gpmml)).
- **Beginning September 2026**, responses are required when submitting **new apps or updates** to the App Store, **and** when submitting apps for **notarization** for alternative distribution.
- The questions can be answered now — do it before September to avoid submission interruptions.

**Who is affected:** anything with a feed, follower graph, sharing/reposting, or UGC discovery surface — including AI apps whose generated content is shared into a feed. Pair with the age-rating overhaul and §1.2 UGC gates below.

---

## BREAKING: Brazil — Alternative Distribution & Payments (June 18, 2026)

**Source:** <https://developer.apple.com/news/?id=umq9wxmm> · <https://developer.apple.com/news/>  
**Legal basis:** Agreement with Brazil's competition regulator **CADE** (Conselho Administrativo de Defesa Econômica)

As of **iOS 26.5**, developers on the **Brazil storefront** can:

- Distribute apps on **alternative app marketplaces** (and operate marketplaces)
- Process payments for digital goods/services **outside Apple In-App Purchase** (**Alternative Payments**)
- Use **Out-of-App Offers** to promote purchases outside the app
- Fees follow the **Core Technology Commission (CTC)** model

**ADPLA impact:** Attachment 12 was revised and retitled — it is now **"Additional Terms for iOS Apps in Japan, Brazil"**. Apple set a **July 6, 2026** deadline for accepting the updated agreement. Expect Japan-style mechanics (entitlements, StoreKit eligibility/disclosure flows, default-browser links, prominence parity) but **verify Brazil-specific Apple Materials** — entitlement names, minimum OS, commission rates, and reporting differ per territory.

**Practical impact:** the external-purchase storefront tables in [SKILL.md](SKILL.md) Gate D and [EDGE-CASES.md](EDGE-CASES.md) include Brazil; audit Brazilian distribution the same way as the [Japan checklist](#japan-app-store--adpla-attachment-12-commerce--distribution) below.

---

## BREAKING: June 8, 2026 — Guidelines + ADPLA Revision (WWDC week)

**Source:** <https://developer.apple.com/news/?id=a233fmpw>  
**Coverage:** <https://www.macrumors.com/2026/06/09/app-store-guidelines-low-quality-apps/>  
**Date:** June 8, 2026

### App Review Guidelines changes

**Introduction** — revised kid and teen safety guidance.

**§1.2 (UGC) — new enforcement paragraph:**

> *"It is your responsibility to remove content that violates this guideline, your terms of service, or your community standards. If we find such content, we will ask you to remove it, and provide a plan to improve your compliance with this guideline. Based on your response, your app may be removed from the App Store until you can demonstrate improvements that bring your app into compliance. Egregious or repeated behavior is grounds for immediate removal of your app from the App Store, and from the Apple Developer Program."*

- Apps without a **compliance plan** for content violations can be removed from the App Store.
- Context: Apple threatened to pull **Grok** under this rule (April 2026).

**§4.3(a)** — clarifies the basis for the guideline and adds an example (near-identical apps under multiple Bundle IDs).

**§4.3(b) — full new text:**

> *"Don't submit apps that are indistinguishable from what's already widely available. Opportunistically creating variants of existing app categories or popular apps degrades App Store discovery, reduces overall app quality, and harms both users and developers. Certain kinds of apps, such as dating, flashlight, sound effects, wallpaper, simple timers, and fortune telling, are well established on the App Store and we will not accept new submissions unless they offer a meaningfully different or improved experience. We may remove these apps from the App Store going forward if they are not updated, improved, or do not attract customers. Other kinds of apps, such as drinking games, Kama Sutra, fart, and burp apps, are mediocre, low-quality, or low-effort and do not add value to the App Store. Repeated submissions of this kind may lead to removal from the Apple Developer Program."*

Old text (for comparison): *"Also avoid piling on to a category that is already saturated; the App Store has enough fart, burp, flashlight, fortune telling, dating, drinking games, and Kama Sutra apps, etc. already. We will reject these apps unless they provide a unique, high-quality experience. Spamming the store may lead to your removal from the Apple Developer Program."*

What changed in practice:

- **Saturated tier** (dating, flashlight, sound effects, wallpaper, simple timers, fortune telling): new submissions need a *meaningfully different or improved* experience; **existing** apps may be **removed** if not updated/improved or attracting no customers — a new clutter-cleanup power.
- **Low-effort tier** (drinking games, Kama Sutra, fart, burp): labeled *"mediocre, low-quality, or low-effort"*; **repeated submissions → Apple Developer Program removal**.
- Lists are explicitly **examples** ("such as") — expect application to adjacent categories.

**§4.5.3** — clarifies that **Live Activities** may not be used to spam, phish, or send unsolicited messages to customers.

### ADPLA changes (same revision — accept updated terms in your developer account)

- **§3.1, §14.8**: requirements for providing information / responding to questions about **developer identity**, incl. export-compliance context.
- **§3.3.3(N)**: Sensitive Content Analysis framework requirements clarified.
- **§3.3.3(Q)**: Suggested Actions API requirements specified.
- **§3.3.3(R)**: Trust Insights framework requirements specified.
- **§3.3.4(A)**: terms on end users modifying content for personal **accessibility** purposes.
- **§3.3.7(L)**: Media Device Extension framework requirements.
- **§3.3.7(M)**: Spatial Audio Extension APIs requirements.
- **§3.3.9(E)**: Customer Engagement APIs requirements.
- **§3.2(h)**: updated terms for use of and access to **Apple models**.
- **§3.3.11 (new)**: AI and machine learning technologies grouped under this subsection; **§3.3.11(A)**: updated **Foundation Models** framework requirements (formerly §3.3.8(I)).
- **§6.7**: analytics may additionally be provided via **Xcode** and/or **App Store Connect API**.
- **§7.9**: requirements on providing app information in App Store Connect and protection of **minor** end users.
- **§10**: indemnification terms clarified.
- **Attachment 2 §1.1**: In-App Purchase API requirements clarified.
- **Attachment 5 §3.3**: updated **privacy** requirements for use of **Passes**.
- **Attachment 11 §4**: EnergyKit identity-guidelines name updated.

---

## BREAKING: Vibe Coding App Crackdown (March 2026)

**Source:** <https://www.macrumors.com/2026/03/30/apple-pulls-vibe-coding-app/>  
**Date:** March 30, 2026  
**Guideline:** §2.5.2

Apple has escalated enforcement against "vibe coding" apps — tools that let users generate functional apps from natural language prompts using AI.

**What happened:**

- "Anything" (raised $11M, $100M valuation) was **removed entirely** from the App Store on March 26, 2026
- Updates to **Replit** and **Vibecode** were blocked starting earlier in March 2026
- Apple cited **Guideline 2.5.2** specifically: apps may not "download, install, or execute code which introduces or changes features or functionality"
- Even attempts to comply (previewing generated apps in a web browser instead of natively) were rejected

**Apple's official statement:** There are "no specific rules against vibe coding," but apps must adhere to longstanding §2.5.2 guidelines. The generated code constitutes new executable functionality introduced after review.

**What this means for developers:**

- AI-generated code that executes within your app = §2.5.2 violation
- AI-generated code the user views/edits but doesn't execute in the host app = may be OK (educational exception)
- Generating apps and publishing them as separate App Store submissions = each submission reviewed independently
- The enforcement is **active and escalating** — not theoretical

**Safe patterns:**

- AI generates code → user copies it to Xcode → user submits their own app: OK
- AI generates code → displayed in syntax-highlighted view → user can edit: OK (educational exception)
- AI generates code → app executes the generated code natively: REJECTED

**Rejected patterns:**

- App generates mini-apps/tools and runs them in a native sandbox
- App generates UI and renders it using dynamically loaded components
- App creates functional software that operates within the host app

This is the most significant recent enforcement action related to AI code generation and directly affects any developer building AI-powered app builders, no-code tools, or LLM-based coding assistants for iOS.

---

## ADPLA Updated (March 30, 2026)

**Source:** <https://developer.apple.com/news/?id=03302026a>

The Apple Developer Program License Agreement was revised on **March 30, 2026** with new framework-specific requirements:

- **Foveated Streaming framework** — New specified requirements and data privacy clarifications
- **Family Controls framework** — New specified requirements
- **Accessory Notifications framework** — New specified requirements
- **Accessory Live Activities framework** — New specified requirements

**What this means for developers:**

- If your app uses any of these frameworks, review the updated ADPLA terms before your next submission.
- All current Apple Developer Program members should accept the updated agreement in their developer account.
- Apps using Family Controls (Screen Time API) or accessory-related frameworks should verify compliance with the new terms.

---

## Age Rating System Overhaul (July 2025 — Deadline January 31, 2026)

**Source:** <https://developer.apple.com/news/upcoming-requirements/?id=07242025a>  
**Source:** <https://developer.apple.com/help/app-store-connect/reference/age-ratings>

Apple completely overhauled the age rating system in July 2025. **All apps must be compliant by January 31, 2026.**

### New age categories

| Old System | New System (iOS 26+) |
| ----------- | --------------------- |
| 4+ | 4+ |
| 9+ | 9+ |
| 12+ | **13+** (new) |
| 17+ | **16+** (new) |
| — | **18+** (new) |

### What changed in the questionnaire

The revised questionnaire now asks about:

- **Parental controls** — Does the app have them?
- **Messaging features** — Can users message each other?
- **Health topics** — Does the app address health in ways requiring age gating?
- **Violence depictions** — More granular than before
- **Developer override** — Developers can now **manually set a stricter age rating** if their terms of service require it (e.g., COPPA compliance requiring 13+)

### Territory-specific ratings

Different regions now have distinct rating thresholds:

- Australia, Brazil, South Korea, and others may show different age ratings for the same content
- Apps must answer the questionnaire accurately; Apple maps answers to regional standards

### The age-rating catch-22

**Known issue:** A COPPA-compliant app that requires users to be 13+ may answer "No" to all adult content questions, get assigned a 4+ rating, and then be rejected for inconsistent metadata (requiring 13+ in-app but showing 4+ on the store).

**Fix:** Use the new developer override to manually set the age rating to 13+ regardless of questionnaire answers.

### §2.3.8 metadata trap

> *"Use of terms like 'For Kids' and 'For Children' in app metadata is reserved for the Kids Category."*

Using "For Kids" or "For Children" in your app name, subtitle, description, or keywords while NOT being in the Kids Category = metadata rejection. This also applies to screenshots containing child-oriented messaging.

---

## Xcode 26 / iOS 26 SDK Minimum Requirement (Deadline: April 28, 2026)

**Source:** <https://developer.apple.com/news/upcoming-requirements/>

Starting **April 28, 2026**, all apps uploaded to App Store Connect must be built with **Xcode 26 or later** using an SDK for:

- iOS 26
- iPadOS 26
- tvOS 26
- visionOS 26
- watchOS 26

Builds compiled with older Xcode versions or SDKs will be **rejected at upload time**. This is a hard submission blocker — plan migration before the deadline.

**Previous requirement (still in effect until April 28):** Xcode 16 / iOS 18 SDK (effective since April 24, 2025).

**Status (June 2026):** This deadline has now passed and is in force — builds on older SDKs are rejected at upload.

**Next (announced September 9, 2026):** iOS/iPadOS uploads must target **iOS 13+** now, and from **April 2027** must use the **iOS & iPadOS 27 SDK** with an **iOS 15+** deployment target (tvOS/visionOS/watchOS: 27 SDKs). See [SDK & deployment-target minimums](#sdk--deployment-target-minimums-september-9-2026).

---

## BREAKING: Texas Age Assurance — SB 2420 (Effective June 4, 2026)

**Source:** <https://developer.apple.com/news/?id=sg176nne>
**Source:** <https://www.macrumors.com/2026/06/03/apple-app-store-texas-sb-2420/>
**Date:** Live since June 4, 2026 (a federal injunction was lifted, putting the law into effect)

Texas law **SB 2420** introduces age-assurance and parental-consent obligations for app marketplaces and developers. It now applies to **new Apple Accounts created in Texas**. This is a live legal requirement, not a future deadline — treat it as a hard compliance item for any app available in the US.

**What Apple changed for users:**

- People creating an Apple Account in Texas must confirm whether they are 18 or older.
- Accounts for users under 18 must belong to a **Family Sharing** group.
- Parents/guardians must **consent** to App Store downloads, in-app purchases, and in-app transactions for minors.

**What developers must implement (App Store Connect / APIs):**

- **Declared Age Range API** — retrieve the broad age band of a Texas Apple Account (an age *category*, not a birth date or ID document). This is no longer beta-only for these obligations.
- **Significant Change API** (under the **PermissionKit** framework) — you are responsible for determining when your app has a "significant change"; a significant change requires **re-obtaining parental consent**.
- **New age-rating property type in StoreKit**.
- **App Store server notifications** for **consent revocations** — handle the case where a parent withdraws consent.

**Practical implication:**

- If you serve US users, assume Texas users may be minors with consent gating on downloads and purchases. Build entitlement/consent checks server-side.
- Pair this with the existing age-rating overhaul work (see below) — declared/verified age enforcement is now both a guideline obligation *and* a state-law obligation.

---

## Age Ratings: Australia & Vietnam Update (Effective June 18, 2026)

**Source:** <https://developer.apple.com/news/>

Starting **June 18, 2026**, App Store age ratings are updated in **Australia** and **Vietnam**. Notably, the **15+ age rating is no longer available** on the App Store in **Australia**. If you publish to these storefronts, re-check your App Store Connect age-rating answers so your displayed rating maps correctly to the local scheme.

---

## WWDC 2026 (June 8–12, 2026) — concluded; follow-ups tracked

**Source:** <https://www.apple.com/newsroom/2026/05/apple-kicks-off-worldwide-developers-conference-on-june-8/>

WWDC 2026 keynote was **June 8, 2026**. ✅ The expected Guidelines + ADPLA revision landed the same day — see [June 8, 2026 revision](#breaking-june-8-2026--guidelines--adpla-revision-wwdc-week). Post-WWDC follow-ups so far: **Brazil commerce opening (June 18)** and **social media age-rating questions (July 9, mandatory September 2026)** — both covered above — plus the **Sign in with Apple relay-domain change** announced June 15, 2026 — **revised August 24, 2026**: only **new Sign in with Apple** addresses move to **`private.icloud.com`**; existing `privaterelay.appleid.com` addresses keep working and **Hide My Email stays on `icloud.com`** (see [Fall 2026 changes](#sign-in-with-apple-relay-domain--correction-august-24-2026)). Accept both relay domains in allowlists and validators. The next-year SDK minimums have since been announced: **iOS 27 SDK + iOS 15 deployment target from April 2027** (see [Fall 2026 changes](#sdk--deployment-target-minimums-september-9-2026)).

---

## App Size Limits & Distribution Constraints

**Source:** <https://developer.apple.com/help/app-store-connect/reference/app-uploads/maximum-build-file-sizes>

### Maximum build sizes by platform

| Platform | Max Uncompressed App Size | Max Executable (__TEXT) | Notes |
| ---------- | -------------------------- | ------------------------ | ------- |
| iOS / iPadOS (iOS 9+) | 4 GB | 80 MB (total __TEXT) | — |
| macOS | 200 GB | — | — |
| tvOS | 4 GB | 500 MB | — |
| visionOS | 4 GB | 500 MB | — |
| watchOS | 75 MB | — | Total app |
| App Clips (iOS 17+) | 100 MB | — | Digital invocations only |
| App Clips (iOS 16) | 15 MB | — | — |
| App Clips (< iOS 16) | 10 MB | — | — |

> ⚠️ Apple's current table lists **80 MB** total `__TEXT` for iOS 9+ (tvOS/visionOS remain 500 MB). This is far below the historical 500 MB figure — re-verify on the live page before shipping unusually large binaries.

### Cellular download limit

- Apps over **200 MB** require Wi-Fi to download (default user setting)
- Users can override this in Settings, but many don't
- Best practice: keep initial download under 200 MB for maximum install conversion
- Use **Background Assets** framework (iOS 16+) to download large assets after install
- Use **On Demand Resources** (ODR) for conditional content

### Practical impact on rejection

- Apple won't reject for a large app, but conversion drops significantly above 200 MB
- watchOS apps exceeding 75 MB are rejected outright
- App Clip variants exceeding size limits after app thinning are rejected

```swift
// Use Background Assets for large content (games, ML models)
import BackgroundAssets

// Register assets in your AppDelegate
func application(_ application: UIApplication,
    handleEventsForBackgroundURLSession identifier: String) { ... }
```

---

## Screenshot & Media Requirements

**Source:** <https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications>

### iPhone screenshot sizes (updated October 2026)

Apple's spec page now groups iPhones by display class. Its "Required device sizes" text asks for **at least one screenshot for "iPhone with Dynamic Island (medium display)"**, while the size table still marks the 6.5" set as required when no Dynamic Island large-display screenshots are provided and scales everything else down from the largest set. Until Apple reconciles the page, upload **both** a large (6.9") and a medium (6.3") set.

| Display class | Dimensions (portrait) | Status | Device examples |
| --------- | ---------------------- | -------- | ----------------- |
| iPhone Duo | 1398 x 2034 (outer), 2007 x 2853 (inner); landscape also accepted | Optional; **required from April 2027** for apps built with the iOS 27.1 SDK+ | iPhone Duo |
| Dynamic Island, large | 1320 x 2868 · 1290 x 2796 · 1260 x 2736 | Recommended (scaling source) | iPhone 18/17/16 Pro Max, iPhone Air |
| Face ID, large (6.5") | 1284 x 2778 · 1242 x 2688 | Required if no Dynamic Island large set | iPhone 14 Plus, 13 Pro Max, XS Max |
| Dynamic Island, medium | 1206 x 2622 · 1179 x 2556 | **Listed as required** in the page header | iPhone 18 Pro, 17 Pro, 17, 16, 15 |
| Face ID, medium | 1170 x 2532 · 1125 x 2436 · 1080 x 2340 | Optional | iPhone 17e, 16e, 14, 13, 12 |
| Home button, large (5.5") | 1242 x 2208 | Optional (legacy) | iPhone 8 Plus, 7 Plus |

### iPad screenshot sizes (required baseline: 13", if app supports iPad)

| Display | Dimensions (portrait) | Status |
| --------- | ----------------------- | -------- |
| 13" | 2064 x 2752 | **Required** |
| 12.9" (older) | 2048 x 2732 | Optional |
| 11" | 1488 x 2266 | Optional |
| 10.5" | 1668 x 2224 | Optional |

### Screenshot rules

- Format: PNG or JPEG, RGB color space, **no transparency**
- 1–10 screenshots per device size
- **Exact pixel dimensions are mandatory** — even 1 pixel off can fail upload
- Screenshots must reflect the **current** build's UI — outdated screenshots trigger §2.3 rejection
- **More than half** of the screenshots for each device must show the app in use (e.g. 2 of 3, 3 of 4); the rest can be marketing frames, splash art, or benefits text ([App Review guidance for 2.3.3](https://developer.apple.com/help/app-review/guideline-reference/2-3-3-screenshots)). Login-only or splash-only sets are rejected; overlays may not crowd out the app UI. Third-party platform references (e.g. in a status bar) fall under §2.3.10.
- Screenshots cannot contain misleading content, fake device frames from wrong device, or competitor app names
- Brand or seasonal art that doesn't show the app belongs in **creative assets** (§2.3.14), not screenshots — see [Creative assets](#creative-assets-asset-library--product-page-preview-august-5--october-5-2026)

### App Previews (video)

- Up to 3 per localization
- 15–30 seconds
- Must capture actual app usage (not marketing video with actors)
- Audio is optional but must represent the app experience

### Automation recommendation

```bash
# Fastlane snapshot — auto-generate device screenshots
fastlane snapshot --devices "iPhone 16 Pro Max" "iPhone SE" "iPad Pro (12.9-inch)"
```

---

## visionOS-Specific Requirements

**Source:** <https://developer.apple.com/visionos/submit>

### App motion declaration (REQUIRED)

All visionOS apps **must** declare app motion information before submission. This is a **required property** in App Store Connect.

Options:

- **No high motion** — Standard spatial app
- **High motion** — App contains frequent fast/sudden camera movements, quick turns, or unusual orientations

This declaration appears on the product page to warn users sensitive to motion. Failing to declare it blocks submission.

### visionOS design requirements

- Must follow visionOS Human Interface Guidelines
- Proper use of **Shared Space** or **Full Space** layouts
- Spatial audio integration where applicable
- Gaze tracking and gesture input must work correctly
- Avoid cluttered or blocked visuals in 3D space
- Screenshots must be captured using **Reality Composer Pro Developer Capture** (not Control Center screen recordings)

### Compatible iPad/iPhone apps on visionOS

- iPad and iPhone apps can run on Vision Pro by default in "Compatible" mode
- Developers can opt out in App Store Connect
- If opted in: app runs in a flat window; review still applies
- "Designed for visionOS" requires native visionOS build and separate review

---

## Launch Screen Requirement

**Required since:** April 30, 2020  
**Source:** <https://developer.apple.com/documentation/xcode/specifying-your-apps-launch-screen>

All iOS apps submitted to the App Store **must** use an Xcode storyboard or Info.plist configuration to provide a launch screen. Apps without a launch screen storyboard are rejected.

### Xcode 16 known issue

The launch screen configuration UI in Xcode 16 has known bugs:

- Blue dropdown autofill button doesn't work reliably
- Fix: manually type the launch screen filename in the field
- Verify `UILaunchStoryboardName` key exists in Info.plist

```xml
<!-- Info.plist — required -->
<key>UILaunchStoryboardName</key>
<string>LaunchScreen</string>
```

### SwiftUI apps

SwiftUI apps using `@main` App protocol may not have an explicit storyboard. Ensure the Info.plist or target settings specify the launch configuration:

```swift
// In your app's target → General → App Icons and Launch Screen
// Set "Launch Screen File" to your LaunchScreen.storyboard name
```

---

## App Transport Security (ATS) Exceptions

**Source:** <https://developer.apple.com/documentation/bundleresources/information-property-list/nsapptransportsecurity>

### What triggers review scrutiny

Setting `NSAllowsArbitraryLoads = YES` globally disables ATS and requires **documented technical justification** during App Review. Apps have been rejected for weak or missing justification.

### Recommended approach

Use domain-specific exceptions instead of global disabling:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <!-- ❌ Global disable — requires justification, may be rejected -->
    <!-- <key>NSAllowsArbitraryLoads</key><true/> -->
    
    <!-- ✅ Domain-specific exception — targeted and more likely approved -->
    <key>NSExceptionDomains</key>
    <dict>
        <key>legacy-api.example.com</key>
        <dict>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.2</string>
        </dict>
    </dict>
</dict>
```

### iOS 17+ changes

iOS 17 introduced stricter IP-based connection restrictions. Apps connecting to IP addresses (not hostnames) now require explicit `NSExceptionDomains` entries or `NSAllowsLocalNetworking` for local network access.

### ATS and web content

- `NSAllowsArbitraryLoadsInWebContent = YES` allows WKWebView to load arbitrary URLs without affecting the rest of the app's ATS policy
- This is the recommended approach for apps that display third-party web content

---

## ITMS Error Codes Beyond ITMS-91061

### ITMS-9000: Invalid Binary (catch-all)

Most common causes:

- Incomplete or missing metadata/assets
- Entitlements in app don't match App Store Connect capabilities
- Mismatched or expired provisioning profiles
- Incorrect Xcode build settings or SDK version

### ITMS-90161: Invalid Provisioning Profile

Causes:

- Expired provisioning profile
- Bundle identifier mismatch between Xcode project and profile
- Revoked distribution certificate
- App ID capabilities don't include entitlements claimed in the binary

Fix workflow:

```
1. Apple Developer Portal → Profiles → find your distribution profile
2. Verify: not expired, correct bundle ID, correct certificate
3. If expired/revoked: regenerate profile with valid certificate
4. Xcode → Signing & Capabilities → match profile
5. Clean build folder (Cmd+Shift+K) → rebuild → re-archive
```

### ITMS-90809: Deprecated API Usage

Triggered when the binary contains references to deprecated APIs like `UIWebView`. Even third-party framework binaries containing `UIWebView` symbols will trigger this. Fix: update all dependencies.

### ITMS-91053: Missing Privacy Manifest for Required Reason API

Similar to ITMS-91061 but specifically for your own code (not SDKs) using required-reason APIs without declaring the reason in your app-level `.xcprivacy` manifest.

Fix:

```xml
<!-- YourApp/PrivacyInfo.xcprivacy -->
<dict>
    <key>NSPrivacyAccessedAPITypes</key>
    <array>
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryFileTimestamp</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>C617.1</string>
            </array>
        </dict>
    </array>
</dict>
```

### Entitlements mismatch pattern

```
ITMS error: "Invalid Entitlements — the entitlements in your app bundle 
do not match the ones in your provisioning profile"
```

Common when:

- You add a capability in Xcode (e.g., Push Notifications, Sign in with Apple) but don't regenerate the provisioning profile
- You remove a capability from ASC but the entitlements file still claims it
- Your development entitlements leak into the distribution build

Debug:

```bash
# Compare app entitlements vs provisioning profile
codesign -d --entitlements :- YourApp.app

# Extract provisioning profile entitlements
security cms -D -i embedded.mobileprovision
```

---

## App Store Improvements: Inactive App Removal

**Source:** <https://developer.apple.com/help/app-review/safe-and-trusted-store/app-store-improvements> (moved from developer.apple.com/support/app-store-improvements; linked from "Before You Submit" since October 2026)

Apple evaluates all apps for function, current-guideline compliance, and staleness; App Review contacts you when an app has an issue. It proactively removes apps that meet ALL of these criteria:

1. Not updated within the **last 3 years**
2. Fail to meet a **minimum download threshold** (extremely few or zero downloads in a rolling 12-month period)

### Process

- Developer receives email notification identifying the app
- **90-day window** to submit an update
- Apps that **crash on launch** are removed immediately (no 90-day grace period)
- Removed apps can be resubmitted anytime if developer maintains active membership
- Existing users retain access (including in-app purchases); only new downloads are blocked; the app name stays reserved to your account

### Other live-app removals (not inactivity)

Per [Respond to an app removal](https://developer.apple.com/help/app-review/safe-and-trusted-store/app-removals), App Review also removes live apps that include **hidden features** or were **modified after approval** (e.g. via a server-side change) in a way that introduces a violation, apps with user-reported issues that didn't occur in review, and apps subject to legal / IP / government takedown requests:

- Less severe issues: usually **14 days** to submit a compliant update
- Serious safety or legal issues: **immediate** removal; restore by shipping a compliant update
- Fundamentally incompatible concepts (e.g. built to deliver objectionable content): removed and **cannot be restored** — only a new app with a compatible concept

### Implications for long-lived apps

- Even "finished" apps need periodic updates (at minimum: rebuild with latest Xcode/SDK)
- Test old apps on latest OS versions before they break
- Keep your Developer Program membership active

---

## Regulated Medical Device Apps (2027 Deadline)

**Source:** <https://www.world-today-news.com/app-store-requirements-for-regulated-medical-device-apps-in-eea-uk-and-us/>  
**Update (March 26, 2026):** <https://developer.apple.com/news/?id=03262026a>

Apps in the **Health & Fitness** or **Medical** categories targeting EEA, UK, and US markets will be required to declare **"Regulated Medical Device"** status in App Store Connect metadata.

**Current status (as of March 26, 2026):**

- The App Store **now displays** regulated medical device status on product pages in EEA, UK, and US
- **New apps**: Must provide regulated device status **immediately** to distribute in these regions
- **Existing apps**: Must provide status by **early 2027**; after that deadline, app updates cannot be submitted without the declaration
- Apps with frequent references to Medical/Treatment Information must also declare status
- Developers can select **"No"** if the app is not a regulated medical device
- CI/CD pipelines that auto-submit may break if the metadata declaration isn't added

### What this means for health apps

If your app makes medical claims, measures health metrics, or claims to diagnose conditions:

- Declare medical device status in App Store Connect **now** (don't wait for the 2027 deadline)
- Ensure regulatory clearance documentation is ready
- Include FDA/CE clearance information in Notes for Review

---

## Code Signing & Provisioning: Complete Troubleshooting

### The signing chain

```
Apple Root CA
  └── Your Distribution Certificate (WWDR-signed)
       └── Provisioning Profile (cert + App ID + capabilities + devices)
            └── Your App Binary (codesigned with the cert referenced in the profile)
```

All four must be valid and internally consistent at upload time.

### Common failures and fixes

| Symptom | Cause | Fix |
| --------- | ------- | ----- |
| "No matching provisioning profile" | Profile doesn't include current entitlements | Regenerate profile in Developer Portal with current capabilities |
| "Certificate has been revoked" | Someone on team revoked the cert | Create new distribution cert; regenerate all profiles |
| ITMS upload fails silently | Expired profile | Check profile expiry in Keychain Access |
| "Entitlements not present in provisioning profile" | Capability added in Xcode but not in App ID | Enable capability in Developer Portal → App IDs → Your App → Capabilities |
| Different signing on different Macs | Each dev has different cert/profile | Use Xcode automatic signing for development; manual for distribution with shared profile |

### Xcode automatic signing gotcha

Automatic signing generates development profiles automatically but does **NOT** handle distribution profiles. For App Store submission:

- Use manual signing for Release configuration
- Or use `fastlane match` for team-shared certificates and profiles

---

## TestFlight Review vs. App Store Review

TestFlight builds also undergo review. Key differences:

| Aspect | TestFlight Beta Review | App Store Review |
| -------- | ---------------------- | ------------------ |
| Scope | Lighter; checks for crashes, basic compliance | Full guideline review |
| Timeframe | Usually < 24 hours | 24-72 hours (can be longer) |
| Demo account required? | Sometimes | Yes, for account-based apps |
| Rejection consequence | Cannot distribute to external testers | Cannot distribute to public |
| Privacy label required? | Not fully | Yes |
| IAP review? | No (sandbox only) | Yes (products must be submitted) |

### TestFlight-specific rejection triggers

- App crashes on launch (auto-rejected, no review needed)
- Export compliance not answered
- Privacy-impacting SDKs without manifests (ITMS errors at upload)
- Beta Entitlement issues

### TestFlight to App Store transition trap

Apps that work fine on TestFlight may still fail App Store review because:

- TestFlight uses sandbox receipts; App Store review uses a different environment
- IAP products must be explicitly submitted with the App Store build (TestFlight doesn't check this)
- Privacy nutrition labels are enforced for App Store but not TestFlight
- Full guideline review applies only at App Store submission

---

## Universal Links / Associated Domains

Common rejection-adjacent issue: Universal Links don't work during review because the Apple App Site Association (AASA) file is misconfigured.

### Requirements

```json
// https://yourdomain.com/.well-known/apple-app-site-association
// Must be served with Content-Type: application/json
// Must be accessible without redirects at this exact path
// Must be served over HTTPS (no ATS exceptions)
{
    "applinks": {
        "details": [{
            "appID": "TEAMID.com.yourcompany.yourapp",
            "paths": ["/path/*", "/other-path"]
        }]
    }
}
```

### Common failures

- AASA file behind authentication (CDN, firewall)
- AASA file returns 301 redirect instead of direct response
- Wrong `appID` format (must include Team ID prefix)
- AASA file not at `.well-known/apple-app-site-association` exact path
- Server doesn't return `Content-Type: application/json`

### Verification

```bash
# Check if Apple can reach your AASA file
curl -v "https://app-site-association.cdn-apple.com/a/v1/yourdomain.com"

# Or use Apple's diagnostic tool:
# https://search.developer.apple.com/appsearch-validation-tool/
```

---

## Widget, Live Activity, and App Clip Rules (§2.5.16)

### Widgets

> *"Widgets, extensions, and notifications should be related to the content and functionality of your app."*

Rejection triggers:

- Widget that displays advertising
- Widget unrelated to host app's core function
- Widget that deep-links to purchase flows
- Widget that duplicates another widget without differentiation

iOS app widgets can also appear on **CarPlay and Apple HomeView** (ADPLA §6.4.1, October 13, 2026): mark widgets that need heavy interaction or an unlocked iPhone with `disfavoredLocations` so they aren't offered while driving.

### Live Activities

- Must represent real-time data (sports scores, delivery tracking, timers)
- Cannot be used for persistent advertising
- Must end when the activity is complete (don't leave stale Live Activities)
- Dynamic Island content must be concise and glanceable
- **§4.5.3 (June 8, 2026)**: may not be used to **spam, phish, or send unsolicited messages** to customers

### App Clips

- Cannot contain advertising (§2.5.16(a))
- Must include all features in the main app binary
- Size limits enforced strictly (10MB–100MB depending on iOS version)
- Must provide genuine value in the clip experience
- Cannot require account creation for basic App Clip functionality

---

## Accessibility as a Rejection Vector

While Apple doesn't have an explicit "accessibility" guideline that causes rejection, the **Human Interface Guidelines** strongly recommend accessibility, and several guidelines indirectly enforce it:

- §4.2 Minimum Functionality: An app with inaccessible UI may be considered "not app-like"
- §2.1 App Completeness: VoiceOver causing crashes = completeness failure
- §5.1.1 Privacy: Users who deny permissions must still be able to use the app

### Best practices that reduce rejection risk

```swift
// Ensure all interactive elements have accessibility labels
Button(action: {}) {
    Image(systemName: "plus")
}
.accessibilityLabel("Add new item")

// Ensure Dynamic Type support (reduces §4.2 risk on iPad)
Text("Welcome")
    .font(.title)  // System fonts automatically support Dynamic Type

// Support VoiceOver navigation order
VStack {
    headerView
    contentView
    footerView
}
.accessibilityElement(children: .contain)
```

---

## Commission Rate Changes (2026)

### China (effective March 15, 2026)

**Source:** <https://developer.apple.com/news/?id=03122026a>

- Standard IAP commission: **25%** (down from 30%)
- Small Business Program & Mini Apps Partner Program (first year): **12%** (down from 15%)
- Auto-renewal rate after year 1: **12%** (down from 15%)

### Global rates

- Standard: 30%
- Small Business Program (< $1M revenue): 15%
- Auto-renewal after year 1: 15%

### EU — unified business terms (effective October 1, 2026)

⚠️ **Superseded.** The interim EU fee stack this section used to describe (5% CTC on all channels, 5%/13% store services tiers, 2% initial acquisition fee, and the rule that one app could not offer IAP and alternative payments together) **no longer applies**. Since **October 1, 2026**, ADPLA **Attachment 14** sets: **26% / 15%** for IAP, **20% / 10%** for in-app alternative payments, **15% / 10%** store services commission on out-of-app offers with an actionable link, and **5%** CTC only for alternatively distributed apps; IAP and alternative payments may be offered **together**. Full details: [EU Unified Business Terms — Attachment 14](#eu-unified-business-terms--adpla-attachment-14-effective-october-1-2026).

**Source:** <https://developer.apple.com/support/apps-in-the-eu/>

> Always confirm exact current percentages against Apple's EU support page before quoting them to stakeholders — EU terms have changed repeatedly.

---

## Legal Precedent: Apple's Right to Remove (March 2026)

**Source:** <https://www.macrumors.com/2026/03/18/apple-wins-decisive-victory-in-musi-app-store-removal-lawsuit/>

A federal judge ruled that Apple's developer agreement gives it the right to **remove any app from the App Store at any time, "with or without cause."**

Implications for developers:

- Appealing a removal is a business request, not a legal right
- The developer agreement supersedes the published guidelines
- Apple can remove apps even if they comply with all published guidelines
- This reinforces the importance of maintaining a positive relationship with App Review

---

## Overlooked Permission: Local Network Access

iOS 14+ requires user permission for local network discovery (Bonjour, mDNS, multicast).

### Rejection pattern

- App requests local network without a clear purpose string
- Reviewer denies the prompt and app becomes non-functional

### Info.plist requirement

```xml
<key>NSLocalNetworkUsageDescription</key>
<string>Discover and connect to smart home devices on your Wi-Fi network.</string>

<!-- Also declare Bonjour services if applicable -->
<key>NSBonjourServices</key>
<array>
    <string>_http._tcp.</string>
    <string>_myservice._tcp.</string>
</array>
```

---

## Notification Permission Timing

A frequently overlooked rejection trigger: requesting notification permission at app launch.

### Rejected pattern

```swift
// ❌ Requesting at launch — before user sees any value
func application(_ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { _, _ in }
    return true
}
```

### Correct pattern

```swift
// ✅ Request in context, after user has seen value
struct OnboardingCompleteView: View {
    var body: some View {
        VStack {
            Text("Stay updated on your orders")
            Text("Get notified when your items ship or are delivered.")
            
            Button("Enable Notifications") {
                UNUserNotificationCenter.current().requestAuthorization(
                    options: [.alert, .badge, .sound]
                ) { granted, _ in
                    // Continue regardless of user choice
                }
            }
            
            Button("Not Now") {
                // Continue without notifications — app must work normally
            }
        }
    }
}
```

### Apple's review behavior

Reviewers test what happens when they **deny** every permission prompt. If the app:

- Crashes → §2.1 rejection
- Shows a blocking error screen → §5.1.2 rejection (coercion)
- Shows empty state with "enable notifications to continue" → rejection
- Continues with reduced functionality → approved

---

## Guideline 5.6.4: App Quality (Post-Approval Risk)

A frequently overlooked post-approval guideline:

> *"Customers expect the highest quality from the App Store... Indications that this expectation is not being met include excessive customer reports about concerns with your app, such as negative customer reviews, and excessive refund requests."*

This means:

- Even approved apps can be removed for low quality
- High refund rates trigger review
- Many 1-star reviews with "scam" or "doesn't work" flag the app
- Apple monitors post-approval quality metrics

### Proactive defense

- Monitor crash rates via App Store Connect → Analytics → Crashes
- Respond to negative reviews via ASC (shows Apple you're engaged)
- Fix reported issues promptly in updates
- Don't buy, incentivize, or filter reviews (§3 Business intro, §5.6.3 Discovery Fraud — Program-removal risk); don't force ratings to unlock anything (§3.2.2(x)); prompt only through Apple's review API (§5.6.1)

---

## Japan App Store — ADPLA Attachment 12 (commerce & distribution)

**Source:** Apple Developer Program License Agreement, **Attachment 12** — retitled **"Additional Terms for iOS Apps in Japan, Brazil"** in the June 18, 2026 revision — as amended from time to time. For Brazil-specific terms, see [the Brazil section](#breaking-brazil--alternative-distribution--payments-june-18-2026) above.

This is **contractual** and **territory-specific**. Always verify **Apple Materials** in App Store Connect and the developer portal before shipping; the ADPLA defers details to those materials.

### Who it affects

- Apps distributed on the **Japan storefront** of the App Store that use **Alternative Payment Processing**, **Out-of-App Offers**, and/or participate in **Alternative App Marketplaces (Japan)**.
- **Alternative App Marketplace (Japan)** is a separate track (marketplace app + **Marketplace Website (Japan)**, **MarketplaceKit**, install verification tokens, eligibility such as org enrollment, standing, volume, or letter-of-credit path per ADPLA).

### Alternative Payment Processing & Out-of-App Offers (high-signal rules)

- **Entitlement + StoreKit**: Before each payment or “enter payment info” flow, call the **documented StoreKit APIs** to confirm eligibility and, when required, show Apple’s **system disclosure sheet** (follow Apple Materials for timing/wording).
- **Payment flow must complete inside the same app** for Alternative Payment Processing; **no hidden or undocumented** payment behavior.
- **Out-of-App Offers**: Actionable link must open the **default web browser** app — **not** an in-app web view (`WKWebView`).
- **Parity / prominence**: On any UI that merchandises a digital purchase, **Apple’s IAP** must be offered **alongside** any other payment option, **at least as prominently**, and per Apple Materials when shown together.
- **No discouragement**: Do not use **disparaging** copy or visuals that **discourage** use of Apple’s IAP.
- **Product page**: Do **not** put website purchase info or outbound purchase links on the **App Store product page** for the app.
- **Reader external link entitlement**: Cannot use the **StoreKit External Link Account** (reader) entitlement **as** Alternative Payment Processing / Out-of-App Offers; they are distinct programs.
- **Age / Kids**: **Kids** category apps cannot use Out-of-App Offers to minors; **under 13** — no Out-of-App Offers; **13–17** — parental gate may be required for certain options; **under 18** — restrictions on Alternative Payment Processing per ADPLA §3.1(B).
- **TestFlight**: Allowed for beta-testing these flows only if **transactions are free** to testers.
- **Device / OS**: Entitlements are tied to **Japan** devices and minimum iOS versions specified in Apple Materials (ADPLA text references **iOS 26.2** or later for certain capabilities — treat as “current Apple Materials” if version numbers shift).

### Commissions & reporting (summary)

Attachment 12 specifies **commission** rates for Alternative Payment Processing, Apple IAP (amended rates for Japan storefront), Out-of-App Offers (e.g. **7-day** attribution window after actionable link), and **Core Technology Commission** for marketplace / related web sales. It also requires **reporting**, **invoicing**, **payment** to Apple, **tax** handling, **audit** retention (e.g. **3 years**), and **PCI Level 1** certification attestation for external payment providers where applicable.

**Engineering + finance + legal** should review together — this is not just App Review UI polish.

### Related Apple pages

- [Apple Developer Program License Agreement](https://developer.apple.com/support/terms/apple-developer-program-license-agreement/) (full text; search “Attachment 12”)
- App Store Connect **Agreements, Tax, and Banking** and **entitlement** request flows for Japan-specific capabilities
