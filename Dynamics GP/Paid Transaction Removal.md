<!-- b13f58c625094ed08549f276a86bed66 -->
# Overview
In order to make the system-generated statements look a lot more like one would expect, specifically by removing the transactions that are paid, Dynamics GP contains a routine that should be run on a periodic basis.  A user should be designated to perform this maintenance on a monthly basis.  If your company uses "BBF" [Balance Brought Forward] type statements, this rolls the balance forward.  If your company does not use BBF style (therefore uses the "OPEN" style) this routine will remove the transactions from the statement.

# Routine
## Paid Transaction Removal
Navigate to the "Dynamics GP" menu --> Tools --> Routines --> Sales --> Paid Transaction Removal:

![image.png](/img/b13f58c625094ed08549f276a86bed66/01%20-%20Menu%20Navigation.png)

Alternatively, you can access it from the Sales main screen:

![image.png](/img/b13f58c625094ed08549f276a86bed66/02%20-%20Pane%20Navigation.png)

This window then opens:

![image.png](/img/b13f58c625094ed08549f276a86bed66/03%20-%20Paid%20Transaction%20Removal%20Screen.png)

On this window, the user can select what range of customers to operate on, what types of transactions to remove and the cut off date.  If, for example, your company policy is to show the paid transactions from the last month on the statements, then on May 31st 2024, the correct selection for the "Cut Off" would be April 30th 2024.  If the user wishes to have a report that prints out showing what actually occurred, then ensure that the "Print Register" selection is active.  (We recommend printing the report, even to screen, in order to verify when the system is done.)

Example report:

![image.png](/img/b13f58c625094ed08549f276a86bed66/04%20-%20Sample%20Report.png)

## Effect

Before you remove the paid transactions, the paid transactions will appear in inquiry tables and on customer statements as OPEN transactions:

![image.png](/img/b13f58c625094ed08549f276a86bed66/05%20-%20Before%20running%20routine.png)

Once the paid transaction removal is execute, it will move the sales document to history (assuming your company keeps the transaction history) and the document will now show as HIST and no longer appear on customer statements:

![image.png](/img/b13f58c625094ed08549f276a86bed66/06%20-%20After%20running%20routine.png)

Please note that any SSRS or customized reports that your environment has may be affected by this.  From a technical perspective, this operation also moves the documents from the open tables (i.e. SOP10100) to the historical tables (i.e. SOP30200.)  If your reports are not designed to be able to handle this situation, please let us know and we'll be happy to take a look and see if they need to be fixed.

## Reprint Report

If the paid transaction removal report needs to be regenerated, there is a limited capability to do so by using the Sales History Reports (Reports --> Sales --> History) and selecting the "Receivables Trx History" option:

![image.png](/img/b13f58c625094ed08549f276a86bed66/07%20-%20reprint%20report.png)

In here, the user will need to specify ranges to ensure that the correct document range is captured:

![image.png](/img/b13f58c625094ed08549f276a86bed66/08%20-%20reprint%20options.png)

<span style="font-size:0.75em;">
<end>
</span>
<br /><br />

<span style="font-size:0.5em;">
© 2024 Evergrowth Consulting - This article is provided ​“AS IS”. Evergrowth Consulting makes no other warranties, express or implied, and hereby disclaims all implied warranties, including any warranty of merchantability and warranty of fitness for a particular purpose.  
</span>
