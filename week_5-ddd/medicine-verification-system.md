##### Medicine Verification System:
https://github.com/codurance/apprenticeship/wiki/Medicine-Verification-System


##### Description
The European Commission (EC) have created a Falsified 
Medicines Directive (Falsified Medicines Directive) and a Delegated Regulation. This 
legislation is designed to safeguard human health across the European market by requiring each pack of medicine, with certain exceptions, to be verified at the 
point of dispense. The Falsified Medicines Directive further mandates verification of packs at certain points within the European supply chain, e.g., when 
passed from a wholesaler recognised by the original manufacturer to another wholesaler who is not recognised by that manufacturer. 
The Falsified Medicines Directive requires that a 'rElectronic Point of Saleitories system' is set up across Europe to manage the distribution and storage of product and 
pack data and to enable verification of individual packs. This is called the European Medicines Verification System (European Medicines Verifications System). 
It includes a centralised European Hub to which Marketing Authorisation Holders (Marketing Authorization Holderss - manufactures and parallel distributors) upload product, batch and pack data.

The European Hub is managed through the European Medicines Verification Organisation (European Medicines Verification Organisation), and will be designed and implemented by Codurance. Codurance has a contract to 
run the European Hub for a number of years on behalf of the European Medicines Verification Organisation. The European Medicines Verifications System also 
includes National Systems in each market to handle verification and status change 
requests from wholesalers, pharmacies and other locations. Two or more countries may 
establish a single supra-national market if they so choose. Each nation must set up a National Medicines Verification Organisation (National Medicines Verification Organisation) to oversee the implementation and operation of their National System.

Each market is free to implement a National System as it wishes. However, each system 
must support interchange with Hub through a standardised interface. It must also provide sufficient functionality to allow local 
wholesalers and persons authorised or entitled to supply medicinal products to the public to verify individual packs of 
medicine, especially at the point of dispense (PoD). The burden of implementing a custom system and connecting to every 
wholesale organisation, pharmacy, hospital dispensary and other locations across an entire nation can be challenging, especially 
given the fixed deadline for compliance. To mitigate this, European Medicines Verification Organisation has established the notion of 'blueprint' systems as part of its 
model. Codurance is one of three companies appointed as official suppliers of National Blueprint Systems.

Each National Blueprint System is verified by the European Medicines Verification Organisation to ensure it has been built in compliance to industry standards (GAMP5) to precisely meet the 
requirements of the Falsified Medicines Directive and the Delegated Regulation.

##### European Hub
The European Hub maintains a product catalogue together with batch information. As this information is uploaded to the Hub 
by manufacturers and parallel distributors, it is routed to each of the National Systems. In addition, Marketing Authorization Holderss upload pack data 
including a product code, serial number, batch code and expiry date. This data is 
routed to the National System in the market to which the original manufacturer or 
parallel distributor intends to supply the product. Some products are designated as 
'multi-market', and may be supplied to one of a number of markets. The European Hub 
distributes multi-market pack data to each target market.

When a state change occurs to the physical multi-market pack (e.g., it is dispensed or decommissioned) the National System in the market where the pack resides communicates the state change to the European Hub which 
ensures that each of the other target markets is updated with this change. In this way, when a multi-market pack is dispensed in one market, other markets will be aware that it is no longer available. In rare cases, packs may be legally dispensed in 
markets other than those designated by the manufacturer. This can happen on compassionate grounds or as a result of local arrangements between two markets. In this case, the pack data may not be present in the market where the medicine is 
dispensed. In this case, the National System requests validation of the pack from another National System via the European Hub.

Pharmacy and Wholesaler Systems
Pharmacies and other locations where persons are authorised or entitled to supply medicinal products to the public (e.g., 
hospital dispensaries) must validate each pack of medicine before it is dispensed using the information printed on the secondary packaging for that pack. 
Wholesalers may, and in certain circumstances must, validate each pack of medicine using the information printed on the secondary packaging for that pack. This is generally done by scanning a DataMatrix 
2D barcode containing GS1 data, printed on the secondary packaging as mandated by the Falsified Medicines Directive.

In most cases, the validation request will be handled by some local software system such as a Patient Medical Records (PMR) 
system, Electronic Point of Sale or Enterprise Resource Planning system. The Codurance National Blueprint System establishes a clear boundary of responsibility between local systems and the National System. Local organisations have 
existing relationships with software providers that specialise in pharmaceutical industry software. Existing software 
providers can integrate their systems using an API.

Local systems will be extended to support pack validation and decommission and other actions. Pharmacy systems will support 
dispense of packs. Wholesaler systems will support export from the EU. Integration of existing systems with the National Blueprint System involves more 
that REST calls. Software providers must ensure secure storage of client credentials and should implement robust forms of recovery, including store-and-forward 
mechanisms. They may need to integrate scanners and intEnterprise Resource Planningret the GS1 data stored in barcodes. Their system should provide clear visual feedback of pack status and 
verification to end users. It must provide opaque identifiers to the National Blueprint System to represent authenticated and authorised users for auditing purposes. It should maintain 
mappings between those identifiers and user accounts for forensic purposes.

National Blueprint System
The National Blueprint System (National Blueprint System) is a rElectronic Point of Saleitory of product and pack data at the 
national level. It handles verification and status change requests from connected organisations (pharmacies, wholesalers, etc.) as well as interchange with the European Hub. Status change requests include 
dispensing and re-introducing dispensed packs, locking packs for further investigation, destroying packs, recording their theft and exporting packs from the EU. The National Blueprint System also allows packs to be designated as samples, including free samples.

Interchange with the European Hub includes distribution and modification of product master data and pack data as well as product withdrawal and batch recall initiated by manufacturers. The National Blueprint System also handles 
interchange with the European Hub for multi-market packs in order to synchronise pack status with other National Systems.

Portals
The National Blueprint System provides browser-based portal capabilities to deliver UIs to different groups of users. The UIs are described 
below. In each case, users should be authenticated using two-factor authentication. This comprises their username (email alias) and password together with an additional factor.

Administration and Operations UI
This UI supports the needs of administrators and operational management staff. It provides current status information and reports, allows administrators to force password resets and transfer

Registration and On-Boarding UI
National Medicines Verification Organisations must on-board pharmacies, hospitals, wholesale companies, software providers and other organisations. The National Blueprint System supports this through an on-line registration and 
on-boarding process that supports the processes implemented by a given market. Registration of organisation involves ‘known facts’ and a designated ‘named person’ who 
act as the super-user for that organisation. Once an organisation has been registered, the super-user can set up additional organisational accounts and assign these to defined roles. These roles support 
administration of local accounts, download and management of client credentials to enable secure, authorised system-to-system interchange and access to the software development kits (SDKs).

Emergency Verification UI
Registered organisations can create and manage verification user accounts. These users can access the emergency verification UI. This UI is mandated by the Delegated 
Regulation and allows authenticated users to manually enter pack data for the purposes of verification and decommission. The UI is intended only to be used in the case of loss of service between the National Blueprint System and a local 
system. The European Medicines Verification Organisation restricts the functionality of this UI within the scope of the blueprint model. Specifically, they do not support scanning of Data Matrix barcodes.

National Medicines Verification Organisation UI
The National Blueprint System will support the needs of National Medicines Verification Organisations, providing a UI for access to reports. These include statistical, forensic and performance reports. Users can view reports on-line and download them as PDFs or CSV files.

National Competent Authority UI
The National Blueprint System will support the needs of National Competence Authoritys in accordance with the Delegated Regulation. It will provide a UI for access to reports. These include supervisory, reimbursement, pharmacovigilance and pharmacoepidemiology reports. Users can view reports on-line and download them as PDFs or CSV files.

Users
System Administrator
System administrators have access to administration and operational management features.

National Competence Authority User
National Competence Authority users have access to reports for supervisory purposes (statistics, metrics and forensics), reimbursement, pharmacovigilance and pharmacoepidemiology. These reports will be provided via a portal interface and will be downloadable as PDFs or for import into Excel (CSV).

National Medicines Verification Organisation User
National Medicines Verification Organisation users have access to reports for supervisory purposes (statistics, metrics and forensics). These reports will be provided via a portal interface and will be downloadable as PDFs or for import into Excel (CSV).

Local User
A user in a Local Organisation. Includes the Named Person and additional users configured by them. The Prime Contact is not necessarily a local user, but is represented as the first point of contact during the registration process.

From Marketing Authorization Holders to PoD workflow
Manufacturer uploads PMD
Manufacturer uploads Batch and Pack data
Hub disperses data to relevant National Blueprint Systems
Pharmacist verifies a single pack by scanning barcode
Pharmacy system calls National Blueprint System api
National Blueprint System looks up barcode data
If it matches, the pack is valid
If it partially matches or doesn't match, request Hub to validate pack
If no match, send an alert to National Medicines Verification Organisation
Once it is valid, pharmacist dispenses the pack and marks the pack as supplied
If the pack is a local pack, the National Blueprint System updates the pack state
If the pack is multi-market, the National Blueprint System updates the local state and requests Hub to change the state in other market

Falsified Medicines Directive - Falsified Medicines Directive
DR - Delegated Regulation



##### Our work with Event Storming:
We started with a diagram:

![Screenshot 2020-03-31 at 18 13 26](https://user-images.githubusercontent.com/27693622/78055421-6b80db80-737b-11ea-900f-223326b62fae.png)

Then we added a list for the tasks of the session:
![Screenshot 2020-03-31 at 18 15 14](https://user-images.githubusercontent.com/27693622/78055533-9703c600-737b-11ea-8e51-3c154a44cd25.png)

Next we explored Product, Batch and Pack more closely:
![Screenshot 2020-03-31 at 18 17 01](https://user-images.githubusercontent.com/27693622/78055708-d6caad80-737b-11ea-84bf-6ca1400176fd.png)


This is the NBS - National Blueprint System:
![Screenshot 2020-03-31 at 18 27 50](https://user-images.githubusercontent.com/27693622/78056625-5ad16500-737d-11ea-8588-20f9e95fd39c.png)

This was the UML:

![Screenshot 2020-03-31 at 18 29 35](https://user-images.githubusercontent.com/27693622/78056864-b3a0fd80-737d-11ea-99e8-b6c7a43b3fdc.png)

![Screenshot 2020-03-31 at 18 29 52](https://user-images.githubusercontent.com/27693622/78056894-be5b9280-737d-11ea-88bf-4bc3457c02fc.png)
