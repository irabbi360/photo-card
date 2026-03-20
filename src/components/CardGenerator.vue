<template>
  <div class="card-generator-wrapper">
    <div class="main-container">
      
      <!-- Input Section (Left) -->
      <div class="input-section">
        <h2 class="section-title">✍️ কার্ডের তথ্য দিন</h2>

        <!-- Event Selection -->
        <div class="form-group">
          <label>উৎসব নির্বাচন করুন:</label>
          <select v-model="selectedEvent" @change="drawCanvas" class="event-select">
            <option value="ঈদ মোবারক">ঈদ মোবারক</option>
            <option value="রমজানুল মোবারক">রমজানুল মোবারক</option>
            <option value="শুভ নববর্ষ">শুভ নববর্ষ</option>
            <option value="শুভ জন্মদিন">শুভ জন্মদিন</option>
            <option value="custom">অন্যান্য (নিজে লিখুন)</option>
          </select>
        </div>

        <!-- Custom Event Input -->
        <div class="form-group" v-if="selectedEvent === 'custom'">
          <label>আপনার উৎসবের নাম লিখুন:</label>
          <input type="text" v-model="customEvent" placeholder="যেমন: স্বাধীনতা দিবস" @input="drawCanvas" />
        </div>

        <!-- Event Text Color -->
        <div class="form-group">
          <label>উৎসবের পাঠের রঙ:</label>
          <div class="color-picker-wrapper">
            <input type="color" v-model="eventTextColor" @change="drawCanvas" class="color-picker" />
            <span class="color-value">{{ eventTextColor }}</span>
          </div>
        </div>

        <!-- Event Text Font Size -->
        <div class="form-group">
          <label>উৎসবের পাঠের আকার: <span class="font-size-display">{{ eventFontSize }}px</span></label>
          <input type="range" v-model.number="eventFontSize" min="30" max="150" step="5" @change="drawCanvas" class="font-size-slider" />
        </div>

        <!-- Event Text Font Family -->
        <div class="form-group">
          <label>উৎসবের পাঠের ফন্ট:</label>
          <select v-model="eventFont" @change="drawCanvas" class="font-select">
            <option value="Hind Siliguri">Hind Siliguri</option>
            <option value="Noto Sans Bengali">Noto Sans Bengali</option>
            <option value="Playfair Display">Playfair Display</option>
            <option value="Montserrat">Montserrat</option>
            <option value="Poppins">Poppins</option>
            <option value="Roboto Slab">Roboto Slab</option>
          </select>
        </div>

        <!-- Template Selection -->
        <div class="form-group">
          <label>ডিজাইন টেমপ্লেট নির্বাচন করুন:</label>
          <div class="template-selector">
            <label class="radio-label">
              <input type="radio" value="classic" v-model="selectedTemplate" @change="drawCanvas" />
              সাধারণ (গোল ছবি)
            </label>
            <label class="radio-label">
              <input type="radio" value="political" v-model="selectedTemplate" @change="drawCanvas" />
              রাজনৈতিক (বহুভুজ)
            </label>
          </div>
        </div>

        <!-- Background Selection -->
        <div class="form-group">
          <label>কার্ডের ব্যাকগ্রাউন্ড নির্বাচন করুন:</label>
          <div class="bg-selector">
            <div class="prebuilt-bgs">
              <div 
                v-for="(bg, idx) in prebuiltBackgrounds" 
                :key="idx" 
                class="bg-thumbnail" 
                :class="{ active: currentBgUrl === bg }"
                @click="setPrebuiltBackground(bg)"
              >
                <img :src="bg" alt="Background Option" />
              </div>
            </div>
            <div class="upload-wrapper">
              <span>অথবা নিজের ব্যাকগ্রাউন্ড আপলোড করুন:</span>
              <input type="file" accept="image/*" @change="onBackgroundUpload" />
            </div>
          </div>
        </div>

        <div class="form-group">
          <label>কার্ডে আপনার যে নাম দেখাবে</label>
          <input type="text" v-model="userName" placeholder="নাম লিখুন..." @input="drawCanvas" />
        </div>

        <div class="form-group">
          <label>পদবী (**যদি থাকে, যেমন বিশিষ্ট সমাজ সেবক/ইত্যাদি) না থাকলে খালি রাখুন</label>
          <input type="text" v-model="userDesignation" placeholder="যেমন: বিশিষ্ট সমাজ সেবক" @input="drawCanvas" />
        </div>

        <div class="form-group" v-if="selectedTemplate === 'classic'">
          <label>শুভেচ্ছা বার্তা:</label>
          <textarea v-model="userMessage" rows="3" @input="drawCanvas"></textarea>
        </div>

        <div class="form-group">
          <label>আপনার ছবি:</label>
          <input type="file" accept="image/*" @change="onImageUpload" />
        </div>
        
        
      </div>

      <!-- Preview Section (Right) -->
      <div class="preview-section">
        <div class="canvas-container">
          <canvas ref="canvasRef" width="800" height="1000" class="preview-canvas"></canvas>
          
          <!-- Action -->
          <div class="action-group">
            <button @click="downloadCard" class="download-btn">
              Download Card Image
            </button>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import defaultBg from '../assets/images/default-bg.svg';
import bg1 from '../assets/images/bg-1.jpg';
import bg2 from '../assets/images/bg-2.svg';
import bg3 from '../assets/images/bg-3.svg';
import bg2x from '../assets/images/bg-2.jpg';
import bg3x from '../assets/images/bg-3.png';
import bg4 from '../assets/images/bg-4.jpg';
import bg5 from '../assets/images/bg-5.jpg';
import bg6 from '../assets/images/bg-6.jpg';

const canvasRef = ref(null);

// Import multiple static prebuilt backgrounds into an array
const prebuiltBackgrounds = [defaultBg, bg2, bg3, bg1, bg2x, bg3x, bg4, bg5, bg6];

// State bound to inputs
const selectedEvent = ref('ঈদ মোবারক');
const customEvent = ref('');
const selectedTemplate = ref('classic');
const eventTextColor = ref('#ff0000');
const eventFontSize = ref(95);
const eventFont = ref('Hind Siliguri');
const userName = ref('');
const userDesignation = ref('');
const userMessage = ref('পবিত্র ঈদুল ফিতরের আনন্দ ছড়িয়ে\nপড়ুক সবার মনে। ঈদ মোবারক!');
const userImage = ref(null);
const backgroundImage = ref(null);
const currentBgUrl = ref(defaultBg); // Track which background string is active

// Helper to load image
const loadImage = (src) => {
  return new Promise((resolve) => {
    const img = new Image();
    img.crossOrigin = 'anonymous';
    img.onload = () => resolve(img);
    img.src = src;
  });
};

const onImageUpload = async (event) => {
  const file = event.target.files[0];
  if (!file) {
    userImage.value = null;
    drawCanvas();
    return;
  }
  const url = URL.createObjectURL(file);
  userImage.value = await loadImage(url);
  drawCanvas();
};

const onBackgroundUpload = async (event) => {
  const file = event.target.files[0];
  if (!file) return;
  const url = URL.createObjectURL(file);
  currentBgUrl.value = url;
  backgroundImage.value = await loadImage(url);
  drawCanvas();
};

const setPrebuiltBackground = async (src) => {
  currentBgUrl.value = src;
  backgroundImage.value = await loadImage(src);
  drawCanvas();
};

const drawTextWithShadow = (ctx, text, x, y, options) => {
  ctx.save();
  ctx.font = options.font || 'bold 50px sans-serif';
  ctx.textAlign = options.align || 'center';
  ctx.textBaseline = 'middle';
  
  if (options.shadowColor) {
    ctx.shadowColor = options.shadowColor;
    ctx.shadowOffsetX = options.shadowX || 0;
    ctx.shadowOffsetY = options.shadowY || 5;
    ctx.shadowBlur = options.shadowBlur || 10;
  }
  
  ctx.fillStyle = options.color || '#000';
  
  if (options.strokeColor) {
    ctx.strokeStyle = options.strokeColor;
    ctx.lineWidth = options.strokeWidth || 2;
    ctx.strokeText(text, x, y);
  }
  
  ctx.fillText(text, x, y);
  ctx.restore();
};

const drawMultilineText = (ctx, text, x, y, lineHeight, options) => {
  const lines = text.split('\n');
  lines.forEach((line, index) => {
    drawTextWithShadow(ctx, line, x, y + (index * lineHeight), options);
  });
};

const drawCanvas = () => {
  const canvas = canvasRef.value;
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  const width = canvas.width;
  const height = canvas.height;

  // Clear canvas
  ctx.clearRect(0, 0, width, height);

  // Background Image or Fallback
  if (backgroundImage.value) {
    ctx.drawImage(backgroundImage.value, 0, 0, width, height);
  } else {
    ctx.fillStyle = '#ffffff';
    ctx.fillRect(0, 0, width, height);
  }

  // Get current event text
  const mainEventText = selectedEvent.value === 'custom' ? customEvent.value : selectedEvent.value;

  if (selectedTemplate.value === 'political') {
    // --- POLITICAL TEMPLATE ---
    ctx.fillStyle = '#01949a';
    ctx.fillRect(0, 880, width, 120);

    const polyYStart = 450;
    const polyYEnd = 880;
    
    ctx.save();
    ctx.beginPath();
    ctx.moveTo(0, polyYStart);
    ctx.lineTo(260, polyYStart);
    ctx.lineTo(440, 665);
    ctx.lineTo(260, polyYEnd);
    ctx.lineTo(0, polyYEnd);
    ctx.closePath();

    ctx.save();
    ctx.clip();
    if (userImage.value) {
      const polyW = 440;
      const polyH = polyYEnd - polyYStart;
      const imgWidth = userImage.value.width;
      const imgHeight = userImage.value.height;
      const ratio = Math.max(polyW / imgWidth, polyH / imgHeight);
      const drawW = imgWidth * ratio;
      const drawH = imgHeight * ratio;
      const drawX = 0;
      const drawY = polyYStart + (polyH - drawH) / 2;
      ctx.drawImage(userImage.value, drawX, drawY, drawW, drawH);
    } else {
      ctx.fillStyle = '#e9ecef';
      ctx.fill();
    }
    ctx.restore();
    
    ctx.beginPath();
    ctx.moveTo(0, polyYStart);
    ctx.lineTo(260, polyYStart);
    ctx.lineTo(440, 665);
    ctx.lineTo(260, polyYEnd);
    ctx.lineTo(0, polyYEnd);
    ctx.closePath();
    ctx.strokeStyle = '#01949a';
    ctx.lineWidth = 14;
    ctx.stroke();
    ctx.restore();

    drawTextWithShadow(ctx, userName.value || 'আপনার নাম', width / 2, 920, {
      font: 'bold 45px "Hind Siliguri", sans-serif',
      color: '#ffffff'
    });

    if (userDesignation.value) {
      drawTextWithShadow(ctx, userDesignation.value, width / 2, 970, {
        font: 'bold 24px "Hind Siliguri", sans-serif',
        color: '#e0ecec'
      });
    }

    // Floating header text for political mode
    if (mainEventText) {
      drawTextWithShadow(ctx, mainEventText, width / 2, 80, {
        font: `bold ${Math.round(eventFontSize.value * 0.52)}px "${eventFont.value}", sans-serif`,
        color: eventTextColor.value,
        strokeColor: '#ffffff', strokeWidth: 4,
        shadowColor: 'rgba(0,0,0,0.3)', shadowY: 3, shadowBlur: 5
      });
    }

  } else {
    // --- CLASSIC TEMPLATE ---
    if (!backgroundImage.value) {
      ctx.strokeStyle = '#f8f9fa';
      ctx.lineWidth = 15;
      ctx.strokeRect(7.5, 7.5, width - 15, height - 15);
    }

    const centerX = width / 2;
    const avatarY = 250;
    const radius = 130;

    ctx.save();
    ctx.beginPath();
    ctx.arc(centerX, avatarY, radius, 0, Math.PI * 2);
    ctx.closePath();
    ctx.save();
    ctx.clip();
    if (userImage.value) {
      const imgWidth = userImage.value.width;
      const imgHeight = userImage.value.height;
      const ratio = Math.max((radius * 2) / imgWidth, (radius * 2) / imgHeight);
      const drawW = imgWidth * ratio;
      const drawH = imgHeight * ratio;
      const drawX = centerX - drawW / 2;
      const drawY = avatarY - drawH / 2;
      ctx.drawImage(userImage.value, drawX, drawY, drawW, drawH);
    } else {
      ctx.fillStyle = '#e9ecef';
      ctx.fill();
    }
    ctx.restore();

    ctx.beginPath();
    ctx.arc(centerX, avatarY, radius, 0, Math.PI * 2);
    ctx.strokeStyle = '#d4af37';
    ctx.lineWidth = 8;
    ctx.stroke();
    ctx.restore();

    // Replaced hardcoded 'ঈদ মোবারক' with dynamic text
    drawTextWithShadow(ctx, mainEventText || 'উৎসবের নাম', centerX, 470, {
      font: `bold ${eventFontSize.value}px "${eventFont.value}", sans-serif`,
      color: eventTextColor.value,
      shadowColor: 'rgba(0,0,0,0.3)', shadowY: 6, shadowBlur: 10
    });

    drawTextWithShadow(ctx, 'শুভেচ্ছান্তে', centerX, 580, {
      font: 'bold 45px "Hind Siliguri", sans-serif',
      color: '#ffffff',
      strokeColor: '#cccccc', strokeWidth: 4,
      shadowColor: 'rgba(0,0,0,0.2)', shadowY: 3, shadowBlur: 6
    });

    drawTextWithShadow(ctx, userName.value || 'আপনার নাম', centerX, 680, {
      font: 'bold 65px "Hind Siliguri", sans-serif',
      color: '#d4af37',
      shadowColor: 'rgba(0,0,0,0.15)', shadowY: 4, shadowBlur: 8
    });

    if (userDesignation.value) {
      drawTextWithShadow(ctx, userDesignation.value, centerX, 760, {
        font: 'bold 32px "Hind Siliguri", sans-serif',
        color: '#444444'
      });
    }

    drawMultilineText(ctx, userMessage.value, centerX, userDesignation.value ? 840 : 800, 50, {
      font: 'bold 38px "Hind Siliguri", sans-serif',
      color: '#ffffff',
      strokeColor: '#d6d6d6', strokeWidth: 5,
      shadowColor: 'rgba(0,0,0,0.15)', shadowY: 3, shadowBlur: 5
    });
  }
};

const downloadCard = () => {
  const canvas = canvasRef.value;
  const dataUrl = canvas.toDataURL('image/png');
  const link = document.createElement('a');
  link.download = 'eid-card.png';
  link.href = dataUrl;
  link.click();
};

onMounted(async () => {
  // Load default background initially
  backgroundImage.value = await loadImage(defaultBg);
  
  // Draw canvas immediately and when fonts are ready
  drawCanvas();
  
  // Ensure we load Bengali font properly and redraw
  document.fonts.ready.then(() => {
    drawCanvas();
  });
});
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@500;700&family=Noto+Sans+Bengali:wght@400;700&family=Poppins:wght@600;700&family=Playfair+Display:wght@700&family=Montserrat:wght@600;700&display=swap');

.card-generator-wrapper {
  font-family: 'Hind Siliguri', sans-serif;
  padding: 2rem 1rem;
  background-color: #f4f6f8;
  width: 100%;
}

.main-container {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  gap: 3rem;
  flex-wrap: wrap;
}

.input-section {
  flex: 1;
  min-width: 320px;
  background: #ffffff;
  padding: 2.5rem;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0,0,0,0.06);
}

.section-title {
  margin-top: 0;
  margin-bottom: 2rem;
  font-size: 1.6rem;
  color: #333;
  border-bottom: 2px solid #eaeaea;
  padding-bottom: 0.8rem;
}

.template-selector {
  display: flex;
  gap: 1.5rem;
  margin-bottom: 0.5rem;
}

.radio-label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-weight: normal;
  cursor: pointer;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  font-weight: 600;
  margin-bottom: 0.6rem;
  color: #444;
  font-size: 0.95rem;
}

.event-select,
.form-group input[type="text"],
.form-group textarea {
  width: 100%;
  padding: 0.85rem;
  border: 1px solid #ced4da;
  border-radius: 6px;
  font-size: 1rem;
  font-family: inherit;
  transition: border-color 0.2s, box-shadow 0.2s;
  box-sizing: border-box;
  background-color: #fdfdfd;
}

.font-select {
  width: 100%;
  padding: 0.85rem;
  border: 1px solid #ced4da;
  border-radius: 6px;
  font-size: 1rem;
  font-family: inherit;
  transition: border-color 0.2s, box-shadow 0.2s;
  box-sizing: border-box;
  background-color: #fdfdfd;
  cursor: pointer;
}

.event-select:focus,
.font-select:focus,
.form-group input[type="text"]:focus,
.form-group textarea:focus {
  outline: none;
  border-color: #80bdff;
  box-shadow: 0 0 0 0.2rem rgba(0,123,255,.25);
}

.form-group input[type="file"] {
  border: 1px dashed #ced4da;
  padding: 1rem;
  border-radius: 6px;
  width: 100%;
  box-sizing: border-box;
  background: #f8f9fa;
  cursor: pointer;
}

.bg-selector {
  display: flex;
  flex-direction: column;
  gap: 1.2rem;
}

.prebuilt-bgs {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.bg-thumbnail {
  width: 70px;
  height: 85px;
  border: 2px solid transparent;
  border-radius: 6px;
  overflow: hidden;
  cursor: pointer;
  transition: transform 0.2s, border-color 0.2s;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.bg-thumbnail img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.bg-thumbnail:hover {
  transform: scale(1.05);
}

.bg-thumbnail.active {
  border-color: #28a745;
  box-shadow: 0 0 0 3px rgba(40, 167, 69, 0.3);
}

.upload-wrapper span {
  display: block;
  font-size: 0.9rem;
  color: #666;
  margin-bottom: 0.4rem;
}

.color-picker-wrapper {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.color-picker {
  width: 60px;
  height: 45px;
  border: 2px solid #ced4da;
  border-radius: 6px;
  cursor: pointer;
  padding: 3px;
  background-color: #fff;
}

.color-picker:hover {
  border-color: #80bdff;
}

.color-value {
  font-family: 'Courier New', monospace;
  font-weight: 600;
  color: #333;
  font-size: 0.95rem;
}

.font-size-slider {
  width: 100%;
  height: 6px;
  border-radius: 3px;
  background: linear-gradient(to right, #e9ecef, #80bdff);
  outline: none;
  -webkit-appearance: none;
  appearance: none;
}

.font-size-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: #007bff;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(0,123,255,.3);
  transition: background 0.2s;
}

.font-size-slider::-webkit-slider-thumb:hover {
  background: #0056b3;
}

.font-size-slider::-moz-range-thumb {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: #007bff;
  cursor: pointer;
  border: none;
  box-shadow: 0 2px 6px rgba(0,123,255,.3);
  transition: background 0.2s;
}

.font-size-slider::-moz-range-thumb:hover {
  background: #0056b3;
}

.font-size-display {
  display: inline-block;
  margin-left: 0.5rem;
  background: #f1f3f5;
  padding: 0.25rem 0.6rem;
  border-radius: 4px;
  font-weight: 600;
  font-size: 0.85rem;
  color: #495057;
}

.action-group {
  margin-top: 0;
  width: 100%;
}

.download-btn {
  background-color: #28a745;
  color: #fff;
  border: none;
  padding: 1rem;
  font-size: 1.15rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
  width: 100%;
  transition: background-color 0.2s;
  font-family: inherit;
}

.download-btn:hover {
  background-color: #218838;
}

.preview-section {
  flex: 1;
  min-width: 320px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  position: sticky;
  top: 20px;
  max-height: 100vh;
  overflow-y: auto;
}

.canvas-container {
  width: 100%;
  max-width: 500px;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

/* Make canvas strictly fit parent in Preview, but inside maintain 800x1000 */
.preview-canvas {
  width: 100%;
  height: auto;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0,0,0,0.1);
  background-color: #fff;
}
</style>
