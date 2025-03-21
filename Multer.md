# Multer File Upload Workflow with File Renaming

## 1️⃣ User Uploads a File

✅ The user selects a file and submits a form.

✅ The form data is sent to the server as `multipart/form-data`.

---

## 2️⃣ Multer Intercepts the Request

✅ Multer extracts the file from the request.

✅ Before storing, the file is renamed to avoid conflicts (e.g., adding a timestamp or unique ID).

✅ The file is stored on the server (e.g., in an `uploads/` folder).

✅ The file’s name, path, and metadata (size, type) are stored in `req.file`.

### File Renaming Example in Multer:

```javascript
const multer = require("multer");

const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, "uploads/"); // Save files in uploads/ folder
  },
  filename: (req, file, cb) => {
    const uniqueName = Date.now() + "-" + file.originalname;
    cb(null, uniqueName); // Rename file before saving
  },
});

const upload = multer({ storage });
```

---

## 3️⃣ Server Stores File Name in the Database

✅ The actual file is **NOT** stored in the database (to save space).

✅ Instead, only the file’s name or path (e.g., `uploads/image.jpg`) is stored in the database.

### Storing in MongoDB:

```javascript
const user = new User({
  name: req.body.name,
  profilePic: req.file.path, // Only store path, not the whole file
});
await user.save();
```

---

## 4️⃣ Retrieving the File Later

✅ When needed, the application fetches the file path from the database.

✅ The file is served from the server’s storage using Express.

### Serving the File with Express:

```javascript
const path = require("path");

app.get("/uploads/:filename", (req, res) => {
  const filePath = path.join(__dirname, "uploads", req.params.filename);
  res.sendFile(filePath);
});
```

---

## 🔹 Workflow Diagram

1. **User Uploads File** → 2. **Multer Processes (Extracts file, Renames it, Saves to server, Stores name in req.file)**  
   ↓
2. **Server Stores File Name in Database**  
   ↓
3. **File is Retrieved Using Stored Path**  
   ↓
4. **Server Sends File to User When Requested**

---

## ✅ Key Benefits of Renaming:

✔ Prevents filename conflicts.

✔ Helps in better organization.

✔ Avoids accidental overwrites of files with the same name.

---

## 🔎 Understanding `req.file`

❌ `req.file` itself is **NOT** stored in the database.

✅ Only **specific data** (like the file name or path) from `req.file` is stored in the database.

✅ The actual file stays on the server (or cloud storage if used).

### Example of `req.file` Object:

```json
{
  "fieldname": "profilePicture",
  "originalname": "avatar.png",
  "encoding": "7bit",
  "mimetype": "image/png",
  "destination": "uploads/",
  "filename": "1710632178493-avatar.png",
  "path": "uploads/1710632178493-avatar.png",
  "size": 12345
}
```

### What is Stored in the Database?

Instead of storing the entire `req.file` object, only the file path or filename is saved in the database.

---

## 📄 Understanding `multipart/form-data` & JSON

### What is `multipart/form-data`?

Multer is a middleware for handling `multipart/form-data`, which is primarily used to handle file uploads in Node.js and Express applications.

## What is multipart/form-data?
`multipart/form-data` is used to send **files (like images, PDFs, etc.), text fields, and other form data** in a single request. It allows both **binary (file) data** and **text data** to be transmitted together properly.

For example, if you upload:
- A **profile picture** (`image.jpg`)
- A **username** (`"JohnDoe"`)

Both will be sent in the same request but in **separate parts**. This is why it's called **"multipart"**!

- It is an encoding type used for submitting files via forms.
- Unlike `application/json`, it allows binary file data to be sent along with text fields.
- Each field in the form, including the file, is sent as a separate part of the request body.
- Required when dealing with file uploads in HTML forms (`<form enctype="multipart/form-data">`).

### Why not just use JSON?

- `application/json` **cannot handle file uploads**, as it only supports text-based data.
- `multipart/form-data` **splits the request into multiple parts**, allowing file and text data to be sent together.
- This is why Multer is needed to parse `multipart/form-data` requests and extract the file.

---

## 📈 Summary

❌ `req.file` itself is **NOT** stored in the database.

✅ Only the file path (`req.file.path`) or filename (`req.file.filename`) is stored.

✅ The actual file stays on the server or cloud storage.

---

## **Proper Use of `app.get('/uploads/:filename')`**

This route is used to serve uploaded files from the server.

✅ When a user requests a file, the server fetches it from the `uploads/` directory and sends it.

✅ Using `res.sendFile(filePath)` ensures that the file is properly delivered to the user.

✅ This approach keeps file access secure and efficient by only allowing stored filenames to be retrieved.

### Why `path.join(__dirname, 'uploads', req.params.filename)`?

- `__dirname` ensures that the correct absolute path is used regardless of where the script is executed.
- `'uploads'` is the designated folder where files are stored.
- `req.params.filename` dynamically fetches the requested file.
- `path.join` constructs the full path in a way that works across different operating systems.

---

🚀 **Multer renames the file before storing it, then saves its metadata in `req.file`.**

🚀 **The database only stores the file name/path, keeping it lightweight.**

🚀 **The file can be retrieved later using the stored path and served to users.**
