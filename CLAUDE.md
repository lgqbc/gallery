# CLAUDE.md - Gallery Project Guide

## Project Overview

This is a **client-side photo gallery web application** that displays images in a fullscreen viewer with grid view support. The application is designed to be simple, fast, and mobile-friendly with no backend dependencies.

### Tech Stack
- **HTML5** - Single page application
- **Vanilla JavaScript** - No frameworks, pure JS
- **TailwindCSS** - Via CDN (https://cdn.tailwindcss.com)
- **albums.txt** - Plain text file defining album structure and image order

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
├── albums.txt           # Album structure and image order definition
├── images/              # Gallery images directory
│   ├── 1.jpg            # Sequential numbered images
│   ├── 2.jpg
│   ├── ...
│   ├── 28.jpg
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
- **/images directory**: Contains all gallery images in flat structure (no subdirectories)
- **albums.txt**: Defines logical grouping of images into albums
- Images are numbered sequentially (1.jpg, 2.jpg, etc.) for easy management
- Supported formats: `.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`

### albums.txt Format
Simple text file structure:
```
/album_name_1
image1.jpg
image2.jpg
...

/album_name_2
image5.jpg
image6.jpg
...
```
- Album names start with `/`
- Image filenames listed below their album (one per line)
- Defines both album grouping AND image display order
- Single file fetch (no API rate limits)

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
- Dot pagination indicators at bottom with loading animation (line 43: 0.4s delay, CSS keyframes)
- Title display (hidden for DSC* filenames)
- Click areas for prev/next navigation
- Touch gesture support for mobile

#### 2. Grid View (`#grid`)
- Responsive grid layout (2-3 columns)
- Thumbnail view of all images
- Hidden by default, toggles with fullscreen view

#### 3. Album Selector Popup (`#welcomePopup`)
- Displays on first visit to select an album
- Shows grid of albums with 2x2 preview thumbnails
- Album name displayed on right side of preview (centered)
- Uses `localStorage.getItem("visitedGallery")` and `selectedAlbum` to track state
- Can be reopened via menu button (⋅⋅⋅) to switch albums
- Click any album to load and view its images
- Empty albums show black square placeholder

### Image Loading Strategy
```javascript
// Parse albums.txt to load album structure
fetch('albums.txt')

// Load images from selected album
images = albums[currentAlbum].map(filename => ({
  name: filename.replace(/\.[^/.]+$/, ''),
  url: `images/${filename}`
}));

// Preloading logic for smooth navigation
new Image().src = images[current + 1].url;  // Next image
new Image().src = images[current - 1].url;  // Previous image
```

### Navigation Methods
1. Arrow buttons (left/right)
2. Click areas (left/right half of screen)
3. Touch swipe gestures (mobile)
4. Dot indicators (click to jump)
5. Keyboard arrows (left/right for navigation, up for grid view, down for album selector)

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
1. Add image files to `/images` directory (use sequential numbering: 29.jpg, 30.jpg, etc.)
2. Update `albums.txt` to include new images in the appropriate album
3. Commit and push to repository
4. Images appear in gallery after refresh
5. **Note**: Images must be in `/images` directory AND listed in albums.txt to appear

### Creating New Albums
1. Add album images to `/images` directory
2. Add new album section to `albums.txt`:
   ```
   /new_album_name
   image1.jpg
   image2.jpg
   ```
3. Commit and push changes
4. New album appears in album selector

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
- Lines 101-118: Album selector popup management
- Lines 120-121: State variables (includes `albums`, `currentAlbum`)
- Lines 123-162: `loadAlbums()` - Parse albums.txt and initialize
- Lines 164-175: `loadAlbumImages()` - Load images from selected album
- Lines 177-184: `selectAlbum()` - Handle album selection
- Lines 186-228: `renderAlbums()` - Render album selector with event delegation
- Lines 230+: **Dynamic element positioning system** (`positionElements()`)
  - Calculates image bounds and positions UI elements accordingly
  - Responsive positioning for mobile, medium, and desktop screens
  - Centers arrows in margin space or falls back to screen edges
  - Positions buttons and dots bar relative to image
  - Uses CSS transforms for precise centering
- Render function (updates UI for fullscreen and grid views)
- `setCurrent()` function for navigation state management
- Touch gesture handlers for mobile swipe navigation
- Keyboard navigation (arrows, up/down)
- Event listeners for navigation and view switching
- Initialization calls `loadAlbums()` instead of `loadImages()`

### Content Security Policy
Line 6 defines CSP rules:
```html
content="default-src 'self';
         script-src 'unsafe-inline' https://cdn.tailwindcss.com;
         img-src 'self' https:;
         style-src 'unsafe-inline' https://cdn.tailwindcss.com;
         connect-src 'self'"
```

**Changes from original**:
- Removed `https://api.github.com` from `connect-src` (no longer using GitHub API)
- Only fetches local `albums.txt` file

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
- Images: Sequential numbers (1.jpg, 2.jpg, etc.) for easy management
- Image titles: Derived from filename without extension (shown in UI)
- Numeric filenames: Clean and simple, no title display needed
- SVG icons: Descriptive names (gridBtn.svg, next.svg, etc.)

### State Management
- Use `let` variables at script top for global state
- `albums`: Object mapping album names to arrays of image filenames
- `currentAlbum`: Name of currently selected album
- `current`: Current image index within album
- `images`: Array of image objects `{name, url}` for current album
- `loadingImage`: Index of image currently loading (for loading indicator)
- LocalStorage for persistent user preferences:
  - `selectedAlbum`: Remember which album user selected
  - `visitedGallery`: Track if user has visited before

### Mobile Considerations
1. Touch events are primary navigation on mobile
2. Responsive breakpoints: `sm:`, `md:`, `lg:`, `xl:`, `2xl:`
3. Opacity changes replace hover states on touch devices (lines 14-16)
4. Scale adjustments for different screen sizes

### Performance
1. **Preload adjacent images** for smooth navigation
2. **Single file fetch** - albums.txt loads once at startup (no API rate limits)
3. **Lazy render**: Only render visible view (fullscreen OR grid)
4. **Dynamic positioning**: UI elements reposition on window resize for optimal layout
5. **Event delegation**: Album selector uses efficient event handling

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
1. **Understand** albums.txt format and parsing logic
2. **Maintain** error handling in `loadAlbums()` function
3. **Keep** preloading strategy for adjacent images
4. **Test** with slow network conditions
5. **Update** CSP if fetching from new domains
6. **Remember** to update albums.txt when adding/removing images

---

## Testing Checklist

When making changes, verify:
- [ ] Fullscreen view displays images correctly
- [ ] Grid view shows only current album's thumbnails
- [ ] Navigation (arrows, dots, click areas) works
- [ ] Touch gestures work on mobile
- [ ] Album selector popup shows on first visit
- [ ] Albums display with preview grids and names
- [ ] Album selection loads correct images
- [ ] Can switch between albums via ⋅⋅⋅ button
- [ ] Images preload smoothly
- [ ] Keyboard navigation works (arrows, up, down)
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

## Dependencies

### albums.txt
- **Location**: `/albums.txt` in repository root
- **Format**: Plain text with album names (prefixed with `/`) and image filenames
- **Loading**: Single fetch at application startup
- **No rate limits**: Local file access

**Important**: Application breaks if:
1. albums.txt is missing or malformed
2. Image filenames in albums.txt don't match actual files in `/images`
3. Network connectivity issues (for hosted version)

### TailwindCSS CDN
- **URL**: `https://cdn.tailwindcss.com`
- **Version**: Latest (unversioned)
- **Config**: Custom theme with `coal` color (#111111)

---

## Security Considerations

1. **CSP**: Strict Content Security Policy defined
2. **No User Input**: No forms or user-submitted content
3. **Read-Only**: Only fetches local albums.txt file, never writes
4. **LocalStorage**: Only stores UI preferences (selected album, visited state)
5. **No Credentials**: No authentication tokens or sensitive data
6. **No External APIs**: All resources are local or CDN-based

---

## Future Enhancement Ideas

If user requests improvements, consider:
1. ✅ ~~Keyboard navigation~~ (implemented: arrows, up, down)
2. Album descriptions or metadata
3. Image metadata display (EXIF data)
4. Slideshow/autoplay mode within album
5. Download image button
6. Share functionality
7. Search across albums
8. Lazy loading for grid view
9. Image zoom/pan controls
10. Fullscreen API integration
11. PWA support (offline viewing)
12. Album cover image selection (instead of first 4)
13. Drag-and-drop album reordering in albums.txt

**Remember**: Keep enhancements simple and inline with existing architecture.

---

## Troubleshooting

### Images Not Loading
- Check `/images` directory has files
- Verify filenames in albums.txt match actual files
- Check browser console for 404 errors
- Verify CSP allows image sources
- Ensure albums.txt is accessible and properly formatted

### Albums Not Appearing
- Check albums.txt exists in repository root
- Verify albums.txt format (album names start with `/`)
- Check browser console for fetch errors
- Ensure at least one album is defined

### Album Selection Not Working
- Check browser console for JavaScript errors
- Verify event delegation is working (click on any part of album item)
- Clear localStorage and try again: `localStorage.clear()`
- Check that `selectAlbum()` function is defined

### Navigation Not Working
- Check JavaScript console for errors
- Verify event listeners are attached
- Test both click and touch events
- Check current index boundaries
- Ensure current album has images

### Styling Issues
- Verify TailwindCSS CDN loaded
- Check custom theme config (line 11)
- Test responsive breakpoints
- Clear browser cache

### Performance Issues
- Reduce image file sizes
- Check network tab for slow loads
- Verify preloading is working
- Check albums.txt parse time (should be instant)

---

## Contact & Context

**Repository**: lgqbc/gallery
**Owner**: Lukas Gilow (lukas.gilow@gmail.com)
**Purpose**: Personal photography gallery
**Theme**: Unedited photos from 2004 digicam
**Started**: 2026 (bofu/2026)

This is a personal project focused on simplicity and aesthetic presentation.

---

*Last Updated: 2025-12-06*
*Maintained by Claude for AI-assisted development*

---

## Recent Major Changes

### 2025-12-06: Album System Implementation
- **Removed GitHub API dependency** - Now uses local `albums.txt` file
- **Added album-based organization** - Images grouped into albums
- **Redesigned welcome popup** - Now shows album selector with preview grids
- **Renamed all images** - Sequential numbering (1-28.jpg) for easier management
- **Updated localStorage** - Tracks `selectedAlbum` and `visitedGallery`
- **Added keyboard navigation** - Arrow keys, up for grid, down for album selector
- **Event delegation** - More reliable click handling for album selection
- **Updated CSP** - Removed GitHub API endpoint from connect-src
