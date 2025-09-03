# SuperButtonToolbar

A container component for organizing and managing groups of action buttons with flexible layout options, shared styling controls, and collapsible functionality.

## 🚀 Features

### Button Management

- **Multiple Buttons**: Manage and configure multiple action buttons as a group
- **Shared Configuration**: Apply consistent styling and behavior across all buttons
- **Quick Configuration**: Configure multiple buttons through single button configuration panel

### Layout Options

- **Directional Control**: Horizontal (row) or vertical (column) button arrangements
- **Alignment Control**: Left, center, right alignment for horizontal layouts
- **Flexible Sizing**: Small, medium, large size options with consistent scaling

### Advanced Functionality

- **Collapsible Mode**: Can collapse to show only a dropdown trigger
- **Icon Support**: Consistent icon styling and positioning
- **Visual Consistency**: Maintains uniform appearance across button groups
- **State Management**: Shared disabled, quiet, and compact states

### Styling Controls

- **Button Styling**: Action button or regular button variants
- **Visual States**: Quiet mode for subtle appearance
- **Compact Mode**: Reduce spacing and padding
- **Icon-Only Mode**: Hide text and show only icons

## 🎯 Usage Instructions

### Basic Setup

1. Add the SuperButtonToolbar component to your screen
2. Configure button group settings (layout, style, sizing)
3. Define your action buttons through the button configuration panel
4. Optionally enable collapsible mode for space-saving
5. Customize toolbar styling and visual options

### Button Configuration

#### Managing Button Groups

- **Configuration Panel**: Use the built-in button configuration to add/edit actions
- **Shared Properties**: Direction, size, and styling apply to all buttons
- **Individual Actions**: Each button can have its own text, icon, and click action
- **Consistent Behavior**: All buttons match the toolbar's configured modes

#### Toolbar Layout

- **Row Layout**: Horizontal arrangement suitable for action bars
- **Column Layout**: Vertical arrangement for stacked button groups
- **Alignment**: Left, center, right positioning for horizontal toolbars

### Advanced Features

#### Collapsible Toolbar

- **Space Saving**: When collapsed, shows only trigger icon/button
- **Dropdown Menu**: Clicking collapsed toolbar shows button menu
- **Custom Trigger**: Configure collapsed state icon and text
- **Auto-collapse**: Option to automatically collapse for space efficiency

#### Icon Management

- **Consistent Icons**: All buttons can use the same icon styling
- **Icon Positioning**: Control whether icons appear before or after text
- **Icon-Only Mode**: Convert all buttons to icon-only variants

## 🔧 Configuration Properties

### Layout Settings

- **Direction**: Row (horizontal) or column (vertical) layout
- **Align**: Left, center, right alignment (row layout only)
- **Size**: Small (S), medium (M), large (L) toolbar sizing

### Button Settings

- **Button Class**: Button or action button variant
- **Icon Only**: Hide text and show only icons
- **Quiet**: Enable subtle styling
- **Compact**: Reduce padding and spacing
- **Disabled**: Disable entire button group

### Collapsible Settings

- **Collapsed**: Start in collapsed state
- **Collapsed Text**: Label when collapsed
- **Icon**: Icon for collapsed state trigger
- **Icon First**: Icon position in collapsed trigger

### Button Configuration

- **Buttons**: Array of button configurations with individual properties

## 📋 Usage Examples

### Header Action Bar - Row Layout

Configure a horizontal toolbar for page actions:

- Direction: Row
- Align: Left
- Size: Medium
- Buttons: [Save, Edit, Delete, Settings]

### Secondary Action Group - Compact Row

Create a compact secondary actions bar:

- Direction: Row
- Size: Small
- Quiet: Enabled
- Compact: Enabled
- Buttons: [Copy, Share, Export]

### Sidebar Controls - Column Layout

Build vertical sidebar controls:

- Direction: Column
- Align: Center
- Size: Medium
- Buttons: [Dashboard, Settings, Reports]

### Collapsible Menu - Space Saving

Create a collapsible action menu:

- Direction: Row
- Size: Small
- Collapsed: True
- Collapsed Text: "More Actions"
- Icon: ThreeDots

### Icon Toolbar - Minimal UI

Build an icon-only toolbar:

- Direction: Row
- Size: Small
- Icon Only: True
- Buttons: [Save, Edit, Delete] with corresponding icons

## 🎨 Styling & Theming

### Visual States

- **Quiet Mode**: Subtle appearance for secondary actions
- **Compact Mode**: Reduced padding and spacing
- **Disabled State**: Visual feedback for unavailable actions
- **Hover Effects**: Interactive feedback on button hover

### Responsive Design

- **Adaptive Sizing**: Button sizes adjust with container space
- **Layout Responsiveness**: Converts modes based on available space
- **Touch-Friendly**: Proper sizing for mobile interactions

### Accessibility

- **Keyboard Navigation**: Full keyboard access support
- **Screen Reader**: Label and action descriptions
- **Focus Management**: Clear focus and tab order
- **Visual Indicators**: Clear state and interaction feedback

## 🔗 Integration

### Action Integration

- **Event Handling**: Individual button click events
- **Data Operations**: Standard Budibase create/read/update/delete actions
- **Navigation**: Page routing and screen switching
- **User Interaction**: Notifications, confirmations, and prompts

### Context Usage

- **Current Record**: Access to current data for actions
- **Selected Items**: Bulk operations on selected data
- **Page Context**: Access to current page variables
- **Component State**: Sharing state with other components

### Nested Actions

- **Modal Triggers**: Open forms and dialogs from buttons
- **External Links**: Navigate to external resources
- **API Integration**: Trigger external service calls

## 📱 Responsiveness

- **Layout Adaptation**: Automatic row/column conversion
- **Size Optimization**: Button sizing based on screen size
- **Content Responsiveness**: Text/icons based on available space
- **Mobile Optimization**: Touch-optimized control sizes

## 🐛 Troubleshooting

### Common Issues

- **Button Actions**: Ensure click events are properly configured
- **Layout Problems**: Check direction and alignment settings
- **Collapsing Issues**: Verify collapsed state configuration
- **Icon Display**: Check icon settings and dependencies

### Performance Notes

- **Button Limit**: Reasonable number of buttons for optimal performance
- **Event Optimization**: Efficient event handling for multiple buttons
- **State Management**: Minimal state changes for smooth updates
- **Rendering Efficiency**: Optimized rendering for large button groups

Find out more about developing [Custom Plugins](https://docs.budibase.com/docs/custom-plugin) and [Budibase](https://github.com/Budibase/budibase).
