# Playwright Complete Guide

📚 **Playwright Test Framework - Complete Guide**

A comprehensive guide to Playwright end-to-end testing framework, reorganized for better readability and learning flow.

## 📖 What's Included

- **Installation & Setup** - Complete installation guide for all platforms
- **Writing Tests** - Actions, assertions, test isolation, and hooks
- **Running & Debugging** - CLI commands, UI mode, inspector, and test reports
- **Advanced Features** - Code generation, trace viewer, CI/CD setup
- **VS Code Integration** - Extension features and debugging tools
- **Quick Reference** - Commands, configurations, and best practices

## 📄 PDF Generation

This repository includes automated PDF generation using GitHub Actions.

### Automatic PDF Generation

The PDF is automatically generated whenever the `Playwright_Guide_Reorganized.md` file is updated:

1. **Push changes** to `Playwright_Guide_Reorganized.md`
2. **GitHub Actions** automatically runs the workflow
3. **Download PDF** from the Actions artifacts or Releases

### Manual PDF Generation

You can also generate PDF locally using the provided script:

```bash
# Make sure you have pandoc and LaTeX installed
./generate-pdf.sh
```

**Requirements:**
- pandoc
- LaTeX (pdflatex or xelatex)

**macOS:**
```bash
brew install pandoc
brew install --cask mactex  # or basictex for minimal install
```

**Ubuntu:**
```bash
sudo apt-get install pandoc texlive-latex-extra
```

## 📁 Files

- `Playwright_Guide_Reorganized.md` - Main guide in Markdown format
- `Playwright_Complete_Guide.html` - HTML version for web viewing
- `Playwright_Complete_Guide.pdf` - Automatically generated PDF (via GitHub Actions)
- `generate-pdf.sh` - Local PDF generation script
- `.github/workflows/generate-pdf.yml` - GitHub Actions workflow

## 🔗 Links

- **Repository**: https://github.com/vanvo19870515/learn-everything-for-me
- **Playwright Docs**: https://playwright.dev/docs/intro
- **GitHub Actions**: View workflow runs in the Actions tab

## 📋 Recent Updates

- ✅ Reorganized content for better learning flow
- ✅ Added automated PDF generation with GitHub Actions
- ✅ Improved formatting and professional PDF output
- ✅ Added local PDF generation script
- ✅ Fixed workflow issues and improved reliability

---

*Automatically generated PDF available in GitHub Actions artifacts and releases.*
