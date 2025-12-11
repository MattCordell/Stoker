# Editorial Compliance Analysis
**FHIR Terminology Resources Authoring Guide - Stoker App Alignment**

*Analysis Date: 2025-12-11*

---

## Executive Summary

This document analyzes how the Stoker App aligns with the FHIR Terminology Resources Authoring Guide as specified in the NCTS (National Clinical Terminology Service) editorial rules. The Stoker App is **substantially aligned** with the editorial rules, particularly in core areas like URL structure, profiles, and copyright management. However, there are **critical compliance issues** with id generation and narrative formatting that require immediate attention.

**Reference Document:** `2. FHIR Terminology Resources Au_ac43227e7a9040fb85a56be4bdac9a73-111225-1208-6.pdf`

---

## Current Alignment (Strengths)

The Stoker App correctly implements the following NCTS editorial requirements:

### ✅ **1. URL Format**
- **Implementation:** `https://healthterminologies.gov.au/fhir/ValueSet/{kebab-name}-{major-version}`
- **Location:** [index.html:802](index.html#L802)
- **Status:** ✓ Compliant

### ✅ **2. Meta Profiles**
- **Implementation:** Includes both required profiles:
  - `http://hl7.org/fhir/StructureDefinition/shareablevalueset`
  - `https://healthterminologies.gov.au/fhir/StructureDefinition/composed-value-set-4`
- **Location:** [index.html:823-826](index.html#L823-L826)
- **Status:** ✓ Compliant

### ✅ **3. OID Identifier Support**
- **Implementation:**
  - System: `urn:ietf:rfc:3986`
  - Value: `urn:oid:{user-provided-OID}`
- **Location:** [index.html:836-841](index.html#L836-L841)
- **Status:** ✓ Compliant

### ✅ **4. Publisher Configuration**
- **Implementation:** Australian Digital Health Agency with correct contact details
- **Configuration:** [config/publishers.json](config/publishers.json)
- **Status:** ✓ Compliant

### ✅ **5. Copyright Templates**
- **Implementation:** Includes approved copyright templates with year and publisher substitution
- **Configuration:** [config/copyrights.json](config/copyrights.json)
- **Status:** ✓ Compliant

### ✅ **6. Compose Options**
- **Implementation:** Supports all required compose types (Code List, ECL, Filter, ValueSet Reference, Entire System)
- **Location:** [index.html:529-617](index.html#L529-L617)
- **Status:** ✓ Compliant

### ✅ **7. File Download Naming**
- **Implementation:** `ValueSet-{id}.json`
- **Location:** [index.html:988](index.html#L988)
- **Status:** ✓ Compliant

---

## Gaps and Differences

### 🔴 **Critical Issues** (Immediate Action Required)

#### **1. ID Generation Strategy**
- **Location:** [index.html:801](index.html#L801)
- **Current Behavior:** Generates `{kebab-name}-{full-version}` (e.g., `adverse-reaction-1.0.0`)
- **Required Behavior:**
  - Latest version: `{kebab-name}-{major}` (e.g., `adverse-reaction-1`)
  - Previous versions: `{kebab-name}-{full-version}` (e.g., `adverse-reaction-1.0.0`)
- **Editorial Rule:** "The 'latest' version is represented by an id ending in the major version number (e.g. relationship-type-1). Previous versions have ids ending in the full version (e.g. relationship-type-1.2.0)."
- **Impact:** **NON-COMPLIANT** - All generated ValueSets have incorrect id format
- **Priority:** **HIGH**

**Current Code:**
```javascript
const id = `${kebabName}-${version}`;  // INCORRECT
```

**Should be:**
```javascript
const id = `${kebabName}-${majorVersion}`;  // CORRECT for latest version
```

---

#### **2. Narrative Text Format (text.div)**
- **Location:** [index.html:814](index.html#L814)
- **Current Behavior:** `<div xmlns="..."><h2>{title}</h2><tt>{url}</tt><p>{narrativeText}</p></div>`
- **Required Behavior:** `[namespace][title][url][description]` format as per authoring templates
- **Editorial Rule:** "Conform to the format below. This format is represented in the authoring templates along with desired HTML tags."
- **Impact:** Format differs from standard NCTS authoring templates
- **Priority:** **HIGH**

---

### 🟡 **Validation/Guidance Issues** (Should Fix)

#### **3. Name Field Validation**
- **Location:** [index.html:432](index.html#L432)
- **Current Behavior:** Free text input with suggestion from title
- **Required Behavior:** Must conform to `[A-Z]([A-Za-z0-9_]){0,254}`
- **Missing:**
  - No regex validation
  - No enforcement of PascalCase
  - No prevention of whitespace/special characters
- **Priority:** **MEDIUM**

#### **4. Title Field Validation**
- **Location:** [index.html:427](index.html#L427)
- **Required Rules:**
  - Title Case
  - No special characters
  - Singular form (where possible)
  - No acronyms
  - No resource type name
- **Missing:** No validation or guidance
- **Priority:** **MEDIUM**

#### **5. ID Character Limit Warning**
- **Location:** [index.html:432](index.html#L432)
- **Required:** Keep id ≤60 characters for latest versions
- **Rationale:** Full version will be appended for previous versions (e.g., `-1.2.0`)
- **Missing:** No character counter or warning
- **Priority:** **MEDIUM**

#### **6. Description Guidance**
- **Location:** [index.html:437](index.html#L437)
- **Required Template:** "The [title] value set includes values..."
- **Missing:** No template suggestion or auto-population
- **Priority:** **MEDIUM**

---

### 🟢 **Functional Gaps** (Nice to Have)

#### **7. Semantic Versioning Guidance**
- **Location:** [index.html:448](index.html#L448)
- **Editorial Rules:**
  - Patch (z): Insignificant changes (typo, copyright update)
  - Minor (y): Significant non-breaking changes (add code)
  - Major (x): Significant breaking changes (remove code)
- **Missing:** No tooltip or help text explaining semantic versioning
- **Priority:** **LOW**

#### **8. Date Change Enforcement**
- **Location:** [index.html:464](index.html#L464), [index.html:1047](index.html#L1047)
- **Required:** Date must change when version or status changes
- **Missing:** No validation that date is updated when modifying existing resources
- **Priority:** **LOW**

---

## Detailed Recommendations

### **High Priority (Compliance) - Address Immediately**

#### **Recommendation 1: Fix ID Generation Logic**
**File:** [index.html:801](index.html#L801)

**Current Code:**
```javascript
const id = `${kebabName}-${version}`;
```

**Proposed Fix:**
```javascript
const majorVersion = version.split('.')[0];
const id = `${kebabName}-${majorVersion}`;
```

**Impact:** This is a breaking change but necessary for compliance.

---

#### **Recommendation 2: Update text.div Format**
**File:** [index.html:814](index.html#L814)

Align with NCTS authoring template format. Recommend consulting actual templates for exact format.

---

#### **Recommendation 3: Add Name Field Validation**
**File:** [index.html:432](index.html#L432)

**Implementation:**
```javascript
function validateName(nameValue) {
  const pattern = /^[A-Z]([A-Za-z0-9_]){0,254}$/;
  if (!pattern.test(nameValue)) {
    // Show error message
    return false;
  }
  return true;
}
```

---

#### **Recommendation 4: Add ID Character Counter**
**File:** [index.html:432](index.html#L432)

Display character count for id field with warning when approaching 60 characters.

---

### **Medium Priority (User Guidance) - Address Soon**

#### **Recommendation 5: Add Semantic Versioning Help**
Include tooltip explaining patch/minor/major version increments.

#### **Recommendation 6: Add Description Template Helper**
Pre-fill or suggest description starting with "The [title] value set includes values..."

#### **Recommendation 7: Add Title Validation Hints**
Provide real-time feedback for special characters, acronyms, plural forms, and Title Case.

#### **Recommendation 8: Add Date Change Validation**
When loading existing ValueSet, detect version changes and auto-update date.

---

### **Low Priority (Enhancement) - Nice to Have**

#### **Recommendation 9: Add Status/Experimental Guidance**
Tooltip explaining production resources should be "active" and "experimental: false".

#### **Recommendation 10: Add OID Registration Helper**
Link to request OID from ADHA via NCTS@digitalhealth.gov.au with arc information.

#### **Recommendation 11: Add File Naming Preview**
Show recommended filename before download: `ValueSet-{Name}-{Version}.json`

#### **Recommendation 12: Add Compose Guidance**
For SNOMED CT, suggest ECL as preferred filter and reference set consideration.

#### **Recommendation 13: Add Multiple Version Support**
Functionality to export previous versions with full version numbers in id.

#### **Recommendation 14: Add Copyright Formatting Preview**
Show how multiple copyright statements are formatted with `\n\n` spacing.

#### **Recommendation 15: Add Validation Report**
"Validate" button that checks resource against all editorial rules and provides compliance report.

---

## Editorial Rules Quick Reference

### **File Naming**
```
ValueSet-<Name>-<Version>.json
```

### **Element: id**
- Latest: `{kebab-name}-{major}` (e.g., `adverse-reaction-1`)
- Previous: `{kebab-name}-{full-version}` (e.g., `adverse-reaction-1.0.0`)
- Limit: ≤60 characters for latest

### **Element: url**
```
https://healthterminologies.gov.au/fhir/ValueSet/{id-with-major-version}
```

### **Element: identifier**
- System: `urn:ietf:rfc:3986`
- Value: `urn:oid:{OID}`
- ValueSet arc: `1.2.36.1.2001.1004.201`

### **Element: version**
- Format: `x.y.z` or `YYYYMMDD`
- Patch (z): Insignificant changes
- Minor (y): Add codes
- Major (x): Remove codes

### **Element: name**
- Pattern: `[A-Z]([A-Za-z0-9_]){0,254}`
- PascalCase, no whitespace

### **Element: title**
- Title Case
- No special characters
- Singular, no acronyms

### **Element: publisher**
```
"Australian Digital Health Agency"
```

### **Element: contact**
```json
"contact": [{
  "telecom": [{
    "system": "email",
    "value": "help@digitalhealth.gov.au"
  }]
}]
```

### **Element: description**
Template: "The [title] value set includes values..."

---

## Implementation Priority Matrix

| Priority | Recommendation | Effort | Impact |
|----------|---------------|--------|--------|
| 🔴 HIGH | Fix ID generation | Low | Critical |
| 🔴 HIGH | Update text.div format | Medium | High |
| 🔴 HIGH | Add name validation | Medium | High |
| 🔴 HIGH | Add ID char counter | Low | Medium |
| 🟡 MEDIUM | Semantic version help | Low | Medium |
| 🟡 MEDIUM | Description template | Low | Medium |
| 🟡 MEDIUM | Title validation hints | Medium | Medium |
| 🟡 MEDIUM | Date change validation | Low | Medium |
| 🟢 LOW | Status/experimental guide | Low | Low |
| 🟢 LOW | OID registration helper | Low | Low |
| 🟢 LOW | Validation report | High | Medium |

---

## Conclusion

The Stoker App provides a solid foundation for creating FHIR R4 ValueSet resources with good alignment to NCTS editorial standards. The critical compliance issues identified (id generation and narrative format) should be addressed immediately to ensure all generated resources conform to the authoring guide.

**Next Steps:**
1. Address HIGH priority items (Recommendations 1-4)
2. Implement MEDIUM priority user guidance (Recommendations 5-8)
3. Consider LOW priority enhancements based on user feedback
4. Test generated ValueSets against NCTS validation tools

---

*Document Version: 1.0*
*Prepared for: Stoker App Development*
*Reference: NCTS FHIR Terminology Resources Authoring Guide*
