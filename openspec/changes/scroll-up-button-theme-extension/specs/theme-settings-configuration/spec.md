## ADDED Requirements

### Requirement: Provide schema-based settings interface
The system SHALL provide a Shopify settings schema that renders a configuration UI in the Theme Editor.

#### Scenario: Settings schema is valid JSON
- **WHEN** extension is loaded in Theme Editor
- **THEN** settings schema is valid JSON format
- **AND** schema follows Shopify's schema specification
- **AND** all settings are rendered in Theme Editor sidebar

#### Scenario: Settings are organized by category
- **WHEN** merchant opens extension settings in Theme Editor
- **THEN** settings are grouped into logical categories (Behavior, Appearance, Position, Mobile)
- **AND** each category has a clear heading
- **AND** settings within each category are related

### Requirement: Behavior settings configuration
The system SHALL provide settings for scroll-related behavior customization.

#### Scenario: Scroll threshold setting
- **WHEN** merchant configures "Scroll Threshold" setting
- **THEN** setting accepts numeric input in pixels
- **AND** setting range is 100px to 2000px
- **AND** default value is 300px
- **AND** setting includes help text explaining its purpose

#### Scenario: Animation duration setting
- **WHEN** merchant configures "Animation Duration" setting
- **THEN** setting accepts numeric input in milliseconds
- **AND** setting range is 200ms to 2000ms
- **AND** default value is 500ms
- **AND** setting includes help text about animation speed

#### Scenario: Easing function setting
- **WHEN** merchant configures "Animation Easing" setting
- **THEN** setting provides dropdown select with options
- **AND** options include: linear, ease, ease-in, ease-out, ease-in-out
- **AND** default value is ease-out
- **AND** each option includes description

### Requirement: Appearance settings configuration
The system SHALL provide settings for button visual customization.

#### Scenario: Button color setting
- **WHEN** merchant configures "Button Color" setting
- **THEN** setting provides color picker input
- **AND** default color is #000000 (black)
- **AND** color is applied to button background

#### Scenario: Icon color setting
- **WHEN** merchant configures "Icon Color" setting
- **THEN** setting provides color picker input
- **AND** default color is #FFFFFF (white)
- **AND** color is applied to SVG icon fill

#### Scenario: Button size setting
- **WHEN** merchant configures "Button Size" setting
- **THEN** setting accepts numeric input in pixels
- **AND** setting range is 40px to 80px
- **AND** default value is 50px
- **AND** minimum value meets WCAG accessibility guidelines (44px)

#### Scenario: Corner radius setting
- **WHEN** merchant configures "Corner Radius" setting
- **THEN** setting accepts numeric input in pixels or percentage
- **AND** setting range is 0px to 50%
- **AND** default value is 50% (fully rounded / pill shape)
- **AND** 0px creates square button, 50% creates circular button

### Requirement: Position settings configuration
The system SHALL provide settings for button placement on the viewport.

#### Scenario: Position select setting
- **WHEN** merchant configures "Position" setting
- **THEN** setting provides dropdown with two options: Bottom Right, Bottom Left
- **AND** default value is Bottom Right
- **AND** selection determines button's fixed position

#### Scenario: Edge offset setting
- **WHEN** merchant configures "Offset from Edge" setting
- **THEN** setting accepts numeric input in pixels
- **AND** setting range is 10px to 100px
- **AND** default value is 20px
- **AND** value controls distance from viewport edge

### Requirement: Mobile settings configuration
The system SHALL provide settings for mobile-specific behavior and appearance.

#### Scenario: Show on mobile toggle
- **WHEN** merchant enables "Show on Mobile" setting
- **THEN** button is visible on mobile devices (<768px)
- **WHEN** merchant disables "Show on Mobile" setting
- **THEN** button is hidden on mobile devices
- **AND** default value is enabled (true)

#### Scenario: Mobile button size setting
- **WHEN** merchant configures "Mobile Button Size" setting
- **THEN** setting accepts numeric input in pixels
- **AND** setting range is 44px to 80px
- **AND** default value is 56px (larger for touch targets)
- **AND** this setting overrides desktop size on mobile devices

### Requirement: Settings are validated
The system SHALL validate all setting values and provide feedback for invalid inputs.

#### Scenario: Invalid number input
- **WHEN** merchant enters value outside allowed range
- **THEN** setting shows validation error
- **AND** setting displays min/max constraints
- **AND** invalid value is not saved

#### Scenario: Invalid color input
- **WHEN** merchant enters invalid color code
- **THEN** color picker shows validation error
- **AND** only valid hex, rgb, or named colors are accepted

### Requirement: Settings persist and apply immediately
The system SHALL save settings to theme configuration and apply changes without page refresh.

#### Scenario: Settings save on change
- **WHEN** merchant modifies any setting value
- **THEN** setting is automatically saved to theme configuration
- **AND** save operation is indicated by UI feedback

#### Scenario: Settings apply via Liquid
- **WHEN** settings are saved
- **THEN** Liquid template accesses values via `settings.<setting_name>`
- **AND** values are output as CSS custom properties or data attributes
- **AND** JavaScript reads values and applies behavior
- **AND** changes are visible immediately (no page refresh required)
