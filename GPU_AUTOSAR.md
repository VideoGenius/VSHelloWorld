# GPU Rendering and AUTOSAR Integration – Technical Insights

## Context and Platform
- **Platform**: TI TDA4 SoC (Jacinto platform)
- **Application**: Surround View and Human Vision Rendering
- **Frameworks used**:
  - **EGL**: For creating and managing GPU contexts and surfaces
  - **OpenGL ES SC (Safety Critical profile)**: For deterministic, safety-relevant rendering
  - **Classic AUTOSAR**: With a middleware wrapper to handle GPU-related tasks outside the standard BSW

## EGL & OpenGL ES SC Usage
- EGL was used to create rendering contexts and manage surfaces offscreen.
- The application allocated GPU memory buffers, used FBOs (framebuffer objects) to render into them, and passed the data downstream for display/compositing.
- OpenGL ES SC ensured compliance with safety profiles (predictable memory usage, no dynamic shaders).
- EGL streams (or DMA buffers in later setups) were used to pass buffers between ISP, GPU, and display units.

## Safety and Mixed-Criticality
- One project goal was to render safety overlays (e.g., parking path lines, 3D obstacle boundaries) alongside non-critical elements (background views).
- We isolated the overlays into different rendering layers, assigned with higher criticality, then composited them safely at the display level.
- On some setups, we worked closely with video drivers to enable multi-layer display blending under ASIL-B compliance.

## Challenges and Improvement Proposals for AUTOSAR
### Current Gaps:
- **No GPU abstraction layer in AUTOSAR (Classic or Adaptive)**, leading to platform-specific GPU access via vendor APIs.
- **Limited control or supervision of GPU workloads** by AUTOSAR RTE or OS schedulers.
- **Lack of standardization in handling mixed-criticality content on GPU-rendered outputs.**

### Suggestions:
- Introduce a GPU service abstraction in AUTOSAR Adaptive Platform to enable consistent access to rendering or compute acceleration across hardware.
- Define a memory and task model for GPU buffers and shaders (e.g., predefined compute kernels or draw calls).
- Support isolation and monitoring of GPU tasks via integration with RTE and Safety Manager (as done with CPU tasks).
- Reference Khronos standards like Vulkan SC or OpenGL ES SC for safe graphics/compute APIs, and define minimal compliant feature sets.

## Final Thoughts
As visual rendering and compute acceleration grow in ADAS and parking systems, AUTOSAR's ability to model, schedule, and supervise GPU tasks could become critical. Standardizing even a basic interface or configuration layer for GPU-related tasks could improve portability and system safety.

Happy to walk through this in a brief call if helpful.
