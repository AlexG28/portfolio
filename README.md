# Aleksandr Gyumushyan - Personal Website

A personal website built with Hugo showcasing my skills, projects, blog posts, and photography.

## 🚀 Features

- **Landing Page**: Professional about page with skills and background
- **Blog Section**: Technical blog posts with code snippets and images
- **Projects Portfolio**: Showcase of software development projects
- **Photography Gallery**: Collection of photography work
- **Responsive Design**: Works on all devices
- **Dark/Light Theme**: Automatic theme switching

## 📁 Project Structure

```
portfolio/
├── content/                 # Content files
│   ├── about.md            # Landing page
│   ├── projects.md         # Projects showcase
│   ├── photography.md      # Photography gallery
│   └── posts/              # Blog posts
│       └── my-first-blog-post.md
├── static/                 # Static assets
│   └── images/            # Images directory
├── hugo.toml              # Hugo configuration
└── scripts/               # Development scripts
    └── dev.sh
```

## 🛠️ Getting Started

### Prerequisites

- [Hugo](https://gohugo.io/installation/) installed on your system
- Git for version control

### Development

1. **Start the development server:**
   ```bash
   ./scripts/dev.sh
   ```
   Or manually:
   ```bash
   hugo server --bind 0.0.0.0 --port 1313
   ```

2. **View the site:**
   Open http://localhost:1313 in your browser

3. **Stop the server:**
   Press `Ctrl+C` in the terminal

### Adding Content

#### Blog Posts
1. Create a new file in `content/posts/`
2. Use the front matter template:
   ```markdown
   +++
   title = "Your Post Title"
   date = 2024-01-15T10:00:00+01:00
   draft = false
   description = "Brief description of your post"
   tags = ["tag1", "tag2"]
   categories = ["category"]
   author = "Aleksandr Gyumushyan"
   +++
   
   # Your content here
   ```

#### Images
1. Place images in `static/images/`
2. Reference them in content: `![Description](/images/filename.jpg)`

#### Photography
1. Add your photos to `static/images/`
2. Update `content/photography.md` with your work

## 📝 Content Management

### Navigation Menu
The menu is configured in `hugo.toml`:
- Blog: `/posts/`
- Projects: `/projects/`
- Photography: `/photography/`
- About: `/about/`

### Social Links
Update social links in `hugo.toml`:
- GitHub
- LinkedIn
- Resume

### Theme Customization
The site uses the `hugo-coder` theme. You can customize:
- Colors and styling
- Layout templates
- Custom CSS/JS

## 🎨 Customization

### Colors and Styling
- Edit `themes/hugo-coder/assets/scss/` files
- Add custom CSS in `static/css/`

### Layout Changes
- Override theme templates in `layouts/`
- Create custom shortcodes in `layouts/shortcodes/`

### Configuration
- Update site settings in `hugo.toml`
- Modify theme parameters
- Add new menu items

## 📸 Required Images

Add these images to `static/images/`:
- `avatar.jpg` - Your profile photo
- `microservice-architecture.png` - For blog post
- `street-photography.jpg` - Photography page
- `landscape-photography.jpg` - Photography page
- `portrait-photography.jpg` - Photography page

## 🚀 Deployment

### Build for Production
```bash
hugo --minify
```

### Deploy Options
- **Netlify**: Connect your Git repository
- **GitHub Pages**: Use GitHub Actions
- **Vercel**: Connect your repository
- **Traditional hosting**: Upload `public/` folder

## 📚 Resources

- [Hugo Documentation](https://gohugo.io/documentation/)
- [Hugo Coder Theme](https://github.com/luizdepra/hugo-coder)
- [Markdown Guide](https://www.markdownguide.org/)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

**Built with ❤️ using Hugo and the Coder theme** 