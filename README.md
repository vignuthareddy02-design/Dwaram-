# Dwaram Interiors Quotation System

A professional, responsive web-based quotation tool designed for interior designers. This application allows you to manage rooms, add detailed line items with dimensions, calculate costs with GST, and generate clean, printable PDFs.

## Features

- **Authentication**: Secure login system with session persistence.
- **Room Management**: Create, edit, and delete multiple rooms (e.g., Kitchen, Living Room, Master Bedroom).
- **Itemized Quotes**: Add items with width, height, rate, and unit calculations.
- **Dynamic Pricing**: Automatic subtotal and grand total calculations.
- **GST Support**: Optional GST (18%) toggle.
- **Save/Load**: Save multiple quotations to local storage for later retrieval.
- **Printable Reports**: Clean, professional print layout designed for sharing with clients.
- **Responsive Design**: Works on desktop and handles basic mobile viewing.

## Tech Stack

- **Framework**: React 18
- **Build Tool**: Vite
- **Styling**: Tailwind CSS
- **Animations**: Framer Motion
- **Icons**: Lucide React

---

## How to Upload to GitHub Manually

If you want to move this project to your own GitHub account:

### 1. Initialize Git Locally
If you have Git installed on your computer, navigate to the project folder and run:
```bash
git init
git add .
git commit -m "Initial commit"
```

### 2. Create a Repository on GitHub
1. Go to [github.com/new](https://github.com/new).
2. Give it a name (e.g., `dwaram-interiors-quote`).
3. Click "Create repository".

### 3. Push to GitHub
Copy the commands from the "push an existing repository" section on GitHub:
```bash
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git branch -M main
git push -u origin main
```

---

## Local Development

1. **Install Dependencies**:
   ```bash
   npm install
   ```

2. **Run Development Server**:
   ```bash
   npm run dev
   ```

3. **Build for Production**:
   ```bash
   npm run build
   ```

## Configuration

You can change the contact details (Phone, Email) at the top of `src/App.tsx`:

```typescript
const CONTACT_PHONE = "+91 9742430002";
const CONTACT_EMAIL = "dwaraminteriors@gmail.com";
```

