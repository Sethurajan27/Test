from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
from config import get_config
import os
import sys

SCREENSHOT_DIR = "screenshots"

def init_driver(headless=True):
    """Initialize Chrome WebDriver"""
    chrome_options = Options()
    if headless:
        chrome_options.add_argument("--headless")
    chrome_options.add_argument("--no-sandbox")
    chrome_options.add_argument("--disable-dev-shm-usage")
    
    try:
        driver = webdriver.Chrome(ChromeDriverManager().install(), options=chrome_options)
        return driver
    except Exception as e:
        print(f"🚨 Failed to start Chrome: {e}")
        sys.exit(1)

def ensure_screenshot_dir():
    """Create screenshots directory if it doesn't exist"""
    if not os.path.exists(SCREENSHOT_DIR):
        os.makedirs(SCREENSHOT_DIR)

def news_login(driver, config):
    """Automate login for a news website"""
    try:
        driver.get(config['url'])
        
        # Enter username
        WebDriverWait(driver, 10).until(
            EC.presence_of_element_located((By.ID, config['username_field']))
        ).send_keys(config['username'])
        
        # Enter password
        WebDriverWait(driver, 10).until(
            EC.presence_of_element_located((By.ID, config['password_field']))
        ).send_keys(config['password'])
        
        # Click login button
        WebDriverWait(driver, 10).until(
            EC.element_to_be_clickable((By.ID, config['login_button']))
        ).click()
        
        # Verify successful login
        WebDriverWait(driver, 10).until(
            lambda d: "dashboard" in d.current_url.lower() or "account" in d.current_url.lower()
        )
        return True
        
    except Exception as e:
        print(f"❌ Login failed: {e}")
        ensure_screenshot_dir()
        driver.save_screenshot(f"{SCREENSHOT_DIR}/login_error.png")
        return False

def main():
    config = get_config()
    driver = init_driver(headless=config.get('headless', True))
    ensure_screenshot_dir()
    
    try:
        print("🔐 Attempting login...")
        if news_login(driver, config):
            print("✅ Login successful!")
            driver.save_screenshot(f"{SCREENSHOT_DIR}/news_login_success.png")
        else:
            print("❌ Login failed. Check 'screenshots/login_error.png'")
            sys.exit(1)
    finally:
        driver.quit()

if __name__ == "__main__":
    main()
