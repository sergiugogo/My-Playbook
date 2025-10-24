## **Canvas Sizing Issue - Root Cause & Solution**

### **Problem:**
The Three.js canvas was rendering with incorrect dimensions on initial page load, causing 3D objects to appear cut off or misaligned. The canvas would only display correctly after scrolling or triggering a browser resize event.

### **Root Cause:**
React Three Fiber's automatic canvas sizing depends on CSS being fully loaded and the browser completing its layout calculations. On initial render:
1. The parent container's height is defined via Tailwind classes (e.g., `h-[450px]`)
2. CSS needs to be parsed and applied
3. The browser needs to calculate the final layout
4. Only then can the canvas measure its container and set the correct aspect ratio

This creates a **race condition** where the canvas initializes before knowing its true container dimensions, resulting in an incorrect camera aspect ratio and viewport.

### **Solution:**
Manually control the canvas initialization using the `onCreated` callback to set dimensions explicitly:

```tsx
<Canvas
  onCreated={({ gl, camera }) => {
    // Manually set renderer size using actual container dimensions
    gl.setSize(
      containerRef.current?.clientWidth || 800, 
      containerRef.current?.clientHeight || 450
    );
    
    // Update camera aspect ratio to match container
    if (camera.type === 'PerspectiveCamera') {
      (camera as THREE.PerspectiveCamera).aspect = 
        (containerRef.current?.clientWidth || 800) / 
        (containerRef.current?.clientHeight || 450);
      camera.updateProjectionMatrix(); // Required after changing aspect
    }
  }}
>
```

### **Key Changes Made:**

1. **Added `containerRef`** to access the actual DOM dimensions
2. **Used inline styles** instead of Tailwind classes for the wrapper div (`style={{ width: '100%', height: '100%', position: 'relative' }}`)
3. **Added `onCreated` callback** to manually initialize canvas size and camera aspect ratio
4. **Added `position: 'relative'`** to establish proper positioning context

### **Why This Works:**
- The `onCreated` callback fires **after** the canvas DOM element exists but **before** the first render
- We read the actual `clientWidth` and `clientHeight` from the container
- We manually set both the WebGL renderer size AND the camera aspect ratio
- We call `updateProjectionMatrix()` to recalculate the camera's projection matrix with the correct aspect ratio
- This ensures the 3D scene renders with correct proportions from the very first frame

### **Fallback Values:**
The `|| 800` and `|| 450` provide sensible defaults if the container hasn't calculated dimensions yet, preventing a 0x0 canvas.

### **Lesson Learned:**
When working with dynamically sized canvases in React Three Fiber, don't rely solely on automatic sizing. Use `onCreated` to manually control initialization when consistent initial rendering is critical.
