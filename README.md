# ALL-UNREAD-GMAILS-TO-TELEGRAM-SEPERATE-BOT
 Multi-Gmail Unread to Telegram Bot using n8n
📖 Description
This n8n workflow is designed for beginners and tech users alike to effortlessly manage unread emails across multiple Gmail accounts. It automatically fetches unread emails from up to four specified Gmail accounts, aggregates the total count, and then sends detailed email information to a designated Telegram chat. If there are no unread emails, it sends a simple notification to Telegram.

For Tech Users: This workflow utilizes the Gmail API (via n8n's Gmail and HTTP Request nodes) to query unread messages. It employs n8n's Code node for custom logic to count unread emails and determine the appropriate message path. A Merge node consolidates data from multiple Gmail sources, and a Switch node intelligently routes the workflow based on whether unread emails are found. Finally, the Telegram node sends notifications.

⚙️ How It Works (step-by-step with emojis)
🔹 1. Telegram Trigger 🤖
Simple: This node acts as the starting point of the workflow, likely triggered manually or by a Telegram message.

Tech: Uses the Telegram Trigger node, configured to listen for incoming messages (though in this context, it seems to initiate the process of checking emails).

🔹 2. COUNTS NO.OF GMAILS (x4) 📧
Simple: These nodes check the number of unread emails in four different Gmail accounts.

Tech: Each HTTP Request node makes a GET request to the Gmail API endpoint https://gmail.googleapis.com/gmail/v1/users/me/messages?q=is:unread to retrieve the count of unread messages. They use gmailOAuth2 credentials for authentication.

🔹 3. MERGE COUNT 🧩
Simple: This node brings together the unread email counts from all four Gmail accounts.

Tech: The Merge node combines the outputs from the four "COUNTS NO.OF GMAILS" HTTP Request nodes into a single dataset.

🔹 4. SENDS COUNT NUMBER 🔢
Simple: This node calculates the total number of unread emails and creates a message based on the count.

Tech: A Code node iterates through the merged data. It sums up the resultSizeEstimate from each Gmail account. It then outputs a JSON object indicating hasMail (true if total > 0), count (total unread emails), and a message string (e.g., "Total unread emails: X" or "No unread emails...").

🔹 5. SENDS MESSAGE BADEG ON COUNT 🚦
Simple: This node decides whether to proceed with fetching individual emails or just send a "no unread emails" message.

Tech: Another Code node checks the hasMail property from the previous node. If hasMail is true, it outputs a path of 'gmail'; otherwise, it outputs a path of 'fallback' along with the no-mail message.

🔹 6. SWITCHES BASED ON GMAILS ARE THERE OR NOT 🔄
Simple: This node acts as a fork in the road, sending the workflow down one path if there are unread emails and another path if there aren't.

Tech: A Switch node evaluates the path property set by the "SENDS MESSAGE BADEG ON COUNT" node. It has two branches: one for path === 'gmail' (to fetch and send individual emails) and another for path === 'fallback' (to send the "no unread emails" message).

🔹 7. Gmail 1, Gmail 2, Gmail 3, Gmail 4 📧
Simple: If unread emails exist, these nodes fetch the actual details of the unread emails from each of your four Gmail accounts.

Tech: Each Gmail node, configured with operation: getAll and filters: { q: "is:unread -in:trash -in:spam" }, retrieves the full details of all unread emails from its respective Gmail account.

🔹 8. EDITS GMAILS, EDITS GMAILS1, EDITS GMAILS2, EDITS GMAILS3 ✍️
Simple: These nodes format the important details of each unread email (like sender, subject, date, and a link) so they can be easily sent to Telegram.

Tech: Each Set node takes the output from its corresponding Gmail node. It extracts specific fields such as headers.to, headers.from, headers.subject, headers.date, and constructs a LINK to the Gmail inbox. The "EDITS GMAILS3" node also extracts text for the email content.

🔹 9. SEND ALL GMAILS 💬
Simple: This node sends the formatted details of each unread email to your Telegram chat.

Tech: A Telegram node, configured with your YOUR_TELEGRAM_CHAT_ID, constructs a message using the data prepared by the "EDITS GMAILS" nodes. It sends the "TO", "FROM", "SUBJECT", "LINK", and "DATE" for each unread email.

🔹 10. SEND NO GMAILS MESSAGE 📵
Simple: If there are no unread emails in any account, this node sends a simple "no unread emails" message to your Telegram.

Tech: A Telegram node, connected to the 'fallback' branch of the Switch node, sends a predefined message "NO UNREAD GMAILS" to your specified Telegram chat ID.

🔁 Replace the following before running:
🔐 REPLACE_ME for gmailOAuth2 credentials (e.g., "Gmail account", "Gmail account 2", etc.)
Replace these with the actual IDs of your configured Gmail OAuth2 credentials in n8n. You will need to set up distinct credentials for each Gmail account.

💬 YOUR_TELEGRAM_CHAT_ID
Insert your personal Telegram chat ID in the "SEND ALL GMAILS" and "SEND NO GMAILS MESSAGE" nodes.

🤖 REPLACE_ME for telegramApi credentials (e.g., " EMAIL")
Add your Telegram bot token to the Telegram credentials in n8n under the Credentials section and ensure it's linked to the Telegram nodes.

🧪 Testing Instructions
✅ Open n8n at http://localhost:5678 (or your n8n instance URL).
🔧 Click “Import Workflow” and paste the provided JSON content.
🔗 Ensure all connections between nodes are correctly established.
🔑 Navigate to the Credentials section in n8n and set up your Gmail OAuth2 credentials for each of your Gmail accounts and your Telegram API credential for your bot. Make sure the credential names match those referenced in the workflow (e.g., "Gmail account", "EMAIL").
⚙️ To test the full workflow, activate the workflow by toggling the "Active" switch.
➡️ You can manually execute the "Telegram Trigger" node by clicking "Execute Workflow" or sending a message to your Telegram bot (if configured as a webhook trigger).
🧪 Click “Execute Node” on individual nodes (starting from the HTTP Request nodes to check email counts, then Gmail nodes to fetch emails) to test each step independently and observe their outputs.
🪵 Check the execution log at the bottom of the n8n interface to troubleshoot any issues or verify data flow.
📬 Check your Telegram chat for messages indicating the unread email count or the details of individual unread emails.

💡 Tips for Beginners
💡 Use the “Credentials” tab in n8n to securely set up your Gmail and Telegram API keys. Never hardcode sensitive information directly into the workflow nodes.
💡 Check node outputs after each step to verify that the data is flowing as expected. This is crucial for debugging.
💡 The Gmail nodes are configured to fetch all unread emails. If you want to process them, remember to mark them as read or integrate a node to do so after processing, otherwise, they will be fetched again on the next run.
💡 This workflow uses multiple Gmail nodes and HTTP Request nodes for different Gmail accounts. Ensure each Gmail node is linked to the correct Gmail credential for proper authentication.
💡 Monitor your Telegram chat after running the workflow to see the messages. If you don't receive anything, double-check your Telegram Chat ID and Bot Token.








