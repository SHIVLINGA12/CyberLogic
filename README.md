<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>File Upload with Firebase</title>
    
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-storage.js"></script>

    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
        }
        input[type="file"] {
            margin: 10px 0;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
        #status {
            margin-top: 20px;
        }
    </style>
</head>
<body>

    <h1>Upload Files to Firebase Storage</h1>

    <!-- File Upload Form -->
    <form id="uploadForm" enctype="multipart/form-data">
        <input type="file" id="fileInput" name="file" multiple>
        <button type="submit">Upload</button>
    </form>

    <p id="status"></p>

    <!-- Initialize Firebase -->
    <script>
        // Your Firebase configuration (replace with your Firebase config)
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_AUTH_DOMAIN",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_STORAGE_BUCKET",
            messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
            appId: "YOUR_APP_ID"
        };

        // Initialize Firebase
        const app = firebase.initializeApp(firebaseConfig);
        const storage = firebase.storage();

        const form = document.getElementById('uploadForm');
        const fileInput = document.getElementById('fileInput');
        const statusText = document.getElementById('status');

        // Handle form submission
        form.addEventListener('submit', function (event) {
            event.preventDefault();  // Prevent page reload

            const files = fileInput.files;
            if (files.length === 0) {
                statusText.innerHTML = "Please select a file.";
                return;
            }

            const file = files[0];  // Get the first file selected (you can also handle multiple files)
            const storageRef = storage.ref('uploads/' + file.name);  // Create a reference in Firebase Storage

            // Upload the file to Firebase Storage
            const uploadTask = storageRef.put(file);

            uploadTask.on('state_changed', 
                (snapshot) => {
                    // Track upload progress (optional)
                    const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
                    statusText.innerHTML = `Uploading: ${progress.toFixed(2)}%`;
                }, 
                (error) => {
                    // Handle upload error
                    statusText.innerHTML = `Error: ${error.message}`;
                }, 
                () => {
                    // Upload complete
                    uploadTask.snapshot.ref.getDownloadURL().then((downloadURL) => {
                        statusText.innerHTML = `File uploaded successfully! Access it <a href="${downloadURL}" target="_blank">here</a>`;
                    });
                }
            );
        });
    </script>

</body>
</html>
