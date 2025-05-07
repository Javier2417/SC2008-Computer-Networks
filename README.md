# SC2008 Computer Networks - Lab 4: sFlow Traffic Analysis

This repository contains the code and analysis for Lab 4 of the SC2008 Computer Networks course at Nanyang Technological University. The project involves analyzing network traffic data captured in sFlow format (`Data_3.csv`) to identify key communication patterns, protocols, and traffic characteristics.

## Table of Contents
* [Project Objectives](#project-objectives)
* [Dataset](#dataset)
* [Methodology](#methodology)
* [Technologies Used](#technologies-used)
* [Setup and Installation](#setup-and-installation)
* [Usage](#usage)
* [Key Findings](#key-findings)
* [Network Visualization](#network-visualization)
* [Author](#author)
* [License](#license)

## Project Objectives

The main goals of this analysis were to:
1.  Identify the **Top 5 Talkers** (most active source IPs) and **Top 5 Listeners** (most active destination IPs) in the network trace, including their associated organizations. (Exercise 4A)
2.  Determine the distribution of traffic based on **Transport Layer protocols** (TCP vs. UDP). (Exercise 4B)
3.  Identify the **Top 5 Application Layer protocols** based on destination port numbers and map them to known services. (Exercise 4C)
4.  Estimate the **total network traffic volume** represented by the sampled data, accounting for the sampling rate. (Exercise 4D)
5.  Perform additional analysis by identifying the **Top 5 communication pairs** (Source IP <-> Destination IP) and visualizing the network communication patterns. (Exercise 4E)

## Dataset

*   **Source:** `Data_3.csv`
*   **Format:** sFlow records, loaded without headers and assigned column names based on the standard sFlow FLOW sample format.
*   **Key Columns Used:** `src_IP`, `dst_IP`, `IP_protocol`, `dst_transport_port`, `IP_size`, `sampling_rate`.

## Methodology

1.  **Data Loading & Cleaning:** Loaded the CSV data into a Pandas DataFrame, assigned appropriate headers, and dropped an unnecessary trailing column.
2.  **Top Talkers/Listeners:** Used Pandas `value_counts()` and `nlargest(5)` on `src_IP` and `dst_IP` columns. Utilized the `ipwhois` library to perform RDAP lookups and identify the registered organization name for each top IP address.
3.  **Transport Protocol Analysis:** Calculated the frequency of each `IP_protocol` value. Filtered for TCP (protocol number 6) and UDP (protocol number 17) and calculated their percentage contribution to the total packet count.
4.  **Application Protocol Analysis:** Identified the top 5 most frequent `dst_transport_port` values. Mapped common ports (e.g., 443, 80) to their corresponding services (HTTPS, HTTP) and identified others as dynamic/unassigned based on standard port ranges.
5.  **Traffic Volume Calculation:** Summed the `IP_size` column (assuming bytes, *adjust if bits*) and multiplied by the `sampling_rate` (2048) to estimate the total traffic. Converted the result to Megabits (Mb) or Megabytes (MB) as appropriate (*Clarify unit if possible*).
6.  **Communication Pairs & Visualization:** Grouped data by `src_IP` and `dst_IP` pairs, calculated the packet count for each pair, and identified the top 5/top 100 pairs. Constructed a directed graph using `NetworkX` representing the top 100 communication flows, visualizing nodes (IPs) and weighted edges (packet counts). Applied a spring layout, adjusted for component clumping, to visualize connectivity patterns.

## Technologies Used

*   **Language:** Python 3.x
*   **Libraries:**
    *   Pandas: Data manipulation and analysis.
    *   NetworkX: Network graph creation and analysis.
    *   Matplotlib: Plotting (specifically for the NetworkX graph).
    *   ipwhois: Retrieving registration details (organization names) for IP addresses.
    *   Plotly Express (Optional, if used for other plots not shown in provided code): Interactive plotting.
    *   Math: For calculations (e.g., log for edge width).
*   **Environment:** Jupyter Notebook

## Setup and Installation

1.  Clone the repository:
    ```bash
    git clone [Your GitHub Repo URL for this project]
    cd [repository-name]
    ```
2.  (Recommended) Create and activate a Python virtual environment:
    ```bash
    python -m venv venv
    # On Windows: venv\Scripts\activate
    # On macOS/Linux: source venv/bin/activate
    ```
3.  Install required dependencies:
    ```bash
    pip install pandas networkx matplotlib ipwhois plotly # Add any other specific libraries used
    ```
4.  Ensure the `Data_3.csv` file is present in the same directory or provide the correct path in the script.

## Usage

1.  Open the Jupyter Notebook `SC2008_lab4.ipynb` (or your actual notebook filename).
    ```bash
    jupyter notebook SC2008_lab4.ipynb
    ```
2.  Run the cells sequentially to execute the data loading, analysis steps for each exercise, and view the results and visualizations.

## Key Findings

*   **Top Talkers:** The most active source IPs were identified as belonging to organizations like EUR-BIO-INST, NTUNET1, NLM-ETHER, NKN-IIT-GUW, and T-NTU.EDU.TW-NET. *(See specific IPs in the output tables)*.
*   **Top Listeners:** The most frequent destination IPs belonged to A-STAR-AS-AP, NUSNET, RPNET, PNNL, and IITB-IN. *(See specific IPs in the output tables)*.
*   **Transport Protocols:** TCP (Protocol 6) accounted for approximately **80.8%** of the sampled packets, while UDP (Protocol 17) accounted for roughly **13.6%**.
*   **Application Protocols:** HTTPS (Port 443) and HTTP (Port 80) were the most frequently observed destination ports, indicating significant web traffic. Several dynamic/unassigned ports also showed high activity.
*   **Total Traffic:** The estimated total traffic volume, accounting for the sampling rate, was approximately **126,519 Mb** (or ~15,815 MB if IP_size was in bytes).
*   **Top Communication Pairs:** The most frequent communication was observed between `193.62.192.8` (EUR-BIO-INST) and `137.132.228.15` (NUSNET). Other top pairs involved NLM-ETHER -> A-STAR, NKN-IIT-GUW -> PNNL, etc. *(Refer to the output table for details)*.

## Network Visualization

A network graph visualizing the top 100 communication flows was generated using NetworkX. Nodes represent IP addresses, and directed edges represent traffic flow, with edge width proportional to the logarithm of the packet count between IPs. This helps visualize the primary communication hubs and paths in the sampled data.


## Author

*   Javier Tin Jing Lun ([Javier2417])

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
