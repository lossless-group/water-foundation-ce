---
date_created: 2025-08-11
title: "Water Foundation Website Implementation Plan"
lede: "A phased approach to building a scalable, multi-client water foundation platform"
status: Draft
category: Planning
authors:
  - Michael Staton
---

# Water Foundation Website Implementation Plan

## Executive Summary

This document outlines a systematic approach to implementing the Water Foundation website, prioritizing reusable components, scalable architecture, and multi-client theming capabilities.

## Tech Stack

### Core Framework
- **Astro** for static site generation with islands architecture  
- **Tailwind CSS** with custom design tokens and theme modifications
- **Vanilla JavaScript** for interactivity (no React/JSX/TSX)
- **Astro components** (.astro files) for templating
- **Embedded CMS for easy Content Editing**

### Supporting Libraries
- **CSS custom properties** for dynamic theming with Tailwind
- **Native JavaScript** for all interactions
- **Web Components** for complex interactive elements when needed
- **Vitest** for unit and integration testing
- **Playwright** for E2E testing

## Step 1: Foundation

### 1.1 Project Setup
- [x] Create Astro project
- [ ] Install dependencies
```bash
pnpm create astro@latest water-foundation
cd water-foundation
# Choose "Empty" template, include Tailwind
pnpm install

# Install testing dependencies
pnpm add -D vitest @vitest/ui happy-dom @testing-library/dom
pnpm add tailwindcss@latest
```

### 1.2 Design System Implementation

#### Tasks:
1. **Configure Tailwind with custom theme**
   - Create `water-theme.css` file with appropriate variable names and stand in values
   - Set up CSS custom properties in `/src/styles/global.css`
   - Modify `tailwind.config.mjs` with custom design tokens
   - Create theme switcher using data attributes
   - Implement `defaultTheme` and `client1Theme` CSS files

2. **Build Text Component System**
   ```
   /src/components/base/
     - DisplayText.astro
      - ContextSetter.astro
      - IconHeaderInline.astro
      - IconHeaderStacked.astro
      - Heading.astro
      - Subheading.astro
      - Paragraph.astro
      - Link.astro
      - List.astro
      - UnorderedList.astro
      - OrderedList.astro
      - Quote.astro
      - Citation.astro
      - CitationList.astro
      - MetricValue.astro
      - MetricExplainer.astro
   ```

3. **Establish Base Components**
   ```
   /src/components/base/
     - Card.astro
     - Button.astro
     - Container.astro
   ```

### 1.3 Validation Checkpoints
- [ ] Theme switching works (light/dark) via data attributes
- [ ] Text scales properly across breakpoints
- [ ] Tailwind classes integrate with CSS custom properties
- [ ] Vitest runs successfully with example test

## Step 2: Core Patterns

### 2.1 Gallery Pattern Implementation

Create reusable gallery system:

```
/src/components/patterns/
  - Gallery/
    - GalleryWrapper.astro
    - GalleryGrid.astro
    - GalleryCard.astro
    - InteractionsMenu.astro
```

**Implementation Order:**
1. Build generic `GalleryWrapper` with props for:
   - `cardComponent` (custom card renderer)
   - `data` array
   - `columns` (responsive grid)
   - `onCardClick` handler

2. Create specialized galleries:
   - `EventsGallery` extends GalleryWrapper
   - `ProjectGallery` extends GalleryWrapper
   - `CaseGallery` extends GalleryWrapper

### 2.2 Carousel Pattern

```
/src/components/patterns/
  - Carousel/
    - CarouselBase.astro
    - CarouselControls.astro
    - CarouselIndicators.astro
    - carousel.js (vanilla JS for interactions)
```

### 2.3 List Pattern

```
/src/components/patterns/
  - List/
    - ListContainer.astro
    - ListItem.astro
    - UpcomingSection.astro
```

## Step 3: Domain Components

### 3.1 Content Cards

Build domain-specific cards:

```
/src/components/domain/
  - events/
    - EventCard.astro
    - EventListItem.astro
    - UpcomingEventsSection.astro
  - projects/
    - ProjectCard.astro
    - ProjectPage.astro
  - cases/
    - CaseCard.astro
    - CasePage.astro
  - metrics/
    - MetricCard.astro
```

### 3.2 Hero Components

```
/src/components/domain/hero/
  - HeroSection.astro
  - GifCarousel.astro
  - CaseByCaseFlipper.astro
  - flipper.js (vanilla JS animations)
```

### 3.3 Data Models & API

Define data structures:

```
/src/types/
  - event.js
  - project.js
  - case.js
  - portfolio.js
  - person.js
```

Set up API routes or CMS integration:

```
/src/pages/api/
  - events.js
  - projects.js
  - cases.js
```

### 3.4 Testing Domain Components

Example test structure with Vitest:

```javascript
// src/tests/unit/MetricCard.test.js
import { describe, it, expect } from 'vitest';
import { render } from '@testing-library/dom';

describe('MetricCard', () => {
  it('displays metric value and explainer text', () => {
    const metricData = {
      value: '1.2M',
      explainer: 'Gallons of water saved'
    };
    
    // Test rendering logic
    const element = document.createElement('div');
    element.innerHTML = `
      <div class="metric-card">
        <span class="metric-value">${metricData.value}</span>
        <span class="metric-explainer">${metricData.explainer}</span>
      </div>
    `;
    
    expect(element.querySelector('.metric-value').textContent).toBe('1.2M');
    expect(element.querySelector('.metric-explainer').textContent).toBe('Gallons of water saved');
  });
});
```

## Step 4: Complex Features

### 4.1 Calendar & Booking System

```
/src/components/domain/calendar/
  - DiaryCalendar.astro
  - BusyFreeIndicator.astro
  - BookingModal.astro
  - ItineraryView.astro
  - calendar.js (vanilla JS for interactions)
```

**Key Considerations:**
- Integrate with calendar API (Google Calendar, Calendly)
- Handle timezone conversions
- Implement conflict detection

### 4.2 Relationship Matrix

```
/src/components/domain/matrix/
  - RelationshipMatrix.astro
  - MatrixCell.astro
  - AudienceRow.astro
```

**Implementation Notes:**
- Use CSS Grid or table for layout
- Implement filtering/sorting
- Add export functionality

### 4.3 Portfolio System

```
/src/components/domain/portfolio/
  - PortfolioGallery.astro
  - PortfolioEntity.astro
  - InvestorDashboard.astro
```

## Step 5: Page Assembly

### 5.1 Page Templates

```
/src/pages/
  - index.astro (Home with Hero)
  - mission.astro
  - events.astro
  - projects.astro
  - research/
    - cases.astro
    - water-facts.astro
  - portfolio.astro
  - team.astro
```

### 5.2 Navigation & Layout

```
/src/components/layout/
  - Header.astro (with Jumbotron Popover)
  - Navigation.astro
  - Footer.astro
  - Layout.astro
```

## Step 6: Enhancement & Optimization

### 6.1 Performance Optimization
- Implement lazy loading for galleries
- Add image optimization (Astro Image)
- Set up incremental static regeneration
- Implement proper caching strategies

### 6.2 SEO & Accessibility
- Add meta tags and OpenGraph data
- Implement proper heading hierarchy
- Ensure WCAG 2.1 AA compliance
- Add proper ARIA labels

### 6.3 Progressive Enhancement
- Add search functionality
- Implement filters for galleries
- Add pagination for large datasets
- Create loading and error states

## Step 7: Testing & Deployment

### 7.1 Testing Strategy

```bash
/src/tests/
  - unit/          # Component tests with Vitest
  - integration/   # API and feature tests with Vitest
  - e2e/          # User journey tests with Playwright
  - fixtures/     # Test data and mocks
```

**Vitest Configuration:**
```javascript
// vitest.config.js
import { defineConfig } from 'vite';
import { getViteConfig } from 'astro/config';

export default defineConfig(
  getViteConfig({
    test: {
      globals: true,
      environment: 'happy-dom',
      coverage: {
        provider: 'v8',
        reporter: ['text', 'html'],
      },
    },
  })
);
```

**Testing Checklist:**
- [ ] Component unit tests with Vitest
- [ ] Astro component tests using experimental test utilities
- [ ] Integration tests for API routes with Vitest
- [ ] E2E tests with Playwright for critical user paths
- [ ] Visual regression testing
- [ ] Performance testing (Lighthouse)
- [ ] Accessibility testing (axe-core)

### 7.2 Deployment Pipeline

1. **Staging Environment**
   - Deploy to Vercel/Netlify preview
   - Run automated tests
   - Client review and feedback

2. **Production Deployment**
   - Set up CI/CD pipeline
   - Configure environment variables
   - Set up monitoring (Sentry, Analytics)
   - Implement CDN for assets

## Risk Mitigation

### Technical Risks
1. **Complex Calendar Integration**
   - Mitigation: Start with simple availability display, add booking later
   
2. **Multi-client Theming Complexity**
   - Mitigation: Build with single theme first, abstract later

3. **Performance with Large Galleries**
   - Mitigation: Implement virtualization early

### Content Risks
1. **Missing Content/Assets**
   - Mitigation: Use placeholder content, build CMS integration early

2. **Unclear Requirements**
   - Mitigation: Build MVPs for review, iterate based on feedback

## Success Metrics

### Technical Metrics
- Lighthouse score > 90
- First Contentful Paint < 1.5s
- Time to Interactive < 3s
- 0 critical accessibility issues

### Business Metrics
- Support for 3+ client themes
- All component patterns reusable
- CMS integration for non-technical updates
- Mobile-responsive across all breakpoints

## Development Workflow

### Progress Checkpoints

#### After Step 1: Foundation
- Basic components working
- Theme system operational
- TypeScript configured

#### After Step 2: Core Patterns
- Gallery pattern fully reusable
- Carousel functioning
- List components complete

#### After Step 3: Domain Components
- All content cards built
- Hero section animated
- Data models defined

#### After Step 4: Complex Features
- Calendar system functional
- Matrix displaying data
- Portfolio galleries working

#### After Step 5: Page Assembly
- All pages accessible
- Navigation complete
- Layout responsive

#### After Step 6: Enhancement
- Performance optimized
- SEO implemented
- Accessibility verified

#### After Step 7: Testing & Deployment
- All tests passing
- Deployed to production
- Monitoring active

## Notes for AI Assistant Implementation

When implementing with an AI assistant, provide these context documents:
1. This implementation plan
2. Component specifications (`Content-Component-List.md`)
3. Theme specifications (`Styles-and-Themes.md`)
4. Text component specs (`Text-Components.md`)

### Prompt Strategy
1. Start each session by setting up the project structure
2. Build components in dependency order (base → patterns → domain)
3. Test each component in isolation before integration
4. Request code reviews after each major component

### Critical Path Items
These must be built in order:
1. Theme provider → Text components → Card → Gallery
2. Data models → API routes → Domain components
3. Layout → Pages → Navigation

### Recommended Session Structure
```
Session 1: Complete Step 1 (Foundation)
Session 2: Complete Step 2 (Core Patterns)
Session 3: Complete Step 3 (Domain Components)
Session 4: Complete Step 4 (Complex Features)
Session 5: Complete Step 5 (Page Assembly)
Session 6: Complete Steps 6-7 (Enhancement & Deployment)
```

## Implementation Dependencies Graph

```
Foundation (Step 1)
    ├── Core Patterns (Step 2)
    │   ├── Domain Components (Step 3)
    │   │   ├── Complex Features (Step 4)
    │   │   └── Page Assembly (Step 5)
    │       └── Enhancement (Step 6)
    │           └── Testing & Deployment (Step 7)
```

## Conclusion

This implementation plan provides a structured approach to building the Water Foundation website. The step-by-step approach ensures that foundational elements are solid before building complex features, and the emphasis on reusable patterns will accelerate development in later phases.

The key to success is maintaining discipline in the early steps - resist the temptation to jump ahead to flashy features before the foundation is solid. Each step builds upon the previous one, creating a robust and maintainable application.