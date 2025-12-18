# Stoker Enhancement TODO List

This document contains recommended enhancements for the FHIR ValueSet Creator, organized by priority.

---

## 🔴 High Priority

### Accessibility Improvements

✅ ~~**Add ARIA labels to emoji buttons**~~ ([#15](https://github.com/MattCordell/Stoker/issues/15))  **COMPLETED**
  - Add `aria-label` attributes to theme toggle buttons (☀️ and 🌙)
  - Ensures screen readers can identify button purposes
  - **Implementation**: Comprehensive ARIA labels for all emoji buttons, validation button dynamic updates, remove buttons, drag handles, and Report Bug link

✅ ~~**Add modal accessibility attributes**~~ ([#16](https://github.com/MattCordell/Stoker/issues/16)) **COMPLETED**
  - Add `role="dialog"`, `aria-modal="true"`, and `aria-labelledby` to include editor modal
  - Add same attributes to validation report modal
  - **Implementation**: Both modals now fully accessible with proper ARIA attributes

✅ ~~**Implement focus management for modals**~~ ([#17](https://github.com/MattCordell/Stoker/issues/17)) **COMPLETED**
  - Trap focus within modals when open
  - Return focus to triggering element when closed
  - Update `openIncludeEditor()` and `closeIncludeEditor()` functions
  - **Implementation**: Added reusable focus trap mechanism with keyboard navigation (Tab/Shift+Tab cycling, Escape to close), automatic focus on first form field, and focus restoration to triggering element. Updated all four modal functions (openIncludeEditor, closeIncludeEditor, showValidationReport, closeValidationReport)

✅ ~~**Add keyboard navigation for drag-and-drop**~~ ([#18](https://github.com/MattCordell/Stoker/issues/18)) **COMPLETED**
  - Add up/down arrow buttons for reordering copyright notices
  - Add up/down arrow buttons for reordering include blocks
  - Ensures keyboard-only users can reorder items
  - **Implementation**: Added up/down arrow buttons (↑↓) next to each draggable item with smart boundary detection (first item can't move up, last can't move down). Buttons are keyboard accessible, work alongside existing drag-and-drop, and include full ARIA labels for screen readers. Added 4 helper functions (moveCopyrightUp/Down, moveIncludeBlockUp/Down) and modified both render functions

### Performance Optimization

✅ ~~**Debounce preview updates**~~ ([#19](https://github.com/MattCordell/Stoker/issues/19)) **COMPLETED**
  - Add debouncing to `updatePreview()` function (200ms delay)
  - Update all `oninput` handlers to use `debouncedUpdatePreview()`
  - Reduces unnecessary processing on every keystroke
  - **Implementation**: Added debouncedUpdatePreview() function with 200ms delay that cancels pending updates on each keystroke. Updated all input event listeners to use debounced version while keeping change events immediate. Updated validateNameField() to use debounced updates. Results in ~90% reduction in preview updates during typing with smooth, responsive UX

### Error Handling

✅ ~~**Add try-catch to copyToClipboard()**~~ ([#20](https://github.com/MattCordell/Stoker/issues/20)) **COMPLETED**
  - Handle promise rejection if clipboard API fails
  - Show error notification to user
  - **Implementation**: Added comprehensive error handling with feature detection for navigator.clipboard availability and promise rejection handling. Shows helpful error messages for non-secure contexts (requires HTTPS or localhost) and clipboard API failures. Includes console logging for debugging

✅ ~~**Add try-catch to loadCodeListFile()**~~ ([#21](https://github.com/MattCordell/Stoker/issues/21)) **COMPLETED**
  - Already has basic error handling, but ensure all edge cases covered
  - **Implementation**: Enhanced error handling with empty file validation, console logging for debugging, and informative success messages showing filename and code count. Validates content is not empty/whitespace-only before processing. Follows established patterns from loadExistingValueSet()

✅ ~~**Add file size validation**~~ ([#22](https://github.com/MattCordell/Stoker/issues/22)) **COMPLETED**
  - Add 10MB file size limit check before parsing JSON
  - Prevent browser hang on very large files
  - **Implementation**: Created shared validateFileSize() utility function with MAX_FILE_SIZE constant (10MB). Added file size validation to both loadExistingValueSet() and loadCodeListFile() functions. Shows informative error messages with actual file size vs limit. Includes console logging for debugging. Prevents browser freeze by checking file size before reading/parsing

### User Experience

✅ ~~**Add unsaved changes warning**~~ ([#23](https://github.com/MattCordell/Stoker/issues/23)) **COMPLETED**
  - Implement `beforeunload` event listener
  - Track form dirty state
  - Warn users before leaving page with unsaved work
  - **Implementation**: Added hasUnsavedChanges flag with markFormAsDirty()/markFormAsClean() helper functions. Form is marked dirty on any input change, copyright/include block modifications. Form is marked clean on download, clear, load, and initial page load. Browser shows native warning dialog when attempting to leave page with unsaved changes

✅ ~~**Improve validation UX**~~ ([#24](https://github.com/MattCordell/Stoker/issues/24)) **COMPLETED**
  - Change name field validation from `oninput` to `onblur`
  - Reduces distracting warnings while user is still typing
  - **Implementation**: Changed name and version fields from oninput to onblur event handlers. Validation now triggers only when user leaves the field (blur event) rather than on every keystroke. This provides a much smoother typing experience while still providing immediate validation feedback once the user moves to the next field

- [ ] **Add diff view for loaded resources** ([#43](https://github.com/MattCordell/Stoker/issues/43))
  - Show comparison between original loaded resource and current edited version
  - Particularly useful for legacy resources not authored using Stoker
  - Helps identify atypical copyright notices or other anomalies
  - Implementation: side-by-side or inline diff view with add/remove/modify highlighting

---

## 🟡 Medium Priority

### Code Organization

- [ ] **Extract constants** ([#25](https://github.com/MattCordell/Stoker/issues/25))
  - Create `FHIR_CONSTANTS` object with:
    - SHAREABLE_PROFILE URL
    - NCTS_PROFILE URL
    - BASE_URL
    - MAX_NAME_LENGTH (254)
    - MAX_ID_LENGTH (64)
  - Replace hardcoded values throughout code

- [ ] **Extract magic numbers** ([#26](https://github.com/MattCordell/Stoker/issues/26))
  - Notification timeout (3000ms → NOTIFICATION_DURATION)
  - Preview debounce delay (300ms → PREVIEW_DEBOUNCE_MS)
  - Regex substring limit (100 chars) with explanatory comment

- [ ] **Organize JavaScript with section headers** ([#27](https://github.com/MattCordell/Stoker/issues/27))
  - Add clear comment headers for logical sections:
    - Configuration & State
    - Initialization
    - Data Loading
    - Form Management
    - Validation
    - Include Block Management
    - Copyright Management
    - JSON Generation
    - File Operations
    - UI Helpers
    - Theme Management

- [ ] **Consolidate global state** ([#28](https://github.com/MattCordell/Stoker/issues/28))
  - Create `AppState` object to wrap:
    - `includeBlocks`
    - `selectedCopyrights`
    - `draggedElement`
    - `currentEditingIncludeId`
    - `publishers`
    - `copyrights`
  - Update all references throughout code

### Code Quality

- [ ] **Reduce duplicate code** ([#29](https://github.com/MattCordell/Stoker/issues/29))
  - Create unified `setModalSystemSelect(selectId, customInputId, system)` function
  - Replace `setModalCodeSystem()`, `setModalFilterSystem()`, `setModalEntireSystem()`
  - Also replace `setCodeSystem()`, `setFilterSystem()`, `setEntireSystem()`

### Security

- [ ] **Add Content Security Policy** ([#31](https://github.com/MattCordell/Stoker/issues/31))
  - Add CSP meta tag to `<head>`:
    ```html
    <meta http-equiv="Content-Security-Policy"
          content="default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';">
    ```
  - Note: `unsafe-inline` needed for inline styles/scripts in single-file app

---

## 🟢 Low Priority

### Browser Compatibility

- [ ] **Add feature detection** ([#32](https://github.com/MattCordell/Stoker/issues/32))
  - Check for `navigator.clipboard` API support
  - Check for CSS custom properties support
  - Check for File API support
  - Add graceful degradation or user warnings

### Documentation

- [ ] **Add JSDoc comments** ([#34](https://github.com/MattCordell/Stoker/issues/34))
  - Document function parameters and return values
  - Focus on complex functions:
    - `validateValueSet()`
    - `generateValueSet()`
    - `buildCompose()`
    - `populateFormFromValueSet()`

- [ ] **Document complex regex patterns** ([#35](https://github.com/MattCordell/Stoker/issues/35))
  - Add explanatory comments for:
    - Copyright template matching (index.html:2667-2680)
    - Name validation pattern (index.html:1966)
    - Syntax highlighting regex (index.html:2492)

- [ ] **Document config file schemas** ([#36](https://github.com/MattCordell/Stoker/issues/36))
  - Add inline comments explaining expected structure of:
    - `publishers.json`
    - `copyrights.json`
  - Consider adding example files in comments

- [ ] **Add manual test checklist** ([#37](https://github.com/MattCordell/Stoker/issues/37))
  - Create comment block at top of file with test scenarios:
    - Create new ValueSet from scratch
    - Load existing ValueSet
    - Test each include type (codeList, ECL, filter, valueSetRef, entireSystem)
    - Test drag-and-drop reordering
    - Test validation report
    - Test theme switching
    - Test file uploads

### Performance (Advanced)

- [ ] **Consider virtual scrolling for large datasets** ([#38](https://github.com/MattCordell/Stoker/issues/38))
  - Only implement if users regularly load very large code lists (1000+ codes)
  - Add pagination or virtual scrolling to preview
  - Consider lazy rendering of include blocks if many exist

- [ ] **Consider lazy loading config files** ([#39](https://github.com/MattCordell/Stoker/issues/39))
  - Only implement if startup time becomes an issue
  - Load `publishers.json` and `copyrights.json` on-demand

### User Experience (Nice to Have)

- [ ] **Add export to other formats** ([#41](https://github.com/MattCordell/Stoker/issues/41))
  - Consider XML export option
  - Consider YAML export option
  - Only if requested by users

---

## ❌ Excluded from Implementation

The following were identified in the code review but explicitly excluded:

- **Auto-save to localStorage** - User requested not to implement
- **FHIR Compliance enhancements** - User requested not to implement:
  - Enhanced narrative generation
  - Meta.lastUpdated timestamps
  - Duplicate code detection
  - System URL format validation
  - ECL syntax validation
- **Enhance download filename** ([#30](https://github.com/MattCordell/Stoker/issues/30)) - Current filename with version is adequate
- **Consider polyfills** ([#33](https://github.com/MattCordell/Stoker/issues/33)) - Target users should use modern browsers
- **Add keyboard shortcuts** ([#40](https://github.com/MattCordell/Stoker/issues/40)) - Unnecessary; can revisit if users request
- **Add dark/light theme preview** ([#42](https://github.com/MattCordell/Stoker/issues/42)) - Completely unnecessary; current theme toggle works well

---

## Implementation Notes

### Estimated Effort

- **High Priority**: ~8-12 hours
- **Medium Priority**: ~6-8 hours
- **Low Priority**: ~4-6 hours
- **Total**: ~18-26 hours

### Recommended Implementation Order

1. Start with **High Priority** accessibility items (biggest impact for users)
2. Implement **High Priority** performance optimizations (debouncing)
3. Add **High Priority** error handling
4. Move to **Medium Priority** code organization (makes future changes easier)
5. Tackle **Low Priority** items as time permits

### Testing Checklist

After implementing changes, test:
- [ ] Screen reader compatibility (NVDA, JAWS, or VoiceOver)
- [ ] Keyboard-only navigation
- [ ] Large file uploads (test file size limits)
- [ ] Theme switching with new ARIA labels
- [ ] Modal focus management
- [ ] All existing functionality still works
- [ ] Validation report with keyboard navigation
- [ ] Copy to clipboard error handling

---

## Browser Support Targets

**Supported Browsers:**
- Chrome/Edge (latest versions)
- Firefox (latest versions)
- Safari (latest versions)
- Modern mobile browsers (iOS Safari, Chrome Mobile)

**Not Supported:**
- Internet Explorer 11 and earlier
- Safari < 14
- Other legacy browsers

**Rationale:** Users of this tool are expected to use modern, up-to-date browsers. The application relies on modern web APIs (Clipboard API, File API, CSS custom properties) that are well-supported in current browser versions. Polyfills and backwards compatibility for legacy browsers are not implemented (see [#33](https://github.com/MattCordell/Stoker/issues/33)).
