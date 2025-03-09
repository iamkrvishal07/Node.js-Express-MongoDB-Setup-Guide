# Node.js-Express-MongoDB-Setup-Guide

# README.md

## Node.js + Express + MongoDB Setup Guide

### 1. Project Initialize karo

```sh
npm init -y
```

### 2. Install Dependencies

```sh
npm install express mongoose
npm i -D nodemon  # Dev Dependency
```

### 3. Script Setup & `index.js` Banao

#### `package.json` mei script add karo:

```json
"scripts": {
  "server": "nodemon index.js"
}
```

#### `index.js` banao aur yeh likho:

```js
const express = require("express");
const app = express();
const PORT = 5000;

// Testing Route
app.get("/", (req, res) => {
    res.send("Hello User");
});

app.listen(PORT, () => {
    console.log("Server is running on http://localhost:" + PORT);
});
```

#### Server Start Karo:

```sh
npm run server
```

Check karo: `http://localhost:5000/`

---

### 4. Database Connection (utils/db.js) 

#### `utils/db.js` banao aur yeh likho:

```js
const mongoose = require('mongoose');

const DB_URL = "mongodb://user:<password_required>@cluster.mongodb.net";
const DB_NAME = "<db_name>";

async function dbConnect() {
    try {
        await mongoose.connect(`${DB_URL}/${DB_NAME}`);
        console.log("Database Connected");
    } catch (error) {
        throw error;
    }
}

module.exports = dbConnect;
```

#### `index.js` mei import karo:

```js
const dbConnect = require("./utils/db.js");

dbConnect();
```

---

### 5. Model Banao (`models/user.model.js`)

#### `models/user.model.js` banao aur yeh likho:

```js
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true,
    },
});

const User = mongoose.model("User", userSchema);
module.exports = User;
```

---

### 6. Controller Setup (`controllers/user.controller.js`)

#### `controllers/user.controller.js` banao:

```js
const User = require("../models/user.model.js");

const addUser = async (req, res) => {
    try {
        let newUser = req.body;
        let user = await User.create(newUser);
        res.status(201).send(user);
    } catch (error) {
        res.status(400).send(error.message);
    }
};

module.exports = { addUser };
```

---

### 7. Router Setup (`routers/user.router.js`)

#### `routers/user.router.js` banao:

```js
const express = require("express");
const { addUser } = require("../controllers/user.controller.js");

const UserRouter = express.Router();

UserRouter.post("/users", addUser);

module.exports = UserRouter;
```

#### `index.js` mei import karo:

```js
const userRouter = require("./routers/user.router.js");

app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(userRouter);
```

---

### 8. Final Steps

#### Server Dobara Run Karo:

```sh
npm run server
```

