<template>
    <div class="operator mt-2 mb-2">
      <h2 class="weight-6 f-20 fw-500">Upload resume</h2>
      <div class="container mt-1">
        <!-- Drop area for uploading files -->
        <div v-if="!fileUploaded && !uploading && !fileProcessed" class="drop-area" @drop="handleDrop" @dragover.prevent>
          <img src="@/assets/images/cloud.svg" alt="">
          <h3 class="mt-1 weight-5">Browse and choose the resume you want to enhance.</h3>
          <p>Should not be more than <span>5mb</span></p>
          <!-- Input element for selecting files -->
          <input type="file" @change="handleFileInput" style="display: none" ref="fileInput">
          <button class="mt-1" @click="$refs.fileInput.click()">Upload +</button>
        </div>
        <!-- Error message -->
        <div v-if="error" class="error">
          <h3 class="mt-1 weight-5">{{ error }}</h3>
          <button class="mt-1" @click="retryUpload">Retry</button>
        </div>
        <!-- Uploaded message -->
        <div v-if="fileUploaded && !uploading && !fileProcessed && !error" class="uploaded">
          <div class="content">
            <img src="@/assets/images/cloud_done.svg" alt="">
            <h3 class="mt-1 weight-5">File uploaded successfully!</h3>
          </div>
        </div>
        <!-- Uploading message -->
        <div v-if="uploading && !fileProcessed && !error" class="loading">
          <div class="content">
            <!-- Loading animation -->
            <div class="loading-svg"></div>
            <h3 class="mt-1 weight-5">Uploading your resume. Please wait...</h3>
          </div>
        </div>
        <!-- Processed message -->
        <div v-if="fileProcessed && !error" class="processed">
          <div class="content">
            <img src="@/assets/images/done.svg" alt="">
            <h3 class="mt-1 weight-5">Your enhanced resume: now ready for the spotlight! Download and shine on!✨📄</h3>
            <button class="mt-1" @click="downloadFile">Download</button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  const API_BASE_URL = "https://resume-enhancer-api.onrender.com/api/v1/resumes";
  
  export default {
    data() {
      return {
        fileUploaded: false,
        uploading: false,
        fileProcessed: false,
        error: null,
        resumeId: null
      };
    },
    methods: {
      async handleDrop(event) {
        event.preventDefault();
        const files = event.dataTransfer.files;
        await this.uploadFiles(files);
      },
      async handleFileInput(event) {
        const files = event.target.files;
        await this.uploadFiles(files);
      },
      async uploadFiles(files) {
        this.uploading = true;
  
        try {
          const formData = new FormData();
          formData.append('resume', files[0]);
  
          // Make POST request to upload resume
          const response = await axios.post(`${API_BASE_URL}/upload`, formData, {
            headers: {
              'Content-Type': 'multipart/form-data'
            }
          });
  
          // Upload successful
          this.uploading = false;
          this.fileUploaded = true;
          this.resumeId = response.data.data.resume._id; // Extract resume ID for download
          this.fileProcessed = true; // Set flag to true for download option
        } catch (error) {
          // Upload failed
          this.uploading = false;
          this.error = error.message || 'An error occurred while uploading the resume.';
        }
      },
      async downloadFile() {
        try {
          // Make GET request to download the enhanced file
          const response = await axios.get(`${API_BASE_URL}/download/${this.resumeId}`, {
            responseType: 'blob', // Set response type to blob to handle binary data
          });
  
          // Create a URL for the blob response
          const url = window.URL.createObjectURL(new Blob([response.data]));
  
          // Create a link element and simulate a click to trigger the download
          const link = document.createElement('a');
          link.href = url;
          link.setAttribute('download', 'enhanced_resume.pdf'); // Set desired filename
          document.body.appendChild(link);
          link.click();
  
          // Clean up by removing the link and revoking the URL
          document.body.removeChild(link);
          window.URL.revokeObjectURL(url);
        } catch (error) {
          // Handle error
          console.error('Error downloading file:', error);
          alert('An error occurred while downloading the enhanced resume.');
        }
      },
      retryUpload() {
        // Reset error and retry upload
        this.error = null;
        this.fileUploaded = false;
        this.uploading = false;
        this.fileProcessed = false;
      }
    }
  };
  </script>
  
  
  <style scoped>
    .operator {
      display: flex;
      flex-direction: column;
      align-self: stretch;
    }
  
    .container {
      width: 100%;
      height: 60vh;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 4px;
      border: 2px #b2bbc868 dashed;
    }
  
    .container .drop-area {
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 15px;
    }
  
    h3 {
        color: var(--primary-color);
        font-size: 18px;
        width: 100%;
        text-align: center;
    }
  
    .container .drop-area p {
      font-weight: 300;
    }
  
    .container .drop-area p span {
      font-weight: 500 !important;
    }
  
    button {
      background-color: #4B8DD9;
      font-size: 14px;
      border: none;
      border-radius: 4px;
      padding: 6px 10px;
      cursor: pointer;
      color: #fff;
      font-weight: 700;
    }
  
    .content {
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }
  
    .loading-svg {
      width: 30px;
      height: 30px;
      border: 2px solid transparent;
      border-top-color: #007bff;
      border-radius: 50%;
      animation: spin 1.5s linear infinite;
    }
  
    @keyframes spin {
      100% {
        transform: rotate(360deg);
      }
    }
  
    .processed {
      color: #28a745;
      text-align: center;
    }
  
    .processed i {
      font-size: 2em;
      margin-bottom: 10px;
    }
  </style>