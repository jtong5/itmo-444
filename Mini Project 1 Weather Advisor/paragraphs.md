1. One paragraph on why the secrets are in SSM and each is read by one role, instead of in .env or the user-data. 

The secrets are in the SSM because user-data is readable by anything on the instance and by anyone who can describe the launch template. An .env file has a risk of getting committed to Git by accident. In contrast, SSM is read at run time by the instance role and never appears in your files. In other words, this can help prevent private information like the Gemini API key or database password from being exposed.  

---

2. One paragraph on turning backups off: what it saved, what it cost you, and when you would never do it. 

Turning backups off would save me money by eliminating the automated backup storage charges. At the same time, it also makes sure that the whole database is gone and cannot be restored. For the purpose of this lab, turning off backups is ideal to prevent any additional cost. However, if the database holds data that we care about, then turning backups off is something you should never do as the cost of data loss exceeds the cost of backups. 

---

3. One paragraph on what data you sent to Gemini and why the free tier makes that matter. 

The data that is sent to Gemini is only the zipcode and public forecast text. It does not send a name, an address, a street address, or an account ID. The free tier makes that matter because most AI services that are free are allowed to use your prompts to train other AI products. Additionally, humans may review your prompts too. For the purpose of this lab, we don't use our university emails as it can hold private information.