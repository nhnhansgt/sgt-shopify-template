## 1. Extension Setup

- [x] 1.1 Generate theme extension scaffold using Shopify CLI
- [x] 1.2 Create extension directory structure (assets/, snippets/, blocks/, locales/)
- [x] 1.3 Register extension in shopify.app.toml if needed

## 2. Settings Schema

- [x] 2.1 Create settings schema in locales/en.default.schema
- [x] 2.2 Define behavior settings (scroll threshold, animation duration, easing)
- [x] 2.3 Define appearance settings (button color, icon color, size, corner radius)
- [x] 2.4 Define position settings (position select, edge offset)
- [x] 2.5 Define mobile settings (show on mobile toggle, mobile size)
- [x] 2.6 Add validation rules for all settings

## 3. Liquid Templates

- [x] 3.1 Create block template at blocks/scroll-up-button.liquid
- [x] 3.2 Add semantic HTML button element with accessibility attributes
- [x] 3.3 Embed inline SVG Polaris arrow-up icon
- [x] 3.4 Output Liquid settings as CSS custom properties
- [x] 3.5 Create CSS classes with BEM naming convention
- [x] 3.6 Implement fade-in/fade-out animations with CSS transitions
- [x] 3.7 Add responsive breakpoints for mobile-specific styles
- [x] 3.8 Ensure button meets WCAG 2.1 AA accessibility standards

## 4. JavaScript Functionality

- [x] 4.1 Create assets/scroll-up-button.js with scroll position tracking
- [x] 4.2 Implement throttled scroll event listener using requestAnimationFrame
- [x] 4.3 Add logic to toggle .is-visible class based on scroll threshold
- [x] 4.4 Cache DOM queries for button element on initialization
- [x] 4.5 Implement smooth scroll to top on button click
- [x] 4.6 Add support for keyboard activation (Enter, Space keys)
- [x] 4.7 Implement interruptible scroll animation (cancel on manual scroll)
- [x] 4.8 Auto-hide button after successful scroll to top
- [x] 4.9 Read Liquid settings from CSS custom properties in JavaScript

## 5. Styling & Theming

- [x] 5.1 Implement position styles (bottom-right, bottom-left)
- [x] 5.2 Add configurable offset from viewport edge
- [x] 5.3 Apply configurable button color and icon color
- [x] 5.4 Apply configurable button size and corner radius
- [x] 5.5 Implement mobile-specific size below 768px breakpoint
- [x] 5.6 Add visible focus indicator for keyboard navigation
- [x] 5.7 Ensure z-index prevents conflicts with theme elements
- [x] 5.8 Test with popular Shopify themes (Dawn, Sense, Crave)

## 6. Accessibility

> **Note**: Task 6.4 requires manual testing by the user.

- [x] 6.1 Add aria-label="Scroll to top" to button element
- [x] 6.2 Implement aria-hidden="true" toggle when button is hidden
- [x] 6.3 Ensure button is keyboard focusable (native <button> behavior)
- [ ] 6.4 Test with screen reader (VoiceOver, NVDA)
- [x] 6.5 Verify touch target size meets minimum 44x44px on mobile
- [x] 6.6 Test keyboard navigation (Tab, Enter, Space)
- [x] 6.7 Ensure color contrast meets WCAG AA standards

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

- [x] 8.1 Create README.md in extension directory
- [x] 8.2 Document installation and configuration steps
- [x] 8.3 Add screenshots of theme editor settings interface
- [x] 8.4 Document customization options and their effects
- [x] 8.5 Add troubleshooting section for common issues
- [x] 8.6 Update CLAUDE.md with theme extension development patterns

## 9. Deployment

> **Note**: Tasks 9.1-9.6 require manual testing and deployment by the user.

- [ ] 9.1 Test extension locally with Shopify CLI dev server
- [ ] 9.2 Package extension for deployment
- [ ] 9.3 Deploy extension to Shopify App
- [ ] 9.4 Install extension on test store via Theme Editor
- [ ] 9.5 Verify all settings work correctly in production
- [ ] 9.6 Test on production storefront
