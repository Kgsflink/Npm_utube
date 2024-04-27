# Npm_utube

Sure, I'll provide you with a modified `App.js` file that includes the file upload component and instructions on how to set up the backend code separately.

First, let's create the `FileUpload` component in `App.js`:

```jsx
import React from 'react';
import axios from 'axios';

class FileUpload extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            selectedFiles: [],
            progress: 0
        };
    }

    handleFileChange = (event) => {
        this.setState({
            selectedFiles: event.target.files
        });
    };

    handleSubmit = async (event) => {
        event.preventDefault();
        const formData = new FormData();
        for (let file of this.state.selectedFiles) {
            formData.append('files', file);
        }

        try {
            const response = await axios.post('http://localhost:5000/upload', formData, {
                headers: {
                    'Content-Type': 'multipart/form-data'
                },
                onUploadProgress: (progressEvent) => {
                    this.setState({
                        progress: Math.round((progressEvent.loaded / progressEvent.total) * 100)
                    });
                }
            });
            console.log(response.data);
        } catch (error) {
            console.error('Error uploading files:', error);
        }
    };

    render() {
        return (
            <div>
                <h1>File Upload</h1>
                <form onSubmit={this.handleSubmit}>
                    <input type="file" name="files" onChange={this.handleFileChange} multiple />
                    <button type="submit">Upload</button>
                </form>
                {this.state.progress > 0 && <div>Uploading... {this.state.progress}%</div>}
            </div>
        );
    }
}

export default FileUpload;
```

Now, let's set up the backend code separately. Create a new file named `server.js` for the backend code:

```javascript
const express = require('express');
const multer = require('multer');
const path = require('path');

const app = express();
const PORT = 5000;

// Multer setup
const storage = multer.diskStorage({
    destination: function (req, file, cb) {
        cb(null, 'uploads/');
    },
    filename: function (req, file, cb) {
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
        cb(null, uniqueSuffix + path.extname(file.originalname));
    }
});
const upload = multer({ storage: storage });

// Serve the frontend build (assuming it's located in the "build" folder)
app.use(express.static('build'));

// File upload endpoint
app.post('/upload', upload.array('files'), (req, res) => {
    console.log(req.files);
    res.send('Files uploaded successfully!');
});

// Start the server
app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
});
```

Make sure to create an empty folder named `uploads` in the root directory of your backend project to store the uploaded files.

To run the backend code, navigate to the directory containing `server.js` in your terminal and run:

```bash
node server.js
```

Now, you can integrate the `FileUpload` component into your `App.js` and run your React app using `npm start`. Make sure the backend server is running simultaneously.
