# Features for the testing

This document highlights the major features implemented from the beginning to transform the base Docusaurus framework into a fully functional CMS, and explains the mechanics of how they were achieved.

## 1. Internal Developer Workspace
*   **Feature:** A dedicated UI where developers can view repositories, explore files, and edit code directly in the browser.
*   **How we achieved it:** We created a protected route (`/internal/workspace`). The React frontend fetches a directory tree from our Express backend API. When a user clicks a file, the API reads the file from the local disk (`fs.readFile`) and sends the raw text to the frontend, which is then loaded into the `@monaco-editor/react` component. When saved, an API request writes the updated text back to the file system.

## 2. Dynamic Git Synchronization
*   **Feature:** The ability to sync with remote GitHub repositories using Personal Access Tokens (PATs) and push commits directly from the dashboard UI.
*   **How we achieved it:** We built a PAT management UI that saves encrypted tokens to the SQLite database. The backend uses the `simple-git` NodeJS library. When a "Sync" or "Push" action is triggered, the backend constructs the remote Git URL using the stored PAT (e.g., `https://<PAT>@github.com/...`) and executes the Git commands on the server's local file system in an isolated `workspaces/` directory.

## 3. Session-Aware Authentication & Protected Routes
*   **Feature:** Securing the internal dashboards so only authorized users can access the CMS, while keeping public docs open.
*   **How we achieved it:** We created a `<ProtectedRoute />` React wrapper component. It checks localStorage for a valid JWT token. If missing or invalid (verified via an API call to the Express backend), the user is redirected to the `/internal` login page. This ensures the static React site can handle dynamic user sessions.

## 4. Admin Profile & Branding Management
*   **Feature:** Admins can dynamically change the site title, upload custom logos, and apply branding across the site without manually editing code.
*   **How we achieved it:** We built a Profile Management UI on the Admin dashboard. When settings are updated, the frontend sends a `multipart/form-data` request to the backend. The backend uses `multer` to save uploaded logos to the `static/` directory and writes the new configuration variables directly into the project's `.env` file using the `fs` module. Finally, it triggers a programmatic server restart to re-build Docusaurus with the new assets.

## 5. Clean, Professional Admin Interface
*   **Feature:** A minimalist, monochrome UI design for the developer and admin portals (White/Gray/Black).
*   **How we achieved it:** We stripped out standard Docusaurus CSS and implemented custom CSS modules and global variables. We focused on high-contrast borders, flat design, subtle hover states, and SVG iconography to replace generic emojis and gradients, creating a highly polished, enterprise-ready look.
