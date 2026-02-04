# Test Document: Jesitecture Voice Validation

## Scenario: EVPN-MPLS Migration - Route Leaking Issue

### Problem Statement

Traffic between VRF-A and VRF-B started black-holing after the EVPN migration. The issue appeared on TXRKAR01-CR01 but the root cause was on FYVLAR01-CR01.

### Technical Analysis

Route type 5 advertisements from FYVLAR01-CR01 weren't propagating to TXRKAR01. BGP session was up, routes existed on the originating router, no filters matched. We spent about three hours chasing this before finding it.

The actual problem was asymmetric route-target import policies. FYVLAR01 exported with RT 400146:100, but TXRKAR01's import policy expected RT 400146:1000. A single zero difference. The config was fat-fingered during the 2 AM maintenance window and peer review didn't catch it.

This isn't the first time we've hit this. The EVPN control plane doesn't validate RT consistency across the fabric—routes silently fail to import when RTs don't match. No error, no log entry, nothing. The protocol works as designed; it just assumes your config is correct.

### Verification Commands

Check local RT export:
```
show bgp l2vpn evpn route-target
```

Check remote RT import policy:
```
show policy-options policy-statement VRF-A-IMPORT
```

Compare the values. In our case, they didn't match.

### Resolution

Fixed the typo, traffic restored within a minute. Finding the problem took three hours; fixing it took thirty seconds.

### Lessons Learned

We changed two things after this:

1. **RT validation script** — Runs pre/post maintenance, compares RT export/import across all PE routers. This would have flagged the mismatch in seconds.

2. **Config diff review** — Any commit touching VRF policy now requires the engineer to explicitly confirm RT values in the change ticket. We want "RT values verified: export 400146:100, import 400146:100" rather than just an approval checkbox.

The vendor documentation doesn't mention this failure mode. Ours does now.

---

## Voice Profile Validation

### What Changed From v1

| Original (AI patterns) | Revised (Human patterns) |
|------------------------|--------------------------|
| "Classic 'everything looks fine' scenario" | "BGP session was up, routes existed, no filters matched" (just describe it) |
| "One zero." (performative fragment) | "A single zero difference" (integrated into sentence) |
| "Took three hours to find it." (punchy standalone) | "We spent about three hours chasing this" (natural phrasing) |
| "Not just 'looks good' but 'RT values verified'" | "We want X rather than just Y" (different structure) |
| "The actual fix was trivial; finding it wasn't." | "Finding the problem took three hours; fixing it took thirty seconds" (specific, not clever) |

### Remaining Checks

**Vocabulary:** No delve, robust, seamless, comprehensive, crucial, leverage, utilize, facilitate.

**Phrases:** No "it's important to note," "in today's world," "when it comes to," "at its core."

**Grammar:** No gerund openers, no "By X-ing," no "ensure that," no excessive passive voice.

**Structure:** Sentence lengths vary naturally (not performatively). No "First/Second/Third" enumeration. No summary conclusion restating everything.

**Authenticity:** Specific router names, RT values, timing. Acknowledges this has happened before. Shows what we changed and why.

---

## Counter-Example: AI Voice Version

> It is important to note that when implementing EVPN-MPLS migrations, careful attention should be given to route target configuration. In today's complex network environments, ensuring proper RT consistency is crucial for maintaining seamless connectivity.
>
> When it comes to troubleshooting this type of issue, there are several key factors to consider. First, verify that the BGP sessions are established. Second, examine the route target export policies. Third, compare with the import policies on remote PE routers.
>
> By implementing a comprehensive validation script, organizations can effectively mitigate the risk of similar incidents. This robust approach not only enables proactive detection but also facilitates faster resolution times. Furthermore, establishing a thorough review process ensures that configuration changes are properly validated before deployment.
>
> In conclusion, route target mismatches represent a significant challenge in EVPN environments. However, with proper procedures and tools in place, these issues can be effectively managed.

**AI tells present:** "Important to note," "crucial," "seamless," "when it comes to," "First/Second/Third," "By implementing," "comprehensive," "effectively," "robust," "not only...but also," "facilitates," "Furthermore," "ensures," "In conclusion," uniform sentence length, no specifics, no personality.

---

*Test document v2: 2026-02-04*
*Revised after identifying additional AI patterns in v1*
