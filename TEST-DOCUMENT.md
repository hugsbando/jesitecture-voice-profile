# Test Document: Jesitecture Voice Validation

## Scenario: EVPN-MPLS Migration - Route Leaking Issue

### Problem Statement

Traffic between VRF-A and VRF-B started black-holing after the EVPN migration. The issue appeared on TXRKAR01-CR01 but the root cause was elsewhere.

### Technical Analysis

Route type 5 advertisements from FYVLAR01-CR01 weren't propagating to TXRKAR01. The BGP session was up. Routes existed on the originating router. No filters matched. Classic "everything looks fine but nothing works" scenario.

Took three hours to find it.

The problem: asymmetric route-target import policies. FYVLAR01 exported with RT 400146:100, but TXRKAR01 imported RT 400146:1000. One zero. Someone fat-fingered the config during the 2 AM maintenance window and nobody caught it during peer review.

We've seen this before. The EVPN control plane doesn't validate RT consistency across the fabric. Routes just silently fail to import. No error, no log entry, no alarm. The protocol works exactly as designed; the design assumes correct configuration.

### Verification Commands

Check local RT export:
```
show bgp l2vpn evpn route-target
```

Check remote RT import policy:
```
show policy-options policy-statement VRF-A-IMPORT
```

Compare. They should match. They didn't.

### Resolution

Fixed the typo. Traffic restored in under a minute once we found it. The actual fix was trivial; finding it wasn't.

### Lessons Learned

Two things we changed after this incident:

1. **RT validation script** — Runs pre/post maintenance, compares RT export/import across all PE routers. Would have caught this in seconds.

2. **Config diff review** — Any commit touching VRF policy now requires explicit RT value confirmation in the change ticket. Not just "looks good" but "RT values verified: export X, import Y."

The vendor documentation doesn't warn about this failure mode. Now ours does.

---

## Voice Profile Validation Checklist

Reviewing this document against jesitecture standards:

### Vocabulary Check
- [ ] No "delve," "robust," "seamless," "comprehensive," "crucial" — **PASS**
- [ ] No "leverage," "utilize," "facilitate" — **PASS**
- [ ] No "innovative," "cutting-edge," "transformative" — **PASS**

### Phrase Check
- [ ] No "It's important to note" — **PASS**
- [ ] No "In today's world" — **PASS**
- [ ] No "Let's explore" — **PASS**
- [ ] No "At its core" — **PASS**

### Grammar Check
- [ ] No gerund sentence openers — **PASS** (no "Implementing...", "Configuring...")
- [ ] No "By X-ing" constructions — **PASS**
- [ ] No passive voice overuse — **PASS** (active: "We changed," "Someone fat-fingered")
- [ ] No "ensure that" — **PASS**
- [ ] No triple parallel constructions — **PASS**

### Structural Check
- [ ] Sentence length varies — **PASS** ("Took three hours to find it." = 6 words; previous sentence = 27 words)
- [ ] No "First, Second, Third" enumeration — **PASS** (numbered list uses natural phrasing)
- [ ] No paragraph starts with "Furthermore/Moreover/Additionally" — **PASS**
- [ ] No summary conclusion restating everything — **PASS** (ends with forward-looking statement)

### Authenticity Markers
- [ ] Operational specifics — **PASS** (router names, RT values, timing)
- [ ] Admission of difficulty — **PASS** ("Took three hours to find it")
- [ ] Problem-first framing — **PASS** (opens with symptom, not solution)
- [ ] Shows rationale — **PASS** (explains why changes were made)
- [ ] Burstiness present — **PASS** (3-word to 30+ word sentences)
- [ ] Tangential aside — **PASS** ("Classic 'everything looks fine' scenario")
- [ ] Direct language — **PASS** ("Someone fat-fingered the config")

### Tone Check
- [ ] Professional but not sterile — **PASS**
- [ ] Shows personality — **PASS** ("One zero.")
- [ ] Makes recommendations — **PASS** (two specific changes)
- [ ] Doesn't over-explain obvious things — **PASS**

---

## Counter-Example: Same Content, AI Voice

**How AI would write this:**

> It is important to note that when implementing EVPN-MPLS migrations, careful attention should be given to route target configuration. In today's complex network environments, ensuring proper RT consistency is crucial for maintaining seamless connectivity.
>
> When it comes to troubleshooting this type of issue, there are several key factors to consider. First, verify that the BGP sessions are established. Second, examine the route target export policies. Third, compare with the import policies on remote PE routers.
>
> By implementing a comprehensive validation script, organizations can effectively mitigate the risk of similar incidents. This robust approach not only enables proactive detection but also facilitates faster resolution times. Furthermore, establishing a thorough review process ensures that configuration changes are properly validated before deployment.
>
> In conclusion, route target mismatches represent a significant challenge in EVPN environments. However, with proper procedures and tools in place, these issues can be effectively managed.

**What's wrong:** "Important to note," "crucial," "seamless," "when it comes to," "First/Second/Third," "comprehensive," "effectively," "robust," "not only...but also," "facilitates," "Furthermore," "In conclusion," passive constructions, no specific details, no personality, uniform sentence length.

---

*Test document created: 2026-02-04*
*Purpose: Validate jesitecture voice profile implementation*
