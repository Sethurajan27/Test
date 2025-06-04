import os
from dotenv import load_dotenv

def get_config():
    """Load settings from .env file"""
    load_dotenv()
    
    required_vars = [
        'NEWS_URL',
        'NEWS_USERNAME',
        'NEWS_PASSWORD',
        'NEWS_USERNAME_FIELD',
        'NEWS_PASSWORD_FIELD',
        'NEWS_LOGIN_BUTTON'
    ]
    
    config = {
        'url': os.getenv('NEWS_URL'),
        'username': os.getenv('NEWS_USERNAME'),
        'password': os.getenv('NEWS_PASSWORD'),
        'username_field': os.getenv('NEWS_USERNAME_FIELD'),
        'password_field': os.getenv('NEWS_PASSWORD_FIELD'),
        'login_button': os.getenv('NEWS_LOGIN_BUTTON'),
        'headless': os.getenv('HEADLESS', 'true').lower() == 'true'
    }
    
    missing = [var for var in required_vars if not os.getenv(var)]
    if missing:
        raise ValueError(f"Missing env vars: {', '.join(missing)}")
    
    return config
