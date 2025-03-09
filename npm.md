# NPM Basics

## 1. `npm init`

- Initializes a new `package.json` file.
- Asks for project details (name, version, description, entry point, etc.).
- Without `npm init`, your project won’t have a structured way to track dependencies.

## 2. `npm install <package-name>`

### **Old Behavior (Older NPM Versions)**

- Installs a specific package.
- If `package.json` exists, it adds the package to `dependencies`.
- If `package.json` is missing, it installs the package but does **not** create `package.json`, meaning the project has no record of installed packages.

### **New Behavior (Latest NPM Versions)**

- Installs a specific package **and automatically creates `package.json`** if it doesn’t exist.
- The package gets added to `dependencies` inside `package.json`.
- This makes `npm init` optional for simple projects.

## 3. `npm install` (without a package name)

- Installs all dependencies listed in `package.json`.

## Do You Always Need `npm init`?

- **Yes (Older NPM Versions)** → Required to create `package.json` before installing packages.
- **No (Newer NPM Versions)** → `package.json` is automatically created when installing the first package.

---

# Short Notes (With Your Friend Example)

## 1. Why is `package.json` Important?

- If you push your project to GitHub **without `package.json`**, your friend:
  - Won’t know which dependencies to install.
  - Running `npm install` won’t work (older npm versions).
  - Will have to guess and manually install packages.

## 2. What If You Push `node_modules` But Not `package.json`?

- Your friend **might** be able to run the project, but:
  - `node_modules` is **huge** and will slow down Git.
  - If they accidentally delete `node_modules`, they won’t know how to reinstall it.
  - If they use a different Node.js version, dependencies **may break**.

## 3. Correct Way to Share Your Project

- **Push only** `package.json` and `package-lock.json`.
- **Add `node_modules` to `.gitignore`** (don’t push it).
- After downloading, your friend should run:
  ```sh
  npm install
  ```
  - This will **automatically** download all required dependencies.

## Final Conclusion

Without `package.json`, your friend will struggle to run your project. **Always push `package.json`, not `node_modules`!**

### **New NPM Versions Make It Easier!**

- If `package.json` is missing, running `npm install <package-name>` will **automatically create it**.
- This means `npm init` is no longer strictly required before installing packages.
