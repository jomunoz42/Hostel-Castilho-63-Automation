# HC63 Hostel Operations Automation Pipeline

<div align="center">

![Python](https://img.shields.io/badge/Python-Automation-blue?style=for-the-badge&logo=python)
![Selenium](https://img.shields.io/badge/Selenium-Browser%20Automation-green?style=for-the-badge&logo=selenium)
![Excel](https://img.shields.io/badge/Excel-openpyxl-darkgreen?style=for-the-badge)
![WhatsApp](https://img.shields.io/badge/WhatsApp-Web%20Automation-25D366?style=for-the-badge&logo=whatsapp)
![Status](https://img.shields.io/badge/Status-Used%20Internally-success?style=for-the-badge)

</div>

I have built a Python automation pipeline to streamline daily hostel operations at **Castilho Hostel & Suites 63**.

This project was developed to solve real operational problems in a live hospitality environment, automating repetitive workflows related to reservations, breakfast lists, city tax processing, door code generation, Word document creation, VCF contact generation, and WhatsApp guest communication.

This pipeline automates the full process from raw reservation exports to printed operational documents and guest communication.

The system is currently used internally and was designed with a practical goal: reduce manual workload, avoid human errors, and make daily operations way faster and more reliable.

Below is the access-code automation in action — one of the highest-impact parts of the pipeline.

<img src="gifs/code_generation.gif" width="100%"/>

<p align="center">
    <strong>VCF Contact Generation and Delivery</strong>
</p>

Automated contact generation and delivery as part of the guest communication workflow.

<img src="gifs/VCF_to_myself.gif" width="100%"/>

<p align="center">
    <strong>Complete Pipeline Execution Output</strong>
</p>

The CLI output of the full automation pipeline running from reservation export to final cleanup.

<img src="gifs/terminal_output.gif" width="100%"/>

---

## 🚀 Impact

The automation reduced a workflow that previously took approximately:

- **1h30 of concentrated work for me**
- **Up to 3h for a less computer-experienced person**

to approximately:

- **8 minutes of automated execution**

While improving reliability and reducing manual errors.

---

## 🏛️ General Architecture

The pipeline is controlled by a central orchestrator:

```text
manager.py
```

`manager.py` is responsible for coordinating the full workflow, including:

- Creating the daily log file if it does not exist
- Checking required environment credentials
- Logging into Ynnov, the reservation platform
- Exporting, converting and duplicating Excel files for each automation stage
- Creating and printing the breakfast list
- Creating and setting the city tax list
- Logging into TTLock, the access-code platform
- Parsing reservation data and generating all required access codes
- Inserting generated codes into a Word template and printing the final document
- Creating and sending the VCF contact file
- Parsing guest contacts and sending WhatsApp messages
- Cleaning temporary files safely
- Committing and pushing the daily execution log to a dedicated repository for centralized execution history

The project follows a simple principle:

> The Excel files are the source of truth.

---

## 🔗 Full Automation Flow

### 1. PMS Login and Reservation Export

The automation logs into **Ynnov**, the PMS used to manage reservations.

From there, it exports two `.xls` files:

1. A file containing all guests currently staying in the hostel
2. A file containing all guests checking in the next day

These files are then converted to `.xlsx` and duplicated depending on the operational task they will support.

---

### 2. Breakfast List Generation

From the reservation export, the automation creates a breakfast list containing:

- Guest names
- Number of adults
- Number of children
- Room information
- Total number of people

The script also parses and cleans invalid entries, removing guests who should not appear in the breakfast list for several operational reasons.

After the list is generated, it is printed automatically.

<p align="center">
    <img src="images/breakfast-list.jpg" width="75%">
</p>

---

### 3. City Tax List Generation

The tax list is generated from reservation data and contains:

- Guest names
- Dates
- Tax values to be paid
- Booking reservation ID
- Property associated with the reservation

The final result is prepared in a format suitable for operational usage and tracking.

<img src="images/tax-list.png" width="100%"/>

---

## 🔑 Door Code Automation

The heart of the pipeline is the access code automation.

Starting from a copied `.xlsx` file, the system parses the reservation data to extract and normalize:

- Guest first name
- Number of nights
- Room name in a presentable format
- Reservation grouping information

The automation then groups guests by number of nights and creates a shared **street door code** for each group.

After that, it processes each reservation individually and creates a specific **room code** for each guest, with:

- The correct number of nights
- The correct room
- The correct guest assignment

This is one of the most important parts of the system because creating a single guest code manually requires around **50 clicks and/or key presses**.

Each code creation is wrapped in error handling so that if one code fails, the automation logs the problem and continues instead of crashing the entire pipeline.

---

## 📄 Code List Generation

After the access codes are created, the automation generates a final `code.xlsx` file with the following structure:

| Column | Data |
|---|---|
| A | Guest Name |
| B | Room |
| C | Number of Nights |
| D | Room Code |
| E | Street Code |

This information is then inserted into a pre-existing Word document template.

The automation fills the table in the correct format and ensures that the final document is ready to be printed and used by the team.

If the table is completed successfully, the code list is printed automatically.


<table>
<tr>
<td align="center"><strong>Generated Code List</strong></td>
<td align="center"><strong>Printed Operational Document</strong></td>
</tr>
<tr>
<td><img src="images/code-table-empty.png" width="100%"></td>
<td><img src="images/code-table-completed.jpg" width="100%"></td>
</tr>
</table>

---

## 📱 WhatsApp and VCF Contact Automation

The pipeline also automates part of the remote check-in process for a separate property.

From another copy of the reservation file, the automation extracts the guests associated with the remote property and formats:

- Names
- Phone numbers
- Room information

It then creates a `.vcf` contact file containing all required guest contacts.

The file is sent to the Hostel's WhatsApp, opened on the phone, and imported directly into the contacts list.

This allows multiple guest contacts to be created almost instantly instead of manually registering each one.

After that, the automation uses WhatsApp Web links to send the first remote check-in message to each guest.

To reduce the risk of being blocked or flagged by WhatsApp, messages are sent with a randomized delay between **8 and 10 seconds**.


<!-- WhatsApp messages sent -->
<img src="images/whatsapp-messages.png" width="100%"/>

---

## 🛠 Technologies Used

- Python
- Selenium
- openpyxl
- WhatsApp Web automation
- VCF contact generation
- Excel file processing
- Word document automation
- Environment variables
- Logging
- Browser automation
- File cleanup utilities

---

### Excel Parsing and Data Cleaning

Reservation exports contain data that needs to be cleaned, filtered, converted, and reorganized.

The automation handles:

- `.xls` to `.xlsx` conversion
- Duplicated files for different workflows
- Row deletion based on keywords
- Stale row issues after deletion
- Guest filtering
- Reservation grouping
- Formatted operational outputs

---

### Real-World Workflow Design

A major challenge was not only writing code, but understanding the actual hostel workflow well enough to automate it correctly.

The system had to match the way the team works, including:

- Printed documents
- Existing templates
- WhatsApp habits
- PMS exports
- Access code systems
- Remote check-in workflow
- Manual fallback needs

---

## 🛡️ Reliability and Recovery Strategy

The automation is designed to continue whenever partial recovery is possible instead of terminating the entire workflow after a localized failure.

Logging and localized error handling isolate failures so that one failed section or individual guest operation does not necessarily prevent the remaining workflow from completing. This is especially important during access code generation, where guests are processed individually.

At the end of execution, temporary files that are no longer needed are removed safely. However, useful generated files, especially the code list, are intentionally preserved when their corresponding stage fails.

This allows valid intermediate results to remain available for manual use, supports debugging, and ensures that operational work can still be completed when part of the automation fails.

> Avoid total failure whenever partial recovery is possible.

This approach is essential because the project operates in a real business environment where the underlying work still needs to be completed even when one part of the automation encounters an error.


## 🔒 Repository and Security Note

Credentials are stored in environment variables instead of being hardcoded in the source code. Before execution, the system verifies that all required credentials are available, preventing incomplete runs caused by missing configuration.

This repository does not contain the full private production source code. The project is used internally in a real business environment, and the complete implementation includes operational details, workflows, integrations, and other production-specific information that are not appropriate to publish publicly.

This repository exists as a technical overview and portfolio presentation of the project's architecture, workflow, engineering decisions, and operational impact.


---

## 👤 Author

- João Muñoz
