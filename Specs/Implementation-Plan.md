---
date_created: 2025-08-11
title: "Water Foundation Website Implementation Plan"
lede: "A phased approach to building a scalable, multi-client water foundation platform"
status: Draft
category: Planning
authors:
  - AI Assistant
  - Michael Staton
---

# Water Foundation Website Implementation Plan

## Executive Summary

This document outlines a systematic approach to implementing the Water Foundation website, prioritizing reusable components, scalable architecture, and multi-client theming capabilities.

## Tech Stack Recommendations

### Core Framework
- **Next.js 14+** with App Router for SSR/SSG capabilities
- **TypeScript** for type safety across components
- **Tailwind CSS** with custom design tokens
- **Contentful/Sanity** for headless CMS (water facts, cases, events)

### Supporting Libraries
- **Framer Motion** for animations (CaseByCaseFlipper, carousels)
- **React Query/SWR** for data fetching and caching
- **date-fns** for calendar/scheduling functionality
- **MDX** for rich content rendering

## Step 1: Foundation

### 1.1 Project Setup
```bash
npx create-next-app@latest water-foundation --typescript --tailwind --app
cd water-foundation
npm install framer-motion date-fns @tanstack/react-query
```

### 1.2 Design System Implementation

#### Tasks:
1. **Configure Tailwind with custom theme**
   - Set up `/styles/themes/` directory structure
   - Implement `defaultTheme` and `client1Theme` configurations
   - Create theme provider component with context

2. **Build Text Component System**
   ```typescript
   /components/base/
     - Text.tsx (with role-based sizing)
     - Heading.tsx
     - Paragraph.tsx
   ```

3. **Establish Base Components**
   ```typescript
   /components/base/
     - Card.tsx
     - Button.tsx
     - Container.tsx
   ```

### 1.3 Validation Checkpoints
- [ ] Theme switching works (light/dark)
- [ ] Text scales properly across breakpoints
- [ ] TypeScript types are properly defined

## Step 2: Core Patterns

### 2.1 Gallery Pattern Implementation

Create reusable gallery system:

```typescript
/components/patterns/
  - Gallery/
    - GalleryWrapper.tsx
    - GalleryGrid.tsx
    - GalleryCard.tsx
    - InteractionsMenu.tsx
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

```typescript
/components/patterns/
  - Carousel/
    - CarouselBase.tsx
    - CarouselControls.tsx
    - CarouselIndicators.tsx
```

### 2.3 List Pattern

```typescript
/components/patterns/
  - List/
    - ListContainer.tsx
    - ListItem.tsx
    - UpcomingSection.tsx
```

## Step 3: Domain Components

### 3.1 Content Cards

Build domain-specific cards:

```typescript
/components/domain/
  - events/
    - EventCard.tsx
    - EventListItem.tsx
    - UpcomingEventsSection.tsx
  - projects/
    - ProjectCard.tsx
    - ProjectPage.tsx
  - cases/
    - CaseCard.tsx
    - CasePage.tsx
  - metrics/
    - MetricCard.tsx
```

### 3.2 Hero Components

```typescript
/components/domain/hero/
  - HeroSection.tsx
  - GifCarousel.tsx
  - CaseByCaseFlipper.tsx (with Framer Motion)
```

### 3.3 Data Models & API

Define TypeScript interfaces:

```typescript
/types/
  - event.ts
  - project.ts
  - case.ts
  - portfolio.ts
  - person.ts
```

Set up API routes or CMS integration:

```typescript
/app/api/
  - events/route.ts
  - projects/route.ts
  - cases/route.ts
```

## Step 4: Complex Features

### 4.1 Calendar & Booking System

```typescript
/components/domain/calendar/
  - DiaryCalendar.tsx
  - BusyFreeIndicator.tsx
  - BookingModal.tsx
  - ItineraryView.tsx
```

**Key Considerations:**
- Integrate with calendar API (Google Calendar, Calendly)
- Handle timezone conversions
- Implement conflict detection

### 4.2 Relationship Matrix

```typescript
/components/domain/matrix/
  - RelationshipMatrix.tsx
  - MatrixCell.tsx
  - AudienceRow.tsx
```

**Implementation Notes:**
- Use CSS Grid or table for layout
- Implement filtering/sorting
- Add export functionality

### 4.3 Portfolio System

```typescript
/components/domain/portfolio/
  - PortfolioGallery.tsx
  - PortfolioEntity.tsx
  - InvestorDashboard.tsx
```

## Step 5: Page Assembly

### 5.1 Page Templates

```typescript
/app/
  - page.tsx (Home with Hero)
  - mission/page.tsx
  - events/page.tsx
  - projects/page.tsx
  - research/
    - cases/page.tsx
    - water-facts/page.tsx
  - portfolio/page.tsx
  - team/page.tsx
```

### 5.2 Navigation & Layout

```typescript
/components/layout/
  - Header.tsx (with Jumbotron Popover)
  - Navigation.tsx
  - Footer.tsx
  - Layout.tsx
```

## Step 6: Enhancement & Optimization

### 6.1 Performance Optimization
- Implement lazy loading for galleries
- Add image optimization (next/image)
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
/tests/
  - unit/          # Component tests
  - integration/   # Feature tests
  - e2e/          # User journey tests
```

**Testing Checklist:**
- [ ] Component unit tests with React Testing Library
- [ ] Integration tests for API routes
- [ ] E2E tests for critical user paths
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