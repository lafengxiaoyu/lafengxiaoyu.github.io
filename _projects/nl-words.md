---
title: "NL Words - 荷兰语单词学习应用"
collection: projects
permalink: /projects/nl-words
date: 2025-01-01
---

A modern Dutch language learning application built with React, TypeScript, and Vite.

**Repository:** https://github.com/lafengxiaoyu/nl-words

## Description

NL Words is a comprehensive Dutch vocabulary learning platform designed to help learners improve their language skills through interactive word cards and progress tracking.

## Key Features

- 📚 **Rich Vocabulary Database** - Includes detailed word information: part of speech, variations, example sentences
- 🎯 **Difficulty Levels** - A1-C2 six levels suitable for learners at different stages
- 📊 **Progress Tracking** - Real-time learning progress and statistics display
- ✅ **Mastery Marking** - Mark words as mastered to track your learning journey
- 🔀 **Random Shuffle** - Randomize word order to increase learning challenge
- 💾 **Cloud Sync** - Optional Supabase cloud synchronization for data backup
- 📱 **Responsive Design** - Perfect adaptation for mobile and desktop devices
- 🚀 **Auto Deployment** - GitHub Actions automatic deployment to GitHub Pages

## Technology Stack

- **Frontend:** React 19, TypeScript, Vite
- **Backend:** Supabase (authentication and database)
- **CI/CD:** GitHub Actions
- **Deployment:** GitHub Pages

## Screenshots

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/projects/nl-words/截屏2026-03-08 11.02.36.png" title="Word Card Interface" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/projects/nl-words/截屏2026-03-08 11.02.52.png" title="Word Details View" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/projects/nl-words/截屏2026-03-08 11.04.53.png" title="Learning Progress" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/projects/nl-words/截屏2026-03-08 11.07.04.png" title="Difficulty Filtering" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

## Usage

### Basic Operations

1. **View Words**: The Dutch word card is displayed in the center of the page
2. **Flip Card**: Click the card to view the Chinese translation
3. **Navigate**: Use "Previous" and "Next" buttons to browse words
4. **Mark Mastered**: Click "Mark as Mastered" to track learned words
5. **View Statistics**: Click "Show Statistics" to see detailed learning data

### Advanced Features

- **Difficulty Filtering**: Click level buttons (A1-A2, B1-B2, C1-C2) to filter words
- **Random Order**: Click "🔀 Shuffle" to randomize word order
- **View Details**: Click "Show Details" to see complete part of speech, example sentences, etc.
- **Familiarity Level**: Set word familiarity (New/Learning/Familiar/Mastered)
- **Cloud Sync**: Login to sync learning progress to cloud

## License

MIT License
