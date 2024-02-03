1. Exploring FHIR Resources with Python

This example uses the `requests` library to fetch data from a public FHIR server for the `Patient` resource type and extracts key information.

```python
import requests
import json

# Fetch Patient data from a FHIR server
url = 'http://hapi.fhir.org/baseR4/Patient?_format=json'
response = requests.get(url)
data = response.json()

# Extract and print patient names
for entry in data['entry']:
    patient = entry['resource']
    name = patient.get('name', [])[0]  # Assuming first name entry is primary
    if name:
        full_name = f"{name.get('given', [''])[0]} {name.get('family', '')}"
        print(f"Patient Name: {full_name}")
```

### 2. Data Manipulation and Analysis

This example demonstrates how to convert FHIR JSON data into a pandas DataFrame for analysis. It assumes you have a JSON file `observations.json` containing FHIR Observation resources.

```python
import pandas as pd

# Load FHIR Observation data from a JSON file
observations_df = pd.read_json('observations.json', orient='records')

# Normalize the JSON structure into a flat table
observations_normalized = pd.json_normalize(observations_df['entry'].apply(lambda x: x['resource']))

# Calculate basic statistics for a health measurement (e.g., blood glucose levels)
blood_glucose = observations_normalized[observations_normalized['code.text'] == 'Blood Glucose']
print(blood_glucose['valueQuantity.value'].describe())
```

### 3. Integration and Interoperability Analysis

The following code merges data from two FHIR resources, `MedicationRequest` and `Condition`, based on patient identifiers. It assumes you have two pandas DataFrames: `med_requests_df` and `conditions_df`.

```python
# Example data frames
# med_requests_df = pd.DataFrame([...])  # Assume this contains medication request data
# conditions_df = pd.DataFrame([...])  # Assume this contains condition data

# Merge data frames on patient identifier
integrated_data = pd.merge(med_requests_df, conditions_df, on='patient.identifier', how='inner')

# Analyze the integrated dataset (example: find common conditions for a specific medication)
common_conditions = integrated_data[integrated_data['medication.code.text'] == 'Aspirin']['condition.code.text'].value_counts()
print(common_conditions)
```

### 4. Visualization and Reporting

This example uses the `matplotlib` library to visualize the frequency of conditions from a dataset containing patient encounter information.

```python
import matplotlib.pyplot as plt

# Assume encounters_df is a DataFrame containing encounter data, including a 'condition' column
# encounters_df = pd.DataFrame([...])

# Plot the frequency of conditions
encounters_df['condition'].value_counts().plot(kind='bar')
plt.title('Frequency of Patient Conditions')
plt.xlabel('Condition')
plt.ylabel('Frequency')
plt.xticks(rotation=45)
plt.show()
```

Each of these examples provides a foundation for students to begin exploring, manipulating, and visualizing FHIR data using Python. They are encouraged to expand upon these examples, exploring more complex analyses and visualizations as they become more comfortable with the data and tools.
