# Code Organization and Quality Review - Stoker

**Date:** December 18, 2025
**Current State:** Single HTML file, 3,781 lines
**Status:** Investigation only - No changes made

---

## Executive Summary

The Stoker application has grown from a simple tool to a sophisticated FHIR ValueSet authoring application. At nearly 4,000 lines in a single HTML file, the codebase is reaching the practical limits of the single-file approach. This review provides recommendations for transitioning to a more maintainable, modular architecture while preserving the deployment simplicity that makes Stoker effective.

### Current Metrics
- **Total Lines:** 3,781
  - CSS: ~1,020 lines (27%)
  - HTML: ~340 lines (9%)
  - JavaScript: ~2,413 lines (64%)
- **JavaScript Functions:** 86
- **Global Variables:** ~10 key state variables
- **Configuration Files:** 2 (publishers.json, copyrights.json)

---

## Current Architecture Analysis

### ✅ Strengths
1. **Zero Dependencies** - Pure vanilla JS, no framework lock-in
2. **Static Deployment** - Works on GitHub Pages, Netlify without build steps
3. **Progressive Enhancement** - Works offline after initial load
4. **Accessibility** - Good ARIA support, keyboard navigation, focus management
5. **Performance** - Debounced updates, efficient rendering
6. **Code Quality** - Well-structured functions, decent separation of concerns

### ⚠️ Challenges
1. **File Size** - Single 3,781-line file is difficult to navigate
2. **Code Organization** - Limited modularity makes testing and maintenance harder
3. **Reusability** - Some duplicated code patterns (e.g., system select functions)
4. **Scalability** - Adding new features becomes increasingly complex
5. **Collaboration** - Merge conflicts more likely in single file
6. **Developer Experience** - No IDE benefits (auto-imports, tree-shaking, etc.)

---

## Recommended Architecture

### Option A: Modular Structure with Simple Build (RECOMMENDED)

Transition to a modular architecture with minimal tooling:

```
stoker/
├── src/
│   ├── index.html          (minimal HTML shell)
│   ├── styles/
│   │   ├── variables.css   (CSS custom properties)
│   │   ├── layout.css      (grid, containers)
│   │   ├── components.css  (buttons, cards, modals)
│   │   ├── forms.css       (form elements)
│   │   └── themes.css      (light/dark themes)
│   ├── js/
│   │   ├── main.js         (entry point, initialization)
│   │   ├── config/
│   │   │   └── constants.js
│   │   ├── state/
│   │   │   └── app-state.js
│   │   ├── ui/
│   │   │   ├── theme.js
│   │   │   ├── notifications.js
│   │   │   ├── modals.js
│   │   │   └── focus-trap.js
│   │   ├── forms/
│   │   │   ├── metadata-form.js
│   │   │   ├── copyright-manager.js
│   │   │   └── include-blocks.js
│   │   ├── fhir/
│   │   │   ├── valueset-builder.js
│   │   │   ├── validator.js
│   │   │   └── diff-engine.js
│   │   ├── io/
│   │   │   ├── file-loader.js
│   │   │   ├── file-saver.js
│   │   │   └── clipboard.js
│   │   └── utils/
│   │       ├── debounce.js
│   │       └── dom-helpers.js
│   └── templates/
│       └── include-editor.html
├── config/
│   ├── publishers.json
│   └── copyrights.json
├── dist/                   (build output)
│   └── index.html          (bundled single file)
├── build.js                (simple bundler script)
└── package.json            (minimal - just build tools)
```

**Build Process:**
- Use a simple bundler (Vite, esbuild, or custom Node script)
- Output remains a single `dist/index.html` file
- Deploy `dist/index.html` to GitHub Pages/Netlify
- Development uses dev server with hot reload
- **Production artifact is identical** - still one HTML file

**Benefits:**
- ✅ Modular development with all modern IDE benefits
- ✅ Same deployment target (single HTML file)
- ✅ Easy testing of individual modules
- ✅ Better collaboration (smaller files = fewer conflicts)
- ✅ Optional tree-shaking and minification
- ✅ Can use ES modules during development

**Drawbacks:**
- ⚠️ Requires Node.js for development
- ⚠️ Build step added (but only for development)
- ⚠️ Initial setup complexity

---

### Option B: Modular Structure WITHOUT Build (Simpler)

Split into multiple files loaded via ES modules, no build step:

```
stoker/
├── index.html              (loads modules via <script type="module">)
├── styles/
│   ├── main.css
│   └── themes.css
├── js/
│   ├── main.js
│   ├── config/constants.js
│   ├── state/app-state.js
│   ├── ui/theme.js
│   ├── ui/notifications.js
│   └── ... (same structure as Option A)
└── config/
    ├── publishers.json
    └── copyrights.json
```

**index.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="styles/main.css">
    <link rel="stylesheet" href="styles/themes.css">
</head>
<body>
    <!-- HTML content -->
    <script type="module" src="js/main.js"></script>
</body>
</html>
```

**Benefits:**
- ✅ No build step required
- ✅ Modular development
- ✅ ES modules work in all modern browsers
- ✅ Simple deployment (just upload files)

**Drawbacks:**
- ⚠️ Multiple HTTP requests (slower initial load)
- ⚠️ No minification/optimization
- ⚠️ Can't use older browser features easily
- ⚠️ More complex deployment (multiple files to manage)

---

### Option C: Enhanced Single File (Minimal Change)

Keep single file but improve organization:

```html
<script>
// ============================================================================
// CONFIGURATION & CONSTANTS
// ============================================================================

const FHIR_CONSTANTS = { /* ... */ };
const UI_CONSTANTS = { /* ... */ };

// ============================================================================
// APPLICATION STATE
// ============================================================================

const AppState = {
    includeBlocks: [],
    selectedCopyrights: [],
    // ... centralized state
};

// ============================================================================
// THEME MANAGEMENT
// ============================================================================

function setTheme(theme) { /* ... */ }

// ... continue with clear sections
</script>
```

Apply all the "Code Organization" issues from [TODO.md](TODO.md:80-119):
- Extract constants (#25)
- Extract magic numbers (#26)
- Add section headers (#27)
- Consolidate global state (#28)
- Reduce duplicate code (#29)

**Benefits:**
- ✅ Minimal disruption
- ✅ No build process
- ✅ Same deployment
- ✅ Improved readability

**Drawbacks:**
- ⚠️ Still a very large file
- ⚠️ Doesn't solve long-term scalability
- ⚠️ Limited testability improvements

---

## Specific Recommendations by Category

### 1. Code Organization (High Impact)

#### 1.1 Extract Constants
Create a constants section at the top of the script:

```javascript
const FHIR_CONSTANTS = {
    SHAREABLE_PROFILE: 'http://hl7.org/fhir/StructureDefinition/shareablevalueset',
    NCTS_PROFILE: 'https://www.healthterminologies.gov.au/fhir/StructureDefinition/ncts-valueset-2',
    BASE_URL: window.location.origin + window.location.pathname.replace(/\/[^\/]*$/, ''),
    MAX_NAME_LENGTH: 254,
    MAX_ID_LENGTH: 64,
    FHIR_VERSION: '4.0.1'
};

const UI_CONSTANTS = {
    NOTIFICATION_DURATION: 3000,
    PREVIEW_DEBOUNCE_MS: 200,
    MAX_FILE_SIZE: 10 * 1024 * 1024, // 10MB
    REGEX_DISPLAY_LIMIT: 100
};
```

**Issue:** [#25](https://github.com/MattCordell/Stoker/issues/25)
**Impact:** High - Improves maintainability and eliminates magic strings
**Effort:** 2-3 hours

#### 1.2 Consolidate Global State
Wrap all global variables in a state object:

```javascript
const AppState = {
    // UI State
    includeBlocks: [],
    selectedCopyrights: [],
    currentEditingIncludeId: null,
    hasUnsavedChanges: false,

    // Configuration
    publishers: [],
    copyrights: [],

    // Focus Management
    focusTraps: {
        includeEditor: null,
        validationReport: null,
        diffReport: null
    },

    // Loaded Data
    originalLoadedValueSet: null,

    // Drag & Drop
    draggedElement: null
};
```

**Issue:** [#28](https://github.com/MattCordell/Stoker/issues/28)
**Impact:** High - Single source of truth, easier to debug
**Effort:** 3-4 hours

#### 1.3 Add Section Headers
Organize code into clear logical sections:

```javascript
// ============================================================================
// CONFIGURATION & CONSTANTS
// ============================================================================

// ============================================================================
// APPLICATION STATE
// ============================================================================

// ============================================================================
// INITIALIZATION
// ============================================================================

// ============================================================================
// THEME MANAGEMENT
// ============================================================================

// ============================================================================
// FOCUS MANAGEMENT & ACCESSIBILITY
// ============================================================================

// ============================================================================
// FORM MANAGEMENT
// ============================================================================

// ============================================================================
// COPYRIGHT MANAGEMENT
// ============================================================================

// ============================================================================
// INCLUDE BLOCK MANAGEMENT
// ============================================================================

// ============================================================================
// FHIR VALUESET GENERATION
// ============================================================================

// ============================================================================
// VALIDATION
// ============================================================================

// ============================================================================
// FILE I/O OPERATIONS
// ============================================================================

// ============================================================================
// DIFF ENGINE
// ============================================================================

// ============================================================================
// UI UTILITIES & HELPERS
// ============================================================================
```

**Issue:** [#27](https://github.com/MattCordell/Stoker/issues/27)
**Impact:** Medium - Dramatically improves navigation
**Effort:** 1-2 hours

---

### 2. Code Quality (Medium Impact)

#### 2.1 Reduce Duplicate Code

Currently there are 6 nearly-identical functions for setting code system selects:
- `setModalCodeSystem()`
- `setModalFilterSystem()`
- `setModalEntireSystem()`
- `setCodeSystem()`
- `setFilterSystem()`
- `setEntireSystem()`

**Consolidate into:**
```javascript
function setSystemSelect(selectId, customInputId, systemValue) {
    const select = document.getElementById(selectId);
    const customInput = document.getElementById(customInputId);

    const isCustom = !Array.from(select.options)
        .some(opt => opt.value === systemValue && opt.value !== 'custom');

    if (isCustom) {
        select.value = 'custom';
        customInput.value = systemValue;
        customInput.style.display = 'block';
    } else {
        select.value = systemValue;
        customInput.style.display = 'none';
    }
}

// Usage:
function setModalCodeSystem(system) {
    setSystemSelect('modalCodeSystem', 'modalCustomCodeSystem', system);
}
```

**Issue:** [#29](https://github.com/MattCordell/Stoker/issues/29)
**Impact:** Medium - DRY principle, easier maintenance
**Effort:** 2-3 hours

---

### 3. Security (Low Effort, High Value)

#### 3.1 Add Content Security Policy

Add to `<head>`:
```html
<meta http-equiv="Content-Security-Policy"
      content="default-src 'self';
               script-src 'self' 'unsafe-inline';
               style-src 'self' 'unsafe-inline';
               img-src 'self' data:;
               font-src 'self';
               connect-src 'self';">
```

**Issue:** [#31](https://github.com/MattCordell/Stoker/issues/31)
**Impact:** Medium - Protects against XSS attacks
**Effort:** 30 minutes
**Note:** `unsafe-inline` required for single-file approach

---

### 4. File Structure Improvements (If Adopting Modular Approach)

If you choose Option A (recommended), here's the detailed breakdown:

#### 4.1 Core Modules

**`js/config/constants.js`**
- FHIR constants
- UI constants
- Magic numbers

**`js/state/app-state.js`**
- Centralized state object
- State management helpers

**`js/ui/theme.js`**
- `setTheme()`
- `applyTheme()`
- `updateThemeButtons()`
- `initTheme()`

**`js/ui/focus-trap.js`**
- `getFocusableElements()`
- `createFocusTrap()`
- Focus trap class

**`js/ui/notifications.js`**
- `showNotification()`
- Notification queue management

**`js/ui/modals.js`**
- `openIncludeEditor()`
- `closeIncludeEditor()`
- `showValidationReport()`
- `closeValidationReport()`
- `showDiffReport()`
- `closeDiffReport()`

**`js/forms/copyright-manager.js`**
- `addCopyright()`
- `removeCopyright()`
- `renderCopyrightList()`
- Copyright drag/drop handlers

**`js/forms/include-blocks.js`**
- `addIncludeBlock()`
- `editIncludeBlock()`
- `deleteIncludeBlock()`
- `renderIncludeBlocksList()`
- Include block drag/drop handlers

**`js/forms/metadata-form.js`**
- Form field handlers
- `clearForm()`
- `populateFormFromValueSet()`

**`js/fhir/valueset-builder.js`**
- `generateValueSet()`
- `buildCompose()`
- `buildInclude()`

**`js/fhir/validator.js`**
- `validateValueSet()`
- `validateNameField()`
- FHIR validation rules

**`js/fhir/diff-engine.js`**
- `compareObjects()`
- `renderDiffReport()`
- Diff utilities

**`js/io/file-loader.js`**
- `loadExistingValueSet()`
- `loadCodeListFile()`
- File validation

**`js/io/file-saver.js`**
- `downloadJSON()`
- Filename generation

**`js/io/clipboard.js`**
- `copyToClipboard()`

**`js/utils/debounce.js`**
- `debounce()`
- `debouncedUpdatePreview()`

**`js/utils/dom-helpers.js`**
- DOM manipulation utilities
- Event handling helpers

**`js/main.js`** (Entry point)
```javascript
import { initTheme } from './ui/theme.js';
import { loadConfiguration } from './config/loader.js';
import { initEventHandlers } from './events/handlers.js';
import { updatePreview } from './forms/preview.js';

// Initialize app
async function init() {
    initTheme();
    await loadConfiguration();
    initEventHandlers();
    setDefaultDate();
    updatePreview();
}

init();
```

---

### 5. Testing Strategy (Future Enhancement)

With a modular structure, you can add testing:

```
tests/
├── unit/
│   ├── fhir/
│   │   ├── valueset-builder.test.js
│   │   ├── validator.test.js
│   │   └── diff-engine.test.js
│   ├── utils/
│   │   └── debounce.test.js
│   └── io/
│       └── file-loader.test.js
└── integration/
    └── form-submission.test.js
```

Use simple testing tools:
- **Vitest** or **Jest** for unit tests
- **Playwright** for E2E testing

---

## Migration Strategy

### Phase 1: Immediate Improvements (Option C)
**Timeline:** 1-2 days
**Risk:** Very Low

1. ✅ Extract constants (#25) - 2 hours
2. ✅ Extract magic numbers (#26) - 1 hour
3. ✅ Add section headers (#27) - 1 hour
4. ✅ Consolidate global state (#28) - 3 hours
5. ✅ Reduce duplicate code (#29) - 2 hours
6. ✅ Add CSP (#31) - 30 minutes

**Total:** ~10 hours of work
**Result:** Much more maintainable single file
**No deployment changes required**

### Phase 2: Modular Architecture (Option A)
**Timeline:** 1-2 weeks
**Risk:** Medium

1. Set up build tooling (Vite recommended)
2. Create module structure
3. Extract CSS to separate files
4. Extract JS to modules (one section at a time)
5. Update deployment workflow to build before deploy
6. Test thoroughly

**Result:** Modern development experience, same deployment artifact

### Phase 3: Testing & Documentation
**Timeline:** 1 week
**Risk:** Low

1. Add unit tests for critical functions
2. Add E2E tests for user workflows
3. Add JSDoc comments
4. Update documentation

---

## Outstanding TODO Items in Scope

From [TODO.md](TODO.md), the following are directly related to code organization/quality:

### High Priority
- None (all completed! 🎉)

### Medium Priority
- [x] #25 - Extract constants ⭐ **RECOMMENDED**
- [x] #26 - Extract magic numbers ⭐ **RECOMMENDED**
- [x] #27 - Organize JavaScript with section headers ⭐ **RECOMMENDED**
- [x] #28 - Consolidate global state ⭐ **RECOMMENDED**
- [x] #29 - Reduce duplicate code ⭐ **RECOMMENDED**
- [x] #31 - Add Content Security Policy ⭐ **RECOMMENDED**

### Low Priority
- [ ] #32 - Add feature detection (nice-to-have)
- [ ] #34 - Add JSDoc comments (good for modular approach)
- [ ] #35 - Document complex regex patterns
- [ ] #36 - Document config file schemas
- [ ] #37 - Add manual test checklist

---

## Tool & Framework Recommendations

### If Adopting Option A (Modular with Build):

**Build Tool:** [Vite](https://vitejs.dev/)
- Fast dev server with HMR
- Simple config
- Built-in support for bundling to single file
- No framework required

**Alternative:** [esbuild](https://esbuild.github.io/)
- Extremely fast
- Minimal config
- Great for simple bundling

**Example Vite config:**
```javascript
// vite.config.js
export default {
  build: {
    rollupOptions: {
      output: {
        inlineDynamicImports: true,
        manualChunks: undefined,
      }
    },
    assetsInlineLimit: 100000000, // Inline everything
  }
}
```

### CSS Strategy:
- Keep using CSS custom properties (already doing this ✅)
- Consider PostCSS for auto-prefixing if needed
- Or just keep vanilla CSS (works great)

### Testing (Optional):
- **Vitest** - Fast, Vite-native testing
- **Playwright** - E2E testing that works great with static sites

---

## Performance Considerations

### Current Performance: ✅ Good
- Debounced preview updates
- Efficient DOM rendering
- No unnecessary re-renders

### Potential Improvements with Modular Approach:
1. **Code Splitting** - Load validation/diff code only when needed
2. **Tree Shaking** - Remove unused code (minimal benefit for this app)
3. **Minification** - Reduce file size (~30% reduction typical)
4. **Compression** - Enable gzip/brotli on server (works now too)

### Regression Risks:
- Multiple HTTP requests (Option B) - slower initial load
- Bundle overhead (Option A) - negligible with modern tools

---

## Accessibility Impact

**Current state:** ✅ Excellent
- ARIA labels
- Focus management
- Keyboard navigation
- Screen reader support

**Impact of changes:**
- **Option A/B:** No impact - same HTML output
- **Option C:** No impact - only internal organization

---

## Browser Compatibility

**Current:** Modern browsers only (ES6+, CSS custom properties)

**Impact of changes:**
- **Option A:** Can add transpilation if needed (not recommended)
- **Option B:** Same as current
- **Option C:** No change

**Recommendation:** Keep targeting modern browsers (as documented in TODO.md)

---

## Deployment Impact

### Option A (Modular with Build):
**Changes required:**
1. Update GitHub Actions workflow:
```yaml
- name: Build
  run: npm ci && npm run build

- name: Deploy
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./dist
```

2. Update Netlify config:
```toml
[build]
  command = "npm run build"
  publish = "dist"
```

### Option B (Modular without Build):
**Changes required:**
- Update deployment to include all files (not just index.html)
- No build step needed

### Option C (Enhanced Single File):
**Changes required:**
- None! Same deployment as current

---

## Cost-Benefit Analysis

### Option A: Modular with Build
**Costs:**
- Setup time: 20-40 hours
- Learning curve: Medium
- Added complexity: Build step

**Benefits:**
- Modern development experience
- Easy testing
- Better collaboration
- Long-term maintainability
- Same production artifact

**Recommendation:** ⭐ **Best for long-term**

---

### Option B: Modular without Build
**Costs:**
- Setup time: 10-20 hours
- Learning curve: Low
- Multiple files to manage

**Benefits:**
- Modular development
- No build complexity
- Simple deployment

**Recommendation:** Good compromise

---

### Option C: Enhanced Single File
**Costs:**
- Setup time: 10 hours (just TODO items)
- Learning curve: None
- Still a large file

**Benefits:**
- Quick wins
- No deployment changes
- Low risk

**Recommendation:** ⭐ **Do this FIRST, then decide**

---

## Final Recommendations

### Immediate Action (This Week)
**Implement Option C enhancements:**
1. Extract constants (#25)
2. Extract magic numbers (#26)
3. Add section headers (#27)
4. Consolidate global state (#28)
5. Reduce duplicate code (#29)
6. Add CSP (#31)

**Effort:** ~10 hours
**Risk:** Very low
**Impact:** High

### Short Term (Next Month)
**Evaluate transition to Option A:**
1. Create a proof-of-concept branch with Vite
2. Migrate one section (e.g., theme management)
3. Test build output matches current functionality
4. Decide whether to continue

**Effort:** 4-8 hours POC
**Risk:** Low (on separate branch)

### Long Term (Next Quarter)
**If Option A POC successful:**
1. Full migration to modular structure
2. Add testing infrastructure
3. Improve documentation
4. Consider additional enhancements

**Effort:** 40-60 hours total
**Risk:** Medium
**Impact:** Very high

---

## Questions to Consider

1. **How often do you expect to add major new features?**
   - Rarely → Option C is sufficient
   - Frequently → Option A is worth it

2. **Will multiple people be contributing?**
   - Solo → Option C or B works fine
   - Team → Option A reduces conflicts

3. **Is testing important for this project?**
   - Not critical → Option C or B
   - Important → Option A enables testing

4. **How much time can you invest?**
   - Limited → Start with Option C
   - Plenty → Go for Option A

5. **Are you comfortable with build tools?**
   - No → Option C or B
   - Yes → Option A is straightforward

---

## Conclusion

**My Recommendation:**

1. ✅ **Start with Option C** (Enhanced Single File)
   - Complete issues #25-29, #31 from TODO.md
   - Low risk, high impact
   - Can be done in 1-2 days
   - Makes the codebase much more maintainable

2. 🤔 **Then Evaluate Option A** (Modular with Build)
   - Create POC after Option C is complete
   - Test that it produces identical output
   - Decide based on your comfort level and project needs

3. 📅 **Consider Option B** as fallback
   - If Option A feels too complex
   - Provides many benefits without build step
   - Good middle ground

The current codebase is well-written and functional. The single-file approach has served you well up to 3,781 lines. However, crossing the 4,000-line threshold is a good time to invest in better organization. **Start with the quick wins from Option C**, then reassess whether a full modular architecture is worth the investment.

---

## Additional Resources

- [FHIR R4 ValueSet Specification](https://hl7.org/fhir/R4/valueset.html)
- [Vite Documentation](https://vitejs.dev/)
- [ES Modules Browser Support](https://caniuse.com/es6-module)
- [Content Security Policy Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

---

**End of Review**
