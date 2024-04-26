from os import link
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
import pandas as pd
import random

options = webdriver.ChromeOptions()
service = Service()
driver = webdriver.Chrome(service=service, options=options)

driver.get("https://www.davidjones.com/")

# set window size (or else source code is different, cannot find search bar)
time.sleep(random.uniform(3,7))

driver.set_window_size(1080, 1050)

# get search bar
time.sleep(random.uniform(1,2))
search = driver.find_element("name", "q")
# search = driver.find_element("link name", "Search David Jones")

# search for sunglasses
search.send_keys("dior")
search.send_keys(Keys.RETURN) # hit enter -> enter is return

# Scrape page links and page number, combine into a data frame
time.sleep(random.uniform(1,2))
parentPage = driver.find_element("css selector", ".toolbar.top > .page-numbers")

time.sleep(random.uniform(2,5))
links = parentPage.find_elements("tag name", "a")
pageLinks = list(map(lambda link: link.get_attribute("href"), links))
pageNum = list(map(lambda link: link.get_attribute("innerText"), links))  # Element text is rendered by JavaScript, not directly part of HTML hence .text will not work. In such cases, try "innerText" or "textContent"

    # Extract all page links
pageLinksDF = pd.DataFrame({
    "pageNum": pageNum,
    "pageLink": pageLinks
})


    # Get the current page link and insert into 1st row
page1 = {'pageNum': 1, 'pageLink': driver.current_url}
# pageLinksDF.loc[0] = page1

page1 = pd.DataFrame([[1, driver.current_url]], columns=['pageNum', 'pageLink'])
allPageLinksDF = pd.concat([page1, pageLinksDF]).reset_index(drop=True) # need to reset index else there will be two rows set to index = 0

    # remove the last link (next page link)
allPageLinksDF = allPageLinksDF.iloc[:-1]


# Function by page links input
def scrape_pg_map(pg_url): # feed one page html

    # record time
    start_time = time.time()

    driver.get(pg_url)

    time.sleep(random.uniform(4,7))

    # scroll to bottom
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")

    time.sleep(random.uniform(1,5))

    main = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.CLASS_NAME, "products")) 
        # double brackets here bc this fx is expecting a single argument instead of a tuple, so need to put it in a bracket to avoid passing a tuple.
    )

    # brand
    # must use find_elements WITH THE "S" to return not just the first matching web element
    products = main.find_elements("css selector", ".item.isUpdated") # CSS for multiple classes should not have any space in between, needs to be connected with a dot

    l_brands = list(map(lambda product: product.find_element("css selector", ".item-brand").text, products))
    
    l_names = list(map(lambda name: name.find_element("tag name", "h4").text, products))

    l_price = list(map(lambda price: price.find_element("css selector", ".pricing").text, products))

    l_img = list(map(lambda img: img.find_element("css selector", ".image-link > img").get_attribute("src"), products))

    page = pd.DataFrame({
        'Brand': l_brands,
        'Name': l_names,
        'Price': l_price,
        'Image_Link': l_img
    })

    end_time = time.time()
    execution_time = end_time - start_time

    print(f"The time of execution of the code is : {execution_time} seconds")

    print("Scraped, list length: " + str(len(l_brands)))
    print(page)
    return page


page_links_test = page_links[0:3]

# Generate output
result = list(map(scrape_pg_map, allPageLinksDF["pageLink"])) # map the page links in list
print(result)

allPageLinksDF["pageLink"][]

allDJsunnies = pd.concat(result)

driver.quit()
