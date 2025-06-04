import pytest
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
from src.news_login import news_login, init_driver

@pytest.fixture(scope="module")
def driver():
    """Fixture to initialize and quit WebDriver"""
    chrome_options = Options()
    chrome_options.add_argument("--headless")
    chrome_options.add_argument("--no-sandbox")
    driver = webdriver.Chrome(
        ChromeDriverManager().install(),
        options=chrome_options
    )
    yield driver
    driver.quit()

def test_successful_login(driver):
    """Test successful login with valid credentials"""
    test_config = {
        'url': 'https://demo.realworld.io/#/login',
        'username': 'testuser@example.com',
        'password': 'password123',
        'username_field': 'input[type="email"]',
        'password_field': 'input[type="password"]',
        'login_button': 'button[type="submit"]',
        'headless': True
    }
    
    assert news_login(driver, test_config) is True
    assert "feed" in driver.current_url  # Verify redirect after login

def test_failed_login(driver):
    """Test failed login with invalid credentials"""
    test_config = {
        'url': 'https://demo.realworld.io/#/login',
        'username': 'wrong@user.com',
        'password': 'invalidpass',
        'username_field': 'input[type="email"]',
        'password_field': 'input[type="password"]',
        'login_button': 'button[type="submit"]',
        'headless': True
    }
    
    assert news_login(driver, test_config) is False
    assert "login" in driver.current_url  # Still on login page
