# Iteration 7 --- Backend & Proxy Insights

## 1. Virtual Field in `backend/models/jobModel.js`

### **Code Snippet**

``` js
jobSchema.set("toJSON", {
  virtuals: true,
  transform: (doc, ret) => {
    ret.id = ret._id;
    return ret;
  },
});
```

### **What does this accomplish?**

This configuration modifies how Mongoose converts MongoDB documents into
JSON when sending data to the frontend.

-   `virtuals: true` ensures virtual properties are included in the JSON
    output.
-   The `transform` function customizes the JSON format.
-   It adds a new field `id` that copies MongoDB's internal `_id` value.

### **Why is this useful?**

-   React apps commonly work with an `id` field, not `_id`.
-   Avoids confusion and prevents frontend bugs caused by the MongoDB
    naming convention.
-   Creates cleaner, more consistent API responses for your application.
-   Hides database-specific implementation details from the frontend.

**In short:**\
It makes your API more frontend-friendly and improves code consistency.

------------------------------------------------------------------------

## 2. CORS Middleware in `backend/app.js`

### **Code Snippet**

``` js
app.use(cors());
```

### **What is CORS?**

CORS (Cross-Origin Resource Sharing) is a browser security system that
blocks requests made from a different domain or port than the server.

### **Why is it necessary?**

Your setup uses:

-   Frontend: `http://localhost:5173`
-   Backend: `http://localhost:4000`

These are **different origins**, so the browser blocks the requests
unless the server explicitly allows them.

`app.use(cors())` adds the correct headers so the browser permits
communication.

### **Benefits**

-   Allows your frontend to access backend APIs during development.
-   Prevents "Blocked by CORS policy" errors.
-   Makes local development possible without manual header
    configuration.

**In short:**\
It enables your React app to safely communicate with your Express API.

------------------------------------------------------------------------

## 3. Proxy Configuration in `frontend/vite.config.js`

### **Code Snippet**

``` js
proxy: {
  "/api": {
    target: "http://localhost:4000",
    changeOrigin: true,
  },
},
```

### **How does this proxy setting work?**

When the frontend requests something like:

``` js
fetch("/api/jobs")
```

Vite intercepts it and forwards it to:

    http://localhost:4000/api/jobs

This makes the browser think the request came from the **same origin**,
even though Vite forwarded it behind the scenes.

### **What problems does it solve?**

-   Avoids CORS issues without needing complex configurations.
-   Keeps frontend code clean --- no hardcoded backend URLs.
-   Improves developer experience by letting Vite handle routing during
    development.
-   Makes deployments easier, because API paths stay consistent.

**In short:**\
The proxy seamlessly connects your frontend and backend during
development, removing CORS issues and cleaning up your code.
