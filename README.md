# Task--05-10-26

## Task 1-Swag Labs
### Program
```
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()

driver.get("https://www.saucedemo.com/")

username = driver.find_element(By.ID, "user-name")
password = driver.find_element(By.NAME, "password")
login = driver.find_element(By.ID, "login-button")

username.send_keys("standard_user")
password.send_keys("secret_sauce")

print(username.get_attribute("placeholder"))
print(login.is_enabled())
print(username.is_displayed())
input("Press Enter to close the browser...")

driver.quit()
```
### Output

<img width="1600" height="898" alt="image" src="https://github.com/user-attachments/assets/846e88a0-50f3-4308-a942-ba820a1a209c" />
<img width="1600" height="899" alt="image" src="https://github.com/user-attachments/assets/5c96f239-fe8b-49e2-b276-5c875f149c1a" />
<img width="1600" height="899" alt="image" src="https://github.com/user-attachments/assets/2be5b22d-7e8d-4107-9e52-4ea48e6f3b00" />
<img width="1600" height="899" alt="image" src="https://github.com/user-attachments/assets/5dec67d0-bb63-451d-b3da-3441c3ae4c49" />
<img width="1600" height="899" alt="image" src="https://github.com/user-attachments/assets/9858cb84-ede7-498f-965c-1138f6e0b743" />
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/b94dbfb7-eb2f-403b-8506-439fd46df41f" />
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/3979103d-88bc-485e-acd0-4d920fa762ff" />
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/a5623b9c-568f-488e-9292-0abb9f274a09" />
<img width="1600" height="899" alt="image" src="https://github.com/user-attachments/assets/42bf64c7-c6cb-4a77-bfe3-a5a55c4268ef" />


## Task 2-Flipkart 
### Program
```
import time
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException

chrome_options = Options()
chrome_options.add_experimental_option("detach", True)
chrome_options.add_argument("--start-maximized")

driver = webdriver.Chrome(options=chrome_options)

try:
    driver.get("https://www.flipkart.com/account/login")
    wait = WebDriverWait(driver, 15)

    
    input_field = wait.until(
        EC.element_to_be_clickable((By.XPATH, "//input[@type='text']"))
    )
    input_field.clear()
    phone_number = "YOUR_PHONE_NUMBER_OR_EMAIL"
    input_field.send_keys(phone_number)

   
    login_button = wait.until(
        EC.element_to_be_clickable(
            (By.XPATH, "//button[contains(., 'Request OTP') or contains(., 'CONTINUE') or contains(., 'Continue')]")
        )
    )
    driver.execute_script("arguments[0].click();", login_button)
    print("Requested OTP...")

  
    otp_code = input("\nEnter the OTP received via SMS: ").strip()

    
    otp_inputs = driver.find_elements(By.XPATH, "//form//input[contains(@class, 'r4vIwl') or @maxlength='1']")
    if len(otp_inputs) == 6:
        for index, digit in enumerate(otp_code[:6]):
            otp_inputs[index].send_keys(digit)
    else:
        single_otp_box = wait.until(
            EC.element_to_be_clickable((By.XPATH, "//input[@type='text' or @type='number']"))
        )
        single_otp_box.send_keys(otp_code)

  
    verify_button = wait.until(
        EC.element_to_be_clickable(
            (By.XPATH, "//button[contains(., 'Verify') or contains(., 'VERIFY') or contains(., 'Login')]")
        )
    )
    driver.execute_script("arguments[0].click();", verify_button)
    print("\nSMS OTP verified. Moving to Phone Call Verification step...")

    
    post_otp_wait = WebDriverWait(driver, 20)

    call_trigger_xpath = (
        "//button[contains(translate(., 'CALL', 'call'), 'call') or contains(translate(., 'VOICE', 'voice'), 'voice')] | "
        "//a[contains(translate(., 'CALL', 'call'), 'call') or contains(translate(., 'VOICE', 'voice'), 'voice')] | "
        "//span[contains(translate(., 'CALL', 'call'), 'call') or contains(translate(., 'VOICE', 'voice'), 'voice')]"
    )

    try:
        call_btn = post_otp_wait.until(EC.element_to_be_clickable((By.XPATH, call_trigger_xpath)))
        driver.execute_script("arguments[0].click();", call_btn)
        print(">> Triggered 'Call Verification' button on screen.")
    except TimeoutException:
        print(">> No separate call button required (Flipkart is calling automatically or redirecting).")

    print("\n-----------------------------------------------------")
    print("Flipkart is placing the verification call to your phone.")
    print("Answer the call and follow the automated instructions.")
    print("-----------------------------------------------------")
    call_otp_inputs = driver.find_elements(By.XPATH, "//form//input[@type='text' or @type='number']")
    if call_otp_inputs:
        call_code = input("\nIf the call spoke an OTP/code to enter on screen, type it here (otherwise press Enter): ").strip()
        if call_code:
            if len(driver.find_elements(By.XPATH, "//input[@maxlength='1']")) == len(call_code):
                boxes = driver.find_elements(By.XPATH, "//input[@maxlength='1']")
                for i, char in enumerate(call_code):
                    boxes[i].send_keys(char)
            else:
                call_otp_inputs[0].send_keys(call_code)
            try:
                final_btn = driver.find_element(By.XPATH, "//button[contains(., 'Submit') or contains(., 'Verify') or contains(., 'Confirm')]")
                driver.execute_script("arguments[0].click();", final_btn)
            except Exception:
                pass

    print("\nVerification complete! Checking logged-in state...")
    time.sleep(5)

except Exception as e:
    print(f"\n--- ERROR OCCURRED ---\n{e}\n-------------------------\n")

finally:
    input("\nPress Enter in the terminal to close the browser session...")
```
### Output
<img width="1600" height="899" alt="image" src="https://github.com/user-attachments/assets/7474893f-216d-438c-a73c-0f54ce5c957e" />
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/c3966b84-e390-4fec-92f1-7065ed43a8ff" />
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/27d6b4d2-3145-4401-82b9-f8d863a7b9d2" />
<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/7a1a48c6-52c7-4a1c-adff-e4b568565a67" />

