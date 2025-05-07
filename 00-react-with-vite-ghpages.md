# React with Vite Tutorial (with GitHub Pages Deployment)

## Prerequisites

Ensure you have the following installed:

* **Node.js** (>= 14.18.0 or >=16.0.0).
* A GitHub account for deployment.

---

## 1. Create a New React Project

Run the following command to create a new React project using Vite:

```bash
npm create vite@latest my-react-app --template react
```

### Command Breakdown:

* `npm create vite@latest`: Uses Vite to scaffold a project.
* `my-react-app`: The name of your project folder.
* `--template react`: Specifies React as the framework.

---

## 2. Navigate to the Project Directory

```bash
cd my-react-app
```

---

## 3. Install Dependencies

Install the required dependencies:

```bash
npm install
```

---

## 4. Project Structure

After installation, your project directory will look like this:

```
my-react-app
├── node_modules
├── public
├── src
│   ├── App.css
│   ├── App.jsx
│   ├── index.css
│   ├── main.jsx
├── .gitignore
├── index.html
├── package.json
├── vite.config.js
```

---

## 5. Start the Development Server

Run the development server:

```bash
npm run dev
```

The server will start, and you can access your app at `http://localhost:5173`.

---

## 6. Edit the Starter Code

Customize the app by editing `src/App.jsx`:

```jsx
function App() {
  return (
    <div className="App">
      <h1>Hello, React with Vite!</h1>
    </div>
  );
}

export default App;
```

Add styles in `src/App.css`:

```css
body {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
  margin: 0;
  padding: 0;
}

.App {
  text-align: center;
  padding: 2rem;
}
```

---

## 7. Build for Production

When you’re ready to deploy, create a production build:

```bash
npm run build
```

The output will be generated in the `dist` folder.

---

## 8. Deploy to GitHub Pages

### Step 1: Initialize a Git Repository
- You should run the git init command in the root directory of your project, where the package.json file is located.
If you haven’t initialized a Git repository, run:

```bash
git init
git add .
git commit -m "Initial commit"
```

### Step 2: Add a GitHub Repository

Create a repository on GitHub (e.g., `my-react-app`).

Connect your local project to the GitHub repository:

```bash
git remote add origin https://github.com/your-username/my-react-app.git
```

### Step 3: Install GitHub Pages Plugin

Install the GitHub Pages package:

```bash
npm install gh-pages --save-dev
```

### Step 4: Update `package.json`

Add the following fields to `package.json`:

**Add a homepage URL (replace `your-username` and `my-react-app`):**

```json
"homepage": "https://your-username.github.io/my-react-app",
```

**Update the `scripts` section:**

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview",
  "deploy": "npm run build && gh-pages -d dist"
}
```
---

  ### Update `vite.config.js`

Open `vite.config.js` and configure the `base` property. The `base` property is crucial for GitHub Pages to serve the app correctly from the subpath where it’s deployed.

```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  base: '/<your-repo-name>/', // Replace with your repo name
});
```

For example, if your repository name is `my-react-app`, set:

```javascript
base: '/my-react-app/';
```


### Step 5: Deploy to GitHub Pages

Run the deployment command:

```bash
npm run deploy
```

---

## 9. Verify Deployment

Visit your app at:

```
https://your-username.github.io/my-react-app
```

## Troubleshooting: Blank Page Issue

If your deployed page is blank:

1. **Check `vite.config.js`:** Ensure the `base` property is correctly set to `/<your-repo-name>/`.
2. **Clear Browser Cache:** If changes aren’t reflecting, clear your cache or try an incognito browser window.
3. **Rebuild and Redeploy:** Run `npm run build` and `npm run deploy` again.
4. **Verify GitHub Pages Configuration:** Ensure the `gh-pages` branch is selected as the source in GitHub Pages settings.



---
