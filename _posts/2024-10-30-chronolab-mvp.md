---
layout: post
title: Learning React Through Building a Lab Data Visualization Tool
subtitle: Creating Chronolab - A Desktop Application for Synchronized Video and Data Visualization
gh-repo: cormazz/chronolab
gh-badge: [star, fork, follow]
thumbnail-img: /assets/blog_posts/bp.chronolab/cover_img.webp
share-img: /assets/blog_posts/bp.chronolab/cover_img.webp
tags: [react, rust, tauri, software development, data visualization]
comments: true
author: Corrado R. Mazzarelli
---

{: .box-success}
This article explores my journey learning React while building Chronolab, a desktop application that enables synchronized visualization of laboratory video and data. The tool allows researchers to experience recorded lab data as if they were watching it live, with synchronized video and data streams. All the material used to create this is within the [GitHub repository](https://github.com/cormazz/chronolab).

* Do not remove this line (it will not be displayed)
{:toc}

# Summary

Chronolab emerged from a desire to create a tool that would allow researchers to revisit laboratory experiments through synchronized video and data visualization, replicating the experience of being in the lab watching live data streams. The project served dual purposes: creating a useful tool for the scientific community while providing hands-on experience with React, Rust, and modern application development practices.

The development process involved making numerous architectural decisions, from choosing appropriate libraries to determining the optimal state management strategy. These decisions shaped both the application's functionality and my understanding of frontend development.

# Demo

<video width="100%" controls style="border-radius: 10px;">
  <source src="/assets/blog_posts/bp.chronolab/mvp_demo_video.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

# Key Technical Decisions

## Frontend vs Backend State Management

One of the most significant architectural decisions involved implementing the "One Absolute Truth" (OAT) principle for state management. Rather than relying solely on React's built-in state management, I maintained the application state in the Rust backend with the frontend establishing event listeners for state changes. This approach offered several advantages:

1. Easier implementation of save/load functionality through Rust struct serialization
2. Reduced potential for state mismatches between frontend and backend
3. More robust error handling through compiler enforcement
4. Cleaner architecture for future feature additions

The implementation split across two key files:
- Backend: `/src-tauri/src/global_state.rs` containing the core Rust struct
- Frontend: `/src/hooks/useGlobalState.ts` implementing a custom React hook

## UI Framework Selection

I performed extensive research reading Reddit posts about React UI frameworks. After learning a plethora of novel insults, I decided that the community was as opinionated as they were divided on the issue. Given that this is a learning project and I had already learned TailwindCSS, I decided to try Material UI, which seemed to be a popular choice. I also tried Fluent UI, which had a more appealing aesthetic, but was difficult to get working. This experience taught me that when learning new technologies, sometimes the most popular choice is popular for good reasons.

## Plotting Library Evolution

The journey to find the right plotting library was particularly instructive. I initially attempted to use Plotly but encountered browser compatibility issues with WebGL. This led to pivoting to Apache ECharts, which proved to be a superior choice offering:

- Built-in data downsampling capabilities
- Better interactivity features
- More reliable x-axis range slider functionality
- Improved performance with large datasets

This experience highlighted the importance of being flexible with technical choices and willing to pivot when encountering roadblocks.

# Development Challenges

## Cross-Platform Development

The project initially targeted Linux development through Dev Containers, but a Tauri bug preventing video playback forced a pivot to Windows development. This experience highlighted the challenges of cross-platform development and the importance of early testing across different environments.

## State Management Complexity

Implementing the backend state management required careful consideration of type safety and compiler enforcement. I utilized the newtype pattern to prevent mixing up fields with the same primitive types, and created an enum-based system for field access. While this added some initial complexity, it provided better long-term maintainability and reliability.

## Data Handling Architecture

The initial design had the backend loading CSV files using Polars and transmitting data via IPC buffer. While this worked, it became somewhat redundant after switching to Apache ECharts with its built-in downsampling. This taught me the importance of regularly re-evaluating architectural decisions as requirements and capabilities evolve.

# Future Development

The project has several planned enhancements:

1. **IOI (Items of Interest) Noting**
   - Adding capability to mark and annotate specific moments
   - Quick navigation to marked events
   - Interactive timeline visualization

2. **Video Section Labeling**
   - YouTube-style section markers on the timeline
   - Hover previews for different sections

3. **Advanced Video Integration**
   - Frame-by-frame seeking capabilities
   - Automated video start time parsing
   - Interactive synchronization between plot points and video timeline

4. **Performance Optimizations**
   - Adding Polars performance features
   - Improved state management with RwLock
   - Enhanced CSV data handling

# Lessons Learned

1. **Framework Selection**: Popular frameworks often provide the best documentation and community support, which is invaluable when learning.

2. **Architectural Flexibility**: Being willing to pivot technologies (like switching from Plotly to Apache ECharts) can lead to better solutions.

3. **Type Safety**: Investing time in proper type safety and compiler enforcement pays dividends in reduced bugs and easier maintenance.

4. **Cross-Platform Considerations**: Early testing across different platforms can prevent major roadblocks later in development.

# Conclusion

Building Chronolab provided invaluable hands-on experience with React while creating a useful tool for the scientific community. The project demonstrated the importance of thoughtful architectural decisions, the value of being flexible with technical choices, and the benefits of strong type safety. While there are still features to be added and optimizations to be made, the current implementation provides a solid foundation for future development.

The experience gained from this project, particularly in state management and frontend architecture, will inform future development work and has provided practical insights into modern application development practices.
