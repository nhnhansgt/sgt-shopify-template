## 1. Extension Setup

- [ ] 1.1 Generate theme extension scaffold using Shopify CLI
- [ ] 1.2 Create extension directory structure (assets/, snippets/, blocks/, locales/)
- [ ] 1.3 Register extension in shopify.app.toml if needed

## 2. Settings Schema

- [ ] 2.1 Create settings schema in locales/en.default.schema
- [ ] 2.2 Define behavior settings (scroll threshold, animation duration, easing)
- [ ] 2.3 Define appearance settings (button color, icon color, size, corner radius)
- [ ] 2.4 Define position settings (position select, edge offset)
- [ ] 2.5 Define mobile settings (show on mobile toggle, mobile size)
- [ ] 2.6 Add validation rules for all settings

## 3. Liquid Templates

- [ ] 3.1 Create block template at blocks/scroll-up-button.liquid
- [ ] 3.2 Add semantic HTML button element with accessibility attributes
- [ ] 3.3 Embed inline SVG Polaris arrow-up icon
- [ ] 3.4 Output Liquid settings as CSS custom properties
- [ ] 3.5 Create CSS classes with BEM naming convention
- [ ] 3.6 Implement fade-in/fade-out animations with CSS transitions
- [ ] 3.7 Add responsive breakpoints for mobile-specific styles
- [ ] 3.8 Ensure button meets WCAG 2.1 AA accessibility standards

## 4. JavaScript Functionality

- [ ] 4.1 Create assets/scroll-up-button.js with scroll position tracking
- [ ] 4.2 Implement throttled scroll event listener using requestAnimationFrame
- [ ] 4.3 Add logic to toggle .is-visible class based on scroll threshold
- [ ] 4.4 Cache DOM queries for button element on initialization
- [ ] 4.5 Implement smooth scroll to top on button click
- [ ] 4.6 Add support for keyboard activation (Enter, Space keys)
- [ ] 4.7 Implement interruptible scroll animation (cancel on manual scroll)
- [ ] 4.8 Auto-hide button after successful scroll to top
- [ ] 4.9 Read Liquid settings from CSS custom properties in JavaScript

## 5. Styling & Theming

- [ ] 5.1 Implement position styles (bottom-right, bottom-left)
- [ ] 5.2 Add configurable offset from viewport edge
- [ ] 5.3 Apply configurable button color and icon color
- [ ] 5.4 Apply configurable button size and corner radius
- [ ] 5.5 Implement mobile-specific size below 768px breakpoint
- [ ] 5.6 Add visible focus indicator for keyboard navigation
- [ ] 5.7 Ensure z-index prevents conflicts with theme elements
- [ ] 5.8 Test with popular Shopify themes (Dawn, Sense, Crave)

## 6. Accessibility

> **Note**: Task 6.4 requires manual testing by the user.

- [ ] 6.1 Add aria-label="Scroll to top" to button element
- [ ] 6.2 Implement aria-hidden="true" toggle when button is hidden
- [ ] 6.3 Ensure button is keyboard focusable (native <button> behavior)
- [ ] 6.4 Test with screen reader (VoiceOver, NVDA)
- [ ] 6.5 Verify touch target size meets minimum 44x44px on mobile
- [ ] 6.6 Test keyboard navigation (Tab, Enter, Space)
- [ ] 6.7 Ensure color contrast meets WCAG AA standards

## 7. Testing

> **Note**: Tasks 7.1-7.8 require manual testing by the user.

- [ ] 7.1 Manual testing on desktop browsers (Chrome, Firefox, Safari, Edge)
- [ ] 7.2 Manual testing on mobile browsers (iOS Safari, Android Chrome)
- [ ] 7.3 Test scroll threshold behavior (show/hide at correct position)
- [ ] 7.4 Test smooth scroll animation with different easing functions
- [ ] 7.5 Test all theme settings variations
- [ ] 7.6 Test on multiple themes (Dawn, Sense, custom themes)
- [ ] 7.7 Performance audit with Lighthouse (target 90+ score)
- [ ] 7.8 Verify no console errors or warnings

## 8. Documentation

- [ ] 8.1 Create README.md in extension directory
- [ ] 8.2 Document installation and configuration steps
- [ ] 8.3 Add screenshots of theme editor settings interface
- [ ] 8.4 Document customization options and their effects
- [ ] 8.5 Add troubleshooting section for common issues
- [ ] 8.6 Update CLAUDE.md with theme extension development patterns

## 9. Deployment

> **Note**: Tasks 9.1-9.6 require manual testing and deployment by the user.

- [ ] 9.1 Test extension locally with Shopify CLI dev server
- [ ] 9.2 Package extension for deployment
- [ ] 9.3 Deploy extension to Shopify App
- [ ] 9.4 Install extension on test store via Theme Editor
- [ ] 9.5 Verify all settings work correctly in production
- [ ] 9.6 Test on production storefront
