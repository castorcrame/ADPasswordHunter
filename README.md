![ADPasswordHunter Logo](logo.jpeg)

# How to use the tool?

To launch ADPasswordHunter, open an elevated PowerShell command prompt on a domain controller. Navigate to the "ADPasswordHunter" folder, then execute the "ADPasswordHunter.ps1" file located in the "Scripts" folder.
```powershell
cd .\ADPasswordHunter
.\Scripts\ADPasswordHunter.ps1
```
> [!NOTE]
> To run ADPasswordHunter, you must have sufficiently elevated privileges to extract the Active Directory database.

# Why this tool?

This tool was designed to evaluate the complexity of passwords used by users in a domain. As passwords have become increasingly predictable, it is essential to determine if any users are employing passwords that are considered too weak.

The power of ADPasswordHunter is found in its capacity to define complexity levels by providing it with custom word dictionaries. You can generate a dictionary of prohibited words, such as the company name, the name of a physical location, and more. ADPasswordHunter will then inform you if any users are using passwords from this dictionary.

The advantage is that it allows the IT department to eliminate all passwords deemed too weak.

# How does it work?

The Active Directory database is stored in a file called **NTDS.dit**. This file is located on every domain controller and is created when a Windows server is promoted to a domain controller. It contains several tables, some of which store details about objects like computers and users. One of these details is the password hash of each object. Currently, this hash is generated by the **NTHash** algorithm.

To analyze this information, you need to extract the data from the file, retrieve the hashes, and compare them with your local dictionaries.

However, this process is not simple. The file is constantly used by the system, which means it's locked. Additionally, the content of the **NTDS.dit** file is encrypted with a boot key that is unique to each domain controller. To work around these issues, ADPasswordHunter creates a snapshot of the system to locally copy the **NTDS.dit** file. It also retrieves the boot key stored in the HKEY_LOCAL_MACHINE\SYSTEM registry **hive**.

Finally, using the PowerShell module **DSInternals**, ADPasswordHunter scans the decrypted file, extracts the user account hashes, and compares them with the dictionaries.

> [!NOTE] 
> **DSInternals** is a framework developed by Michael Grafnetter, designed to perform AD security audits. It is part of the official PowerShell gallery.


# Why PowerShell?

PowerShell being natively present on a domain controller, I chose to focus on this language for developing ADPasswordHunter. Despite its main drawback, which is execution time, PowerShell remains a compelling choice for auditing Active Directory, especially when used in conjunction with modules like DSInternals.

> [!NOTE] 
> I’m a student who codes for fun, I haven’t taken any developer training, so the code isn’t perfect... but it works!

# What You Can Do

- **Test AD Passwords**: Perform an audit of the passwords used by your domain’s users. This feature extracts the hashes from the Active Directory database and compares them against local dictionaries.

- **Test a Password**: Check if a specific password exists in a local dictionary.

- **Generate Combinations**: Create thousands of combinations from one or more words to build complex dictionaries.
 
- **Calculate Hashes**: Compute the hashes of raw or complex dictionaries for conducting Active Directory audits.

- **Verify Password Change**: Check if one or more users have recently changed their passwords.

- **Check Prerequisites**: Ensure that you have all the necessary prerequisites to run ADPasswordHunter.

  

# Combination Generation

To determine if a specific word is being used as a password within your domain, it's crucial to explore all possible combinations of that word to identify any occurrences. Instead of manually performing this task, **ADPasswordHunter** offers a feature that automatically generates word combinations. These combinations follow a pattern frequently observed in password breaches:

**{word} {digit or number} {special character}**

For example, let’s take the word "castorcrame". **ADPasswordHunter** can generate more than 62 800 combinations, including: *C@storcrame2024**, *castorcram€10*, *CASTORCRAME2003*, and more.

Here’s a detailed explanation of the process used to generate these combinations from the word *"castorcrame"*:

1.  **Character Substitution**: The first step involves replacing the characters *"a"* with *"@"*, and *"e"* with *"€"* in the original word to create various variants.

2.  **Case Variation**: For each variant generated in the previous step, **ADPasswordHunter** produces three new combinations: one in lowercase, one in uppercase, and the last with only the first letter capitalized.

3.  **Addition of Numbers**: Next, numeric sequences are added to each previous combination:

- from 00 to 09

- from 10 to 99

- from 1900 to 2050

4.  **Addition of Special Characters**: Finally, each combination is enhanced by adding a special character selected from the following list: *"!", "@", "#", "%", "-", "_", "+", "=", "*"*.

To estimate the total number of combinations generated for a given word, the following formula can be used:

$2^{n_a + n_e} * 3 * 262 * 10 = 2^{n_a + n_e} * 7860$

$n_a$ = Number of occurrences of the character *"a"*
$n_e$ = Number of occurrences of the character *"e"*
  

The graph below illustrates the evolution of the number of combinations based on the addition of $n_a$ and $n_e$.

  

** GRAPH **

  

Additionally, you will find a table presenting the number of words used as input and the number of combinations obtained during various laboratory tests.

  

| Words | Combinations |
| --- | --- |
| 210 | +7 100 000 |
| 310 | +10 100 000 |
| 1130 | +80 500 000 |
