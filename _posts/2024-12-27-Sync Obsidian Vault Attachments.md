---
layout: post
date: 2024-12-27
title: "Sync Obsidian Attachments When Copying Vault Folders"
categories: [Guide]
tags: [Obsidian]
img_path: /assets/copy-vault-attachments/
render_with_liquid: false
---

When copying over only select folders from one Obsidian vault to another the attachments do not get copied over with the notes. Going through each note and manually copying and pasting the missing attachments would be tedious. Therefore, I am writing this blog post to go with a small Python script in order to automate this process. I provide a breakdown of the regular expression used to identify attachments at the end of the blog post.
## 1. Create New Vault or Select Destination Vault 
The first step is to create a new Obsidian vault that is going to house our old vault's notes and attachments. If you are working with a vault that has already been created then you can skip this step.

![Creating New Obsidian Vault](Create New Vault.png)
_New Vault Creation_
## 2. Copy Obsidian Vault Folders and Notes
If you haven't already done so, the next step would be to copy the original vault's file contents into the new vault's storage. To find where you vault is located right click any of the folders within the vault and select *Show in system explorer*. Then drag and drop the desired folders.

![Copying Vault Contents](Drag and Drop Vault Contents.png)
_Copying Vault Contents_

After the files and folders are copied over we can see that our new vault contains all of the markdown notes, but all of the attachments are not found.

![Obsidian Note Missing Attachments](Note Missing Images.png)
_Note Missing Attachments_
## 3. Update New Vault Default Attachments Directory *(OPTIONAL)*
This step is not required, as you can store all of the attachments in the root folder of your vault; However, I prefer to store my attachments in a newly created directory for management purposes. To create a new attachments directory:
1. Create New Folder
2. Setings > Files and Links > Default Location for New Attachments > Set to "In the folder specified below" 
3. Set Attachment Folder Path to the Folder Created Above

![Update Attachments Directory](Update Default Attachments Directory.png)
_Setting Obsidian Default Directory for Attachments_
## 4. Run My Python Script
Grab my [vaultAttachSync python script](https://github.com/est15/Python-Scripts/tree/main/vaultAttachSync) stored on my GitHub. The script uses only standard Python3 libraries, so no need to install anything additional. Run the script from a command line (the script works on both Windows and Linux):
```powershell
python vaultAttachSync.py -n "C:\New\Vault\Path" --na "Attachments" -o "C:\Old\Vault\Path" --oa "Attachments"
```

The script will recursively go through the New Vault's files and folders to find all references to missing images. We are starting with the new vault first in order to only get relevant attachments. The script will notifiy you if any exceptions or failures to copy occur. Note, if you re-run the script multiple times you will not have to worry about duplicates. 

![Successful Command Execution](Successful Command Execution.png)
_vaultAttachSync Execution Output_

Checking the same file with missing attachments seen in Step #2 we can see that the file now has the attachments:

![Note No Longer Missing Attachments](Note with Attachments now.png){: width="400", height="500"}
_Note with Attachments Now_

### Explaining The Magic
The script utilizes the following regular expression in order to find all references to images in the Obsidian file:
```python
images = re.findall(r"\[\[(?:.*?/)(.*?\.(?:png|jpg|gif))(?:\|.*?)]]", codecs.decode(vaultFile.read_bytes())) # Return List of Images
```

Currently, the script is only programmed to find PNG/JPG/GIF attachments, but adding additional attachment cases would not be difficult. 

#### RegEx Breakdown
Let's break the RegEx down:
- `\[\[...]]`: Obsidian uses the `![[<attachment>]]` synatx for inserting an attachment. This RegEx is looking for text enclosed in the brackets.
- `(?:.*?/)`: Specifies a non-capture group, telling the interpreter to find any pattern that matches `[[<anything>/]]` but not to include it in the output. Additionally, this pattern isn't necessary, meaning that if an attachment doesn't contain this pattern, the rest of the RegEx will work.

> This RegEx was added because some of my Attachment Names contained the vault's attachments directory name. I believe this occurred when I imported my Notion notes into Obsidian.
{: .prompt-tip }

- `(.*?\.(?:png|jpg|gif)`: Specifies a capture-group to look for any text that matches `[[<anything>/<anything>.(png/jpg/gif)]]` and a non-capture group to find alternative extension types but to not include this into the outputted list.
- `(?:\|.*?)`: Another non-capture group looking for attachments that contain `|` after the png extension.

> This RegEx was added because some of my Attachment Names contained alternative names using `|`. Again, I believe this occurred when I imported my Notion notes into Obsidian.
{: .prompt-tip }

- `codecs.decode(vaultFile.read_bytes())`: Previously I used `vaultFile.read_text()` however, I ran into some Unicode decoding errors. Getting the file's raw bytes using `read_bytes()` then decoding them into UTF-8 using `codecs.decode()` stopped this issue from occurring. 

> If you run into any UTF-8 decoding errors add the following additional argument: `codecs.decode(vaultFile.read_bytes(), encoding="latin-1")`.
{: .prompt-info }

#### Supporting Additional Attachments
I haven't tested any additional attachment types other than those outlined in the RegEx. However, adding additional support should be as simple as adding `|<extension>` to the current RegEx:
```python
images = re.findall(r"\[\[(?:.*?/)(.*?\.(?:png|jpg|gif|<EXT HERE>))(?:\|.*?)]]", codecs.decode(vaultFile.read_bytes())) # Return List of Images
```
