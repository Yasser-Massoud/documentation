=============
Authorize.Net
=============

`Authorize.Net <https://www.authorize.net>`_ is a United States-based online payment solution
provider, allowing businesses to accept **credit cards**.

Configuration
=============

.. seealso::
   - :ref:`payment_providers/add_new`

Credentials tab
---------------

Odoo needs your **API Credentials & Keys** to connect with your Authorize.Net account, which
comprise:

- **API Login ID**: The ID solely used to identify the account with Authorize.Net.
- **API Transaction Key**
- **API Signature Key**
- **API Client Key**

To retrieve them, log into your Authorize.Net account, go to :menuselection:`Account --> Settings
--> Security Settings --> API Credentials & Keys`, generate your **Transaction Key** and
**Signature Key**, and paste them on the related fields in Odoo. Then, click on **Generate Client
Key**.

.. important::
   If you are trying Authorize.Net as a test, with a *sandbox account*, change the :guilabel:`State`
   to :guilabel:`Test Mode`. We recommend doing this on a test Odoo database, rather than on your
   main database.
   If you set :guilabel:`Test Mode` on Odoo and use an authorize.net account instead of a
   sandbox.authorize.net account, it results in the following error: *The merchant login ID or
   password is invalid or the account is inactive*.

Configuration tab
-----------------

Place a hold on a card
~~~~~~~~~~~~~~~~~~~~~~

With Authorize.net, you can enable the :ref:`manual capture
<payment_providers/features/manual_capture>`. If enabled, the funds are reserved for 30 days on the
customer's card, but not charged yet.

.. warning::
   After **30 days**, the transaction is **voided automatically** by Authorize.net.

Authorize.Net statement export
==============================

.. _excel-file-template:

.. admonition:: Template

   You can find the Excel import template `here. <https://docs.google.com/spreadsheets/d/1CMVtBWLLVIrUpYA92paw-cL7-WdKLbaa/edit?usp=share_link&ouid=105295722917050444558&rtpof=true&sd=true>`_

To import a statement, log into your Authorize.Net account, and go to :menuselection:`Account -->
Statements --> eCheck.Net Settlement Statement`. Then, define an export range using an **opening**
and **closing** batch settlement. All transactions within the two batch settlements will be exported
to Odoo. Select all transactions within the desired range, and copy/paste them into the
:guilabel:`Report 1 Download` sheet of the :ref:`Excel sheet <excel-file-template>`.

.. image:: authorize/authorize-report1.png
   :align: center
   :alt: Select all transactions for the desired range on Authorize.net, and copy them into
         'report1' of the Excel template.

.. example::
   .. image:: authorize/authorize-settlement-batch.png
      :align: center
      :alt: Settlement batch of the an Authorize.Net statement

   In this case, the first batch (01/01/2021) of the year belongs to the settlement of 12/31/2020,
   so the **opening** settlement is from 12/31/2020.

Once you have pasted the data into the :guilabel:`Report 1 Download` sheet, go to
:menuselection:`Authorize.net --> Transaction Search --> Search for a Transaction`, enter the
previously used range of batch settlement dates, and click :guilabel:`Search`.

When the list has been generated, click :guilabel:`Download to File`. In the pop-up window, select
:guilabel:`Expanded Fields with CAVV Response/Comma Separated`, enable :guilabel:`Include Column
Headings`, and click :guilabel:`Submit`. Open the text file, select :guilabel:`All`, copy the data
and paste it into the :guilabel:`Report 2 Download` sheet of the Excel file.

Transit lines are automatically filled in and updated in the :guilabel:`transit for report 1` and
:guilabel:`transit for report 2` sheets of the Excel file. Make sure all entries are present, and if
not, copy the **formula** from previously filled-in lines of the :guilabel:`transit for report 1` or
:guilabel:`2` and paste it into the empty lines.

.. important::
   To get the correct closing balance, do *not* remove any line from the Excel sheets.

Import into Odoo
----------------

To import the data into Odoo, open the Excel file, copy the data from the :guilabel:`transit for
report 2` sheet and **paste special** only the **values** in the :guilabel:`Odoo Import to CSV`
sheet. Then, look for *blue* cells in the :guilabel:`Odoo Import to CSV` sheet. These are
**chargeback** entries without any **reference** number. As they cannot be imported as such, go to
:menuselection:`Authorize.Net --> Account --> Statements --> eCheck.Net Settlement Statement`, look
for :guilabel:`Charge Transaction/Chargeback`, and click it. Copy the **invoice description**, paste
it into the :guilabel:`Label` cell of the :guilabel:`Odoo Import to CSV` sheet, and add
"**Chargeback /**" before the description. If you have multiple invoices, add a line into the Excel
sheet for each invoice and copy/paste the description into each respective :guilabel:`Label` line.

.. note::
   For combined **chargeback/returns** in the payouts, you need to create a new line in the Excel
   file for each invoice.

.. example::
   .. image:: authorize/authorize-chargeback-desc.png
      :align: center
      :alt: Chargeback description

Next, delete **zero transaction** and **void transaction** line items, and change the **format** of
the :guilabel:`Amount` column in the :guilabel:`Odoo Import to CSV` sheet to **Number**. Go back to
:menuselection:`eCheck.Net Settlement Statement --> Search for a Transaction` and search again for
the previously used batch settlements dates. Verify that the batch settlement dates on
**eCheck.Net** match the related payments' dates found in the :guilabel:`Date` column of the
:guilabel:`Odoo Import to CSV`. If it does not match, replace the date with the one from
**eCheck.Net**. Sort the column by *date*, and make sure the format is `MM/DD/YYYY`. Finally, copy
the data (column headings included) from the :guilabel:`Odoo Import to CSV` sheet, paste into a new
Excel file of your choice, and save it as .CSV format.

Open your Accounting app, go to :menuselection:`Configuration --> Journals`, tick the
:guilabel:`Authorize.Net` box, and click :menuselection:`Favorites --> Import records --> Load
file`. Select the file saved as .CSV, and upload it into Odoo.

.. tip::
   List of eCheck.Net `return codes. <https://support.authorize.net/knowledgebase/Knowledgearticle/?code=000001293>`_

.. seealso::
   - `Authorize.Net: Getting Started Guide
     <https://support.authorize.net/s/article/Authorize-Net-Getting-Started-Guide>`_
   - :doc:`../payment_providers`
