🕵️ Redaction gone wrong
📂 Category : Forensics
📊 Level : Medium
________________________________________
🧠 The Flaw
The document was “redacted” by simply placing black boxes over the text in the PDF.
However, this does not actually remove the data — the original text still exists underneath the overlay.
________________________________________
🛠️ The Process
1.	Download the PDF file:
2.	wget https://artifacts.picoctf.net/c/84/Financial_Report_for_ABC_Labs.pdf
3.	Open the PDF normally → observe blacked-out sections.
4.	Select (highlight) the entire document content.
5.	Copy and paste it into a text editor.
6.	The hidden/redacted text becomes visible because:
o	The text layer is still present
o	The black boxes are only visual elements
7.	From the pasted content, the flag appears:
8.	picoCTF{C4n_Y0u_S33_m3_fully}
________________________________________
🎯 The Lesson
Relying on visual redaction (drawing black boxes) is not secure.
Sensitive data must be properly removed or sanitized, otherwise it can be easily recovered using simple actions like copy-paste.
________________________________________
✅ Final Flag
picoCTF{C4n_Y0u_S33_m3_fully}
________________________________________


