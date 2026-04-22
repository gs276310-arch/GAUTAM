<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PAN Services Portal</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/js/all.min.js"></script>
    <style>
        :root {
            --primary: #2c3e50;
            --accent: #27ae60;
            --bg: #f4f7f6;
        }

        body { font-family: 'Segoe UI', sans-serif; background: var(--bg); margin: 0; padding: 20px; }
        .container { max-width: 800px; background: white; margin: auto; padding: 30px; border-radius: 12px; box-shadow: 0 5px 15px rgba(0,0,0,0.1); }
        
        h2 { text-align: center; color: var(--primary); border-bottom: 2px solid var(--accent); padding-bottom: 10px; }
        .rights { text-align: center; font-size: 0.8rem; color: #777; margin-bottom: 20px; }

        .form-group { margin-bottom: 15px; }
        label { display: block; font-weight: bold; margin-bottom: 5px; color: #333; }
        
        input[type="text"], input[type="date"], select, input[type="file"] {
            width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 5px; box-sizing: border-box;
        }

        input[type="text"] { text-transform: uppercase; }

        .payment-section { 
            background: #fffbe6; border: 1px dashed #f39c12; padding: 20px; 
            margin-top: 20px; border-radius: 8px; text-align: center; display: none;
        }

        .qr-placeholder { background: #eee; width: 150px; height: 150px; margin: 10px auto; display: flex; align-items: center; justify-content: center; font-size: 0.8rem; border: 1px solid #ccc; }

        /* Modified Success Styling for Animation */
        #success-mark { display: none; margin-top: 15px; }
        
        .verified-box {
            display: inline-flex;
            align-items: center;
            gap: 10px;
            color: var(--accent);
            font-weight: bold;
            font-size: 1.2rem;
            padding: 10px 20px;
            border: 2px solid var(--accent);
            border-radius: 5px;
            background: #eafaf1;
        }

        /* Spinning Animation Definition */
        @keyframes spinCheck {
            0% { transform: scale(0) rotate(-180deg); opacity: 0; }
            70% { transform: scale(1.2) rotate(10deg); opacity: 1; }
            100% { transform: scale(1) rotate(0deg); opacity: 1; }
        }

        /* Applied to the Icon when Success Mark is Displayed */
        #success-mark i {
            font-size: 2rem;
            animation: spinCheck 0.6s ease-out forwards;
        }
        
        .minor-fields { display: none; background: #eef2f3; padding: 15px; border-radius: 5px; }

        button { 
            background: var(--accent); color: white; border: none; padding: 12px 25px; 
            border-radius: 5px; cursor: pointer; width: 100%; font-size: 1rem; margin-top: 20px;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>ONLINE PORTAL OF PAN</h2>
    <div class="rights">ALL RIGHTS RESERVED BY GAUTAM</div>

    <form id="panForm">
        <div class="form-group">
            <label>CHOOSE APPLICATION TYPE *</label>
            <select id="appType" required onchange="updateUI()">
                <option value="">--Select--</option>
                <option value="300">NEW PAN CARD (Rs. 300)</option>
                <option value="350">FOR CORRECTION (Rs. 350)</option>
                <option value="300">MINOR TO MAJOR (Rs. 300)</option>
                <option value="800">LOST PAN CARD (Rs. 800)</option>
                <option value="400">LOST (HAVE PICTURE) (Rs. 400)</option>
            </select>
        </div>

        <div class="form-group">
            <label>APPLICANT'S FULL NAME (AS PER AADHAAR) *</label>
            <input type="text" placeholder="IN CAPITAL LETTERS ONLY" required>
        </div>

        <div class="form-group">
            <label>MOBILE NUMBER (APPLICANT/PARENT) *</label>
            <input type="text" maxlength="10" required>
        </div>

        <div class="form-group">
            <label>DATE OF BIRTH *</label>
            <input type="date" required>
        </div>

        <div class="form-group">
            <label>FATHER'S FULL NAME *</label>
            <input type="text" required>
        </div>

        <div id="minorSection" class="minor-fields">
            <p><strong>MINOR/REQUIRED DOCUMENTS:</strong></p>
            <div class="form-group">
                <label>UPLOAD PARENT'S SIGNATURE *</label>
                <input type="file">
            </div>
            <div class="form-group">
                <label>PARENT'S DOCUMENT (AADHAAR/VOTER) *</label>
                <input type="file">
            </div>
        </div>

        <div class="form-group">
            <label>UPLOAD AADHAAR (FRONT & BACK COMBINED) *</label>
            <input type="file" required>
        </div>

        <div class="form-group">
            <label>UPLOAD PHOTO *</label>
            <input type="file" required>
        </div>

        <div class="form-group">
            <label>UPLOAD SIGNATURE *</label>
            <input type="file" required>
        </div>

        <div id="extraUploads" class="form-group" style="display:none;">
            <label id="extraLabel">UPLOAD OLD PAN / PICTURE *</label>
            <input type="file">
        </div>

        <div id="paymentArea" class="payment-section">
            <h4>PAYMENT DETAILS</h4>
            <p>Amount to Pay: <strong>Rs. <span id="amountTxt">0</span></strong></p>
            <div class="qr-placeholder">
                <img src="https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=upi://pay?pa=GS276310-4@OKSBI&pn=GAUTAM&am=0" id="qrImg" alt="Payment QR">
            </div>
            <p><small>UPI ID: GS276310-4@OKSBI</small></p>
            <hr>
            <label>UPLOAD PAYMENT SCREENSHOT *</label>
            <input type="file" id="ssUpload" onchange="showSuccess()">
            
            <div id="success-mark">
                <div class="verified-box">
                    <i class="fas fa-check-circle"></i> 
                    <span>VERIFIED</span>
                </div>
            </div>
        </div>

        <button type="submit">SUBMIT APPLICATION</button>
    </form>
</div>

<script>
    function updateUI() {
        const select = document.getElementById('appType');
        const amount = select.value;
        const text = select.options[select.selectedIndex].text;
        
        // Show Payment Section
        const payArea = document.getElementById('paymentArea');
        if(amount) {
            payArea.style.display = 'block';
            document.getElementById('amountTxt').innerText = amount;
            // Update QR code dynamically with amount
            document.getElementById('qrImg').src = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=upi://pay?pa=GS276310-4@OKSBI&pn=GAUTAM&am=${amount}&cu=INR`;
        } else {
            payArea.style.display = 'none';
        }

        // Minor logic
        const minorSec = document.getElementById('minorSection');
        minorSec.style.display = (text.includes("MINOR")) ? 'block' : 'none';

        // Correction/Lost logic
        const extra = document.getElementById('extraUploads');
        const extraLabel = document.getElementById('extraLabel');
        if(text.includes("CORRECTION") || text.includes("LOST")) {
            extra.style.display = 'block';
            extraLabel.innerText = text.includes("CORRECTION") ? "UPLOAD OLD/MINOR PAN CARD" : "UPLOAD LOST PAN PICTURE (IF ANY)";
        } else {
            extra.style.display = 'none';
        }
    }

    function showSuccess() {
        const ssInput = document.getElementById('ssUpload');
        const successMark = document.getElementById('success-mark');
        if(ssInput.files.length > 0) {
            successMark.style.display = 'block';
        } else {
            successMark.style.display = 'none';
        }
    }
</script>

</body>
</html>
