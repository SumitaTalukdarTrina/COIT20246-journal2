# Week 8 Journal

**Student Name:** Sumita Talukdar Trina

**Topics:** Cloud Computing (tutorial) and Attacks and Vulnerabilities (lecture)

---

## Task 1 - Knowledge Test

![Knowledge Test 08 result](images/KT_8.png)

I did the Week 8 Knowledge Test on Attacks and Vulnerabilities (Information Security Protections) and got **10 out of 10 (100%)**. I got all 5 questions right in about 3 minutes.

I used certainty level 1 for all 5 questions again. The feedback said I was under-confident, and the CBM bonus was -20%, so my accuracy + bonus was only 80%. This is the fifth week in a row I have used C=1 and lost marks even when I got everything right. For Week 9 I am going to use C=2 for every question I can explain, no exceptions.

---

## Task 2 - Login to Microsoft Learn on Demand

I registered at msle.learnondemand.net using the training key provided on Moodle and my @cqumail.com address to create a Skillable account. After accepting the EULA, I logged back in using Sign In and Skillable Account, then opened the COIT20246 class to access the Azure Fundamentals lab activities.

![Lab login](images/loginlab.png)

---

## Task 3 - Create an Azure Resource

I completed the lab for creating Azure resources. I created a resource group called **rg-gp-static-website** in East US, then created a storage account called **stgpstaticsite65482021** inside it. The storage account uses Azure Blob Storage with Standard performance and Locally-redundant storage (LRS).

![Resource group created](images/resourcegrpcreated.png)

![Storage account created](images/Storageaccountcreated.png)

The resources created and what they are for:

| Resource | Type | What it is for |
|---|---|---|
| rg-gp-static-website | Resource group | A container that holds all the related resources together. It makes it easy to manage and delete them all at once when finished. |
| stgpstaticsite65482021 | Storage account (StorageV2) | Holds the blob containers and files. Azure Blob Storage can serve static HTML and CSS files directly to browsers, so no web server is needed. |
| $web (container, created automatically) | Blob container | The special container Azure creates when static website hosting is enabled. Files uploaded here are served to anyone who visits the public URL. |

I also enabled **static website hosting** on the storage account and set the index document to `index.html` and the error document to `404.html`. Azure then gave me a public primary endpoint URL: `https://stgpstaticsite65482021.z13.web.core.windows.net/`.

![Static website hosting enabled](images/Enable_static_website_hosting.png)

The thing I found most interesting is that I never needed to set up a web server. Azure serves the files directly from blob storage. This is a good example of cloud **PaaS** (Platform as a Service): I only manage the content, and Azure manages everything underneath.

---

## Task 4 - Create an Azure Virtual Machine and Allow Web Access

After enabling static website hosting, I created the website content. I made two HTML files on the local computer: `index.html` (the landing page) and `404.html` (the custom error page). I uploaded both to the `$web` container.

![Files uploaded to $web container](images/Upload_the_file_to_the__web_container.png)

The container shows both files uploaded successfully with their sizes and access tiers (Hot, Inferred). Hot tier is the default for frequently accessed files, which makes sense for a website that people visit all the time.

I then visited the primary endpoint URL in a browser and confirmed the page loaded correctly, showing "Version 1 - Landing Page". I also tested the custom 404 page by adding `/fakepage` to the URL, and it showed my custom "Page Not Found" page instead of the generic Azure XML error.

After that, I updated the page content to "Version 2 - Landing Page" by overwriting `index.html` in the container. The change appeared immediately on the website after refreshing the browser.

**What the two security rules allow:**

| Rule | Port | What it allows |
|---|---|---|
| default-allow-ssh | 22 | SSH (Secure Shell) access to manage the VM from the command line. This lets the admin log in and make changes, like editing the web page. |
| AllowAnyHTTPInbound | 80 | HTTP web traffic from any source. This is what lets browsers open the website. Without this rule, the connection times out. |

Without the HTTP rule, anyone trying to visit the website gets a "connection timed out" error, because the firewall blocks all incoming traffic on port 80 by default. Adding the rule immediately makes the site accessible.

---

## Task 5 - Compare Cloud vs On-premise Costs

I compared a consumer desktop PC from mwave.com.au with a similar Azure virtual machine using the Azure pricing calculator (Australia East region, AUD, pay-as-you-go).

### The PC I chose

**Dell Pro Micro Desktop PC - Intel i5-14500T, 16GB RAM, 512GB SSD, WiFi+BT, Windows 11 Pro**
Price: **AUD $1,499.00** (from mwave.com.au)

![Consumer PC price](images/week8-task5-pc.png)

### The Azure VM I chose

**Standard_D2s_v3** - 2 vCPU, 8GB RAM, 16GB temporary storage (Australia East, Linux, pay-as-you-go)
Monthly cost: **approx. AUD $142/month**

![Azure pricing calculator](images/week8-task5-azure.png)

### Specifications table

| Specification | Desktop PC (Dell i5-14500T) | Azure VM (Standard_D2s_v3) |
|---|---|---|
| CPU | Intel Core i5-14500T (14 cores) | 2 vCPU |
| RAM | 16 GB | 8 GB |
| Storage | 512 GB SSD | 16 GB temp + managed disk extra |
| OS | Windows 11 Pro (included) | Linux (included) |
| Upfront cost | AUD $1,499 | AUD $0 |
| Monthly cost | ~$0 (after purchase) | ~AUD $142/month |
| 1-year total | AUD $1,499 | AUD $1,704 |
| 3-year total | AUD $1,499 | AUD $5,112 |

Note: The Azure estimate does not include managed disk or data transfer costs, so the real Azure cost would be slightly higher.

### Discussion of trade-offs

**Desktop PC advantages:**
- Much cheaper over 3 years, because you pay once and own the hardware.
- More powerful CPU (14 cores vs 2 vCPU) and double the RAM for the same cost range.
- No internet needed to run it once set up.
- Data stays on your own hardware, which can be important for privacy.

**Desktop PC disadvantages:**
- High upfront cost. You pay $1,499 even if you only need the computer for a week.
- If it breaks, you pay for repairs or a replacement.
- You have to manage updates, backups and security yourself.
- It is in one physical location. If the building burns down or is flooded, the data is gone.
- Cannot scale up easily: to get more power, you buy another machine.

**Azure VM advantages:**
- No upfront cost. You pay only for what you use, by the hour.
- If you only need a VM for a week, you pay for one week and stop.
- Can scale up or down in minutes: change the VM size or add more VMs.
- Azure manages the hardware, physical security and some updates.
- Accessible from anywhere in the world with an internet connection.
- Built-in redundancy options (LRS, GRS) to protect against data loss.

**Azure VM disadvantages:**
- Much more expensive over 3 years for constant 24/7 use.
- Needs a reliable internet connection to access and use it.
- Running costs never stop as long as the VM is on.
- Data is stored in Microsoft's data centres, which raises compliance questions for some industries.

**My conclusion:** For a small business or student running a server 24/7 for 3 years, the desktop PC is cheaper overall. But for a company that needs to scale quickly, only runs workloads part-time, or has staff in different countries, the Azure VM is much more flexible. The lecture described this as the difference between **CapEx** (capital expenditure, like buying the PC) and **OpEx** (operational expenditure, like paying monthly for the VM). Most businesses are moving toward OpEx because it is easier to plan and does not require a large upfront investment.

---

## Reflection

This week's lecture was about Attacks and Vulnerabilities. The main framework from the lecture was the **CIA triad**: Confidentiality, Integrity and Availability. These three things are what security is trying to protect.

The Azure lab connected directly to this. My static website has all three concerns:

- **Confidentiality:** I used a private container by default. Files in a private container cannot be read unless the user has a key or permission. If I had put sensitive files there instead of a public HTML page, the private setting would protect them.
- **Integrity:** Azure blob storage uses checksums when uploading and downloading files to detect corruption. When I uploaded Version 2 and it replaced Version 1, Azure confirmed the upload was successful before I could access the new file.
- **Availability:** The static website uses Azure's infrastructure, which runs across multiple datacenters. Even if one server goes down, the website keeps running. I did not have to do anything to get this. The lecture called this part of why cloud is useful: availability is built in.

The lecture also covered **STRIDE** (Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege). Looking at my website, the most obvious threat is **information disclosure** if I accidentally made a container public that should have been private. Another is **tampering**: anyone with the storage account key could overwrite my files. The lecture's point that you have to think about these threats before you build, not after, made sense after doing this lab.

The part I found most interesting was learning about the **shared responsibility model** from last week, and how it applies here. Azure is responsible for the physical datacentres and the infrastructure. I am responsible for what I put in the storage account, who has the keys, and whether my containers are private or public. If I got that wrong, it is my mistake, not Azure's.
