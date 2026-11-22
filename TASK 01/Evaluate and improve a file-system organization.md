
# Task 1.1 – Evaluate the Proposal 

## Proposed System
One flat directory containing files of 12,000 students, with each file named only using the student's name.

## Weaknesses of the Proposed System

1. **Name Conflicts:** Different students may have the same name, resulting in duplicate filenames and file overwriting.

2. **Slow Searching:** Searching for a particular file among 12,000 files increases directory lookup time.

3. **File Growth:** Contiguous allocation may cause external fragmentation when student files grow.

4. **Lack of Security:** Without access control, students may access or modify other students' files.

5. **Backup and Recovery:** A single shared directory creates a single point of failure, making recovery difficult.

6. **File Sharing:** Managing group project files becomes difficult without proper sharing permissions.

---

# Task 1.2 – Improve the Design 

## Proposed Improved File Organisation

A hierarchical directory structure with role-based access control is recommended.

## Directory Structure

```text
College_Server/
│
├── Students/
│   ├── Department_CSE/
│   │   ├── REG001/
│   │   │   ├── Assignments/
│   │   │   ├── Projects/
│   │   │   └── Documents/
│   │   └── REG002/
│   │
│   ├── Department_IT/
│   │   └── REG003/
│   │
│   └── Department_ECE/
│       └── REG004/
│
├── Faculty/
│   ├── REG001/
│   └── REG002/
│
├── Shared_Projects/
│   ├── Project_01/
│   └── Project_02/
│
└── Backup/
    ├── Daily/
    └── Weekly/
```

## Design Improvements

| Component | Proposed Solution |
|---|---|
| Directory structure | Hierarchical tree structure |
| File naming | Register number and file type |
| File attributes | Owner, size, date and permissions |
| Access control | Role-Based Access Control (RBAC) |
| File allocation | Indexed allocation |
| Backup | Daily incremental and weekly full backup |
| File sharing | Shared project folders with permissions |

## Naming Convention

**Format:**

`RegisterNumber_FileName_Date.Extension`

**Example:**

`REG001_Assignment1_2026.pdf`

## Access Control Permissions

- **Students:** Read and write access to their own folders.
- **Faculty:** Read access to assigned student folders.
- **Administrators:** Complete access to all folders.
- **Project members:** Access only to their assigned shared project folders.

---

# Task 1.3 – Design Defence 

The hierarchical directory structure separates student files based on department and register number. Unique filenames prevent conflicts, while role-based permissions protect privacy. Shared project folders allow controlled collaboration. Regular backups and recovery mechanisms protect files against accidental deletion and system failure. Therefore, the proposed design provides better security, organisation, scalability and reliability.

---

# Alternative Concept and Complexity Analysis

If cloud storage services such as Google Drive or OneDrive are used instead of a shared college server, each student can access files from different locations.

## Shared Server vs Cloud Storage

| Parameter | Shared Server | Cloud Storage |
|---|---|---|
| File organisation | College-managed directories | Cloud folders |
| Search | Depends on directory structure | Metadata-based search |
| Storage | College server capacity | Scalable cloud storage |
| Privacy | College controls permissions | Provider and college share responsibility |
| Availability | Depends on server uptime | Generally high availability |
| Backup | Managed by college | Cloud versioning and backup options |
| Cost | Hardware and maintenance | Subscription and usage charges |

## Time Complexity

- **Directory search:** `O(n)` in a simple linear directory.
- **Hierarchical search:** Depends on the number of directory entries at each level.
- **Cloud metadata search:** Typically uses indexed lookup, approximately `O(log n)` for suitable index structures.

## Conclusion

Cloud storage provides better accessibility and scalability, while a college-managed server provides greater direct control over infrastructure and data. A hybrid model is recommended for important academic files.
