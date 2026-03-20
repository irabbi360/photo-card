<script setup lang="ts">
import CardGenerator from "@/components/CardGenerator.vue";
</script>

<template>
  <main>
    <CardGenerator />
  </main>
  <footer class="app-footer">
    <div class="footer-content">
      <p>Developed by <span class="developer-name">Fazle Rabbi</span></p>
      <div class="social-links">
        <a href="https://www.facebook.com/frabbiofficial" target="_blank" rel="noopener noreferrer" class="social-link" title="Facebook">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
            <path d="M24 12.073c0-6.627-5.373-12-12-12s-12 5.373-12 12c0 5.99 4.388 10.954 10.125 11.854v-8.385H7.078v-3.47h3.047V9.43c0-3.007 1.792-4.669 4.533-4.669 1.312 0 2.686.235 2.686.235v2.953H15.83c-1.491 0-1.956.925-1.956 1.874v2.25h3.328l-.532 3.47h-2.796v8.385C19.612 23.027 24 18.062 24 12.073z"/>
          </svg>
        </a>
        <a href="https://www.linkedin.com/in/frabbiofficial" target="_blank" rel="noopener noreferrer" class="social-link" title="LinkedIn">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
            <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.225 0z"/>
          </svg>
        </a>
        <a href="https://github.com/irabbi360" target="_blank" rel="noopener noreferrer" class="social-link" title="GitHub">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
            <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v 3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
          </svg>
        </a>
      </div>
    </div>
  </footer>
</template>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
  background-color: #fafafa;
}

main {
  min-height: calc(100vh - 140px);
  display: flex;
  align-items: center;
  justify-content: center;
}

.app-footer {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 2rem 1rem;
  border-top: 3px solid #667eea;
  color: #ffffff;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

.footer-content {
  max-width: 1200px;
  margin: 0 auto;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.footer-content p {
  margin: 0;
  font-size: 1rem;
  font-weight: 500;
}

.developer-name {
  font-weight: 700;
  color: #ffd700;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.social-links {
  display: flex;
  gap: 1.5rem;
  justify-content: center;
  align-items: center;
}

.social-link {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 48px;
  height: 48px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  color: #ffffff;
  transition: all 0.3s ease;
  text-decoration: none;
  border: 2px solid transparent;
}

.social-link:hover {
  background-color: rgba(255, 255, 255, 0.3);
  transform: translateY(-4px);
  border-color: #ffd700;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.social-link svg {
  width: 24px;
  height: 24px;
  transition: transform 0.3s ease;
}

.social-link:hover svg {
  transform: scale(1.1);
}

@media (max-width: 768px) {
  main {
    min-height: calc(100vh - 200px);
  }

  .footer-content {
    gap: 0.8rem;
  }

  .social-links {
    gap: 1rem;
  }

  .social-link {
    width: 44px;
    height: 44px;
  }

  .footer-content p {
    font-size: 0.95rem;
  }
}
</style>
