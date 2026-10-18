---
name: appstore-deployment
description: App Store deployment compliance agent for iOS/macOS apps. Audits submissions for rejection risk, guides pre-submission preparation, and produces App Review notes, demo account packages, and appeal templates. Use when submitting to the App Store, debugging a rejection or removal, preparing review notes, checking IAP/subscription compliance, auditing privacy manifests, reviewing permissions, location and advertising/ATT rules, Apple or third-party trademarks and marketing assets, encryption export compliance, safety or objectionable content (violence, weapons, gambling), speech/UGC policy, sandboxing and system-level access, evaluating AI features, handling external purchase links, avoiding account-termination risks (hidden or dormant code, bait-and-switch, fake reviews or ranking manipulation, copycats and impersonation), or aligning with Apple Developer Program License Agreement (ADPLA) program requirements.
---

# App Store Deployment Agent Skill

Reduces App Store rejection risk by applying Apple's current review criteria (guidelines last updated **October 16, 2026**) to your submission before it reaches reviewers.

> **Skill content last reviewed: October 17, 2026.** Latest: **October 16, 2026 guidelines revision** ([announcement](https://developer.apple.com/news/?id=92th7e5i) · [Apple's rationale](https://developer.apple.com/news/?id=vvzx6cul)) — new **§2.5.19** moves the hidden / dormant / undocumented-features ban out of §2.3.1 and adds that hiding features in the binary (e.g. **code obfuscation**) → **app removal + developer account termination**; this is Apple's answer to **bait-and-switch** apps that pass review and reveal harmful features (e.g. "nudification") after approval (**Gate L**, **Gate F**). **§1.2** now explicitly covers **AI content creation apps** and **nonconsensual intimate imagery** (incl. AI-generated); missing safeguards or unremediated violations = **Developer Code of Conduct** violation (**Gate G**). New **§2.3.14** creative assets (**Gate E**); **§3.1.1(a)** links go to a "permitted destination", Music Streaming entitlement merged (**Gate D**); **§5** apps facilitating human trafficking / child exploitation are removed **and blocked from alternative distribution** (**Gate K**). **October 13, 2026 ADPLA** — §3.2 now prohibits deceiving about a product's identity/purpose/behavior "including through post-review modifications", credential-phishing impersonation, intentionally weakened security, collecting data beyond what consent covered, and using users' device/network resources for third parties without consent (**Gate J**, **Gate L**). **October 1, 2026** — EU unified business terms (ADPLA **Attachment 14**): IAP and alternative payments side by side, new commission table (**Gate D**). Fall 2026 operations: iPhone Duo screenshots (required from April 2027), creative assets, April 2027 SDK minimums, ATT alternative prompt in the EU. Details: [ADDENDUM.md](ADDENDUM.md). Earlier: social media age-rating questions (July 9, 2026), Brazil alternative distribution/payments (June 18, 2026), June 8, 2026 revision (§4.3 spam tiers, §1.2 compliance plans, §4.5.3 Live Activities, ADPLA §3.3.11 AI/ML), Texas SB 2420 age assurance (June 4, 2026).

> **Authority hierarchy**: Apple's **App Review Guidelines** → **Program Requirements** (including Human Interface Guidelines and API docs incorporated into the [Apple Developer Program License Agreement](https://developer.apple.com/support/terms/apple-developer-program-license-agreement/) — “ADPLA”) → Apple Support/Developer Docs → Apple WWDC transcripts → Community evidence (Reddit/StackOverflow/blogs).  
> When sources conflict, defer to the most recent Apple document. **Guidelines and ADPLA overlap but are not identical**: review can cite either; binary behavior must satisfy documented APIs, privacy rules, and contractual program requirements, not only guideline prose.

## Quick Approach

When invoked, run these gates in order — stop at the first blocker and fix it before continuing:

1. **Gate A – Hard submission blockers** (ITMS errors, missing manifests, broken URLs, **encryption/export**)
2. **Gate B – App completeness** (crashes, placeholders, missing demo access, broken IAPs)
3. **Gate C – Privacy & permissions** (policy, purpose strings, **location & ads**, ATT, account deletion)
4. **Gate D – Payments & monetization** (IAP rules, storefront scope, subscription UI)
5. **Gate E – Design & metadata** (4.2 minimum functionality, hidden features, spam, copycats, screenshots & creative assets, **IP & marks**)
6. **Gate F – Code execution & supply chain** (private APIs, hot updates, **§2.5.19 dormant code & obfuscation**, SDK manifests and SDK behavior)
7. **Gate G – AI, UGC, and age gates** (disclosure, moderation, **NCII safeguards**, declared/verified age limits)
8. **Gate K – Safety, speech, gambling, sandbox & system access** (§1.1 violence/weapons, §5.3, containers vs privileged APIs)
9. **Gate H – Trust & anti-scam checks** (dark patterns, misleading pricing, manipulative flows)
10. **Gate L – Review integrity & account-termination triggers** (hidden/dormant code, bait-and-switch, review/ranking manipulation, copycats & impersonation — these end the **whole developer account**, not one app)
11. **Gate I – Review operations & evidence** (reviewer-only bug logging, Resolution Center packets, removals & appeals)
12. **Gate J – ADPLA & program requirements** (submission duties, TestFlight contract rules, restricted APIs/data uses called out in the license)

Then produce: **Submission readiness verdict → Reviewer path script → App Review Notes draft → escalation options**.

---

## Gate J — ADPLA & program requirements (contract + §3.3)

Use this gate when auditing **contractual** obligations and **Program Requirements** embedded in the ADPLA. These often align with App Review but can bite in legal, enterprise, or niche API cases.

**Submission & honesty (Agreement §6.1, §3.2)**

- Do **not** hide, misrepresent, or obscure features from review; you must cooperate with reasonable information requests.
- **§3.2 security & integrity terms (expanded October 13, 2026)** — you may not create any product or code that would: (1) defeat Apple security, signing, DRM, or authentication mechanisms; (2) violate the security, integrity, or availability of any user, network, or system; **(3) "deceive regarding the identity, purpose, or behavior of the product, code, or program, including through post-review modifications that would cause harm to others"**; (4) impersonate Apple or any third party to solicit credentials or other sensitive information; (5) implement security or privacy protections that are intentionally weakened or inconsistent with what is described; (6) access, collect, or transmit user data — through your code **or someone else's** (SDKs) — beyond what was disclosed when consent was requested; (7) use the user's **device or network resources to provide services to third parties** without explicit prior consent (e.g. bandwidth-sharing / proxy SDKs, background compute). Items (3)–(7) are new; (3) is the contract-level twin of guideline **§2.5.19** (**Gate L**).
- Same section (long-standing): no fraudulent reviews of your own or anyone else's app, no app names **substantially similar** to a third-party app to create confusion, no **name squatting**, no bait-and-switch pricing or deceptive business practices.
- If the app **connects to a physical device** (including MFi accessories), **inform Apple in App Store Connect**: disclose the means of connection (e.g. BLE, Lightning, headphone jack, other protocol) and identify **at least one** physical device the app is designed to work with. Apple may request access or samples **at your expense** (samples may not be returned).
- Any post-submission change to functionality (including IAP-delivered functionality) generally requires **resubmission** for App Store / Custom App Distribution.

**Service providers (§2.9)**

- A third-party **may not** submit your app to the App Store or operate **TestFlight** on your behalf. (They may still host servers, etc., under a written agreement at least as protective of Apple as the ADPLA.)

**Certificates & signing (§5)**

- Safeguard distribution and submission certificates and keys; **do not** put App Store distribution certificates in shared cloud repos for third-party use. Notify Apple promptly if you believe keys are compromised (`product-security@apple.com`).

**Multi-platform visibility (§6.3, §6.4)**

- iOS/iPadOS apps submitted to the App Store may also be offered on **macOS** and **visionOS** via the App Store unless you **opt out** in App Store Connect — confirm rights, compatibility, and testing, or opt out.
- **CarPlay & Apple HomeView (§6.4.1–6.4.2, October 13, 2026)**: iOS app widgets may surface on **CarPlay and Apple HomeView**; you must decide whether a widget suits CarPlay and use **`disfavoredLocations`** when it does not (e.g. heavy interaction while driving, requires unlock) — Apple may also disfavor it. Apps with a CarPlay entitlement in an Apple-identified category may be offered on **Apple HomeView** while running on a paired iPhone.

**New API & entitlement terms (October 13, 2026)**

- **Live Assistance API (§3.3.8(J))**: only at the user's invitation, and **solely** to let an accessibility service provider join the user's FaceTime call (e.g. captioning, translation).
- **App Replacement Entitlement (§3.3.8(K))**: the system sheet that moves data/settings from your old app and deletes it is allowed only when **both** apps are under **your** membership and have **substantively the same primary purpose and functionality** — not a way to swap one app's install base into a different product.

**TestFlight — external testers (§6.5, §7.4)**

- **External** TestFlight distribution requires an **Apple-reviewed** build first. Updates with **significant changes** need re-review per App Store Connect process.
- **No fees** to participate in TestFlight; digital purchases in TestFlight builds must be **free** and for beta only.
- Prohibited: using TestFlight to **circumvent** the App Store, run perpetual demos, or **solicit App Store ratings**.
- **Kids-focused apps**: verify external beta testers meet **age of majority** in their jurisdiction.

**Operations & analytics (§6.7–6.8)**

- Data from Apple’s **developer analytics** is for **improving your apps** only — no handoff to third parties except a **Service Provider** under strict use limits; no cross-developer aggregation.
- Apps in the store must stay **compatible with the currently shipping OS**; Apple may remove apps that fall behind.

**Privacy & data — program text beyond typical guideline bullets**

- **Recordings** (camera, mic, screen, etc.): show a **clear, conspicuous** in-app indicator while recording; do not design for **secret** recording of others.
- **Sensitive Content Analysis**: do **not** send **off device** any signal about whether content was flagged as containing nudity.
- **Face Data**: use only for a **direct app purpose**; no advertising, authentication, or marketing; no selling to brokers; transfer off-device only with **clear consent** and for the stated function.
- **Health / HIPAA**: the ADPLA restricts using Apple Software/Services (including iCloud / CloudKit in scope of the terms) to create, store, or transmit **PHI** in ways that would make Apple your **business associate** — treat regulated health stacks as **legal + architecture** review, not checklist-only.
- **Declared Age Range / Significant App Update APIs**: use only for **age-appropriate features** or **legal** requirements; no selling that data to data brokers.
- **IDFA / AdSupport**: use only for **serving advertising**; honor **Tracking Preference**; after user resets the advertising identifier, do **not** link old and new IDs or derived data.

**In-App Purchase — Attachment 2 hooks**

- **Keyboard extensions** must **not** offer IAP **inside** the keyboard UI (IAP OK elsewhere in the host app).
- **US App Store storefront**: for **one-time** digital content IAP, before purchase you must clearly state the user is buying a **license**, with a link to full terms including **Apple Media Services Terms and Conditions** (and your EULA if applicable) — see Attachment 2 §3.2.
- **Multiseat subscriptions (Schedules 2 & 3, October 13, 2026)**: **Group Purchases** (one subscriber buys seats for a group) and **Volume Purchasing** through Apple Business / Apple School Manager. Multiseat is **on by default** for subscriptions in App Store Connect — your entitlement backend must grant access to **Group Members**, and refunds can be per unused seat. Opt out per subscription if you can't support it (see **Gate D**).
- **Tax data (Schedules 2 & 3 §3.3)**: Apple reports legally required tax/sales/contact information to tax authorities; customer tax information Apple shares with you may be used **only** for legally mandated purposes.

**Maps & Weather — required notice text (§3.3.3(F), WeatherKit Attachment 8)**

- **Real-time route / navigation** apps: EULA must include Apple’s required **“YOUR USE OF THIS REAL TIME ROUTE GUIDANCE APPLICATION IS AT YOUR SOLE RISK. LOCATION DATA MAY NOT BE ACCURATE.”** (exact wording per current ADPLA / docs).
- **Real-time weather guidance** via WeatherKit: EULA must include the **WeatherKit** disclaimer block required in Attachment 8 (accuracy / sole risk).

**AI & machine learning (§3.3.11) — Foundation Models (§3.3.11(A))**

- The **June 8, 2026** ADPLA revision grouped AI/ML technologies under new **§3.3.11** and moved/updated the Foundation Models requirements (formerly §3.3.8(I)); §3.2(h) also updated terms for use of and access to **Apple models**.
- On-device / Apple Intelligence **Foundation Models** use is subject to Apple’s **[Foundation Models Framework Acceptable Use Requirements](https://developer.apple.com/apple-intelligence/acceptable-use-requirements-for-the-foundation-models-framework)**; keep **Adapters** compatible with the shipping model or risk OS incompatibility (§6.8).

**Regional addenda (verify current Apple Materials)**

- **Japan** — Alternative App Marketplaces, **Alternative Payment Processing**, and **Out-of-App Offers** have detailed design, entitlement, browser, prominence, age-gating, and reporting rules (**Attachment 12**, retitled June 18, 2026 to "Additional Terms for iOS Apps in Japan, Brazil"). See [ADDENDUM.md — Japan ADPLA commerce](ADDENDUM.md#japan-app-store--adpla-attachment-12-commerce--distribution).
- **Brazil** — As of **June 18, 2026** (iOS 26.5, CADE agreement): alternative app marketplaces, **Alternative Payments**, and **Out-of-App Offers** with Apple-granted entitlements; **Core Technology Commission** applies. Terms live in the same **Attachment 12**; Apple set a **July 6, 2026** deadline for accepting the updated ADPLA. See [ADDENDUM.md — Brazil](ADDENDUM.md#breaking-brazil--alternative-distribution--payments-june-18-2026).
- **China mainland storefront** — **Attachment 13** amends **paid** commission rates (e.g. **25%** standard / **12%** small business and qualifying cases effective **March 15, 2026** per ADPLA text); confirm current Paid Applications Agreement for accounting.
- **European Union** — **Attachment 14** (added August 18, 2026; **effective October 1, 2026**) replaces the Alternative Terms Addendum and the StoreKit External Purchase Link Entitlement (EU) Addendum with one set of EU business terms: IAP and alternative payments **side by side**, out-of-app offers, child-safety gates, new commission table, expanded alternative-marketplace / Web Distribution eligibility. The Account Holder must accept the updated ADPLA. See **Gate D** and [ADDENDUM.md — EU Attachment 14](ADDENDUM.md#eu-unified-business-terms--adpla-attachment-14-effective-october-1-2026).

**Notarization (macOS §5.3)**

- You may **not** tell users that Apple “approved” or “secured” your app because it was notarized; notarization is **not** a malware or safety guarantee from Apple’s perspective.
- **Developer ID Sub-CA expiry — February 1, 2027**: certificates issued by the original Developer ID Certification Authority stop working; `.pkg` installers signed with them **will not install** after that date. Create a replacement from the **G2** Sub-CA and re-sign packages now; already signed and notarized apps with a secure timestamp keep working ([announcement](https://developer.apple.com/news/?id=w4atic4c)).

**EU — DSA & P2B (Schedule 1 Exhibit D)**

- Developers established in the EU can use Apple’s **DSA redress** information for account/app removal disputes: [apple.com/legal/dsa/redress-options](https://www.apple.com/legal/dsa/redress-options). **P2B** (platform-to-business) complaints for eligible regions: [developer.apple.com/contact/p2b](https://developer.apple.com/contact/p2b/).

**Illegal content & platform safety (§3.2(b))**

- The ADPLA prohibits using Apple tools or services for unlawful activity or content — including material that **facilitates child sexual exploitation or abuse**. Such content is also a **hard rejection** under **Safety** guidelines (**§1.1**). Treat as **legal + safety + immediate removal** if ever implicated.

---

## Rejection Frequency (Apple 2025 — ~22% rejection rate)

Apple reviewed **more than 9.1 million** submissions and rejected **over 2 million** in 2025 (~1.2M new apps + ~800K pending updates; it also rejected ~138,000 developer enrollments and terminated ~193,000 developer accounts for suspected fraud — [2025 App Store Transparency Report](https://www.apple.com/legal/app-store/transparency/2025/)). A single rejection can cite multiple sections. Ranked by section prevalence:

| Rank | Section         | Apple Anchor          | Key Sublaws                                                |
| ---- | --------------- | --------------------- | ---------------------------------------------------------- |
| 1    | **Performance** | §2.1 App Completeness | Crashes, placeholders, missing IAP, broken links           |
| 2    | **Legal**       | §5.1 Privacy          | Policy missing, bad purpose strings, ATT, account deletion |
| 3    | **Design**      | §4.2 / §4.3           | Minimum functionality, spam, copycats                      |
| 4    | **Business**    | §3.1 Payments         | IAP required, external links, subscription UI              |
| 5    | **Safety**      | §1.2 UGC              | Missing moderation, report/block                           |
| —    | **Technical**   | §2.5.1/2.5.2          | Private APIs, code download, hot updates                   |

> Apple states: **"Over 40% of unresolved issues are related to Guideline 2.1: App Completeness."**  
> Apple also states: _"On average, 90% of submissions are reviewed in less than 24 hours."_  
> Source: [developer.apple.com/app-store/review](https://developer.apple.com/app-store/review/)

---

## Gate A — Hard Submission Blockers

These prevent even entering review. Fix before submitting.

**Minimum SDK & deployment-target requirements**

- **Now (since April 28, 2026)**: uploads must be built with **Xcode 26 or later** using the **iOS 26 / iPadOS 26 / tvOS 26 / visionOS 26 / watchOS 26** SDK. **Since September 9, 2026**, iOS and iPadOS uploads must also **target iOS 13 or later**.
- **Next (April 2027)**: iOS/iPadOS apps must be built with the **iOS & iPadOS 27 SDK** and **target iOS 15 or later**; tvOS, visionOS, and watchOS apps need their **27 SDKs**. Starting April 2027, apps built with the **iOS 27.1 SDK or later** must also include **iPhone Duo** screenshots.
- **macOS**: macOS 27 runs only on Apple silicon, and Rosetta is being retired (only older, unmaintained games keep it) — ship **universal or arm64** Mac builds.
- Builds using older SDKs are rejected at upload time.
- Reference: [Submitting apps](https://developer.apple.com/app-store/submitting/) · [Upcoming requirements](https://developer.apple.com/news/upcoming-requirements/)

**Code signing & entitlements**

- Entitlements in the binary must match provisioning profile and App ID capabilities exactly.
- Common errors: `ITMS-90161` (invalid provisioning profile), `ITMS-9000` (invalid binary), entitlements mismatch.
- Fix: Regenerate profiles after adding/removing capabilities. Debug: `codesign -d --entitlements :- YourApp.app`.

**Launch screen storyboard (required since April 2020)**

- All iOS apps must include a LaunchScreen storyboard — apps without one are rejected.
- Xcode 16 bug: manually type the filename; verify `UILaunchStoryboardName` in Info.plist.

**Screenshot dimensions & content**

- Exact pixel dimensions required (1px off fails upload). Apple's [spec page](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications) now names sizes by display class: it lists **at least one iPhone screenshot for "iPhone with Dynamic Island (medium display)"** (1206x2622 or 1179x2556) and **13" iPad (2064x2752)** if the app runs on iPad, while its table still treats the large-display set (**1320x2868** 6.9", 1290x2796, 1260x2736 iPhone Air; 6.5" 1284x2778 fallback) as the source for scaling. The page is internally inconsistent — upload **both** a 6.9" and a 6.3" set to be safe.
- **iPhone Duo** (available October 23, 2026): 1398x2034 / 2034x1398 (outer display) and 2007x2853 / 2853x2007 (inner display). Optional today (earns an "optimized for iPhone Duo" badge); **required from April 2027** for apps built with the iOS 27.1 SDK or later.
- **More than half** of the screenshots for each device must show the **app in use** — App Review approves only those sets ([2.3.3 guidance](https://developer.apple.com/help/app-review/guideline-reference/2-3-3-screenshots)). Marketing frames are fine in the minority.
- Screenshots must reflect current build UI — outdated screenshots trigger §2.3.

**App size limits**

- iOS: 4 GB max; executable limit **80 MB** total `__TEXT` per Apple's current table. tvOS/visionOS: 4 GB, `__TEXT` 500 MB. watchOS: 75 MB hard limit.
- Cellular download: 200 MB default prompt threshold — users are asked before downloading over cellular (and can change the setting).
- Use Background Assets / On Demand Resources for large content.

**App Transport Security (ATS)**

- Global `NSAllowsArbitraryLoads = YES` requires documented justification and may be rejected.
- Use domain-specific `NSExceptionDomains` instead. iOS 17+ has stricter IP-based restrictions.

**Privacy manifests — two separate obligations (both can block upload)**

1. **Third-party SDKs on Apple's list** — When you add or update an app that includes a listed binary SDK, that SDK must ship a valid `PrivacyInfo.xcprivacy` and (for binary deps) a valid signature. Common ITMS error: `ITMS-91061: Missing privacy manifest`.
   - List (updates over time): [developer.apple.com/support/third-party-SDK-requirements](https://developer.apple.com/support/third-party-SDK-requirements/)
   - Fix: Upgrade the SDK; do **not** copy the vendor's data-collection story into your app's manifest as a substitute for their missing file (Apple expects the SDK bundle to carry its own manifest).

2. **Required reason APIs in _your_ code** — If _your_ app or any embedded executable uses Apple's "required reason" API categories (file timestamp, disk space, UserDefaults, system boot time, active keyboard, etc.), **your** app (or that executable's bundle) must declare approved reasons in `PrivacyInfo.xcprivacy` under `NSPrivacyAccessedAPITypes`. Starting **May 1, 2024**, apps without these declarations are not accepted. **Fingerprinting is never allowed**, regardless of user consent.
   - Reference: [Describing use of required reason API](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_use_of_required_reason_api)
   - Xcode: **File → Project → Generate Privacy Report** aggregates SDK + app manifests.

**Broken required URLs**

- Support URL and Privacy Policy URL must return HTTP 200 at time of submission.
- Apple calls these out explicitly as common "unresolved issues."
- Fix: Run a URL health check script before every submission.

**Export compliance & encryption (upload + ADPLA)**

- If the app uses **encryption** (TLS, crypto libraries, proprietary crypto, etc.), set **`ITSAppUsesNonExemptEncryption`** in `Info.plist` accurately and complete the **encryption / export** questionnaire in App Store Connect. Without this, upload or distribution can stall until answered.
- **Scope is broad**: many apps answer “yes” because they use HTTPS or standard OS crypto; follow Apple’s wizard and docs for **exemptions** (e.g. only exempt categories).
- **Stronger cases**: custom crypto, VPN, secure messaging, or regulated markets may require **export classification** work (e.g. **EAR** / **BIS** self-classification or **CCATS** where applicable) and accurate **Schedule 1**–style certifications when you deliver binaries to Apple. Treat this as **legal + security**, not only a plist checkbox.
- **macOS notarization upload (§5.3)**: ADPLA prohibits uploading apps subject to certain **export-controlled** categories without authorization; align notarization payloads with your export story.
- Reference: [Complying with encryption export regulations](https://developer.apple.com/documentation/security/complying_with_encryption_export_regulations) · [App Store Connect — export compliance](https://developer.apple.com/help/app-store-connect/reference/app-information/export-compliance-documentation-for-encryption)

**Distribution scope & compliance (outside guideline text but blocks release)**

- **App Store** — Full [App Review Guidelines](https://developer.apple.com/app-store/review/guidelines/). Prep: [App Review](https://developer.apple.com/app-store/review/).
- **EU alternative marketplaces / Web Distribution / Japan & Brazil marketplaces** — Subset of guidelines + Notarization; use **Highlight Notarization Review Guidelines Only** in the guidelines UI. Still scanned for malware/safety, and violations of §2.3.1, §5, §5.4, and §5.5 can **block** an app from alternative distribution, not only remove it from the App Store. Since October 1, 2026, EU marketplace / Web Distribution eligibility no longer requires an EU legal entity (see ADDENDUM). [Notarization (ASC)](https://developer.apple.com/help/app-store-connect/managing-alternative-distribution/submit-for-notarization), [Apps in the EU](https://developer.apple.com/support/apps-in-the-eu/), [Web Distribution EU](https://developer.apple.com/support/web-distribution-eu/).
- **Mac App Store** — Extra rules in §2.4.5 (sandbox, no third-party installers, MAS-only updates). See [REFERENCE.md — Mac App Store](REFERENCE.md#mac-app-store--additional-rules-245).
- **EU DSA trader** — Traders distributing in the EU must complete verified contact display in App Store Connect (can block submission flow). [Manage EU DSA trader requirements](https://developer.apple.com/help/app-store-connect/manage-compliance-information/manage-european-union-digital-services-act-trader-requirements/).

---

## Gate B — App Completeness (§2.1)

The single largest rejection driver. Apple requires submissions be **"final versions."**

**Mandatory checklist:**

- [ ] App tested on physical device (not only Simulator) via TestFlight build
- [ ] Zero crashes on cold start, onboarding, login, core flows, and checkout
- [ ] No placeholder text ("Lorem ipsum"), empty screens, or stub features
- [ ] All in-app purchase products submitted with the binary and visible to reviewer
- [ ] Demo account provided (or Apple-approved demo mode): username, password, no 2FA blocking access, region matching reviewer environment
- [ ] Backend services live and accessible from outside your VPN/network
- [ ] Support URL and Privacy Policy URL are functional
- [ ] Notes for Review explain every non-obvious feature, IAP purchase path, and any hardware requirement
- [ ] Notes also cover what Apple's [complete-review checklist](https://developer.apple.com/help/app-review/before-submitting-for-review/complete-review) asks for: concept and business model (new apps) or what changed and where (updates), audience, **devices and OS versions tested**, **external services** the core features rely on (auth, payments, data, **AI providers**), and **regional differences**
- [ ] **Regulatory documentation attached** for regulated features (new "Before You Submit" item, October 2026): medical clearance, crypto exchange / lending / gambling licenses, financial-partner agreements, IP permission for third-party marks or content
- [ ] Auto-renewable subscriptions: the demo account has an **expired** subscription so the full purchase flow can be reviewed
- [ ] Required hardware: a **video of the app running on a physical device** while pairing with the hardware (not a screen recording); one-time auth codes supplied in advance (otherwise a call is needed, typically 3–5 business days)

**IAP-specific "catch-22" loop (very common):**  
Products showing blank/missing pricing during review → rejected under 2.1(b). Fix: Submit IAPs together with the binary in App Store Connect. Ensure products are approved (not just created) before submitting the build.

**Receipt validation:**  
Prefer **StoreKit 2** on-device verification plus the **App Store Server API** — the legacy `/verifyReceipt` endpoint is **deprecated**. If a legacy backend still calls it: production first → if status 21007, fallback to sandbox (Apple's own rejection message text confirms this sequence).

**Reference:** [developer.apple.com/app-store/review/guidelines/#app-completeness](https://developer.apple.com/app-store/review/guidelines/#app-completeness)  
**Community case:** RevenueCat ultimate guide — [revenuecat.com/blog/growth/the-ultimate-guide-to-app-store-rejections](https://www.revenuecat.com/blog/growth/the-ultimate-guide-to-app-store-rejections/)

---

## Gate C — Privacy & Permissions (§5.1)

Second-largest rejection bucket. Three artifacts must **agree**: privacy policy content, App Privacy nutrition label, and runtime behavior.

**Purpose strings — literal wording matters:**  
Bad: `"We need your location."`  
Good: `"Your location is used to show nearby coffee shops on the map."`  
Rejection message pattern: _"Your app requests access to [resource] but does not clearly explain why."_  
Apple now links §5.1.1(ii) to dedicated guidance: [writing clear purpose strings](https://developer.apple.com/help/app-review/guideline-reference/5-1-1-purpose-strings) · [account deletion](https://developer.apple.com/help/app-review/guideline-reference/5-1-1-account-deletion).

**Account deletion — required since June 2022:**

- Must be in-app initiated (not email-only or support-ticket-only)
- Must delete account AND associated data (unless legally required to retain)
- Must be easy to find (Settings → [Account] → Delete Account)
- Reference: [developer.apple.com/support/offering-account-deletion-in-your-app](https://developer.apple.com/support/offering-account-deletion-in-your-app/)

**Mandatory login gating rule:**

- If login is required, document in Notes for Review _why_ the feature requires identity (cross-device state, progression, personalization)
- Allow guest/browse mode for features that don't need an account
- Rejection pattern: _"Your app requires users to log in before they can access features that are not account-based."_

**Third-party AI disclosure — explicit Apple requirement (§5.1.2(i)):**

> _"You must clearly disclose where personal data will be shared with third parties, including with third-party AI, and obtain explicit permission before doing so."_

**Location services (§5.1.5 + ADPLA §3.3.3(F))**

- Use **Core Location / MapKit / location-based** features only when **directly relevant** to what the app does.
- **Do not** market or design for **autonomous vehicle control**, **emergency / life-saving** reliance, or other disallowed positioning (see guideline text and ADPLA for navigation/weather disclaimer requirements in **Gate J**).
- **Notify and obtain consent** before collecting, transmitting, or using location; **purpose strings** and in-app explanations must match real use (**§5.1**).
- **Do not** disable, spoof, or wrap Apple’s **system consent UI** for location (or contacts, photos, calendar, etc.) — accurate copy in permission dialogs only (**ADPLA §3.3.3(F)(iv)**).
- **“Always” location**: provide a clear **user benefit** when requesting continuous access.
- **Custom map overlays**: you are responsible for alignment and correctness when combining your data with **Apple Maps**.
- Reference: [§5.1 Legal — Location Services](https://developer.apple.com/app-store/review/guidelines/#legal) · [HIG — accessing private data](https://developer.apple.com/design/human-interface-guidelines/patterns/accessing-private-data/)

**Advertising, tracking & identifiers**

- **ATT** (`AppTrackingTransparency`): required for **cross-app / cross-site tracking** per Apple’s rules; cannot **gate core features** on tracking consent.
- **ATT in the EU (iOS/iPadOS 27.2+)**: an **alternative system prompt** with an optional **"Additional Information"** button (for layered GDPR/ePrivacy consent) is available in the EU and is the **only** prompt in **Germany, France, Italy, Poland, and Romania**. EU users may be re-prompted **one year** after their last choice unless they turned off the system-wide tracking-request setting. When tracking requires permission is unchanged; no IDFA or tracking data before the user allows it ([details](https://developer.apple.com/app-store/user-privacy-and-data-use/)).
- **IDFA / AdSupport (ADPLA §3.3.3(E))**: use **only** to **serve advertising**; check **Tracking Preference** before using IDFA for ads; after user **resets** the advertising identifier, do **not** link old and new IDs or derived data.
- **Ad Network APIs** (if granted): use **only** for **ad validation / conversion** per Apple’s terms — no combining validation payloads with other user profiles.
- **Kids Category**: **no** third-party analytics or third-party advertising in apps **primarily for kids** (**§1.3**); stricter rules for minors’ data.
- **APNs / local notifications (Attachment 1)**: no **spam** or **phishing**; **promotional** push only with **explicit in-app opt-in** language and in-app **opt-out**, except Pass-related promotions tied to the Pass. Do not use notifications to **evade** privacy or tracking rules. **§4.5.3 (June 8, 2026)** now explicitly extends the anti-spam/phishing/unsolicited-message rule to **Live Activities**.
- **Network Extension / “Access Wi‑Fi Info” (ADPLA §3.3.3(G))**: entitlements are for **networking functionality**, **not** ads, profiling, or bypassing user privacy settings (e.g. inferring location when location is off).
- Reference: [§5.1 Privacy](https://developer.apple.com/app-store/review/guidelines/#privacy) · [ATT](https://developer.apple.com/documentation/apptrackingtransparency)

**Reference:** [developer.apple.com/app-store/review/guidelines/#privacy](https://developer.apple.com/app-store/review/guidelines/#privacy)

---

## Gate D — Payments & Monetization (§3.1)

**Core IAP rule:** Unlocking features, subscriptions, in-game currency, premium content → **must use StoreKit IAP.** No QR codes, license keys, AR markers, or direct Stripe links (outside allowed exceptions).

**External purchase links — storefront-dependent (frequently misunderstood):**

| Storefront                               | External CTA allowed?                                                                                                                                                                                                                                                                                              | Entitlement needed?                                                         |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| United States                            | Yes                                                                                                                                                                                                                                                                                                                | No (as of current guidelines)                                               |
| EU (all Apple platforms, EU storefronts) | Since **October 1, 2026** (ADPLA **Attachment 14**): in-app **alternative payment processing** and/or **out-of-app offers** to a destination of your choice (website, alternative marketplace, or another app), **alongside** IAP; keep your chosen payment options for **12 months**; child-safety parental gates | Yes (EU alternative-payments entitlement after accepting the updated ADPLA) |
| Japan (Japan App Store storefront)       | **Alternative Payment Processing** and/or **Out-of-App Offers** only with Apple-granted entitlement + **StoreKit** eligibility/disclosure flows; see ADPLA **Attachment 12** and Apple Materials                                                                                                                   | Yes                                                                         |
| Brazil (Brazil storefront)               | **Alternative Payments** and/or **Out-of-App Offers** with Apple-granted entitlement (iOS 26.5+, since June 18, 2026, CADE agreement); see ADPLA **Attachment 12** ("Japan, Brazil") and Apple Materials                                                                                                           | Yes                                                                         |
| Netherlands dating apps                  | With specific entitlement                                                                                                                                                                                                                                                                                          | Yes                                                                         |
| South Korea                              | With StoreKit External Purchase Entitlement                                                                                                                                                                                                                                                                        | Yes                                                                         |
| All others                               | No                                                                                                                                                                                                                                                                                                                 | N/A                                                                         |

**§3.1.1(a) wording (October 16, 2026):** link-out entitlements now point to a **"permitted destination"** (approved destinations vary by region) instead of "a website the developer owns"; the **Music Streaming Services Entitlement** is folded into the StoreKit External Purchase Link bullet "where permitted"; entitlements are limited to specific storefronts **and to certain platforms in each storefront** — check platform coverage before shipping macOS/visionOS/tvOS builds. Misleading marketing, scams, or fraud tied to an entitlement → app removal and possible Program removal.

**EU (Attachment 14) — what changed on October 1, 2026:** the Core Technology Fee, Initial Acquisition Fee, and the old Store Services Fee tiers are gone. App Store commission: **26%** IAP / **20%** in-app alternative payments (15% / 10% for Small Business, Mini Apps Partner, Video Partner programs and subscriptions after year one); **15%** (10%) store services commission on out-of-app offers with an actionable link, for sales within **7 days** of the tap; **5%** Core Technology Commission on alternatively distributed apps. **Kids category**: alternative payment flows behind a **parental gate**, no out-of-app web offers; **under 13** (or the storefront's higher consent age): parental gate, no out-of-app offers; **13–17**: both behind a parental gate. Alternative-payment transactions must be reported **monthly** (within 15 days of month end). Details: [ADDENDUM.md](ADDENDUM.md#eu-unified-business-terms--adpla-attachment-14-effective-october-1-2026) · [Apple: apps in the EU](https://developer.apple.com/support/apps-in-the-eu/).

**Japan (Attachment 12) — design rules that differ from “US-style” external links:** IAP must be offered **at least as prominently** as any alternative payment option on the same screen; **Out-of-App** purchases use an **actionable link** that opens the **default web browser** (not an in-app `WKWebView`); no website-purchase promo on the **App Store product page**; **Kids** category and age rules block certain offers; entitlement/device OS requirements are defined in Apple Materials (ADPLA references **iOS 26.2+**, Japan devices). Full checklist: [ADDENDUM.md](ADDENDUM.md#japan-app-store--adpla-attachment-12-commerce--distribution).

**Subscription paywall — minimum required disclosures:**

- Subscription price and billing period must be **prominent and legible**
- The **amount actually billed** must be the most prominent pricing element in the layout
- Auto-renewal must be disclosed
- "Cancel anytime" text cannot be the only subscription info shown
- "Restore Purchases" button required
- Trial period terms must be shown before purchase
- Sign-up screen should include subscription name/duration and the service/content provided

**Rejection pattern:** App Review uses §3.1.2 as a catch-all for paywall design problems. If price/period aren't prominent enough → increase contrast and font size.

**Subscription changes for iOS 27 (fall 2026):**

- **Multiseat purchases are enabled by default** for subscriptions in App Store Connect — **Volume Purchasing** (Apple Business / Apple School Manager, from October 22, 2026) and **Group Purchases** (one subscriber buys seats and invites others; announced for winter 2026–27). Make sure server-side entitlements recognize seat holders, or disable multiseat per subscription.
- **Bundles and Suites** (by request): Bundles combine up to **5** subscriptions — yours or up to 5 developers' — in one IAP; Suites cover up to **15** of your apps. Requires **StoreKit 2** and iOS/iPadOS/macOS/tvOS 27+; subscriptions in a Bundle must share the same duration. [Apple guide](https://developer.apple.com/app-store/subscriptions/bundles-and-suites/).

**B2B SaaS edge case:** Even if your app serves enterprise customers who purchased on the web, in-app digital access (features, content, subscriptions) still requires IAP unless the app qualifies as "multiplatform service" or "enterprise service" under §3.1.3.

**Reference:** [developer.apple.com/app-store/review/guidelines/#payments](https://developer.apple.com/app-store/review/guidelines/#payments)

---

## Gate E — Design & Metadata (§4.2, §2.3, §4.1)

**Minimum Functionality (§4.2):** App must "elevate beyond a repackaged website."  
Red flags: WebView as primary experience, app equivalent to browsing a URL, no offline/native value, marketing-only content.  
Fix: Add meaningful native features — device sensors, push notifications, offline mode, native navigation, deep linking.

**Hidden features (§2.5.19, formerly §2.3.1(a) — October 16, 2026):** Any feature reachable via deep link, debug gesture, or feature flag that reviewers can't discover through normal use = hidden feature. The rule now lives in **§2.5.19** (Software Requirements) and hiding features in the binary carries **app removal + developer account termination** — see **Gate L** and **Gate F**.  
**§2.3.1(a) still requires** every new feature, functionality, and product change to be described **with specificity** in Notes for Review (generic descriptions are rejected), and bans misleading marketing (fake capabilities such as "virus scanners", false prices) inside or outside the App Store.  
Fix: Document every non-obvious feature path in Notes for Review with specificity.

**Screenshots & creative assets (§2.3.3, new §2.3.14):**

- More than half of each device's screenshots must show the **app in use**; the rest may be marketing frames ([guidance](https://developer.apple.com/help/app-review/guideline-reference/2-3-3-screenshots)).
- **§2.3.14 creative assets** (product page header, search results, In-App Events; shown on iOS/iPadOS 27+) may show brand or seasonal art instead of app-in-use, but must relate to the app's content, functionality, or overarching brand; follow standard metadata rules; be **4+ appropriate**; and must **not** include references to other apps meant to mislead, unverifiable claims, or **specific or discounted prices**. Apple's [asset best practices](https://developer.apple.com/app-store/asset-best-practices/) also rule out website URLs, copyright symbols, other platforms' or marketplaces' logos, and Apple recognitions (Editor's Choice, App of the Day, Apple Design Award) — Apple shows those itself. Specs: [creative assets](https://developer.apple.com/help/app-store-connect/reference/app-information/creative-assets-specifications).

**Spam (§4.3) — significantly tightened June 8, 2026:**

- **§4.3(a) — duplicates of your own app:** Multiple Bundle IDs for near-identical apps (different regions/teams/languages) = removal risk. Fix: one app with in-app variations or in-app purchase. (June 2026 revision clarifies the basis and adds an example.)
- **§4.3(b) — saturated categories:** New language: _"Don't submit apps that are indistinguishable from what's already widely available."_ **Dating, flashlight, sound effects, wallpaper, simple timer, and fortune telling** apps are accepted **only** if they offer a _"meaningfully different or improved experience."_ Apple **may also remove existing apps** in these categories that are _"not updated, improved, or do not attract customers"_ — keep shipped apps maintained.
- **§4.3(b) — low-effort categories:** **Drinking games, Kama Sutra, fart, and burp** apps are deemed _"mediocre, low-quality, or low-effort"_ and _"do not add value to the App Store."_ **Repeated submissions of this kind may lead to removal from the Apple Developer Program.**
- **Practical impact:** for any app near these categories, prepare a written **differentiation case** (unique features, target audience, quality bar) in Notes for Review; expect higher scrutiny and possible clutter-cleanup removals of stale apps.

**Copycats & impersonation (§4.1):**

- **(a)** Don't copy a popular app or make minor changes to another app's **name or UI** and pass it off as your own → rejection (and IP-claim risk).
- **(b)** Apps that **impersonate** other apps or services are a **Developer Code of Conduct** violation → possible removal from the Apple Developer Program.
- **(c)** You **cannot** use another developer's **icon, brand, or product name** in your app's icon or name without that developer's approval.
- Apple's [4.1 guidance](https://developer.apple.com/help/app-review/guideline-reference/4-1-prevent-copycat-rejection): App Review "closes accounts that repeatedly submit copycat apps or attempt to impersonate a service"; don't copy another app's look and feel; no popular app names or trademarked terms in **any** metadata field (also §2.3.7); keep developer name and Support URL accurate; **fraudulent documentation** (e.g. fake permission letters) → Program removal. If you do have rights to a third-party mark, attach the contract or written permission in App Review Information.

**Intellectual property, Apple marks & third-party brands (§5.2, ADPLA §3.1(d), §3.3.4)**

- You **certify** under the ADPLA that the app, **App Store metadata** (screenshots, description, previews, icons), **Xcode Cloud** content you provide, and **Wallet Pass** materials do **not** infringe **Apple’s or anyone else’s** rights — including **copyright, trademark, trade secret, patent, right of publicity, music / performance / sync licenses, video and stock-photo rights, fonts, and third-party data / API terms**.
- **App content**: only assets **you own** or **properly license** (including user-generated or partner content you display).
- **Third-party logos / names** in UI, notifications, passes, or marketing: use only with **permission** and in ways that **don’t imply endorsement** or confuse origin (**§5.2**, **§5.2.4**).
- **Apple marks & product imagery** (App Store logo, iPhone silhouettes, “Compatible with…,” **Apple Pay** marks, **Wallet** badges, screenshots of Apple UI): follow Apple’s **written** marketing and identity rules — not informal reuse. Primary entry points:
  - [Marketing Resources and Identity Guidelines](https://developer.apple.com/app-store/marketing/guidelines/)
  - [Guidelines for Using Apple Trademarks and Copyrights](https://www.apple.com/legal/intellectual-property/guidelinesfor3rdparties.html) (also cited in ADPLA **§2.6** for referencing Apple products)
  - [Apple Pay Marketing Guidelines](https://developer.apple.com/apple-pay/marketing/) · [Add to Apple Wallet Guidelines](https://developer.apple.com/wallet/add-to-apple-wallet-guidelines/)
- **§5.2.1–5.2.3**: no **protected third-party material** without permission; no **misleading or copycat** representations; honor **third-party service terms** if you access or monetize their content; do not facilitate **illegal file sharing** or unauthorized **download/save** of media from third-party sources (e.g. streaming services) unless expressly permitted.
- **§5.2.4 Apple endorsements**: do **not** suggest or imply Apple **supplies**, **endorses**, or **guarantees** your app’s quality. **“Editor’s Choice”** (and similar) badges are **applied only by Apple**.
- **§5.2.5 Apple products**: do not make an app **confusingly similar** to Apple’s **products, interfaces, App Store / iTunes / Messages**, or **Apple ads** — includes look-and-feel that mimics system or store UI.
- **Disputes**: third-party IP claims against your app are handled via Apple’s [IP dispute process](https://www.apple.com/legal/intellectual-property/dispute-forms/index.html) when applicable.
- **Safari / Website Push IDs (ADPLA Attachment 1 §3)**: notifications must be sent under **your** site’s **name and brand**; include **your** icon / logo / identifying mark; **do not impersonate** another site or mislead about the sender. If you reference a **third-party** trademark in a push, you **warrant** you have the **rights**. Enabling Website Push permits Apple to use **screenshots** of those notifications and **your** associated marks in **Apple marketing**, except portions you **identify in writing** as not cleared for promotion.
- **In-app Apple services** (e.g. **MusicKit**, **ShazamKit**): follow **Apple Music Identity Guidelines** and program docs — artwork and metadata often **cannot** be split from permitted playback/UI patterns.
- Reference: [§5.2 Intellectual Property](https://developer.apple.com/app-store/review/guidelines/#legal) (scroll **Legal** section)

**App Clips / Widgets / Extensions (often overlooked):**

- App Clips cannot include advertising.
- Widgets, extensions, and notifications must be directly related to app content/functionality.
- Siri intents must match what the app actually does; avoid registering irrelevant intents.

**Reference:** [developer.apple.com/app-store/review/guidelines/#minimum-functionality](https://developer.apple.com/app-store/review/guidelines/#minimum-functionality) · [Legal (IP, location)](https://developer.apple.com/app-store/review/guidelines/#legal)

---

## Gate F — Code Execution & Supply Chain (§2.5)

**No code download that changes functionality (§2.5.2):**

- Hot-update frameworks (JSPatch, Rollout, code-push patterns) that modify production JS bundles → rejected
- Rejection message: _"Your app downloads, installs, or executes code that introduces or changes features or functionality."_
- Allowed: Content/config updates that don't add new executable behavior
- Allowed narrow exception: Educational apps where code is user-viewable and user-editable
- Community fix for React Native: Remove or lock hot-update path; ensure release builds don't include dev server URLs

**Hidden, dormant, or undocumented features (§2.5.19 — new October 16, 2026):**

> _"Don't include any hidden, dormant, or undocumented features in your app; your app's functionality should be clear to end users and App Review. Attempting to hide undocumented features in your app binary, such as through code obfuscation or other methods, is grounds for removal of your app from the App Store and termination of your developer account."_

- **Dormant code** = shipped code paths that App Review can't see or reach: server-side kill switches that unlock unreviewed capabilities, remote-config "modes" that swap the app's purpose (e.g. a utility that later loads a casino or adult web experience), time-bombs, or geo/IP/locale checks that show reviewers a different app. Apple's [removal page](https://developer.apple.com/help/app-review/safe-and-trusted-store/app-removals) names "modified after approval, such as through a server-side change" as a removal trigger.
- **Feature flags are still fine** when every flagged capability is in the reviewed binary, reachable by the reviewer (or enabled for review), and described in Notes for Review. Rolling a reviewed feature out gradually is fine; switching on something App Review never saw is the violation.
- **Obfuscation**: the rule targets obfuscation used to **hide features**, not IP protection as such. If you obfuscate (iXGuard, SwiftShield, custom string encryption), everything the obfuscated code can do must still be documented — and consider saying in Notes for Review that obfuscation is for IP/anti-tamper only.
- Never branch **user-facing** behavior on "is this App Review?" signals (sandbox receipt, Apple network ranges, reviewer locale, install-date checks). Using the receipt environment to pick a validation endpoint is fine; changing what the user can do is not.
- **Third-party SDKs count** (ADPLA §3.2, October 13, 2026): code you embed may not collect data beyond what users consented to, or use the user's **device or network resources for third parties** without explicit prior consent — audit "bandwidth-sharing", proxy, background-compute, and aggressive analytics SDKs.

**Private APIs (§2.5.1):**

- `prefs:root://` and similar Settings deep links are private / unsupported → rejection risk. Prefer opening the app's settings page via `UIApplication.openSettingsURLString` (or SwiftUI `openSettingsURLString`) so the user lands in **your** app's settings, not arbitrary system panes.
- Private selectors / SPI in your code or in a dependency → automated rejection. Audit with `strings` / static analysis; update or replace SDKs that embed private symbols (IOKit private I/O, undocumented SpringBoard APIs, etc.).
- StackOverflow pattern: private IOKit or entitlement misuse flagged; fix by updating/removing the offending SDK or code path.

**Reference:** [developer.apple.com/app-store/review/guidelines/#software-requirements](https://developer.apple.com/app-store/review/guidelines/#software-requirements)

---

## Gate G — AI Features, UGC, and Age Gates (§1.2, §4.7, §5.1.2)

**§1.2 scope (rewritten October 16, 2026):**

> _"Apps that enable users to create, generate, upload, share, or otherwise contribute content present particular challenges, including intellectual property infringement, nonconsensual intimate imagery, and anonymous bullying. This includes social media, user-generated content platforms, and AI content creation apps."_

- **AI content creation apps are now named explicitly** — image, video, avatar, voice, and chat generators need the same four safeguards as social apps: filtering, reporting with timely response, blocking, and published contact information.
- **Nonconsensual intimate imagery (NCII)**, including imagery generated with AI tools, is content apps must **proactively prevent and remove** (Apple's [October 16 announcement](https://developer.apple.com/news/?id=vvzx6cul)). "Nudification" apps that sexualize real people without consent were already banned as pornographic content (§1.1.4); hiding that capability until after approval is the bait-and-switch pattern now targeted by **§2.5.19** (**Gate L**).
- New sentence: _"Developers who fail to implement appropriate safeguards or fail to remediate identified violations after notice may be considered in violation of the Developer Code of Conduct."_ Weak UGC/AI safeguards are now an **account-level** risk (§5.6 → termination), not only an app rejection.

**§1.2 enforcement — content removal + compliance plan (June 8, 2026, retained):**

> _"It is your responsibility to remove content that violates this guideline, your terms of service, or your community standards. [...] If we find such content, we will ask you to remove it, and provide a plan to improve your compliance with this guideline. Based on your response, your app may be removed from the App Store until you can demonstrate improvements that bring your app into compliance. Egregious or repeated behavior is grounds for immediate removal of your app from the App Store, and from the Apple Developer Program."_

- Moderation **tooling** (filter/report/block/contact) is not sufficient on its own — you must **actually remove** violating content (e.g. pornographic content, NCII) and be ready to produce a **written compliance plan** if Apple flags your app.
- Enforcement is real: Apple threatened to remove **Grok** over sexualized deepfakes under §1.2 (threat issued January 2026, disclosed April 2026 via a letter to US senators; the app was never removed — xAI remediated). UGC/AI apps should maintain a documented moderation playbook **before** submission and keep takedown-response evidence for Resolution Center.

**If your app has AI-generated content visible to users:**

- Treat it as UGC: must have report/block/filter mechanisms (§1.2)
- Must have published contact information
- Block generation of sexual or intimate imagery of **real, identifiable people** (prompt and output filtering, face/likeness checks on uploads) and keep an NCII takedown path that works fast
- If AI can produce NSFW content, hide by default and only enable via website-side control

**If AI inference is server-side:**

- User data sent off-device → must appear in App Privacy answers and privacy policy
- If a third-party AI provider receives personal data → explicit disclosure + consent required (§5.1.2(i))

**"Coding assistant" apps / LLM code generation — §2.5.2 interpretation:**

- Generating code that the user views/edits (educational exception) = generally OK
- Downloading and executing new code that changes the app's own behavior = rejected
- Mini-apps, chatbots, plug-ins under §4.7 are allowed but require full §5.1 and §1.2 compliance

**Kids Category + AI:** Zero third-party analytics/advertising. AI features that could expose kids to inappropriate content are especially scrutinized.

**Creator apps / age-gated content:** If the app allows creation or discovery of content that can exceed the app's age rating, require a way to flag that content and restrict access using verified or declared age. Keep **App Store Connect age rating** answers current with the [2026 age rating system](https://developer.apple.com/news/upcoming-requirements/?id=07242025a) and [definitions](https://developer.apple.com/help/app-store-connect/reference/age-ratings-values-and-definitions/). **July 9, 2026:** the questionnaire adds **social media capability** questions (redistribute, amplify, or interact with UGC via a feed or similar discovery surface) that drive iOS 27 **Time Allowances**; responses are **mandatory from September 2026** for new apps, updates, and notarization ([announcement](https://developer.apple.com/news/?id=tlur8uvi)).

**Multi-platform targets (tvOS, watchOS, visionOS):** Privacy manifests and required-reason API rules apply per Apple docs across these platforms; validate each target's `Info.plist`, extensions, widgets, and embedded binaries separately.

---

## Gate K — Safety, speech, gambling, sandbox & system access

**Apple anchors:** [Safety (§1.x)](https://developer.apple.com/app-store/review/guidelines/#safety) · [Legal — §5.3 Gaming / gambling](https://developer.apple.com/app-store/review/guidelines/#legal) · [Software requirements / sandbox expectations](https://developer.apple.com/app-store/review/guidelines/#software-requirements)  
**Deeper dives:** [REFERENCE.md — Gaming & gambling](REFERENCE.md#gaming-gambling-and-lotteries-53) · [EDGE-CASES — Content policy](EDGE-CASES.md#content-policy-edge-cases)

### Violence, weapons & objectionable content (§1.1)

- Apple rejects **offensive, gratuitous, or harmful** content — including realistic **killing, torture, or abuse** of people or animals when presented to **shock** or **glorify** harm (see full **§1.1** text for examples and nuance).
- **§1.1.3 — Weapons:** Apps must not **encourage illegal or reckless** use of weapons/dangerous objects or **facilitate purchase** of **firearms/ammunition** where the guideline prohibits it. Hunting/sport contexts still need **appropriate age rating** and compliance with local law.
- **Games:** Fictional “enemies” must not **solely target** a **real** race, culture, government, corporation, or other real-world group (**§1.1.2**).
- **Terrorism / serious harm:** Content that **threatens, incites, or promotes violence** or **CSAM** is prohibited under guidelines and **ADPLA §3.2(b)** — immediate legal/safety escalation, not a routine review tweak.
- **§5 Legal intro (October 16, 2026):** apps found to facilitate **human trafficking and/or the exploitation of children** → authorities notified, the app is **removed from the App Store and blocked from alternative distribution** (EU, Japan, Brazil channels included). Marketplace, classifieds, dating, "companion", and anonymous-chat apps need abuse detection and law-enforcement escalation paths.

### “Open speech,” politics, satire & forums

- The App Store is a **private platform**: Apple enforces **§1.1** (e.g. hate, harassment, defamation) and local-law-sensitive material. **First Amendment / “free speech”** doctrines **do not** bind Apple’s acceptance of your app.
- **§1.1.1** explicitly notes that **professional political satirists and humorists** are **generally exempt** from a narrow part of the objectionable-content rule — **satire used as cover for harassment** or **defamation** still fails review.
- **UGC / social / debate apps:** You still need **§1.2** tooling (filter, report, block, contact, timely response) and **age-appropriate** controls; “open discussion” is not a bypass for **illegal** content or **missing moderation**.

### Gambling, contests & loot boxes (§5.3 + §3.1.1)

- **Real-money gaming** (sportsbook, casino, poker, etc.): **licensing** in every jurisdiction you ship; **geo-restrict** to licensed regions; app must be **free to download**; **no IAP** to purchase **gambling credits** (**§5.3**). Expect **extra review** time.
- **Sweepstakes / contests:** Must be **run by you**; **official rules** in-app must state **Apple is not a sponsor** (**§5.3.1–5.3.2**).
- **Loot boxes / randomized digital items:** Disclose **odds** of item types **before purchase** (**§3.1.1**).
- **Gray areas** (skill contests, fantasy, prediction markets): **legal review** per territory — see [EDGE-CASES — Gambling gray zone](EDGE-CASES.md#gambling-gray-zone).

### Sandboxing, containers & “system access”

- **iOS / iPadOS / tvOS / watchOS / visionOS:** Apps must stay within Apple’s **documented** model: your **sandbox / designated container**, **extensions** only in **documented** surfaces, **no private APIs** to poke other apps’ data or system internals (**§2.5.1**, **ADPLA §3.3.1**). Use **public** APIs for sharing (share sheet, `UIDocumentPicker`, App Groups where appropriate, etc.).
- **Mac App Store:** **Sandbox required** per **§2.4.5** — no third-party installers, no escaping the store for updates, no **root/setuid** — see [REFERENCE.md — Mac App Store](REFERENCE.md#mac-app-store--additional-rules-245).
- **Privileged capabilities** (examples): **VPN** (**§5.4** — org account, `NEVPNManager`, strict no-resale-of-user-data), **MDM** (**§5.5** — enterprise/gov/edu patterns, Apple approval), **Network Extension** (declared networking purpose only; see **Gate C**). Misusing these for **surveillance**, **ads**, or **undeclared** data collection → rejection and program risk.
- **Settings / system UI:** Do not deep-link into arbitrary **Settings** panes with **private URL schemes** (`prefs:root`, etc.); open **your app’s** settings via **`openSettingsURLString`** (**Gate F**).
- **macOS “power user” APIs** (Accessibility, Automation, screen capture, input monitoring): Use **only** for **stated, legitimate** features; reviewers may flag **Automation** or **Accessibility** used to **control other apps** for non–a11y purposes. Document the **user-visible** purpose in **Notes for Review** and match **entitlements** to reality.
- **Full Disk Access (October 2, 2026 notice):** Apple says some apps use Full Disk Access to read mail, messages, and browsing history without users understanding the risk, and will add controls so FDA can only be granted with **very explicit user action** — AI agents and communication apps are named. Don't build features that depend on FDA unless you're a backup-style tool; prefer scoped access (file pickers, security-scoped bookmarks) and expect onboarding friction to grow ([announcement](https://developer.apple.com/news/?id=p6zjojqw)).

**Reference:** [App Review Guidelines — Safety](https://developer.apple.com/app-store/review/guidelines/#safety)

---

## Gate H — Trust, Dark Patterns, and Anti-Scam Enforcement (§3.1.2, §5.6)

Apple increasingly enforces **customer trust**, not just technical compliance.

**High-risk patterns:**

- Misleading trial emphasis where the billed amount is visually subordinate
- Subscription flows that obscure renewal price, duration, or cancellation
- Forcing reviews, downloads, contact uploads, tracking permission, or other store-related actions to unlock access
- Charging for built-in OS capabilities like push notifications, camera access, or iCloud storage
- Manipulative wording, false scarcity, bait-and-switch pricing, or features promised but not delivered
- Subscriptions sold "under false pretenses" or via bait-and-switch (§3.1.2(a)) → removal and possible Program removal; the account-level version of these risks is **Gate L**

**Reference:** [developer.apple.com/app-store/review/guidelines/#payments](https://developer.apple.com/app-store/review/guidelines/#payments)  
**Reference:** [developer.apple.com/app-store/review/guidelines/#legal](https://developer.apple.com/app-store/review/guidelines/#legal)

---

## Gate L — Review Integrity & Account-Termination Triggers (Intro, §2.5.19, §3, §4.1, §5.6)

Most gates cost you one submission. These cost you the **developer account** — and with it every app on it. The guidelines' Introduction: _"If you attempt to cheat the system (for example, by trying to trick the review process, steal user data, copy another developer's work, manipulate ratings or App Store discovery) your apps will be removed from the store and you will be expelled from the Apple Developer Program."_ Apple says it **terminated over 193,000 developer accounts for fraud and abuse** last year and kept **150,000+ apps with safety issues** off the store; it traces these to malicious actors, in some cases coordinated fraud operations, that hide harmful content until after approval ([October 16, 2026](https://developer.apple.com/news/?id=vvzx6cul)).

| Ban trigger                                                     | Where Apple says it                                                                                                                                                                                                                                                                                                                                                                                                                                                         | Status                                                                                                                                               |
| --------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Tricking App Review / hiding dormant code / bait-and-switch** | Intro; **§2.5.19** hidden, dormant, undocumented features incl. **code obfuscation** → removal + account termination; §2.3.1(a)–(b) specific Notes for Review, no misleading marketing; "After You Submit": manipulating review → slower reviews; ADPLA §3.2(3) **post-review modifications** that deceive about identity/purpose/behavior                                                                                                                                  | **Strengthened** — §2.5.19 (Oct 16, 2026), ADPLA §3.2(3) (Oct 13, 2026)                                                                              |
| **Buying fake reviews / manipulating charts or search**         | §3 Business intro: manipulating reviews or inflating rankings with _"paid, incentivized, filtered, or fake feedback"_, or using **third-party services** to do it → possible expulsion; **§5.6.3 Discovery Fraud** (charts, search, reviews, referrals); §5.6.1 Apple's review API only, custom review prompts disallowed; §3.2.2(x) no forced ratings; ADPLA §3.2 fraudulent reviews of your **own or anyone else's** app; ADPLA §7.4 no soliciting ratings via TestFlight | Long-standing — unchanged text                                                                                                                       |
| **Copying other developers' UI, icons, or brand names**         | **§4.1(a)** copycats; **§4.1(b)** impersonation = Code of Conduct violation → Program removal; **§4.1(c)** no other developer's icon, brand, or product name in your icon or name without approval; §5.2.1; §2.3.7; ADPLA §3.2 confusingly similar names, **name squatting**; App Review "closes accounts that repeatedly submit copycat apps"                                                                                                                              | Long-standing; new [4.1 guidance](https://developer.apple.com/help/app-review/guideline-reference/4-1-prevent-copycat-rejection) linked Oct 16, 2026 |
| **UGC/AI apps without NCII safeguards**                         | §1.2: failing to implement safeguards or to remediate after notice → Developer Code of Conduct violation                                                                                                                                                                                                                                                                                                                                                                    | **New** (Oct 16, 2026)                                                                                                                               |
| **Trafficking / child exploitation**                            | §5 intro: removal **and** block from alternative distribution; ADPLA §3.2                                                                                                                                                                                                                                                                                                                                                                                                   | **Strengthened** (Oct 16, 2026)                                                                                                                      |
| **Phishing, data theft, hidden data collection**                | §5.1.1(vi) surreptitiously discovering passwords → Program removal; ADPLA §3.2(4) impersonating Apple/third parties for credentials, §3.2(6) collecting beyond consent, §3.2(7) using device/network resources for third parties                                                                                                                                                                                                                                            | ADPLA items **new** (Oct 13, 2026)                                                                                                                   |
| **Other Program-removal clauses**                               | §4.3(b) repeated low-effort submissions; §4.5.3 Game Center ID mining; §4.4.2 malicious Safari extensions; §2.5.3 malware; §5.4/§5.5 VPN/MDM data misuse; §5.6 harassment, repeated manipulative or fraudulent conduct; §5.6.2 inaccurate developer identity                                                                                                                                                                                                                | Long-standing                                                                                                                                        |

**Pre-submission integrity audit:**

- [ ] Every capability in the binary is reachable by App Review or described in Notes for Review; no remote switch can enable anything the reviewed build didn't show (§2.5.19)
- [ ] No review-detection branches (sandbox receipt, Apple IP ranges, reviewer locale/time) that change user-facing behavior; no geo-cloaked or time-bombed features
- [ ] Obfuscation, if any, protects IP only; its presence and purpose are stated in Notes for Review
- [ ] No paid, incentivized, review-gated, or "filtered" rating campaigns (e.g. asking only happy users to rate, rewarding 5-star ratings); no ASO, install, or review-farm vendors; only `requestReview` for prompts
- [ ] App name, subtitle, icon, keywords, and screenshots contain no other developer's brand, icon, or product name; UI isn't a re-skin of a competitor; no names reserved just to block others
- [ ] Developer name, support URL, and any rights documents are genuine and current (fraudulent documentation → Program removal)
- [ ] Third-party SDKs audited for undisclosed data collection and device/network resale (ADPLA §3.2(6)–(7))
- [ ] For UGC/AI apps: NCII prevention, takedown SLAs, and a compliance-plan template ready (§1.2)

**If it happens:** a pending termination can be appealed to the App Review Board (cite the exact terms and show compliance). After termination, §5.6 allows restoring the account by submitting a **written statement of planned improvements**; Apple restores it only after approving the plan and confirming the changes. Don't use the appeal form to reinstate an already-terminated account — use the appropriate Contact Us form ([appeals guidance](https://developer.apple.com/help/app-review/after-submitting-for-review/appeal-to-the-app-review-board)).

---

## Gate I — Review Operations & Evidence

Use this when the issue only reproduces in App Review but not internally.

**Reviewer-only issue playbook (high success rate):**

- Add temporary structured diagnostics for login, paywall load, and API error paths.
- Log non-PII correlation data: build number, timestamp, endpoint, status code, device model/OS if available.
- Correlate logs with App Review rejection timestamp and attach findings in Resolution Center.
- Include screenshot/video and exact repro path in your response packet.

**Why this matters:** a frequent rejection loop is \"can't log in / can't proceed\" that cannot be reproduced by internal testers. Evidence-backed replies reduce back-and-forth and unblock review faster.

**Operational reminders from Apple guidance:**

- Repeated rejections for the same issue — or attempts to manipulate the review process — slow review down.
- Metadata-only rejections can often be fixed and resubmitted without a new binary.

**Live-app removals ([Apple: respond to an app removal](https://developer.apple.com/help/app-review/safe-and-trusted-store/app-removals)):** apps can be pulled after approval when they include hidden features or were **modified after approval (e.g. a server-side change)** in a way that introduces a violation, when users report issues that didn't occur in review, or on legal / IP / government takedown requests. Less severe issues usually get **14 days** for a compliant update; serious safety or legal issues mean **immediate** removal (restore by shipping a compliant update); a **fundamentally incompatible concept** is removed permanently — only a new app with a compatible concept can return. Reply to the removal message in App Store Connect with your fix plan.

---

## Reviewer Path Script Template

Produce this for every submission. Fill in all `[brackets]`.

```
App Review Notes

App concept: [1-2 sentence description of what the app does and for whom]
Business model: [free / IAP / subscription / ads — and where purchases happen]
What changed (updates): [specific new features + where the reviewer finds them]

Demo account:
  Username: [email or username]
  Password: [password]
  Region/storefront: [e.g. United States]
  Notes: [any pre-seeded data the reviewer needs to see, no 2FA active]

Key feature walkthrough:
  1. [Step to reach Feature A — required for reviewer to find it]
  2. [Step to trigger in-app purchase paywall — exact tap path]
  3. [Step to find account deletion — Settings → Profile → Delete Account]
  4. [Step to access AI feature, if present]

Backend: Live and accessible. No VPN required.

In-app purchases: [List product IDs submitted with this binary]

Non-obvious behaviors: [Anything that might look like a bug but is intentional]

Remote config / feature flags: [Every flag-controlled feature, its state in this build, how to reach it — §2.5.19]

Devices & OS tested: [e.g. iPhone 17 Pro / iOS 27.1, iPad Air M3 / iPadOS 27.0]

External services: [auth, payments, data providers, AI providers the core features depend on]

Regional differences: [features/content that differ by storefront, or "identical in all regions"]

Regulatory / rights documents: [licenses, clearances, trademark permissions attached in App Review Information]

Hardware (if any): [Connection type + device name; confirm App Store Connect §6.1 disclosure matches]

Attachment: [demo video URL — for hardware, a video of the app on a physical device pairing with it, not a screen recording]
```

---

## After Rejection: Resolution Loop

```
Rejected → Read guideline clause cited exactly
         → Reproduce on a TestFlight build on real device
         → Classify: metadata-only fix OR requires new binary?
         → Metadata only: fix + reply in Resolution Center (no new binary needed)
         → New binary: fix code → QA → upload new build → reply with notes
         → Disagree: submit ONE appeal per rejected submission via App Store Connect
         → Appeal upheld: resolved
         → Appeal denied: fix per guidelines and resubmit
```

**Removed live app?** Reply to the removal message in App Store Connect first; ship a compliant update inside the resolution window (often 14 days); appeal only if you believe the app complies (see **Gate I**).  
**Account facing termination?** Appeal to the App Review Board citing the specific terms; once terminated, restoration goes through a written improvement plan under §5.6 (see **Gate L**).

**Appeal tips (from Apple's own guidance):**

- Reply to the rejection/removal message with clarification first; appeal only if that doesn't resolve it
- Cite the exact guideline clause and explain compliance **point by point**; address every cited guideline in **one** appeal
- Do not submit multiple appeals for the same rejection (one appeal per rejected or removed app; one form can cover up to five apps)
- Don't appeal requests for more information or documentation — just provide it
- Bug-fix-only submissions for live apps can bypass some violations (except legal/safety)
- You can attach screenshots, demo videos, and supporting documents until you resubmit
- If only part of a multi-item submission is rejected, you can remove rejected items and continue with accepted ones
- Apple offers App Review appointments for pre-review/process guidance on difficult submissions

**Reference links:**

- Appeals: [developer.apple.com/contact/app-store/?topic=appeal](https://developer.apple.com/contact/app-store/?topic=appeal)
- Expedited review: [developer.apple.com/contact/app-store/?topic=expedite](https://developer.apple.com/contact/app-store/?topic=expedite)

---

## Reference Files

- **[REFERENCE.md](REFERENCE.md)** — Full rejection database with community case studies, Apple doc anchors, Reddit/StackOverflow patterns, fix code examples; **§2.5.19 hidden/dormant features** and **account-termination trigger** tables; **§5.2 / §5.1.5 / ads–push / export** pattern tables; **§5.3 gambling**, **§1.1 safety/speech**, **sandbox & system access** tables
- **[EDGE-CASES.md](EDGE-CASES.md)** — AI policy, hot updates, privacy manifests, external payments, UGC edge cases, regulated domains
- **[CHECKLIST.md](CHECKLIST.md)** — Printable pre-submission checklist and reviewer kit templates
- **[ADDENDUM.md](ADDENDUM.md)** — **October 16, 2026 Guidelines revision** (§2.5.19 hidden/dormant features & bait-and-switch, §1.2 AI/NCII, §2.3.14 creative assets, §3.1.1(a), §5; ban-trigger map), **October 13, 2026 ADPLA revision** (§3.2 integrity terms, Live Assistance, App Replacement, HomeView, multiseat), **EU Attachment 14 (October 1, 2026)**, **fall 2026 operations** (iPhone Duo screenshots, creative assets, April 2027 SDK minimums, ATT in the EU, Bundles & Suites, Sign in with Apple domain, macOS Rosetta / Full Disk Access / Developer ID Sub-CA, Korea age ratings), **social media age-rating questions (July 9, 2026; mandatory Sept 2026)**, **Brazil commerce opening (June 18, 2026)**, **June 8, 2026 Guidelines + ADPLA revision** (4.3 spam tiers, 1.2 UGC enforcement, 4.5.3 Live Activities, §3.3.11 AI/ML), vibe coding crackdown (March 2026), age rating overhaul, visionOS requirements, app size limits, ITMS error codes, ATS exceptions, launch screen, code signing, TestFlight vs App Store review, inactive app removal, screenshot requirements, local network permission, notification timing, post-approval quality (§5.6.4), **Japan & Brazil ADPLA Attachment 12** (alternative payments / out-of-app / marketplace)

**Maintenance:** Rejection statistics (e.g. 2025 transparency counts) should be refreshed when Apple publishes new annual reports; gate logic and guideline links stay authoritative until Apple updates the guidelines page.
