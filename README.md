# IT8007 – L03 Data Preparation & UCINET Import Log

## Purpose
This document records the data preparation and UCINET import steps completed so far for the L03 Individual Lab in IT8007 (Social Network Analysis). The intent is to clearly document how raw transactional data was transformed into network-ready formats, with decisions made now to support more advanced analysis and the final project later in the course.

At this stage, **no network analysis has been conducted**. This file strictly documents data cleaning, restructuring, and import into UCINET.

---

## 1. Original Dataset

### Source
The original dataset was provided as a CSV file containing individual customer purchase records. Each row represents a single transaction associated with a customer.

### Original Columns
The raw dataset included the following variables:

- Customer ID  
- Age  
- Gender  
- Item Purchased  
- Category  
- Purchase Amount (USD)  
- Location  
- Size  
- Color  
- Season  
- Review Rating  
- Subscription Status  
- Payment Method  
- Shipping Type  
- Discount Applied  
- Promo Code Used  
- Previous Purchases  
- Preferred Payment Method  
- Frequency of Purchases  

This dataset represents **transaction-level tabular data**, not network data.

---

## 2. Network Design Decision

Because the dataset is not inherently a network, a network structure was explicitly defined.

### Chosen Network Structure
A **2-mode (affiliation) network** was constructed:

- **Mode 1 (Rows):** Customers  
- **Mode 2 (Columns):** Items purchased  
- **Tie definition:** A customer purchased a specific item  

This structure was selected because it:
- Preserves the original transactional meaning of the data
- Allows conversion into a 1-mode customer–customer network
- Supports later hypothesis testing (centrality, homophily, QAP)

---

## 3. Network Edge List File

### File Created
```
customer_item_edges.csv
```

### Columns Retained
The original dataset was filtered to include only the variables necessary to define network ties:

| Column Name | Description |
|------------|-------------|
| customer_id | Unique identifier for each customer |
| item | Item purchased by the customer |

All other columns were removed from this file.

### Rationale
UCINET requires relational (dyadic) data to construct networks. Non-relational variables were excluded and stored separately as node attributes.

---

## 4. Attribute File (Node-Level Data)

### File Created
```
customer_attributes.csv
```

### Columns Retained
The following columns were extracted and cleaned from the original dataset to serve as **node attributes**:

| Column Name | Description |
|------------|-------------|
| customer_id | Node identifier (matches network rows) |
| age | Customer age |
| gender | Recoded numeric gender variable |
| subscription | Subscription status (binary) |
| purchase_freq_ord | Ordinal purchase frequency |

### Recoding Decisions

#### Gender
- Male = 1  
- Female = 0  

#### Subscription Status
- Yes = 1  
- No = 0  

#### Purchase Frequency (Ordinal Encoding)
Multiple textual values were normalized into a single ordinal scale:

| Original Values | Encoded Value |
|----------------|---------------|
| Weekly | 5 |
| Bi-Weekly / Fortnightly | 4 |
| Monthly | 3 |
| Quarterly / Every 3 Months | 2 |
| Annually | 1 |

This encoding supports ordinal comparisons, correlation, and future QAP-based hypothesis testing.

---

## 5. File Organization

The following directory structure was used to ensure reproducibility and clarity:

```
IT8007/
 ├── raw_data/
 │   ├── customer_purchases_raw.csv
 │   ├── customer_item_edges.csv
 │   └── customer_attributes.csv
 ├── ucinet_files/
 ├── outputs/
 └── notes/
```

---

## 6. UCINET Import – Network File

### Import Method
The edge list file was imported using **DL Type** so UCINET could convert the dyadic data into a matrix internally.

### UCINET Steps
- Menu: `Data → Import → Excel / Text File`
- Data format selected: **DL Type**
- DL subtype: **Edge list**
- Network type: **2-mode**
- Rows: `customer_id`
- Columns: `item`
- Values: Binary

### Saved UCINET File
```
customer_item.##h
```

This file is a **2-mode matrix** where:
- Rows represent customers
- Columns represent items
- Cell values indicate purchase relationships

---

## 7. UCINET Import – Attribute File

### Import Method
The attribute file was imported as **Attributes**, not as a network or matrix.

### UCINET Steps
- Menu: `Data → Import → Excel / Text File`
- Data format selected: **Attributes**
- ID variable: `customer_id`
- Variables imported: All
- Delimiter: Comma

### Saved UCINET File
```
customer_attr.##h
```

This file contains node-level metadata aligned with customer nodes in the network.

---

## 8. Current Status

At this point, the following UCINET files exist:

| File Name | Description |
|----------|-------------|
| customer_item.##h | 2-mode customer × item network matrix |
| customer_attr.##h | Attribute matrix for customer nodes |

No transformations (e.g., 2-mode to 1-mode) or network analyses (density, centrality, components) have been performed yet.

---

## 9. Next Planned Steps

The next steps in the workflow will include:

1. Converting the 2-mode network into a 1-mode customer–customer network  
2. Creating binary and weighted adjacency matrices  
3. Running descriptive network measures (density, degree centrality, components)  

These steps will be documented in a separate analysis log.

---

Density = 0.040
No. of Ties = 607,532
Avg Degree = 155.777
Std Dev = 0.196

