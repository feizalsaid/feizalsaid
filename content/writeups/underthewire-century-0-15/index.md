# Under The Wire: Century Levels 0 to 15 Walkthrough

Under The Wire is a highly regarded Windows-centric wargame platform. The **Century** series serves as an exceptional introductory playground for getting comfortable with the PowerShell command-line environment, pipe-lining mechanics, object collection manipulation, and active system querying. 

In PowerShell, unlike traditional string-bound shells, **everything is an object**. This writeup documents the core methodologies and cmdlet structures required to solve Levels 0 through 15.

---

## Host Connection Details
* **Host:** `century.underthewire.tech`
* **Port:** `22`
* **Note:** *All flag strings/passwords are case-insensitive and evaluated as lowercase.*

---

## Century 0 → 1
**Objective:** Establish a remote terminal shell connection onto the game environment.
* **Methodology:** Retrieve the current session credentials from the `#StartHere` channel inside the official game Slack space, then execute an outbound connection via an SSH client.

```powershell
# Connecting via standard client terminal
ssh century1@century.underthewire.tech -p 22
```
* **Target Prompt Check:** `PS C:\Users\Century1\desktop>`

---

## Century 1 → 2
**Objective:** Discover the exact engine build version of PowerShell running locally on the instance.
* **Methodology:** Query the built-in configuration environmental variable block `$PSVersionTable`. Do not query the explicit binary execution path version wrapper; target the full operational core object context table property.

```powershell
$PSVersionTable
# Alternatively, to extract the version property explicitly:
$PSVersionTable.PSVersion
```

---

## Century 2 → 3
**Objective:** Uncover the name of the system-native cmdlet used to simulate `wget`-style web transfer actions, combined directly with the name of the file visible on the desktop.
* **Methodology:** Use `Get-ChildItem` (the PowerShell object equivalent of the traditional command prompt `dir`) to inspect files, then concatenate the web cmdlet naming pattern to formulate the target credentials.

```powershell
Get-ChildItem
# Native web-transfer alternative is Invoke-WebRequest
```

---

## Century 3 → 4
**Objective:** Count the exact number of flat files residing within the Desktop workspace directory.
* **Methodology:** Filter the object outputs using explicit file flag properties, and map them to count properties using pipeline evaluation components.

```powershell
(Get-ChildItem -File | Measure-Object).Count
```

---

## Century 4 → 5
**Objective:** Identify the specific inner filename nested deep inside an object workspace that contains whitespace spaces as part of its label structure.
* **Methodology:** Leverage structural directory recursion options to trace names deep across sub-folders safely without shifting working contexts out of the home tree.

```powershell
Get-ChildItem -File -Recurse
```

---

## Century 5 → 6
**Objective:** Extract the short name identifier of the Active Directory domain hosting the machine environment, concatenated with the desktop filename.
* **Methodology:** Execute target configuration checks using administrative AD domain inspection hooks, then parse out the specific attribute keys needed.

```powershell
(Get-ADDomain -Current LocalComputer).Name
Get-ChildItem
```

---

## Century 6 → 7
**Objective:** Calculate the total number of subfolders/directories configured on the Desktop directory path.
* **Methodology:** Use parenthetical syntax sorting around native directory listings to fetch explicit counts cleanly.

```powershell
(Get-ChildItem -Directory).Count
```

---

## Century 7 → 8
**Objective:** Locate a specific password string hidden within a `readme` asset inside any of the default user context folders (`Contacts`, `Desktop`, `Documents`, `Downloads`, etc.).
* **Methodology:** Run a safe recursive lookup from the profile root space while suppressing access tracking errors. Use the native text retrieval command `Get-Content` (aliased as `cat`) to process strings.

```powershell
Get-ChildItem -Path ~ -Recurse -Filter "*readme*" -ErrorAction SilentlyContinue
Get-Content .\Downloads\readme.txt
```

---

## Century 8 → 9
**Objective:** Calculate the total count of completely unique entries populated inside the flat file on the Desktop workspace.
* **Methodology:** Feed content objects directly into unique array selectors, then count the filtered array output depth.

```powershell
(Get-Content .\Desktop\file.txt | Select-Object -Unique).Count
```

---

## Century 9 → 10
**Objective:** Isolate the exact 161st individual word entry recorded in the continuous spaces-delimited text file on the Desktop.
* **Methodology:** Read file elements and partition the block by matching against structural whitespace regex operators (`\s`). Access indices using the standard array syntax formula (`n-1`).

```powershell
# Extracting array index 160 to achieve the 161st word positioning
(Get-Content .\Word_File.txt -split "\s")[160]
```

---

## Century 10 → 11
**Objective:** Combine the 10th and 8th words extracted from the Windows Update service description string with the desktop file label.
* **Methodology:** Query systemic Windows management services to hunt for background configurations. Use properties extraction combined with indexing tools to map specific words cleanly.

```powershell
$target = (Get-CimInstance -ClassName Win32_Service -Filter "Name='wuauserv'").Description
($target -split " ")[9] + ($target -split " ")[7]
Get-ChildItem
```

---

## Century 11 → 12
**Objective:** Uncover the name of a hidden asset masked within the core directory profile paths of the user context.
* **Methodology:** Run structural asset trace algorithms while explicitly demanding visibility over hidden properties, screening out permission errors along the way.

```powershell
Get-ChildItem -Path ~ -Recurse -Hidden -ErrorAction SilentlyContinue
```

---

## Century 12 → 13
**Objective:** Retrieve the formal operational description text assigned to the active Domain Controller in the domain, combined with the desktop asset filename.
* **Methodology:** Query domain controllers through the directory framework tools, and explicitly ask for extended properties mapping fields like `Description`.

```powershell
Get-ADDomainController
Get-ADComputer -Identity "UTW" -Properties Description
Get-ChildItem
```

---

## Century 13 → 14
**Objective:** Calculate the comprehensive count of individual words tracked inside the file saved on the Desktop.
* **Methodology:** Use content splitters to break text items into distinct array chunks across standard tracking pipelines, then query the structural element counts.

```powershell
(Get-Content .\file.txt -split " ").Count
```

---

## Century 14 → 15
**Objective:** Discover how many explicit times the target phrase pattern `"polo"` appears standalone inside the large text document.
* **Methodology:** Load textual resources and pipe the resulting stream filters into regex search utilities utilizing exact word boundary definitions (`\b`), counting the output matches.

```powershell
(Get-Content .\file.txt | Select-String -Pattern "\bpolo\b" -AllMatches).Matches.Count
```
