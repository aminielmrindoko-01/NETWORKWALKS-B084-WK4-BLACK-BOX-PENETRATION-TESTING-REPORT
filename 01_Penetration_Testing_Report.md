# NETWORKWALKS-B084-WK4-BLACK-BOX-PENETRATION-TESTING-REPORT
# NETWORKWALKS — PENETRATION TESTING REPORT

## Mediroza General Hospital

**Project:** Week 4 – Penetration Testing Project  
**Assessment type:** Black-box penetration testing and vulnerability assessment  
**Target:** `https://medirozahospital.com`  
**Client:** Mediroza General Hospital  
**Duration:** 5 days  
**Prepared by:** Aminieli Benedict Mrindoko  
**Classification:** **CONFIDENTIAL — AUTHORISED PERSONNEL ONLY**  
**Report status:** Draft based on tester-provided observations

> **Authorisation and handling notice**
>
> This report documents an educational penetration-testing exercise described as authorised by NetworkWalks and the client. Testing must remain within the written scope and rules of engagement. Treat patient, employee, salary, identity and shareholder information as highly confidential. Do not place unredacted sensitive records, recovered passwords or patient reports in a public repository or unrestricted submission folder.

## Document control

| Field | Value |
|---|---|
| Client | Mediroza General Hospital |
| Engagement | Week 4 penetration testing project |
| Assessment type | Black-box web application assessment |
| Primary target | `https://medirozahospital.com` |
| Duration | 5 days |
| Author | Aminieli Benedict Mrindoko |
| Version | 1.0 – Draft |
| Date | 26 November 2026 |
| Classification | Confidential |

## Evidence folder structure

```text
NETWORKWALKS_WEEK4_AMINIELI_MRINDOKO/
├── 01_Penetration_Testing_Report.md
├── 02_Evidence/
│   ├── M1_Reconnaissance/
│   ├── M1_Authentication_SQLi/
│   ├── M1_Patient_Reports/
│   ├── M2_PDF_Recovery/
│   ├── M3_Database_Exposure/
│   └── M4_Reporting/
```

---

# 1. Executive Summary

## 1.1 Engagement overview

A black-box penetration-testing assessment was conducted against the Mediroza General Hospital web application to identify security weaknesses, assess their potential impact and document evidence of the reported findings.

The assessment activities described by the tester included passive reconnaissance, manual review of publicly served source code and application behaviour, endpoint discovery, authentication testing, SQL injection testing, retrieval of three encrypted patient laboratory reports, PDF password-recovery testing and analysis of a publicly accessible historical database backup.

The most significant reported finding was SQL injection in the patient authentication mechanism. The tester reported that this weakness enabled access to the patient portal and retrieval of three encrypted patient reports. Further investigation identified a historical SQL database backup in an exposed `/old/` directory. The supplied backup contents included employee contact and identity information, monthly salaries and shareholder ownership details.

The tester also reported successful password recovery for all three PDF reports. Two were recovered using the NetworkWalks-provided wordlist; a different John the Ripper wordlist was used for the third after the initial attempt failed.

These observations indicate serious potential confidentiality risks. The findings and severity ratings in this draft are based on the supplied account and should be validated against the original evidence before the report is submitted as a final verified assessment.

## 1.2 Key findings

| ID | Finding | Provisional severity |
|---|---|---|
| F-01 | SQL injection in patient authentication | Critical |
| F-02 | Publicly accessible historical database backup | Critical |
| F-03 | Recoverable passwords protecting patient PDF reports | High |
| F-04 | Disclosure of potentially sensitive paths through `robots.txt` and related discovery resources | Low / Informational |

> **Severity note:** The exposure of paths in `robots.txt` is not, by itself, proof of an access-control vulnerability. The security impact arose because a sensitive backup resource was reportedly accessible without adequate access control. Avoid double-counting the same impact across F-02 and F-04.

## 1.3 Overall conclusion

Based on the reported successful access to patient documents and discovery of sensitive database records, the application should be treated as presenting a **critical confidentiality risk** until the relevant issues have been remediated and retested.

### Evidence insertion — Executive Summary

- Directory: `02_Evidence/M4_Reporting/`
- File: `ES-01_Findings_Overview.png`

![Executive summary evidence placeholder](02_Evidence/M4_Reporting/ES-01_Findings_Overview.png)

---

# 2. Scope and Methodology

## 2.1 Scope

The assessment was described as a black-box test of the target web application.

**Primary target:** `https://medirozahospital.com`

Resources reported as discovered or examined:

- `/robots.txt`
- `/sitemap.xml`
- `/old/`
- `/patient/`
- `/staff/`
- Patient login interface
- Staff login interface
- Historical database backup resource

The backup URL supplied in the findings was:

`https://www.medirozahospital.com/old/mediroza_db_backup_2019.sql`

**Scope qualification:** The `www` hostname is distinct from the bare domain. Confirm from the written rules of engagement that it was included in scope before describing testing of that hostname as authorised. Do not test any hostname or resource not explicitly authorised.

## 2.2 Rules of engagement and limitations

The project instructions supplied for this assessment specified:

- Testing limited to the authorised target domain.
- No social engineering.
- No denial-of-service testing.
- No testing outside the agreed scope.
- Written authorisation was stated to have been granted.
- Findings were to be documented for the educational engagement.

This report does not claim that unreported tests were performed. Where the tester's notes do not specify a tool version, timestamp, request/response, or reproducible result, that information is marked as not supplied rather than assumed.

## 2.3 Objectives

1. Identify publicly discoverable resources and potential application entry points.
2. Assess the patient and staff authentication interfaces.
3. Determine whether the patient login was vulnerable to SQL injection.
4. Retrieve the three designated patient PDF reports as required by the project.
5. Assess the password protection applied to the retrieved PDF files.
6. Investigate the reported exposed backup for employee salary and shareholder information.
7. Document findings, evidence, risk and remediation recommendations.

## 2.4 Methodology and evidence

### Phase 1 — Passive reconnaissance and application review

The tester reviewed publicly accessible website resources, inspected served source code and manually considered potential application paths and entry points. The purpose was to understand the externally visible application surface before authentication testing.

**Evidence files**

- `02_Evidence/M1_Reconnaissance/M1-01_Target_Overview.png`
- `02_Evidence/M1_Reconnaissance/M1-02_Source_Code_Review.png`

![Target overview](02_Evidence/M1_Reconnaissance/M1-01_Target_Overview.png)

![Source-code review](02_Evidence/M1_Reconnaissance/M1-02_Source_Code_Review.png)

### Phase 2 — Public path and sitemap review

The tester inspected `robots.txt` and identified the paths `/old/`, `/patient/` and `/staff/`. A sitemap was also identified at `/sitemap.xml`. The paths were treated as leads for manual review, not as proof that the resources were restricted or vulnerable.

**Evidence files**

- `02_Evidence/M1_Reconnaissance/M1-03_Robots_Txt.png`
- `02_Evidence/M1_Reconnaissance/M1-04_Sitemap.png`
- `02_Evidence/M1_Reconnaissance/M1-05_Discovered_Paths.png`

![robots.txt evidence](02_Evidence/M1_Reconnaissance/M1-03_Robots_Txt.png)

![Sitemap evidence](02_Evidence/M1_Reconnaissance/M1-04_Sitemap.png)

### Phase 3 — Authentication assessment

The staff and patient login interfaces were examined. The tester reported that basic authentication testing against the staff login did not identify a successful bypass. SQL injection testing against the patient login produced a database error and was followed by reported successful access to the patient portal.

**Evidence files**

- `02_Evidence/M1_Authentication_SQLi/M1-06_Staff_Login_Review.png`
- `02_Evidence/M1_Authentication_SQLi/M1-07_Patient_Login.png`
- `02_Evidence/M1_Authentication_SQLi/M1-08_SQLi_Error_Redacted.png`
- `02_Evidence/M1_Authentication_SQLi/M1-09_Patient_Portal_Access.png`

![Staff login review](02_Evidence/M1_Authentication_SQLi/M1-06_Staff_Login_Review.png)

![Patient login interface](02_Evidence/M1_Authentication_SQLi/M1-07_Patient_Login.png)

![SQL injection error evidence](02_Evidence/M1_Authentication_SQLi/M1-08_SQLi_Error_Redacted.png)

![Patient portal access evidence](02_Evidence/M1_Authentication_SQLi/M1-09_Patient_Portal_Access.png)

### Phase 4 — Patient report retrieval

Following the reported patient-portal access, the tester downloaded three PDF laboratory reports. The files were encrypted when opened.

**Evidence files**

- `02_Evidence/M1_Patient_Reports/M1-10_Report_01_Retrieval.png`
- `02_Evidence/M1_Patient_Reports/M1-11_Report_02_Retrieval.png`
- `02_Evidence/M1_Patient_Reports/M1-12_Report_03_Retrieval.png`
- `02_Evidence/M1_Patient_Reports/M1-13_Encrypted_PDF_Notice.png`

![Patient report retrieval](02_Evidence/M1_Patient_Reports/M1-10_Report_01_Retrieval.png)

![Encrypted PDF notice](02_Evidence/M1_Patient_Reports/M1-13_Encrypted_PDF_Notice.png)

### Phase 5 — PDF password-recovery assessment

The tester extracted password hashes from the three PDFs using the NetworkWalks PDF Hash Extractor and attempted password recovery with the NetworkWalks password-cracking tool. The first two passwords were reportedly recovered using the supplied wordlist. The third was not recovered with that wordlist; a different John the Ripper wordlist was then used successfully.

**Evidence files**

- `02_Evidence/M2_PDF_Recovery/M2-01_Report_01_Hash_Extraction.png`
- `02_Evidence/M2_PDF_Recovery/M2-02_Report_01_Recovery_Result.png`
- `02_Evidence/M2_PDF_Recovery/M2-03_Report_02_Hash_Extraction.png`
- `02_Evidence/M2_PDF_Recovery/M2-04_Report_02_Recovery_Result.png`
- `02_Evidence/M2_PDF_Recovery/M2-05_Report_03_Initial_Failure.png`
- `02_Evidence/M2_PDF_Recovery/M2-06_Report_03_Alternative_Wordlist.png`
- `02_Evidence/M2_PDF_Recovery/M2-07_Report_03_Recovery_Result.png`

![PDF 1 recovery](02_Evidence/M2_PDF_Recovery/M2-02_Report_01_Recovery_Result.png)

![PDF 2 recovery](02_Evidence/M2_PDF_Recovery/M2-04_Report_02_Recovery_Result.png)

![PDF 3 alternative wordlist](02_Evidence/M2_PDF_Recovery/M2-06_Report_03_Alternative_Wordlist.png)

![PDF 3 recovery](02_Evidence/M2_PDF_Recovery/M2-07_Report_03_Recovery_Result.png)

**Evidence safety:** Screenshots must not reveal recovered passwords, patient names, medical results or other unnecessary sensitive data. Redact screenshots for the report and retain originals securely.

### Phase 6 — Historical database backup analysis

The tester reported that the historical SQL backup was accessible through the `/old/` path. The supplied SQL dump identified a database named `mediroza_hr` and contained `staff` and `shareholders` tables.

**Evidence files**

- `02_Evidence/M3_Database_Exposure/M3-01_Backup_URL_Redacted.png`
- `02_Evidence/M3_Database_Exposure/M3-02_Backup_Metadata.png`
- `02_Evidence/M3_Database_Exposure/M3-03_Staff_Table_Redacted.png`
- `02_Evidence/M3_Database_Exposure/M3-04_Shareholders_Table_Redacted.png`

![Backup URL evidence](02_Evidence/M3_Database_Exposure/M3-01_Backup_URL_Redacted.png)

![Backup metadata](02_Evidence/M3_Database_Exposure/M3-02_Backup_Metadata.png)

![Redacted staff table](02_Evidence/M3_Database_Exposure/M3-03_Staff_Table_Redacted.png)

![Redacted shareholder table](02_Evidence/M3_Database_Exposure/M3-04_Shareholders_Table_Redacted.png)

---

# 3. Findings and Proof of Exploitation

## F-01 — SQL Injection in Patient Authentication

| Attribute | Assessment |
|---|---|
| Severity | **Critical** |
| Affected component | Patient login |
| Category | CWE-89 — Improper Neutralization of Special Elements Used in an SQL Command |
| Reported outcome | Authentication bypass and patient-portal access |
| Evidence directory | `02_Evidence/M1_Authentication_SQLi/` |

### Description

During testing of the patient login interface, the tester submitted SQL injection test input. The application returned a database error containing a reference to PHP's `mysqli_query()` function. This error disclosed implementation details and suggested that user-controlled input was being incorporated into an SQL query in an unsafe manner.

The tester then reported that an authentication test using the username input `admin' --` was accepted and that the patient portal became accessible after a password was submitted. The tester subsequently retrieved the three project-specified patient PDF reports.

The error message is supporting evidence of unsafe query handling, but the strongest proof of impact is the recorded successful authentication and access to the portal. Include the corresponding request/response or screenshots in the evidence package, with secrets and personal data redacted.

### Impact

If exploitable by an unauthorised attacker, this weakness could allow authentication bypass and unauthorised access to patient information. Depending on the application's query structure and database permissions, SQL injection may also expose or alter other records. The actual extent of database access should not be overstated beyond what the evidence demonstrates.

### Evidence insertion

1. `02_Evidence/M1_Authentication_SQLi/M1-08_SQLi_Error_Redacted.png` — database error.
2. `02_Evidence/M1_Authentication_SQLi/M1-09_Patient_Portal_Access.png` — successful portal access.
3. Optional: `02_Evidence/M1_Authentication_SQLi/M1-10_Request_Response_Redacted.txt` — sanitised request/response evidence, if permitted.

![F-01 database error](02_Evidence/M1_Authentication_SQLi/M1-08_SQLi_Error_Redacted.png)

![F-01 patient portal access](02_Evidence/M1_Authentication_SQLi/M1-09_Patient_Portal_Access.png)

### Recommendations and remediation

- Use parameterised queries or prepared statements for every database operation that includes user input.
- Do not construct SQL statements by concatenating untrusted input.
- Review all authentication and data-access queries for the same coding pattern.
- Return generic authentication errors to users and disable detailed database errors in production.
- Apply least-privilege permissions to the database account used by the application.
- Log suspicious authentication activity and monitor for repeated injection attempts.
- Conduct a code review and retest the affected login flow after remediation.

**Implementation guidance:** In PHP, use PDO prepared statements or MySQLi prepared statements with bound parameters. Input validation is useful as an additional control, but it is not a replacement for parameterised queries.

---

## F-02 — Publicly Accessible Historical Database Backup

| Attribute | Assessment |
|---|---|
| Severity | **Critical** |
| Affected resource | `/old/mediroza_db_backup_2019.sql` |
| Category | CWE-538 — Insertion of Sensitive Information into Externally Accessible File or Directory |
| Reported outcome | Access to historical employee and shareholder records |
| Evidence directory | `02_Evidence/M3_Database_Exposure/` |

### Description

During manual review of the paths discovered during reconnaissance, the tester reported that a historical SQL backup was accessible at:

`https://www.medirozahospital.com/old/mediroza_db_backup_2019.sql`

The supplied backup content identified the database as `mediroza_hr`. Its comments identified it as an internal database backup generated by the Mediroza CMS 1.4.2 backup module, with a backup date of 27 August 2019.

The dump contained at least two relevant tables:

- `staff`
- `shareholders`

The `staff` table definition included employee names, job titles, departments, email addresses, telephone numbers, national identification numbers, monthly salaries and joining dates. The supplied dump contained 30 employee records.

The `shareholders` table contained shareholder names, percentage ownership, shares held and share classes. The supplied dump contained 10 shareholder records.

### Exposed information summary

| Data category | Records reported | Sensitive fields |
|---|---:|---|
| Staff | 30 | Names, roles, departments, email, phone, national ID, salary, joining date |
| Shareholders | 10 | Names, ownership percentage, shares held, share class |

The backup is historical. Its contents should not be represented as current employee or ownership information without independent confirmation.

### Salary analysis

Based on the 30 salary values in the supplied SQL dump:

| Metric | Value |
|---|---:|
| Number of staff records | 30 |
| Highest monthly salary | ZAR 160,000 |
| Lowest monthly salary | ZAR 19,000 |
| Total monthly salaries represented | ZAR 2,027,000 |
| Average monthly salary | Approximately ZAR 67,566.67 |

These are calculations from the supplied historical records, not independently verified current payroll figures.

### Shareholder analysis

The supplied records reported the following ownership distribution:

| Shareholder | Share percentage | Shares held | Share class |
|---|---:|---:|---|
| Dr. Rajesh Naidoo | 18% | 180,000 | Ordinary |
| Cedar Health Holdings (Pty) Ltd | 15% | 150,000 | Ordinary |
| Dr. Johan van der Merwe | 12% | 120,000 | Ordinary |
| Reddy Family Trust | 11% | 110,000 | Ordinary |
| Thabo Molefe | 10% | 100,000 | Ordinary |
| Sarah Botha | 9% | 90,000 | Ordinary |
| Dr. Ahmed Kara | 8% | 80,000 | Preferential |
| Naledi Zulu | 7% | 70,000 | Ordinary |
| Michael Roberts | 6% | 60,000 | Ordinary |
| Dr. Vikram Chetty | 4% | 40,000 | Preferential |
| **Total** | **100%** | **1,000,000** | — |

### Impact

Public exposure of this backup could result in:

- Disclosure of employee personal and identity information.
- Targeted phishing, impersonation or social-engineering attempts.
- Exposure of internal compensation information.
- Disclosure of organisational ownership information.
- Increased risk of fraud and business email compromise.
- Legal, regulatory and reputational consequences if personal information is exposed.

### Evidence insertion

1. `02_Evidence/M3_Database_Exposure/M3-01_Backup_URL_Redacted.png` — accessible backup resource.
2. `02_Evidence/M3_Database_Exposure/M3-02_Backup_Metadata.png` — database name, backup date and metadata.
3. `02_Evidence/M3_Database_Exposure/M3-03_Staff_Table_Redacted.png` — staff table structure and redacted sample.
4. `02_Evidence/M3_Database_Exposure/M3-04_Shareholders_Table_Redacted.png` — shareholder table structure and redacted sample.

![F-02 backup resource](02_Evidence/M3_Database_Exposure/M3-01_Backup_URL_Redacted.png)

![F-02 backup metadata](02_Evidence/M3_Database_Exposure/M3-02_Backup_Metadata.png)

![F-02 staff records](02_Evidence/M3_Database_Exposure/M3-03_Staff_Table_Redacted.png)

![F-02 shareholder records](02_Evidence/M3_Database_Exposure/M3-04_Shareholders_Table_Redacted.png)

### Recommendations and remediation

- Remove database backups from the public web root immediately.
- Store backups outside directories served by the web server, preferably in access-controlled backup storage.
- Deny web access to backup, dump, archive and configuration files at the web-server level.
- Disable directory listing.
- Review the server for other exposed backup files and legacy copies.
- Encrypt backups at rest and restrict access to authorised administrators.
- Apply a documented backup retention and secure disposal policy.
- Review database credentials and rotate them if there is any possibility they were exposed.
- Assess the exposure and follow applicable privacy, legal and incident-notification requirements.
- Verify remediation by confirming that the backup is no longer retrievable by an unauthenticated external user.

**Important:** `robots.txt` is not an access-control mechanism. Removing a path from `robots.txt` does not secure the resource. Enforce access restrictions on the server and application.

---

## F-03 — Weak or Recoverable Password Protection on Patient PDF Reports

| Attribute | Assessment |
|---|---|
| Severity | **High — provisional** |
| Affected resources | Three patient PDF reports |
| Category | CWE-521 — Weak Password Requirements (provisional mapping) |
| Reported outcome | Passwords recovered for all three PDFs |
| Evidence directory | `02_Evidence/M2_PDF_Recovery/` |

### Description

After retrieving the three PDF laboratory reports from the patient portal, the tester found that each document required a password to open.

The tester used the NetworkWalks PDF Hash Extractor to obtain password hashes and submitted them to the NetworkWalks password-cracking tool.

- **Report 1:** The password was reportedly recovered using the supplied NetworkWalks wordlist.
- **Report 2:** The password was reportedly recovered using the supplied NetworkWalks wordlist.
- **Report 3:** The initial attempt using the supplied wordlist failed. The tester then used an alternative John the Ripper wordlist and reported successful password recovery.

This demonstrates that the passwords protecting the tested documents were recoverable using the wordlists and tools described. It does not, by itself, demonstrate that the underlying PDF encryption algorithm was broken.

### Impact

An attacker who obtains copies of similarly protected documents may be able to recover weak or predictable passwords and access confidential patient information. This creates a risk to patient privacy and the confidentiality of medical records.

The impact is increased by the reported SQL injection finding, which provided the initial route to obtaining the documents.

### Evidence insertion

1. `02_Evidence/M2_PDF_Recovery/M2-01_Report_01_Hash_Extraction.png`
2. `02_Evidence/M2_PDF_Recovery/M2-02_Report_01_Recovery_Result.png`
3. `02_Evidence/M2_PDF_Recovery/M2-03_Report_02_Hash_Extraction.png`
4. `02_Evidence/M2_PDF_Recovery/M2-04_Report_02_Recovery_Result.png`
5. `02_Evidence/M2_PDF_Recovery/M2-05_Report_03_Initial_Failure.png`
6. `02_Evidence/M2_PDF_Recovery/M2-06_Report_03_Alternative_Wordlist.png`
7. `02_Evidence/M2_PDF_Recovery/M2-07_Report_03_Recovery_Result.png`

![PDF 1 recovery](02_Evidence/M2_PDF_Recovery/M2-02_Report_01_Recovery_Result.png)

![PDF 2 recovery](02_Evidence/M2_PDF_Recovery/M2-04_Report_02_Recovery_Result.png)

![PDF 3 alternative wordlist](02_Evidence/M2_PDF_Recovery/M2-06_Report_03_Alternative_Wordlist.png)

![PDF 3 recovery](02_Evidence/M2_PDF_Recovery/M2-07_Report_03_Recovery_Result.png)

**Evidence safety:** Do not reveal recovered passwords, patient names, medical results or other unnecessary sensitive data in screenshots. Redact screenshots for the report and retain originals securely.

### Recommendations and remediation

- Use strong, randomly generated passwords where PDF password protection is required.
- Avoid predictable passwords based on names, dates, telephone numbers or other personal details.
- Use modern encryption supported by the organisation's document-management solution.
- Do not rely on a PDF password as the only access control for patient records.
- Prefer authenticated portals with role-based access controls and auditable document access.
- Protect documents in storage and in transit.
- Review how passwords are generated, distributed, stored and changed.
- Reassess the protection of existing sensitive documents.

**Qualification:** The exact PDF encryption settings and password strength were not supplied. Confirm these details before making claims about a specific encryption algorithm or configuration.

---

## F-04 — Disclosure of Application Paths Through Public Discovery Resources

| Attribute | Assessment |
|---|---|
| Severity | **Low / Informational** |
| Affected resources | `robots.txt`, sitemap and disclosed paths |
| Category | CWE-200 — Exposure of Sensitive Information to an Unauthorized Actor (context-dependent) |
| Reported outcome | Discovery of `/old/`, `/patient/` and `/staff/` |
| Evidence directory | `02_Evidence/M1_Reconnaissance/` |

### Description

The tester inspected the publicly accessible `robots.txt` file and identified references to `/old/`, `/patient/` and `/staff/`. A sitemap was also identified at `/sitemap.xml`.

These resources provided useful information about the application's publicly discoverable paths and helped guide manual assessment.

The disclosure of a path in `robots.txt` is not automatically a vulnerability. Search-engine crawler directives do not prevent users or attackers from requesting a URL. The more serious issue in this assessment was that the historical backup was reportedly accessible without adequate access control; that issue is documented separately as F-02.

### Impact

Path disclosure may reduce the effort required to discover application components and legacy resources. The actual security impact depends on whether the discovered resources are improperly protected.

### Evidence insertion

- `02_Evidence/M1_Reconnaissance/M1-03_Robots_Txt.png`
- `02_Evidence/M1_Reconnaissance/M1-04_Sitemap.png`
- `02_Evidence/M1_Reconnaissance/M1-05_Discovered_Paths.png`

![F-04 robots.txt](02_Evidence/M1_Reconnaissance/M1-03_Robots_Txt.png)

![F-04 sitemap](02_Evidence/M1_Reconnaissance/M1-04_Sitemap.png)

### Recommendations and remediation

- Do not rely on crawler directives to protect confidential resources.
- Require server-side authentication and authorisation for restricted paths.
- Remove obsolete application directories and unnecessary public resources.
- Review sitemap entries to ensure that only intended public pages are listed.
- Restrict access to legacy and backup resources independently of whether their paths are disclosed.

---

# 4. Risk Rating and Prioritisation

The following ratings are provisional and should be confirmed against the organisation's risk-rating methodology and the evidence collected.

| Finding | Severity | Recommended priority | Rationale |
|---|---|---|---|
| F-01 — Patient-login SQL injection | Critical | Immediate | Reported authentication bypass and access to patient reports |
| F-02 — Exposed database backup | Critical | Immediate | Reported unauthenticated exposure of employee and shareholder records |
| F-03 — Recoverable PDF passwords | High | Urgent | Passwords reportedly recovered for all three tested reports |
| F-04 — Disclosed paths | Low / Informational | Planned | Discovery information alone does not prove unauthorised access |

## 4.1 Overall risk

The combination of the reported SQL injection and exposed database backup creates a critical confidentiality concern. The PDF password-recovery results further demonstrate that document-level password protection did not prevent access once the files had been obtained.

The organisation should prioritise containment of the exposed backup and remediation of the patient authentication weakness. A retest should then verify that the vulnerabilities have been corrected.

---

# 5. Recommendations and Remediation Plan

| Priority | Action | Related finding |
|---|---|---|
| Immediate | Correct SQL injection using parameterised queries | F-01 |
| Immediate | Remove public database backups and block web access to backup files | F-02 |
| Immediate | Review exposure and handling of affected personal information | F-02 |
| Urgent | Strengthen patient document access controls and password practices | F-03 |
| Urgent | Review access permissions and database privileges | F-01, F-02 |
| Planned | Review public discovery resources and remove obsolete paths | F-04 |
| Planned | Implement recurring vulnerability assessments and secure development reviews | All |
| Planned | Improve application logging, alerting and incident response | F-01, F-02 |

## 5.1 Verification testing

After remediation, conduct an authorised retest to confirm:

- The patient login no longer accepts SQL injection input.
- Authentication decisions are performed safely using parameterised database queries.
- The historical SQL backup cannot be retrieved by an unauthenticated external user.
- Other backup and configuration files are not publicly accessible.
- Patient reports are available only to appropriately authenticated and authorised users.
- Document protection and access controls meet the organisation's security requirements.
- Relevant security events are logged and monitored.

---

# 6. Conclusion

The reported black-box assessment of Mediroza General Hospital identified significant weaknesses affecting application authentication, sensitive file exposure and patient document protection.

The most serious reported issue was SQL injection in the patient login mechanism, which reportedly enabled access to the patient portal and retrieval of three encrypted laboratory reports. The assessment also identified a publicly accessible historical database backup containing 30 employee records and 10 shareholder records, including salary and personal information.

The tester reported successful password recovery for all three PDF reports. The results indicate that the password protection applied to the tested documents did not provide sufficient resistance to the recovery techniques used.

The organisation should treat the SQL injection and exposed database backup as immediate remediation priorities. A formal retest should be conducted after corrective actions to verify that the issues have been resolved.

**Final assessment:** Critical confidentiality risk based on the reported findings, pending validation of the submitted evidence.

---

# 7. Appendix A — Evidence Register


| Evidence ID | Evidence description | Suggested file path | Status |
|---|---|---|---|
| ES-01 | Redacted findings overview | `02_Evidence/M4_Reporting/ES-01_Findings_Overview.png` | captured |
| M1-01 | Target overview | `02_Evidence/M1_Reconnaissance/M1-01_Target_Overview.png` |captured |
| M1-02 | Source-code review | `02_Evidence/M1_Reconnaissance/M1-02_Source_Code_Review.png` | captured |
| M1-03 | robots.txt | `02_Evidence/M1_Reconnaissance/M1-03_Robots_Txt.png` | captured |
| M1-04 | Sitemap | `02_Evidence/M1_Reconnaissance/M1-04_Sitemap.png` | captured |
| M1-05 | Discovered paths | `02_Evidence/M1_Reconnaissance/M1-05_Discovered_Paths.png` |captured|
| M1-06 | Staff login review | `02_Evidence/M1_Authentication_SQLi/M1-06_Staff_Login_Review.png` | captured |
| M1-07 | Patient login | `02_Evidence/M1_Authentication_SQLi/M1-07_Patient_Login.png` | captured |
| M1-08 | SQL injection error | `02_Evidence/M1_Authentication_SQLi/M1-08_SQLi_Error_Redacted.png` | captured |
| M1-09 | Patient portal access | `02_Evidence/M1_Authentication_SQLi/M1-09_Patient_Portal_Access.png` | captured |
| M1-10 | Patient report 1 retrieval | `02_Evidence/M1_Patient_Reports/M1-10_Report_01_Retrieval.png` | captured |
| M1-11 | Patient report 2 retrieval | `02_Evidence/M1_Patient_Reports/M1-11_Report_02_Retrieval.png` | captured |
| M1-12 | Patient report 3 retrieval | `02_Evidence/M1_Patient_Reports/M1-12_Report_03_Retrieval.png` | captured |
| M1-13 | Encrypted PDF notice | `02_Evidence/M1_Patient_Reports/M1-13_Encrypted_PDF_Notice.png` | captured |
| M2-01 | Report 1 hash extraction | `02_Evidence/M2_PDF_Recovery/M2-01_Report_01_Hash_Extraction.png` | captured |
| M2-02 | Report 1 recovery | `02_Evidence/M2_PDF_Recovery/M2-02_Report_01_Recovery_Result.png` | captured |
| M2-03 | Report 2 hash extraction | `02_Evidence/M2_PDF_Recovery/M2-03_Report_02_Hash_Extraction.png` | captured |
| M2-04 | Report 2 recovery | `02_Evidence/M2_PDF_Recovery/M2-04_Report_02_Recovery_Result.png` | captured |
| M2-05 | Report 3 initial failure | `02_Evidence/M2_PDF_Recovery/M2-05_Report_03_Initial_Failure.png` | captured |
| M2-06 | Alternative wordlist | `02_Evidence/M2_PDF_Recovery/M2-06_Report_03_Alternative_Wordlist.png` | captured |
| M2-07 | Report 3 recovery | `02_Evidence/M2_PDF_Recovery/M2-07_Report_03_Recovery_Result.png` | captured |
| M3-01 | Backup URL | `02_Evidence/M3_Database_Exposure/M3-01_Backup_URL_Redacted.png` | captured |
| M3-02 | Backup metadata | `02_Evidence/M3_Database_Exposure/M3-02_Backup_Metadata.png` | captured |
| M3-03 | Staff table | `02_Evidence/M3_Database_Exposure/M3-03_Staff_Table_Redacted.png` | captured |
| M3-04 | Shareholder table | `02_Evidence/M3_Database_Exposure/M3-04_Shareholders_Table_Redacted.png` | captured |
| M4-01 | SQL injection retest | `02_Evidence/M4_Reporting/M4-01_SQLi_Retest.png` | captured |
| M4-02 | Backup access blocked | `02_Evidence/M4_Reporting/M4-02_Backup_Access_Blocked.png` | captured |
| M4-03 | Document access retest | `02_Evidence/M4_Reporting/M4-03_Authorised_Document_Access.png` | captured |

---

---

**Prepared for:** NetworkWalks — Week 4 Penetration Testing Project  
**Client:** Mediroza General Hospital  
**Classification:** CONFIDENTIAL — AUTHORISED PERSONNEL ONLY

**END OF REPORT**
