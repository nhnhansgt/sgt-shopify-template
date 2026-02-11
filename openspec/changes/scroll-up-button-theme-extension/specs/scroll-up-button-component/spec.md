## ADDED Requirements

### Requirement: Render floating scroll-up button
The system SHALL render a floating button element that appears on the storefront page when enabled by the merchant.

#### Scenario: Button renders on page load
- **WHEN** a user loads a page with the theme extension enabled
- **THEN** the button element is present in the DOM
- **AND** the button is initially hidden (not visible to user)
- **AND** the button uses semantic HTML `<button>` element

#### Scenario: Button has proper accessibility attributes
- **WHEN** the button is rendered
- **THEN** it includes `aria-label="Scroll to top"` attribute
- **AND** it includes `aria-hidden="true"` when not visible
- **AND** it is keyboard focusable with Tab key
- **AND** it activates on Enter or Space key press

#### Scenario: Button uses inline SVG icon
- **WHEN** the button is rendered
- **THEN** it contains an inline SVG arrow icon pointing up
- **AND** the SVG is from Shopify Polaris icon set
- **AND** the icon color is configurable via theme settings

### Requirement: Button styling is customizable
The system SHALL allow merchants to customize button appearance through theme settings.

#### Scenario: Merchant can set button color
- **WHEN** merchant configures button color in theme settings
- **THEN** the button background uses the configured color
- **AND** the color is applied via CSS custom property

#### Scenario: Merchant can set button size
- **WHEN** merchant configures button size in theme settings
- **AND** the button size meets minimum 44x44px for mobile accessibility

#### Scenario: Merchant can set corner radius
- **WHEN** merchant configures corner radius in theme settings
- **THEN** the button uses the configured border-radius value
- **AND** value can range from 0px (square) to 50% (fully rounded)

### Requirement: Button position is configurable
The system SHALL support positioning the button in bottom-right or bottom-left corner of the viewport.

#### Scenario: Button positioned bottom-right (default)
- **WHEN** merchant selects bottom-right position
- **THEN** button is fixed to bottom-right of viewport
- **AND** button offset from edge can be configured via settings

#### Scenario: Button positioned bottom-left
- **WHEN** merchant selects bottom-left position
- **THEN** button is fixed to bottom-left of viewport
- **AND** button offset from edge can be configured via settings

### Requirement: Button supports responsive design
The system SHALL provide separate configuration options for mobile and desktop appearances.

#### Scenario: Mobile-specific size configuration
- **WHEN** merchant configures mobile size
- **THEN** button uses mobile size on screens below 768px breakpoint
- **AND** uses desktop size on screens 768px and above

#### Scenario: Mobile visibility toggle
- **WHEN** merchant enables "Show on mobile" setting
- **THEN** button is visible on mobile devices
- **WHEN** merchant disables "Show on mobile" setting
- **THEN** button is hidden on mobile devices below 768px breakpoint
