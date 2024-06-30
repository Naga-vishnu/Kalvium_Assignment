import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL of the election results page
url = "https://results.eci.gov.in/PcResultGenJune2024/index.htm"

# Send a GET request to the website
response = requests.get(url)
response.encoding = 'utf-8'

# Parse the HTML content
soup = BeautifulSoup(response.text, 'html.parser')

# Example: Finding all divs with the results
# Modify the selector based on the actual structure you find
results_div = soup.find('div', {'class': 'table-responsive'})

# Check if the results_div was found
if results_div:
    # Initialize lists to store the data
    parties = []
    won = []
    leading = []
    total = []

    # Extract data from the table rows
    for row in results_div.find_all('tr')[1:]:
        cells = row.find_all('td')
        if len(cells) >= 4:
            parties.append(cells[0].text.strip())
            won.append(cells[1].text.strip())
            leading.append(cells[2].text.strip())
            total.append(cells[3].text.strip())

    # Create a DataFrame to store the extracted data
    results_df = pd.DataFrame({
        'Party': parties,
        'Won': won,
        'Leading': leading,
        'Total': total
    })

    # Save the DataFrame to a CSV file
    results_df.to_csv('lok_sabha_election_results_2024.csv', index=False)

    print("Data has been scraped and saved to 'lok_sabha_election_results_2024.csv'.")
else:
    print("Could not find the results div on the page. Please check the HTML structure.")
