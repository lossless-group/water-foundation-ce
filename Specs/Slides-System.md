---
title: Port Interactive Slides System to TWF Site
lede: Technical specification for porting the existing slides system from Lossless Site to TWF site, enabling presentation-as-code functionality in the new environment.
date_authored_initial_draft: 2025-08-13
date_authored_current_draft: 2025-08-13
date_authored_final_draft: 
date_first_published: 
date_last_updated: 
at_semantic_version: 0.1.0
status: Draft
augmented_with: Windsurf Cascade on Claude 3.5 Sonnet
category: Technical Specification
date_created: 2025-08-13
date_modified: 2025-08-13
tags: [Slides, Presentations, Astro, RevealJS, Content-Management, TWF-Site]
authors:
  - Michael Staton
---

# Port Interactive Slides System to TWF Site

## 1. Executive Summary

This specification outlines the plan to port the existing Interactive Slides System from the Lossless Site (`/site/`) to the TWF site (`/astro-knots/twf-site/`). The system enables creation and management of presentations using Markdown, HTML, and Astro components with RevealJS as the presentation engine.

## 2. Current System Analysis

### 2.1 Source System (Lossless Site)
**Location**: `/Users/mpstaton/code/lossless-monorepo/site/src/pages/slides/

**Key Components**:
- `OneSlideDeck.astro` - RevealJS layout wrapper
- `index.astro` - Slides directory/listing page
- `[collection]/[...slug].astro` - Dynamic slide routing
- `SildeElementsAnimationWrapper.astro` - Slide element animation component, for elements in the slides. 
- `SildeTransitionAnimationWrapper.astro` - Slide transition animation component, for the entire slide from one to the next.
- `SildeNavigation.astro` - Slide navigation component, for navigation between slides.
- `SildeControls.astro` - Slide controls component, for controls on the slide.
- `SildeThemeApplier.astro` - Slide theme component, for theming the slide.
- Individual slide files (`.astro` format)
- Individual slide files (`.md` format)

**REALISTIC v1 Features (Single Day Implementation)**:
- ✅ RevealJS 4.5.0 integration
- ✅ Four-way navigation (grid mode)
- ✅ Custom theme application with CSS variables (water-theme.css + global.css)
- ✅ Basic control buttons (exit, restart)
- ✅ Responsive design (16:9 aspect ratio)
- ✅ Syntax highlighting for code blocks
- ✅ Basic .astro slide file rendering
- ⚠️ Light/Dark mode transition (if Water theme supports it)
- ⚠️ Full-screen expand/collapse (browser fullscreen API)

**MOVED TO v2 (Too Complex for Single Day)**:
- 🔄 Extensible Component-Architecture for Slide Elements
- 🔄 Supporting Explainer Article paired by slide
- 🔄 Explainer article reveal/hide controls
- 🔄 Embeddable in Markdown files with custom identifier
- 🔄 Advanced slide element animations

**Wish List for future versions unless easily integrated for v1**:
- CSV embeds to tables.
- Speaker notes support
- Markdown content rendering capability
- CSV Embed to Tables
- Master Variables embed to component.
- Svelte component for more interactivity and Animation.
- Element Animation Components
- Slide Transition Animation Components
- Export capabilities
   - PDF export capability
   - PPTX export capability
   - Keynote export capability

### 2.2 Target System (TWF Site)
**Location**: `/Users/mpstaton/code/lossless-monorepo/astro-knots/twf-site/`

**Current Structure**:
- Astro-based project with TypeScript
- Tailwind CSS for styling
- Component-based architecture
- Content collections support

## 3. Implementation Plan

### 3.1 Phase 1: Core Infrastructure Setup

#### 3.1.1 Directory Structure Creation
```
astro-knots/twf-site/src/
├── layouts/
│   └── OneSlideDeck.astro          # Port from source
├── pages/
│   └── slides/
│       ├── index.astro             # Slides listing page
│       ├── [collection]/
│       │   └── [...slug].astro     # Dynamic routing
│       └── pdf.astro               # PDF export
├── content/
│   └── slides/                     # Slide content directory
│       ├── config.ts               # Content collection config
│       └── [slide-files]           # Individual presentations
└── components/
    └── slides/                     # Slide-specific components
        ├── SlideNavigation.astro
        ├── SlideControls.astro
        └── SlideTheme.astro
```

#### 3.1.2 Dependencies Assessment
**Required Dependencies** (to be added to `package.json`):
- RevealJS 4.5.0 (CDN-based, no npm install needed)
- Existing Astro/TypeScript/Tailwind stack sufficient

#### 3.1.3 Configuration Updates
**Files to modify**:
- `astro.config.mjs` - Add slides routing configuration
- `tailwind.config.js` - Add slides-specific utilities
- `tsconfig.json` - Ensure proper TypeScript support

### 3.2 Phase 2: Component Porting

#### 3.2.1 Layout Component (`OneSlideDeck.astro`)
**Source**: `/site/src/layouts/OneSlideDeck.astro`
**Target**: `/astro-knots/twf-site/src/layouts/OneSlideDeck.astro`

**Modifications Required**:
- Update CSS variables to match TWF site theme
- Adjust control button styling for TWF branding
- Ensure compatibility with TWF site's base layout
- Maintain RevealJS configuration and functionality

#### 3.2.2 Page Components
**Components to port**:
1. **Slides Index** (`pages/slides/index.astro`)
   - List available presentations
   - Provide navigation to individual decks
   - Style to match TWF site design

2. **Dynamic Routing** (`pages/slides/[collection]/[...slug].astro`)
   - Handle slide deck URLs
   - Support both collection-based and direct file access
   - Maintain backward compatibility

3. **PDF Export** (`pages/slides/pdf.astro`)
   - Preserve PDF generation functionality
   - Ensure print styles work with TWF theming

### 3.3 Phase 3: Content Integration

#### 3.3.1 Content Collections Setup
**File**: `/astro-knots/twf-site/src/content/config.ts`

```typescript
// Add slides collection configuration
import { defineCollection, z } from 'astro:content';

const slides = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string().optional(),
    author: z.string().optional(),
    date: z.date().optional(),
    theme: z.string().default('default'),
    transition: z.string().default('slide'),
    layout: z.string().default('center'),
  }),
});

export const collections = {
  slides,
  // ... other existing collections
};
```

#### 3.3.2 Sample Content Migration
**Create initial slide deck** to test the system:
- Port one existing presentation from Lossless Site
- Ensure all features work correctly
- Document any issues or required adjustments

### 3.4 Phase 4: Styling and Theming

#### 3.4.1 TWF Site Theme Integration
**Objectives**:
- Match TWF site's color scheme and typography
- Ensure consistent branding across presentations
- Maintain RevealJS functionality while applying custom styles

**CSS Variables to Update**:
```css
:root {
  --slide-bg-primary: /* TWF primary background */;
  --slide-text-primary: /* TWF primary text */;
  --slide-accent: /* TWF accent color */;
  --slide-font-family: /* TWF font stack */;
}
```

#### 3.4.2 Responsive Design
- Ensure presentations work on TWF site's supported devices
   - Ensure mobile portrait and landscape view. 
   - Ensure desktop view.
- Test control button positioning
- Verify PDF export styling

### 3.5 Phase 5: Testing and Validation

#### 3.5.1 Functionality Testing
- [ ] Slide navigation (arrow keys, touch, buttons)
- [ ] Supporting Explainer Article reveal/hide
- [ ] Responsive behavior
- [ ] Control buttons (exit, restart, explainer article reveal/hide)

#### 3.5.2 Content Testing
- [ ] Astro component slides
- [ ] Markdown slide parsing
- [ ] Mixed content presentations
- [ ] Asset loading from url (images, videos)

#### 3.5.3 Integration Testing
- [ ] TWF site theme direct application
- [ ] Navigation between site and slides

## 4. Technical Considerations

### 4.1 Routing Strategy
**Current Lossless Site**: `/slides/[collection]/[...slug]`
**Proposed TWF Site**: `/slides/[collection]/[...slug]` (maintain consistency)

### 4.2 Asset Management
- Internal slide assets should be stored in `/content/slides/`
- create twf-content submodule outside of src folder, use astro.config to include it in the build process.
- Internal images and media files need proper path resolution
- Consider and explore CDN strategy for large presentation assets

### 4.3 Performance Optimization
- Lazy load RevealJS libraries
- Optimize slide assets for web delivery
- Consider preloading for better UX
- Use Svelte for better interactivity and animation performance.

### 4.4 SEO and Accessibility
- Ensure slide content is crawlable
- Implement proper ARIA labels
- Provide keyboard navigation support
- Include alt text for presentation images

## 5. Migration Checklist

### 5.1 Pre-Migration
- [ ] Backup current TWF site state
- [ ] Document existing TWF site theme variables
   - [ ] Document how to use the `water-theme.css` file
   - [ ] Document how to use the `global.css` file
- [ ] Identify any conflicting CSS classes or IDs
- [ ] Review TWF site's content collection patterns

### 5.2 Migration Steps
- [ ] Create slides directory structure
- [ ] Port `OneSlideDeck.astro` layout
- [ ] Apply Water theme.
- [ ] Port slides page components
- [ ] Configure content collections
- [ ] Update routing configuration
- [ ] Apply TWF site theming
- [ ] Test with sample presentation
- [ ] Validate all functionality

### 5.3 Post-Migration
- [ ] Cross-browser compatibility check
- [ ] Mobile responsiveness verification
- [ ] Documentation update
- [ ] Guide for team on how to generate slide decks in astro, markdown.

## 6. Risk Assessment

### 6.1 Technical Risks
**Risk**: CSS conflicts between TWF site and RevealJS
**Mitigation**: Use CSS scoping and namespace RevealJS styles

**Risk**: Asset path resolution issues
**Mitigation**: Implement proper asset handling and test thoroughly

**Risk**: Performance impact on TWF site
**Mitigation**: Lazy load slides functionality, monitor bundle size
**Mitigation**: Wrap in a SlidesSvelteIsland.astro component, and render slides using Svelte for better interactivity, animation performance.


## 7. Success Criteria

### 7.1 Functional Requirements
- [ ] All existing slide features work in TWF site
- [ ] Slide styles directly pull from and apply the Water theme already in `global.css` and `water-theme.css`
- [ ] Navigation and controls function properly
- [ ] Content collections integrate seamlessly

### 7.2 Design Requirements
- [ ] Slides match TWF site branding and theme
- [ ] Responsive design works across all target devices
- [ ] Typography and spacing consistent with site standards

### 7.3 Performance Requirements
- [ ] Slide loading time < 3 seconds
- [ ] No negative impact on TWF site performance
- [ ] PDF export completes within reasonable time

## 8. Single Day Implementation Timeline (8 hours) - ✅ COMPLETED

**Step 1: Infrastructure Setup** (1.5 hours): ✅ COMPLETED
- [x] Create directory structure in TWF site
- [x] Port basic `OneSlideDeck.astro` layout
- [x] Set up routing (`/slides/[...slug].astro`)
- [x] Configure RevealJS CDN integration

**Step 2: Core Functionality** (2.5 hours): ✅ COMPLETED
- [x] Implement basic slide rendering (.astro files)
- [x] Add four-way navigation (RevealJS grid mode)
- [x] Create control buttons (exit, restart)
- [x] Test basic slide deck functionality

**Step 3: Water Theme Integration** (2 hours): ✅ COMPLETED
- [x] Apply `water-theme.css` and `global.css` to slides
- [x] Ensure responsive design (16:9 aspect ratio)
- [x] Test light/dark mode if supported by Water theme
- [x] Style control buttons to match TWF branding

**Step 4: Polish & Testing** (1.5 hours): ✅ COMPLETED
- [x] Add syntax highlighting for code blocks
- [x] Implement fullscreen toggle (if time permits)
- [x] Test on mobile and desktop
- [x] Create sample slide deck for demonstration

**Step 5: Documentation** (0.5 hours): ✅ COMPLETED
- [x] Document how to create new slide decks
- [x] Update any configuration notes

## 8.1 Implementation Results

**✅ Successfully Delivered v1 Features:**
- RevealJS 4.5.0 integration via CDN
- Water Theme integration with CSS variables
- Four-way navigation (grid mode)
- Control buttons (exit, restart, fullscreen)
- Responsive design (16:9 aspect ratio)
- Syntax highlighting for code blocks
- Sample presentation with Water Theme demonstration
- Content collections configuration
- Complete documentation in README.md

**🚀 Commit Details:**
- **Branch**: `feature/slides`
- **Commit**: `2b2bd0a` - "new(feature, components, page) Implement RevealJS slides system with Water Theme integration"
- **Files Created**: 6 files, 575 insertions
- **Status**: Successfully pushed to GitHub

## 8.2 Next Steps & Recommendations

### Immediate Next Steps (Priority 1)

1. **Iterate on pre-release of v1**
   - [ ] Iterate on style of slides index page.
      - [x] Create `TitleSlidePreviewCard.astro` component. 
   - [ ] Assure theme integration is correct
   - [ ] List possible components
   - [ ] Create and integrate listed components

1b. **Additional Troubleshooting**
   - [ ] Assure mode integration is correct
   
2. **Create Pull Request & Review**
   - Create PR from `feature/slides` to main branch
   - Test the slides system in production environment
   - Verify mobile responsiveness and cross-browser compatibility

3. **Content Creation & Testing**
   - Create real presentation content to replace sample slides
   - Test with actual client presentation content
   - Validate Water Theme integration across different slide types

4. **User Acceptance Testing**
   - Have team members test the slides system
   - Gather feedback on navigation, controls, and visual design
   - Test on various devices (mobile, tablet, desktop)

### Phase 2 Features (Priority 2)

Based on the original wishlist, these features could be implemented next:

1. **Markdown Content Rendering** (Medium Priority)
   - Implement proper Markdown-to-slides conversion
   - Add support for slide separators (`---`) in Markdown files
   - Enable frontmatter-driven slide configuration

2. **Supporting Explainer Articles** (High Priority)
   - Implement paired article system for each slide deck
   - Add reveal/hide toggle for explainer content
   - Create content relationship between slides and articles

3. **Enhanced Content Collections** (Medium Priority)
   - Dynamic slide loading from multiple directories
   - Support for client-specific slide content organization
   - Automated slide deck discovery and listing

### Phase 3 Features (Priority 3)

1. **Advanced Interactions**
   - Svelte component integration for animations
   - Interactive slide elements
   - CSV-to-table embedding
   - Master variables system

2. **Export Capabilities**
   - PDF export functionality
   - PPTX export capability
   - Keynote export capability

### Technical Debt & Improvements

1. **Content Collections Enhancement**
   - Move from hardcoded sample to dynamic content loading
   - Implement proper content validation
   - Add support for slide metadata and tags

2. **Performance Optimization**
   - Implement lazy loading for large presentations
   - Optimize asset loading and caching
   - Monitor bundle size impact

3. **Accessibility Improvements**
   - Add ARIA labels and screen reader support
   - Implement keyboard navigation enhancements
   - Ensure color contrast compliance

### 8.2 Content Risks
**Risk**: Existing slide content incompatibility
**Mitigation**: Create migration scripts and content validation tools

**Risk**: Loss of functionality during port
**Mitigation**: Comprehensive testing plan and feature parity checklist

## 9. Future Enhancements

### 9.1 Content Management
- Markdown-based slide authoring, if not implemented now. 
- YAML frontmatter for slide metadata
- Automated slide generation from content
   - Possible MCP server for this.

### 9.2 Advanced Features
- Export functionality
   - `pdf.astro` - PDF export functionality
   - `pptx.astro` - PPTX export functionality
   - `keynote.astro` - Keynote export functionality

### 9.3 Developer Experience
- Hot reload for slide development
- Slide preview in development mode
- Automated testing for presentations
- Content validation and linting

## 10. Conclusion

This specification provides a comprehensive plan for porting the Interactive Slides System to the TWF site while maintaining all existing functionality and ensuring seamless integration with the target environment. The phased approach minimizes risk and allows for iterative testing and validation.

The estimated timeline of 8-13 days accounts for thorough testing and proper integration with the TWF site's existing architecture and design system. Success will be measured by functional parity with the source system while achieving visual and performance consistency with the TWF site standards.