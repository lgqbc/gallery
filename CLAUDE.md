# CLAUDE.md - Gallery Project Guide

## Project Overview

This is a **client-side photo gallery web application** that displays images in a fullscreen viewer with grid view support. The application is designed to be simple, fast, and mobile-friendly with no backend dependencies.

### Tech Stack
- **HTML5** - Single page application
- **Vanilla JavaScript** - No frameworks, pure JS
- **TailwindCSS** - Via CDN (https://cdn.tailwindcss.com)
- **GitHub API** - Used as image CDN/source

### Key Characteristics
- Zero build process - direct HTML/CSS/JS
- Client-side only (no server required)
- Mobile-first responsive design
- Touch gestures support
- LocalStorage for user preferences

---

## Repository Structure

```
/
├── index.html           # Main application file (contains HTML, CSS, JS)
├── images/              # Gallery images directory
│   ├── *.jpg            # Image files (JPG/JPEG/PNG/GIF/WEBP)
│   ├── *.JPG
│   └── .gitkeep         # Keeps directory in git
├── icons/               # SVG icon files
│   ├── gridBtn.svg      # Grid view button icon
│   ├── next.svg         # Next arrow icon
│   ├── openPopup.svg    # Menu/popup button icon
│   └── prev.svg         # Previous arrow icon
├── *.jpg                # Root-level sample images
└── CLAUDE.md           # This file
```

### Image Organization
- **Root directory**: Contains sample/test images (not displayed in gallery)
- **/images directory**: Contains actual gallery images (fetched via GitHub API)
- Supported formats: `.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`

---

## Core Application Architecture

### Single-File Structure
The entire application is contained in `index.html` with three main sections:
1. **HTML Structure** (lines 1-81): DOM layout
2. **Inline Styles** (lines 13-23): Custom CSS overrides including SVG icon filters
3. **JavaScript** (lines 83-377): All application logic

### Key Components

#### 1. Fullscreen Viewer (`#fullscreen`)
- Main image display with navigation arrows
- Dot pagination indicators at bottom
- Title display (hidden for DSC* filenames)
- Click areas for prev/next navigation
- Touch gesture support for mobile

#### 2. Grid View (`#grid`)
- Responsive grid layout (2-3 columns)
- Thumbnail view of all images
- Hidden by default, toggles with fullscreen view

#### 3. Welcome Popup (`#welcomePopup`)
- Shows on first visit only
- Uses `localStorage.getItem("closedPopup")` to track state
- Can be reopened via menu button (⋅⋅⋅)

### Image Loading Strategy
```javascript
// Images fetched from GitHub API
fetch('https://api.github.com/repos/lgqbc/gallery/contents/images')

// Preloading logic for smooth navigation
preloadImage(images[current + 1].url);  // Next image
preloadImage(images[current - 1].url);  // Previous image
```

### Navigation Methods
1. Arrow buttons (left/right)
2. Click areas (left/right half of screen)
3. Touch swipe gestures (mobile)
4. Dot indicators (click to jump)
5. Keyboard (not currently implemented)

### Dynamic UI Positioning System
A sophisticated positioning system (`positionElements()` function) automatically positions all UI elements relative to the displayed image:

**Positioning Strategy:**
- **Arrows**: Vertically centered with the image; horizontally positioned in margin space or at screen edges
- **Buttons**: Fixed vertical position at top; horizontal positioning follows same logic as arrows
- **Dots**: Positioned 10px below the bottom of the image, horizontally centered
- **Responsive Offsets**:
  - Mobile (<640px): Fixed distance from screen edges
  - Medium (640-1024px): 15px inward offset for arrows, 20px for buttons
  - Desktop (>1024px): Centered in margin space with no offset

**Transform-based Centering:**
- Left-positioned elements: `translateX(-50%)`
- Right-positioned elements: `translateX(+50%)`
- Ensures pixel-perfect centering regardless of element size

This system ensures UI elements never overlap the image and always appear in optimal positions regardless of image aspect ratio or screen size.

---

## Development Workflows

### Adding New Images
1. Add image files to `/images` directory
2. Commit and push to repository
3. Images automatically appear in gallery (fetched via GitHub API)
4. **Note**: Images must be in `/images` directory, not root

### Modifying the Gallery
All code is in `index.html`. Key areas:

#### Styling Changes
- Line 11: TailwindCSS theme configuration
- Lines 13-23: Custom CSS overrides
  - Lines 14-16: Hover state adjustments for touch devices
  - Line 17: Prevent horizontal overflow
  - Lines 19-22: SVG icon color filters (makes icons white)
- Inline classes: TailwindCSS utility classes throughout HTML

#### Layout/UI Changes
- Lines 27-56: Fullscreen viewer structure
  - Image display with navigation arrows and buttons
  - Clickable left/right areas for navigation
  - Dots bar for pagination
- Lines 59-62: Grid view structure
- Lines 64-81: Welcome popup content

#### JavaScript Logic
- Lines 86-104: Welcome popup management
- Lines 106-132: Image loading from GitHub API (with 500ms delay and error handling)
- Lines 110-117: Image preloading functions
- Lines 135-239: **Dynamic element positioning system** (`positionElements()`)
  - Calculates image bounds and positions UI elements accordingly
  - Responsive positioning for mobile, medium, and desktop screens
  - Centers arrows in margin space or falls back to screen edges
  - Positions buttons and dots bar relative to image
  - Uses CSS transforms for precise centering
- Lines 241-284: Render function (updates UI for fullscreen and grid views)
- Lines 286-292: `setCurrent()` function for navigation state management
- Lines 294-330: Touch gesture handlers for mobile swipe navigation
- Lines 334-366: Event listeners for navigation and view switching
- Lines 368-375: Image loading initialization and window resize handler

### Content Security Policy
Line 6 defines CSP rules:
```html
content="default-src 'self';
         script-src 'unsafe-inline' https://cdn.tailwindcss.com;
         img-src 'self' https:;
         style-src 'unsafe-inline' https://cdn.tailwindcss.com;
         connect-src 'self' https://api.github.com"
```

**Important**: When adding external resources, update CSP accordingly.

---

## Key Conventions for AI Assistants

### Code Style
1. **Inline Everything**: Keep HTML, CSS, and JS in single file
2. **No Build Tools**: Don't introduce webpack, npm, or build processes
3. **Vanilla JS**: No frameworks (React, Vue, etc.)
4. **TailwindCSS**: Use utility classes, avoid custom CSS where possible
5. **Minimal Dependencies**: Only CDN-based external resources

### File Naming
- Images: Lowercase with descriptive names or camera defaults (DSC*)
- DSC* filenames: Camera defaults, title is hidden in UI
- SVG icons: Descriptive names (gridBtn.svg, next.svg, etc.)

### State Management
- Use `let` variables at script top for global state
- `current`: Current image index
- `images`: Array of image objects `{name, url}`
- LocalStorage for persistent user preferences

### Mobile Considerations
1. Touch events are primary navigation on mobile
2. Responsive breakpoints: `sm:`, `md:`, `lg:`, `xl:`, `2xl:`
3. Opacity changes replace hover states on touch devices (lines 14-16)
4. Scale adjustments for different screen sizes

### Performance
1. **Preload adjacent images** for smooth navigation (lines 110-117)
2. **500ms delay** before API call (line 120) - prevents rate limiting
3. **Lazy render**: Only render visible view (fullscreen OR grid)
4. **Dynamic positioning**: UI elements reposition on window resize for optimal layout

### Git Workflow
- **Branch naming**: `claude/claude-md-*` for AI assistant work
- **Commits**: Direct commits to feature branches
- **Push**: Always use `git push -u origin <branch-name>`
- **Main branch**: Not specified in current config
- **No merge conflicts**: Single-file structure minimizes conflicts

---

## Common Tasks

### Task: Add a New Feature
1. **Read** `index.html` first to understand current structure
2. **Identify** insertion point (HTML/CSS/JS section)
3. **Preserve** existing functionality
4. **Test** both fullscreen and grid views
5. **Verify** mobile responsiveness
6. **Commit** with descriptive message

### Task: Fix a Bug
1. **Locate** the relevant section in index.html
2. **Understand** interaction with other components
3. **Test** navigation flow after fix
4. **Verify** no console errors
5. **Check** mobile/desktop compatibility

### Task: Update Styling
1. **Prefer** TailwindCSS utility classes over custom CSS
2. **Maintain** responsive breakpoints
3. **Test** on multiple screen sizes
4. **Ensure** dark theme consistency (bg-coal: #111111)
5. **Preserve** opacity/hover states

### Task: Modify Image Loading
1. **Understand** GitHub API rate limits
2. **Maintain** error handling (lines 129-131)
3. **Keep** preloading strategy (lines 110-117)
4. **Test** with slow network conditions
5. **Update** CSP if fetching from new domains

---

## Testing Checklist

When making changes, verify:
- [ ] Fullscreen view displays images correctly
- [ ] Grid view shows all thumbnails
- [ ] Navigation (arrows, dots, click areas) works
- [ ] Touch gestures work on mobile
- [ ] Welcome popup shows/hides correctly
- [ ] Images preload smoothly
- [ ] No console errors
- [ ] Responsive on mobile/tablet/desktop
- [ ] Dark theme maintained
- [ ] CSP doesn't block resources

---

## Constraints & Limitations

### Do NOT:
- Add npm packages or build tools
- Introduce JavaScript frameworks
- Create separate CSS/JS files
- Add backend/server code
- Implement complex state management libraries
- Use TypeScript or transpilation

### Do:
- Keep everything in single HTML file
- Use vanilla JavaScript
- Use TailwindCSS utilities
- Keep code simple and readable
- Maintain mobile-first approach
- Preserve existing user experience

---

## API Dependencies

### GitHub API
- **Endpoint**: `https://api.github.com/repos/lgqbc/gallery/contents/images`
- **Rate Limit**: 60 requests/hour (unauthenticated)
- **Response**: JSON array of file objects
- **Used Fields**: `name`, `download_url`

**Important**: Application breaks if:
1. Repository becomes private
2. Rate limit exceeded
3. GitHub API changes
4. Network connectivity issues

### TailwindCSS CDN
- **URL**: `https://cdn.tailwindcss.com`
- **Version**: Latest (unversioned)
- **Config**: Custom theme with `coal` color (#111111)

---

## Security Considerations

1. **CSP**: Strict Content Security Policy defined
2. **No User Input**: No forms or user-submitted content
3. **Read-Only API**: Only fetches data, never writes
4. **LocalStorage**: Only stores UI preference (popup closed state)
5. **No Credentials**: No authentication tokens or sensitive data

---

## Future Enhancement Ideas

If user requests improvements, consider:
1. Keyboard navigation (arrow keys, ESC)
2. Image metadata display (EXIF data)
3. Slideshow/autoplay mode
4. Download image button
5. Share functionality
6. Image filtering/search
7. Lazy loading for grid view
8. Image zoom/pan controls
9. Fullscreen API integration
10. PWA support (offline viewing)

**Remember**: Keep enhancements simple and inline with existing architecture.

---

## Troubleshooting

### Images Not Loading
- Check `/images` directory has files
- Verify GitHub API endpoint is accessible
- Check browser console for CORS errors
- Verify CSP allows image sources

### Navigation Not Working
- Check JavaScript console for errors
- Verify event listeners are attached
- Test both click and touch events
- Check current index boundaries

### Styling Issues
- Verify TailwindCSS CDN loaded
- Check custom theme config (line 11)
- Test responsive breakpoints
- Clear browser cache

### Performance Issues
- Reduce image file sizes
- Check network tab for slow loads
- Verify preloading is working
- Consider API rate limiting

---

## Contact & Context

**Repository**: lgqbc/gallery
**Owner**: Lukas Gilow (lukas.gilow@gmail.com)
**Purpose**: Personal photography gallery
**Theme**: Unedited photos from 2004 digicam
**Started**: 2026 (bofu/2026)

This is a personal project focused on simplicity and aesthetic presentation.

---

*Last Updated: 2025-12-02*
*Maintained by Claude for AI-assisted development*
