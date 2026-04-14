# GEMINI.md - 42 Guide Website

## Project Overview
This project is a comprehensive GitBook-style documentation and guide for the 42 school curriculum. It covers projects from Rank 00 to Rank 06, providing conceptual explanations, implementation strategies, and exam preparation resources. The content is primarily in Korean and aims to help students understand the "why" and "how" behind each project rather than just providing a solution to copy.

## Directory Overview
The workspace is organized by curriculum ranks and supplementary resources:

- **`rank-0x/`**: Detailed guides for projects in each rank (e.g., Libft, get_next_line, Minishell, etc.).
- **`exams/`**: Resources, level-by-level exercises, and tips for 42 exams (Rank 02 to Rank 05).
- **`useful-tools/`**: Documentation for general C programming concepts (Makefiles, C Structures, Linked Lists, etc.).
- **`minilibx/`**: Specific guidance for the MiniLibX graphics library used in various 42 projects.
- **`.gitbook/`**: Contains static assets like images and diagrams used throughout the documentation.

## Key Files
- **`README.md`**: The landing page of the guide, explaining its purpose and providing contact information for the authors (Laura & Simon).
- **`SUMMARY.md`**: The backbone of the GitBook, defining the table of contents and navigation structure.
- **`team.md`**: Information about the creators and contributors.

## Usage
- **Browsing**: Navigate through the directory structure to find specific project guides. Each project folder typically contains a `README.md` for overview and supplementary files for deeper concepts.
- **Contribution**: To add or update content, modify the respective Markdown files and ensure any new files are linked in `SUMMARY.md` to maintain the GitBook structure.
- **Assets**: When adding images, place them in `.gitbook/assets/` and reference them using relative paths.

## Development Conventions
- **Language**: The primary language for documentation is Korean.
- **Tone**: Professional, encouraging, and educational.
- **Formatting**: Uses standard GitHub Flavored Markdown with GitBook-specific extensions (like `{% code %}` blocks).
