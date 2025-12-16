# MarketixLab Invoice Generator - Backend (Netlify Functions)

This is the backend API for the MarketixLab Invoice Generator. It uses Netlify Functions with Python to generate DOCX invoice documents.

## Features

- ✅ Generate DOCX invoices from JSON data
- ✅ Support for custom invoice templates
- ✅ Automatic currency formatting (Indonesian Rupiah)
- ✅ Late fee calculations
- ✅ Paid stamp and signature support
- ✅ CORS enabled for cross-origin requests

## Project Structure

```
backend/
├── netlify/
│   └── functions/
│       └── generate-invoice.py    # Main invoice generation function
├── public/
│   └── index.html                 # API landing page
├── netlify.toml                   # Netlify configuration
├── requirements.txt               # Python dependencies
└── README.md                      # This file
```

## Setup for Netlify

### Step 1: Create a GitHub Repository

1. Go to [GitHub](https://github.com) and sign in
2. Click the **+** icon and select **New repository**
3. Name it `invoice-generator-backend`
4. Set it to **Public** or **Private**
5. Click **Create repository**

### Step 2: Upload the Backend Files

**Option A: Using GitHub Web Interface**
1. In your new repository, click **Add file** > **Upload files**
2. Upload all files from the `backend` folder
3. Maintain the folder structure exactly as shown above
4. Click **Commit changes**

**Option B: Using Git Command Line**
```bash
# Clone your repository
git clone https://github.com/YOUR_USERNAME/invoice-generator-backend.git
cd invoice-generator-backend

# Copy all backend files to this directory
# Make sure to include:
# - netlify/ folder with functions/
# - public/ folder
# - netlify.toml
# - requirements.txt

# Add and commit files
git add .
git commit -m "Initial commit: Invoice Generator Backend"
git push origin main
```

### Step 3: Deploy to Netlify

1. Go to [Netlify](https://www.netlify.com/) and sign up/log in
2. Click **Add new site** > **Import an existing project**
3. Select **GitHub** as your Git provider
4. Authorize Netlify to access your repositories
5. Select your `invoice-generator-backend` repository

### Step 4: Configure Build Settings

Netlify should auto-detect settings from `netlify.toml`, but verify:

- **Build command**: (leave empty)
- **Publish directory**: `public`
- **Functions directory**: `netlify/functions`

Click **Deploy site**

### Step 5: Configure Environment Variables (Optional)

If you want to use a custom invoice template:

1. Go to **Site settings** > **Environment variables**
2. Add the following variables:

| Variable | Description | Example |
|----------|-------------|---------|
| `INVOICE_TEMPLATE_URL` | URL to your DOCX template | `https://raw.githubusercontent.com/user/repo/main/template.docx` |
| `PAID_STAMP_URL` | URL to paid stamp image | Google Drive direct link |
| `SIGNATURE_URL` | URL to signature image | Google Drive direct link |

### Step 6: Get Your API URL

After deployment, Netlify will provide a URL like:
```
https://your-app-name.netlify.app
```

This is the URL you'll use in the frontend settings.

## API Endpoints

### Generate Invoice

**POST** `/.netlify/functions/generate-invoice`

**Request Body (JSON):**
```json
{
    "client_info": {
        "{{client_name}}": "John Doe",
        "{{client_phone}}": "+1234567890",
        "{{client_email}}": "john@example.com",
        "{{client_address}}": "123 Main St, City"
    },
    "invoice_details": {
        "{{invoice_number}}": "INV2025001",
        "{{invoice_date}}": "16.12.2025",
        "{{due_date}}": "30.12.2025"
    },
    "items": [
        {
            "description": "Web Design Services",
            "unit_price": 5000000,
            "quantity": 1,
            "total": 5000000
        }
    ],
    "financials": {
        "[subtotal]": "Rp 5.000.000",
        "[tax]": "Rp 500.000",
        "[discount]": "Rp 0",
        "[latefee]": "",
        "[grandtotal]": "Rp 5.500.000"
    },
    "apply_late_fee": false,
    "mark_as_paid": false,
    "invoice_number": "INV2025001"
}
```

**Response:**
- Success: DOCX file (binary)
- Error: JSON with error message

## Hosting Your Invoice Template

The backend needs access to your DOCX invoice template. Here are your options:

### Option 1: GitHub Raw URL (Recommended)

1. Upload `Invoice_Template_MarketixLab.docx` to a GitHub repository
2. Get the raw URL:
   ```
   https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/Invoice_Template_MarketixLab.docx
   ```
3. Set this as the `INVOICE_TEMPLATE_URL` environment variable

### Option 2: Google Drive

1. Upload the template to Google Drive
2. Right-click > **Share** > **Anyone with the link**
3. Get the file ID from the URL
4. Create direct download link:
   ```
   https://drive.google.com/uc?export=download&id=YOUR_FILE_ID
   ```

### Option 3: Use Embedded Template

If no template URL is provided, the function will generate a basic invoice without the custom formatting.

## Local Development

### Prerequisites
- Python 3.9+
- pip

### Setup
```bash
# Install dependencies
pip install -r requirements.txt

# Install Netlify CLI
npm install -g netlify-cli

# Run locally
netlify dev
```

### Testing the API
```bash
curl -X POST http://localhost:8888/.netlify/functions/generate-invoice \
  -H "Content-Type: application/json" \
  -d '{"client_info": {...}, "items": [...], ...}'
```

## Troubleshooting

### Function Timeout
Netlify Functions have a 10-second timeout on the free plan. If invoice generation takes too long:
- Optimize the template
- Upgrade to Netlify Pro for longer timeout

### Template Not Loading
- Verify the template URL is accessible
- Check CORS headers on the template host
- Ensure the URL returns the actual file (not a preview page)

### Python Dependencies
If dependencies fail to install:
- Check the `requirements.txt` syntax
- Verify package versions are compatible
- Review Netlify build logs

## License

MIT License - MarketixLab 2025
