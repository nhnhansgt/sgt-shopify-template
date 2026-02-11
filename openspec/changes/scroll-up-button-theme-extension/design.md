## Context

This is a new Shopify Theme App Extension that adds a scroll-up button to storefronts. The project currently has no theme extensions - this will be the first. The existing codebase is a Remix-based Shopify App using Polaris Web Components for the app admin UI.

**Current State:**
- Shopify App built with React Router v7 (Remix)
- No existing theme extensions in the `extensions/` directory
- Prisma + SQLite for session storage
- Admin API integration for app configuration

**Constraints:**
- Must work as a theme extension (embedded in storefront theme, not app admin)
- Must be theme-agnostic (work with any Shopify 2.0 theme)
- Must follow Shopify's Theme App Extension best practices
- Cannot depend on external frameworks (React/Vue) - use vanilla JS or web components
- Must respect Shopify's Content Security Policy (CSP)

**Stakeholders:**
- Merchants: Want easy customization of button appearance and behavior
- Customers: Want non-intrusive, helpful navigation aid
- Developers: Want maintainable, well-documented code

## Goals / Non-Goals

**Goals:**
- Create a reusable theme extension that works across all Shopify 2.0 themes
- Provide merchant-customizable settings through Shopify's schema API
- Ensure mobile-first responsive design
- Follow accessibility best practices (WCAG 2.1 AA compliant)
- Minimize performance impact on storefront load times

**Non-Goals:**
- Advanced scroll behaviors (parallax, scroll progress indicators)
- A/B testing or analytics integration
- Multi-button support (only one scroll-up button)
- Dynamic content injection (button is purely navigational)

## Decisions

### 1. Technology Stack: Vanilla JS + Liquid

**Decision:** Use vanilla JavaScript with Liquid templating instead of a framework.

**Rationale:**
- Theme extensions cannot use npm/build tooling easily
- Vanilla JS has zero runtime overhead
- Liquid is Shopify's native templating - best for theme integration
- Avoids framework version conflicts with merchant's theme

**Alternatives Considered:**
- *Web Components*: Rejected due to limited browser support in older themes
- *React via CDN*: Rejected due to bundle size and no compilation step
- *jQuery*: Often available in themes but not guaranteed - vanilla JS is safer

### 2. Icon Implementation: Inline SVG

**Decision:** Use inline SVG for the scroll-up arrow icon.

**Rationale:**
- No external dependencies or icon font requests
- Fully customizable via CSS (color, size)
- Scales perfectly on all devices
- Shopify Polaris icons available as SVG

**Alternatives Considered:**
- *Icon font*: Requires external request, can cause FOIT
- *Image asset*: Less flexible, requires multiple sizes for retina displays

### 3. Scroll Detection: Throttled Event Listener

**Decision:** Use `window.addEventListener('scroll', ...)` with requestAnimationFrame throttling.

**Rationale:**
- Simpler than Intersection Observer for this use case
- requestAnimationFrame ensures smooth 60fps performance
- Throttling prevents excessive layout recalculations

**Alternatives Considered:**
- *Intersection Observer*: Better for scroll INTO view, not scroll position tracking
- *CSS position: sticky*: Cannot trigger behavior based on scroll threshold

### 4. Button Visibility: CSS Class Toggling

**Decision:** Toggle a CSS class (`.is-visible`) on the button element based on scroll position.

**Rationale:**
- Enables CSS transitions for smooth fade-in/fade-out
- Keeps JS logic minimal (only class manipulation)
- Merchants can customize animation via theme settings

**Alternatives Considered:**
- *Direct inline style*: Less flexible, harder to animate smoothly

### 5. Theme Settings Schema: JSON Schema

**Decision:** Use Shopify's settings schema for all customization options.

**Rationale:**
- Native Shopify UI for merchant customization
- No need to build custom app admin interface
- Settings are version-controlled with the extension
- Supports color pickers, selects, ranges, and text inputs

**Configuration Options:**
- **Behavior**: Scroll threshold (px), animation duration (ms), easing function
- **Appearance**: Button color, icon color, button size (px), corner radius
- **Position**: Bottom-right or bottom-left, offset from edge (px)
- **Mobile**: Show on mobile (toggle), separate size for mobile

## Risks / Trade-offs

### Performance Risk: Scroll Event Listener

**Risk:** Scroll listeners can cause jank if not properly throttled.

**Mitigation:**
- Use requestAnimationFrame for throttling
- Use passive event listeners (`{ passive: true }`)
- Cache DOM queries (get button element once)
- Use CSS transforms instead of changing layout properties

### Compatibility Risk: Theme Conflicts

**Risk:** Some themes may have conflicting CSS or JavaScript.

**Mitigation:**
- Use scoped CSS with BEM naming convention (`.sgt-scroll-up-button__*`)
- Use CSS custom properties for easy override by theme developers
- Test with popular free themes (Dawn, Sense, Crave)
- Provide z-index setting to control stacking context

### Accessibility Risk: Keyboard Navigation

**Risk:** Button must be accessible to keyboard and screen reader users.

**Mitigation:**
- Use semantic `<button>` element (not `<div>`)
- Add proper ARIA labels: `aria-label="Scroll to top"`
- Ensure visible focus indicator
- Support `Enter` and `Space` key activation
- Hide from screen reader when not visible (`aria-hidden="true"`)

### Mobile Risk: Touch Target Size

**Risk:** Button may be too small for comfortable mobile interaction.

**Mitigation:**
- Default to minimum 44x44px target (WCAG 2.1 AAA)
- Allow separate mobile size configuration
- Add padding to increase touch area without increasing visual size

## Migration Plan

### Deployment Steps

1. **Generate Extension Scaffold**
   ```bash
   shopify app generate extension --type theme_app_extension
   ```

2. **Create Extension Files**
   - `assets/scroll-up-button.js` - Main functionality
   - `snippets/scroll-up-button.liquid` - HTML template
   - `blocks/scroll-up-button.liquid` - Theme block for app embed
   - `locales/en.default.schema` - Settings schema

3. **Register Extension**
   - Add to theme via Theme Editor > App Embeds
   - Or add to specific template via Theme App Extension blocks

4. **Testing**
   - Test on multiple themes (Dawn, Sense, custom themes)
   - Test on mobile and desktop
   - Test accessibility with keyboard and screen reader
   - Performance audit with Lighthouse

### Rollback Strategy

- Merchants can simply disable the extension in Theme Editor
- No database migrations or permanent data changes
- Extension removal leaves no residue in theme code

## Open Questions

1. **Should we support multiple button instances on one page?**
   - **Decision**: No, single instance only. Multiple buttons would be confusing UX.

2. **Should we add animation presets (bounce, pulse, fade)?**
   - **Decision**: Start with simple fade-in/out. Can add presets in v2 based on merchant feedback.

3. **Should we integrate with Shopify Analytics to track button usage?**
   - **Decision**: No, out of scope. Analytics integration adds complexity and privacy concerns.

4. **What should the default scroll threshold be?**
   - **Decision**: 300px (approximately 2-3 screen heights on mobile). This is configurable via settings.

5. **Should the button auto-hide after reaching the top?**
   - **Decision**: Yes, automatically hide when user scrolls back up past the threshold or clicks the button.
