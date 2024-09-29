---
layout: post
title: Dance DJ Assistant
subtitle: Learning to Write a Python Web App
gh-repo: cormazz/DanceDJAssistant
gh-badge: [star, fork, follow]
thumbnail-img: /assets/blog_posts/bp.dance_dj_assistant/cover_img.png
share-img: /assets/blog_posts/bp.dance_dj_assistant/cover_img.png
tags: [python, flask, docker, spotify, dance, DJ, nginx]
comments: true
author: Corrado R. Mazzarelli
---

{: .box-success}
This article quickly highlights a Python library and accompanying web application UI to enable dance DJs that use Spotify to organize their playlists. The underlying Python library containing the actual functionality is located in the [GitHub repository](https://github.com/cormazz/DanceDJAssistant). The actual DanceDJ webpage can be accessed at [dancedj.corradomazzarelli.com](https://dancedj.corradomazzarelli.com).

* Do not remove this line (it will not be displayed)
{:toc}

# Summary

{: .box-note}
This project offers a solution for dance DJs using Spotify. It allows users to categorize and organize songs based on tempo, mood, and style, simplifying the DJing process. Built using Flask and Docker, this tool can be easily deployed for quick access on various platforms.

The web application integrates with the Spotify API, giving users real-time control over their playlist creation. Whether it's for a ballroom dance or a fast-paced swing event, the app enables DJs to tailor their music to match the event's energy. See the home page for more information.

To see an example of the project in action, visit the [homepage](https://dancedj.corradomazzarelli.com).

# Features

**Tempo Organization**: Automatically organizes tracks based on beats-per-minute (BPM) to fit a designated profile.
**Spotify Integration**: Syncs seamlessly with Spotify, allowing DJs to access and modify their playlists.
**User-Friendly Interface**: Built with Flask and optimized for ease of use, the web interface provides DJs with quick access to playlist management tools.

## Technologies Used

* Python: Core programming language for the backend.
* Flask: Web framework used to build the application interface.
* Spotify API: Provides real-time access to music data.
* Docker: Enables easy deployment and scaling of the application across platforms.

## Interesting Aspects

The task at hand was to align the BPMs of songs in a playlist to match a defined profile. This is so that you don't get a playlist with all the fast/slow songs grouped together, which is likely to tire/bore dancers. Instead, you try to make your playlist have a cyclic profile, like a sinusoid. Interestingly enough, the problem of aligning song BPMs to a profile can be better thought of as **assigning** different songs to spots on a profile. When thought of like that, the solution is one that has already been found for a common problem in computer science: the assignment problem. Using the Hungarian algorithm implementation in Scipy allowed for relatively quick solutions to assigning the playlist songs. Combine that with Spotify's SpotiPy API, and an application was born.

# Conclusion

The Dance DJ Assistant offers an efficient way for DJs to curate and manage playlists. By leveraging Python and Spotify’s capabilities, DJs can create custom playlists that keep the dance floor energized. For more details, check out the GitHub repository and the [homepage](https://dancedj.corradomazzarelli.com), which goes into details about the functionality. 
