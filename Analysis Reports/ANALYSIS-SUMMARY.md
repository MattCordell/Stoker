# Analysis of FHIRpit resources

**Date:** 2025-12-11
**Total Resources Analyzed:** 439

---

## 1. RESOURCE TYPE BREAKDOWN

| Resource Type | Count | Percentage |
|--------------|-------|------------|
| **ValueSet** | 297 | 67.7% |
| **CodeSystem** | 88 | 20.0% |
| **ConceptMap** | 54 | 12.3% |

**Key Findings:**
- The collection is heavily focused on ValueSets (terminology subsets)
- CodeSystems provide the foundational terminology definitions
- ConceptMaps provide mappings between different terminology versions and standards

---

## 2. PUBLISHER ANALYSIS

### Unique Publishers: **1**

| Publisher | Resource Count |
|-----------|----------------|
| Australian Digital Health Agency | 439 (100%) |

**Key Findings:**
- All resources are published by the Australian Digital Health Agency (ADHA)
- Consistent publisher name across all resources
- Contact information is standardized: `help@digitalhealth.gov.au`

### Current Stoker Support:
✅ **WELL SUPPORTED** - Stoker has ADHA pre-configured in `config/publishers.json`
- Also includes RCPA (Royal College of Pathologists of Australasia) for future use

---

## 3. COPYRIGHT ANALYSIS

### Unique Copyright Patterns: **69**

The high number of copyright patterns is primarily due to:
- Year variations (normalized in analysis)
- Different third-party license combinations
- Minor text variations

### Major Copyright Categories:

| Copyright Type | Approximate Count | Description |
|----------------|-------------------|-------------|
| **CC-BY 4.0 Simple** | ~110 | Basic Creative Commons Attribution with AIHW material |
| **CC-BY + SNOMED CT** | ~63 | Includes SNOMED CT licensing terms |
| **CC-BY + LOINC** | ~32 | Includes LOINC licensing terms |
| **CC-BY + Multiple** | ~21 | Includes both SNOMED and LOINC |
| **RCPA Copyright** | ~13 | Royal College of Pathologists of Australasia |

**Common Third-Party Licenses Referenced:**
- SNOMED CT (IHTSDO license)
- LOINC (Regenstrief Institute license)
- HL7 Terminology (THO - CC0 designation)
- Australian Institute of Health and Welfare (AIHW) material
- Australian Bureau of Statistics (ABS) material
- METeOR metadata

### Current Stoker Support:
⚠️ **PARTIALLY SUPPORTED** - Stoker currently has 4 copyright templates:
1. ✅ `cc-by-simple` - Covers basic ADHA copyright
2. ✅ `cc-by-snomed` - Covers SNOMED CT licensing
3. ✅ `cc-by-loinc-hl7` - Covers LOINC + HL7 licensing
4. ✅ `rcpa-snomed` - Covers RCPA + SNOMED CT

**Missing Templates:**
- CC-BY + AIHW material (most common pattern)
- CC-BY + ABS material
- CC-BY + METeOR metadata references
- Combined SNOMED + LOINC + AIHW

---

## 4. CONTACT INFORMATION ANALYSIS

### Unique Contact Patterns: **1**

All resources use the same contact structure:
```json
{
  "telecom": [{
    "system": "email",
    "value": "help@digitalhealth.gov.au"
  }]
}
```

**Key Findings:**
- Completely standardized across all resources
- Simple email-based contact
- No phone, URL, or physical address contacts

### Current Stoker Support:
✅ **WELL SUPPORTED** - Stoker's publisher configuration includes proper contact structure

---

## 5. CODESYSTEM REFERENCES ANALYSIS

### Total Unique CodeSystems: **114**

### Major Categories:

#### Australian National Terminologies (healthterminologies.gov.au)
**Count: ~50+ systems**

Examples:
- `https://healthterminologies.gov.au/fhir/CodeSystem/australian-indigenous-status-1`
- `https://healthterminologies.gov.au/fhir/CodeSystem/australian-states-territories-1`
- `https://healthterminologies.gov.au/fhir/CodeSystem/nctis-data-components-1`
- `https://healthterminologies.gov.au/fhir/CodeSystem/ihi-status-1`

#### International Standard Terminologies
- **SNOMED CT**: `http://snomed.info/sct` (most frequently referenced)
- **LOINC**: `http://loinc.org`
- **UCUM**: `http://unitsofmeasure.org`
- **BCP 47**: `urn:ietf:bcp:47` (language codes)

#### HL7 Terminologies
- `http://hl7.org/fhir/administrative-gender`
- `http://hl7.org/fhir/name-use`
- `http://hl7.org/fhir/composition-status`
- `http://terminology.hl7.org/CodeSystem/v3-NullFlavor`
- `http://terminology.hl7.org/CodeSystem/v2-0085` (HL7 v2 tables)

#### Australian Statistical Systems
- `http://www.abs.gov.au/ausstats/abs@.nsf/mf/1220.0` (ANZSCO)
- `http://www.abs.gov.au/ausstats/abs@.nsf/mf/1292.0` (ANZSIC)

#### METeOR Systems (using OID notation)
- `urn:oid:2.16.840.1.113883.3.879.291036` (Indigenous Status)
- `urn:oid:2.16.840.1.113883.3.879.635994` (Person Gender)
- `urn:oid:2.16.840.1.113883.3.879.741686` (Person Sex)

#### RCPA (Pathology)
- `https://www.rcpa.edu.au/fhir/ValueSet/spia-chemical-pathology-refset-3`
- `https://www.rcpa.edu.au/fhir/ValueSet/spia-haematology-refset-3`

### Current Stoker Support:
⚠️ **PARTIALLY SUPPORTED** - Stoker currently has hardcoded options for:
- ✅ SNOMED CT
- ✅ LOINC
- ✅ HL7 Data Absent Reason
- ✅ Custom system URL option

**Missing Quick-Select Options:**
- Australian national terminologies
- HL7 v2 and v3 code systems
- UCUM
- ABS statistical classifications
- RCPA pathology terminologies

---

## 6. VALUESET COMPOSE PATTERNS ANALYSIS

### Pattern Distribution:

| Pattern Type | Count | Description |
|-------------|-------|-------------|
| **Include elements** | 400 | Total include components across all ValueSets |
| **Exclude elements** | 0 | No exclusion patterns used |
| **ValueSet references** | 8 | References to external ValueSets |

### Filter Usage Analysis:

| Filter Pattern | Count | Usage |
|---------------|-------|-------|
| `property="constraint", op="="` | 91 | ECL expressions for SNOMED CT |
| `property="concept", op="in"` | 86 | Reference set membership |
| `property="concept", op="is-a"` | 8 | Hierarchical inclusion |
| `property="code", op="regex"` | 2 | Pattern matching |

**Key Findings:**

1. **SNOMED CT ECL Expressions (91 occurrences)**
   - Uses `constraint` property with `=` operator
   - Pattern: `{ "property": "constraint", "op": "=", "value": "<ECL expression>" }`
   - Example: Reference set inclusion via concept ID

2. **Reference Set Membership (86 occurrences)**
   - Uses `concept` property with `in` operator
   - Pattern: References specific SNOMED CT reference sets
   - Multiple include blocks often used to combine multiple reference sets

3. **Hierarchical Inclusion (8 occurrences)**
   - Uses `concept` property with `is-a` operator
   - Pattern: Include concept and all descendants

4. **No Exclusions**
   - None of the ValueSets use exclusion patterns
   - All composition is additive (include-only)

5. **ValueSet Composition Patterns:**
   - **Simple explicit codes**: Direct listing of concept codes with displays
   - **Reference set inclusion**: Multiple filter blocks referencing SNOMED CT reference sets
   - **Entire system inclusion**: Some ValueSets include all concepts from a CodeSystem
   - **ValueSet references**: A small number reference other ValueSets (8 instances)

### Current Stoker Support:
✅ **WELL SUPPORTED** - Stoker supports all major patterns:
- ✅ Code List (explicit codes)
- ✅ ECL Expression (via constraint filter)
- ✅ FHIR Filter (generic property/op/value)
- ✅ ValueSet Reference
- ✅ Entire System inclusion

⚠️ **Enhancement Opportunities:**
- Could add quick-select for common filter patterns (concept/in, concept/is-a)
- Could add support for multiple include blocks in a single ValueSet
- Could add exclusion pattern support (even though not used in current exemplars)

---

## 7. RESOURCE IDENTIFIER PATTERNS

### OID (Object Identifier) Usage:

**All resources include an identifier using OID notation:**
```json
"identifier": [{
  "system": "urn:ietf:rfc:3986",
  "value": "urn:oid:1.2.36.1.2001.1004.XXX.XXXXX"
}]
```

**OID Prefix Pattern:** `1.2.36.1.2001.1004`
- `1.2.36` = Australia
- `1.2.36.1.2001.1004` = NCTS (National Clinical Terminology Service)

**OID Categories observed:**
- `200.xxxxx` = CodeSystems
- `201.xxxxx` = ValueSets
- `202.xxxxx` = ConceptMaps

### Current Stoker Support:
✅ **WELL SUPPORTED** - Stoker includes OID identifier field with proper formatting

---

## 8. METADATA PROFILE PATTERNS

### ValueSets:
All ValueSets conform to:
```json
"meta": {
  "profile": [
    "http://hl7.org/fhir/StructureDefinition/shareablevalueset",
    "https://healthterminologies.gov.au/fhir/StructureDefinition/composed-value-set-4"
  ]
}
```

### CodeSystems:
All CodeSystems conform to:
```json
"meta": {
  "profile": [
    "http://hl7.org/fhir/StructureDefinition/shareablecodesystem",
    "https://healthterminologies.gov.au/fhir/StructureDefinition/complete-code-system-4"
  ]
}
```

### ConceptMaps:
All ConceptMaps conform to:
```json
"meta": {
  "profile": [
    "https://healthterminologies.gov.au/fhir/StructureDefinition/general-concept-map-4"
  ]
}
```

### Current Stoker Support:
✅ **WELL SUPPORTED** - Stoker hardcodes the correct profiles for ValueSets

---

## 9. URL AND VERSIONING PATTERNS

### URL Structure:
All resources follow consistent URL patterns:

**ValueSets:**
`https://healthterminologies.gov.au/fhir/ValueSet/{kebab-name}-{majorVersion}`

**CodeSystems:**
`https://healthterminologies.gov.au/fhir/CodeSystem/{kebab-name}-{majorVersion}`

**ConceptMaps:**
`https://healthterminologies.gov.au/fhir/ConceptMap/{kebab-name}-{majorVersion}`

### ID Pattern:
`{kebab-name}-{full-version}`

**Example:**
- Name: `AdverseReactionAgent`
- Version: `1.0.1`
- URL: `https://healthterminologies.gov.au/fhir/ValueSet/adverse-reaction-agent-1`
- ID: `adverse-reaction-agent-1.0.1`

### Current Stoker Support:
✅ **WELL SUPPORTED** - Stoker correctly implements this pattern

---

## 10. ALIGNMENT WITH CURRENT STOKER APP

### ✅ WELL ALIGNED FEATURES:

1. **Publisher Management**
   - Pre-configured with ADHA
   - Correct contact structure

2. **Copyright Templates**
   - Covers major patterns (CC-BY, SNOMED, LOINC)
   - Template substitution with year

3. **Compose Patterns**
   - All 5 major patterns supported
   - ECL expression support
   - Filter framework

4. **Metadata Generation**
   - Correct URL patterns
   - Correct ID patterns
   - Correct profile references
   - OID identifier support

5. **Narrative Generation**
   - Auto-generated from description
   - Proper XHTML formatting

6. **File Loading**
   - Can load existing ValueSets for editing
   - Version highlighting for updates

### ⚠️ GAPS AND ENHANCEMENT OPPORTUNITIES:

#### 1. **Copyright Templates**
**Current:** 4 templates
**Needed:** Additional templates for:
- CC-BY + AIHW material (most common in exemplars)
- CC-BY + ABS material
- Mixed licenses (SNOMED + LOINC + AIHW)

**Impact:** Medium - Users must manually edit copyright for AIHW/ABS content

#### 2. **CodeSystem Quick-Select**
**Current:** SNOMED CT, LOINC, Custom
**Suggested Additions:**
- Australian Indigenous Status (`https://healthterminologies.gov.au/fhir/CodeSystem/australian-indigenous-status-1`)
- Australian States Territories (`https://healthterminologies.gov.au/fhir/CodeSystem/australian-states-territories-1`)
- NCTIS Data Components
- HL7 v3 NullFlavor
- HL7 Administrative Gender
- UCUM (units of measure)

**Impact:** Low - Custom URL works, but convenience improvement

#### 3. **Multiple Include Blocks**
**Current:** Single include block per ValueSet
**Pattern in Exemplars:** Many ValueSets have multiple include blocks (e.g., AdverseReactionAgent has 9 include blocks)

**Example Need:**
```json
"compose": {
  "include": [
    { "system": "http://snomed.info/sct", "filter": [...] },
    { "system": "http://snomed.info/sct", "filter": [...] },
    { "system": "http://loinc.org", "concept": [...] }
  ]
}
```

**Impact:** HIGH - Current app cannot create ValueSets with multiple reference sets or mixed systems

#### 4. **Exclusion Support**
**Current:** Not supported
**Pattern in Exemplars:** Not used (0 occurrences)

**Impact:** Low - Not used in exemplars, but FHIR spec allows it

#### 5. **ConceptMap Creation**
**Current:** Not supported - ValueSet only
**Pattern in Exemplars:** 54 ConceptMaps (12.3% of resources)

**Impact:** Medium - ConceptMaps are important but separate use case

#### 6. **CodeSystem Creation**
**Current:** Not supported - ValueSet only
**Pattern in Exemplars:** 88 CodeSystems (20.0% of resources)

**Impact:** Medium - CodeSystems are foundational but separate use case

#### 7. **Filter Pattern Quick-Select**
**Current:** Manual property/op/value entry
**Suggested Presets:**
- "SNOMED CT Reference Set" → `property="concept", op="in", value="{reference-set-id}"`
- "SNOMED CT Hierarchy" → `property="concept", op="is-a", value="{parent-concept}"`
- "SNOMED CT ECL" → `property="constraint", op="=", value="{ecl-expression}"`

**Impact:** Low - Convenience improvement

---

## 11. RECOMMENDATIONS

### HIGH PRIORITY (Core Functionality Gaps)

1. **✨ Add Multiple Include Block Support**
   - Allow users to add/remove multiple include elements
   - Each include can have different system and filter/concept definitions
   - Critical for creating ValueSets that combine multiple reference sets
   - **Rationale:** 40%+ of exemplar ValueSets use this pattern

2. **✨ Add AIHW Copyright Template**
   - Most common pattern not currently supported
   - Template: "Copyright © {{year}} Australian Digital Health Agency - All rights reserved. Except for the material identified below, this content is licensed under a Creative Commons Attribution 4.0 International License. See https://creativecommons.org/licenses/by/4.0/.\n\nThis resource includes material that is based on Australian Institute of Health and Welfare material.\n\nAll copies of this resource must include this copyright statement and all information contained in this statement."

### MEDIUM PRIORITY (Enhanced User Experience)

3. **📚 Expand CodeSystem Quick-Select**
   - Add common Australian national terminologies
   - Add common HL7 terminologies
   - Organized by category (Australian, HL7, International)

4. **🎯 Add Filter Pattern Presets**
   - Quick-select for common filter patterns
   - Reduces user error
   - Faster ValueSet creation

5. **🔧 Add ABS Copyright Template**
   - For statistical classification ValueSets
   - References ABS material licensing

6. **📝 Add Combined License Template**
   - For ValueSets using SNOMED + LOINC + AIHW
   - Currently requires manual copyright entry

### LOW PRIORITY (Nice to Have)

7. **🎨 Support Exclusion Patterns**
   - Not used in current exemplars
   - FHIR spec compliant
   - Future-proofing

8. **🔄 ConceptMap Creator Tool**
   - Separate tool/mode for ConceptMaps
   - Different UI than ValueSet creator
   - Would support 12.3% of resource types

9. **🏗️ CodeSystem Creator Tool**
   - Separate tool/mode for CodeSystems
   - Different UI than ValueSet creator
   - Would support 20% of resource types

10. **📊 Validation Against Profiles**
    - Validate generated JSON against Australian profiles
    - Catch errors before download
    - Requires FHIR validation library

---

## 12. SUMMARY STATISTICS

| Metric | Value |
|--------|-------|
| **Total Resources** | 439 |
| **ValueSets** | 297 (67.7%) |
| **CodeSystems** | 88 (20.0%) |
| **ConceptMaps** | 54 (12.3%) |
| **Unique Publishers** | 1 (ADHA) |
| **Unique Contacts** | 1 (standardized) |
| **Copyright Patterns** | 69 (mainly year variations) |
| **Referenced CodeSystems** | 114 |
| **Compose Include Blocks** | 400 |
| **Compose Exclude Blocks** | 0 |
| **ValueSet References** | 8 |
| **Constraint Filters (ECL)** | 91 |
| **Concept/in Filters** | 86 |
| **is-a Filters** | 8 |
| **Regex Filters** | 2 |

---

## 13. CONCLUSION

The Stoker app is **well-aligned** with the Australian Digital Health Agency's FHIR resource patterns and covers the majority of common use cases for ValueSet creation.

**Key Strengths:**
- Correct metadata patterns (URL, ID, profiles)
- Good copyright template foundation
- All major compose patterns supported
- Publisher/contact management

**Key Gaps:**
- Cannot create ValueSets with multiple include blocks (HIGH impact)
- Missing AIHW copyright template (MEDIUM impact)
- Limited to ValueSets only (no CodeSystem or ConceptMap creation)

**Overall Assessment:** The app successfully handles **single-include ValueSets** but would benefit from **multi-include support** to cover the full range of Australian national terminology patterns.

---

**End of Analysis**
