# Awesome Modern Terminals

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![GitHub](https://img.shields.io/badge/GitHub-100000?&logo=github&logoColor=white)](https://github.com/threehymns/awesome-terminals)
[![SvelteKit](https://img.shields.io/badge/SvelteKit-FF3E00?&logo=svelte&logoColor=white)](https://kit.svelte.dev/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-38B2AC?&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)

A curated collection of modern, feature-rich (or minimal!) terminal emulators for developers. Discover, compare, and choose your next terminal with ease!

## 🌟 Features

- **Interactive Web Interface**: Built with SvelteKit + Tailwind CSS
- **Smart Filtering**: Search by name, platform (Windows/macOS/Linux), and features
- **Rich Metadata**: Compare terminals based on 15+ criteria including:
  - GPU acceleration
  - Custom themes
  - Platform (Windows/macOS/Linux/Android)
  - And more!
- **Community-Driven**: Easily add new terminals or update existing entries

### TODO

- [ ] Publish a live website

## 🚀 Getting Started

**Run Locally**:

```bash
git clone https://github.com/threehymns/awesome-terminals.git
cd awesome-terminals
npm install
npm run dev
```

## 📊 Data Structure (terminals.json)

Each terminal entry includes:

```json
{
	"name": "Terminal Name",
	"platform": ["Windows", "macOS", "Linux"],
	"tags": ["Open Source", "File Previews", "AI", "Electron"],
	"features": ["Built-in AI", "File browser/editor", "Tiling layout"],
	"repo": "https://github.com/org/repo",
	"website": "https://terminal-website.com"
}
```

## 🤝 Contributing

Help us improve this resource! To add/update terminals:

1. Edit [`src/terminals.json`](src/terminals.json)
2. Follow existing data format and feature standards
3. Submit a Pull Request

## 📄 License

MIT License - see [LICENSE](LICENSE) file.

## 🙏 Acknowledgments

- Terminal maintainers and open source contributors
- SvelteKit and Tailwind CSS communities
- Inspired by awesome-\* lists across GitHub
