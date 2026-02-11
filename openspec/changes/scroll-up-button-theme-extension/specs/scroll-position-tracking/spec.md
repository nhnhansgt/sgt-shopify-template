## ADDED Requirements

### Requirement: Monitor window scroll position
The system SHALL continuously monitor the window scroll position to determine when to show or hide the scroll-up button.

#### Scenario: Scroll listener is initialized on page load
- **WHEN** the page loads
- **THEN** a scroll event listener is attached to the window object
- **AND** the listener uses passive mode for performance (`{ passive: true }`)
- **AND** the listener is throttled using requestAnimationFrame

#### Scenario: Button appears after scrolling past threshold
- **WHEN** user scrolls down beyond the configured threshold distance (default 300px)
- **THEN** the button becomes visible with a fade-in animation
- **AND** the `.is-visible` CSS class is added to the button element

#### Scenario: Button hides when scrolling back above threshold
- **WHEN** user scrolls back up and position is below the threshold
- **THEN** the button becomes hidden with a fade-out animation
- **AND** the `.is-visible` CSS class is removed from the button element

### Requirement: Scroll threshold is configurable
The system SHALL allow merchants to configure the scroll distance threshold in pixels.

#### Scenario: Merchant configures custom threshold
- **WHEN** merchant sets threshold to 500px in theme settings
- **THEN** button appears only after scrolling 500px from top
- **AND** button hides when scrolling back above 500px

#### Scenario: Threshold defaults to 300px
- **WHEN** merchant does not configure threshold
- **THEN** threshold defaults to 300px
- **AND** this equals approximately 2-3 screen heights on mobile devices

### Requirement: Scroll detection uses efficient throttling
The system SHALL use requestAnimationFrame to throttle scroll event handling for optimal performance.

#### Scenario: Scroll events are throttled
- **WHEN** user scrolls rapidly
- **THEN** scroll position is checked at most once per frame (60fps)
- **AND** no layout thrashing occurs
- **AND** scrolling remains smooth (60fps or higher)

#### Scenario: DOM queries are cached
- **WHEN** scroll position is checked
- **THEN** button element is queried once on initialization
- **AND** cached reference is used in all subsequent checks
- **AND** no repeated DOM queries occur during scroll

### Requirement: Button hides after click and scroll to top
The system SHALL automatically hide the button after user clicks it and the page scrolls to the top.

#### Scenario: Button hides after successful scroll
- **WHEN** user clicks the button
- **AND** page scrolls to top
- **THEN** button visibility is removed
- **AND** `.is-visible` class is removed from button

#### Scenario: Button remains hidden until scroll threshold
- **WHEN** button is hidden after click
- **AND** user has not scrolled past threshold again
- **THEN** button remains hidden
- **AND** button only reappears when user scrolls past threshold again
