# Software Requirement Specification

 

Version 1.0

October 18, 2020

Triage Capacity Planning System

Sam Cymbaluk - 400138646

Shawn Esseltine - 400145888

Daniel Rubinstein - 400111892

Nikhail Singh - 400069771

Kristie Tom - 400018028

**Table of Contents:**


[TOC]



# 1. Introduction


## 
1.1 Document Purpose

The purpose of this document is to provide a detailed description of the **Triage Capacity Planning System**. It will explain: the motivation behind the system, the features and interface of the system, how the system will work, the constraints on the system and its design, and the verification methods that will be used to test the system. This document is intended both as a proposal to be approved by the McMaster Children’s Hospital and as a reference to the development team when they are developing the first version of this system.


## 
1.2 Product Scope

The “Triage Capacity Planning System” (“Triage”) will be a data-driven application for the McMaster Children’s Hospital. The system is designed to eliminate the guesswork involved in scheduling patients by estimating the need for urgent time slots based on past trends which will decrease the backlog of urgent patients. Currently wait times for urgent patients are longer than target wait times, potentially causing increased health risks in patients. Long-term, the system will also help track the hospital’s progress in satisfying the new guidelines set by the CPSO[1] which outline the amount of time specialists have to accept referrals and provide a waiting time estimate, and decrease the overall backlog of patients.

	The system will use historical data on the triage class and wait times of patients in order to predict the number of time slots that need to be reserved each week for urgent and semi-urgent patients. The system is designed to be able to dynamically adjust the number of required slots based on new data while still remaining flexible and easy to use. The system will facilitate the input of data  by users, the setting of parameters for the output, and the visualization of the recommended number of slots. It also contains a relational database for storing the data. These system functionalities are discussed wholly in the subsequent sections.


## 
1.3 Definitions, Acronyms and Abbreviations


<table>
  <tr>
   <td><strong>Term</strong>
   </td>
   <td><strong>Definition</strong>
   </td>
  </tr>
  <tr>
   <td>Triage
   </td>
   <td>the assignment of degrees of urgency to wounds or illnesses to decide the order of treatment of a large number of patients or casualties.
   </td>
  </tr>
  <tr>
   <td>patient
   </td>
   <td>a person receiving or registered to receive medical treatment.
   </td>
  </tr>
  <tr>
   <td>CPSO
   </td>
   <td>College of Physicians and Surgeons of Ontario
   </td>
  </tr>
  <tr>
   <td>Clinic
   </td>
   <td>An establishment where patients are admitted for special study and treatment by a group of health care professionals practicing together.[3]
   </td>
  </tr>
  <tr>
   <td>Wait times
   </td>
   <td>The length of time a patient must wait before being seen by the necessary doctor
   </td>
  </tr>
  <tr>
   <td>Referral
   </td>
   <td>Doctors from clinics around Hamilton refer patients to the Specialists at the McMaster Children’s Hospital
   </td>
  </tr>
  <tr>
   <td>Triage Class
   </td>
   <td>Categorization established by the McMaster Children’s Hospital for triage purposes defined as follows:
<ol>

<li>(<strong>Immediate</strong>) Patient’s condition is determined as life or organ threatening and they would be advised to go to the nearest ER.

<li>(<strong>Urgent</strong>) Patient’s condition is determined as potentially organ or function threatening and they need to be seen in clinic within the next several days up to 2 weeks.

<li>(<strong>Semi-urgent</strong>) Patient condition is determined as potentially progressive and they need to be seen within the next 3 months.

<li>(<strong>Standard)</strong> Patient condition requires specialist input but is determined to be stable (ex: glue ear) and is triaged to the next available appointment.

<li>(<strong>Forwarded</strong>) Patient’s condition does not warrant tertiary care expertise or received by the wrong clinic and will be forwarded to another physician affiliated with an organization.

<li>(<strong>Declined</strong>) Patient cannot be served in a children’s hospital (ex: adult) or referral cannot be forwarded to another physician.
</li>
</ol>
   </td>
  </tr>
  <tr>
   <td>PIPEDA
   </td>
   <td>The Personal Information Protection and Electronic Documents Act
   </td>
  </tr>
  <tr>
   <td>UI
   </td>
   <td>User Interface
   </td>
  </tr>
  <tr>
   <td>Data module
   </td>
   <td>The module that stores data. Also referred to as the datastore module.
   </td>
  </tr>
  <tr>
   <td>Confidence interval (CI)
   </td>
   <td>A confidence interval specifies the range in which a random value appears a given proportion of the time.
<p>
 For example, if we say that the average number of patients a week is 10 with a 95% CI of 2. That means that 95% of the weeks, the number of patients will be between 8 and 12 inclusive.
   </td>
  </tr>
</table>



## 
1.4 References

[1][https://www.cpso.on.ca/Physicians/Policies-Guidance/Policies/Transitions-in-Care#:~:text=Consultant%20physicians%20must%20acknowledge%20referrals,able%20to%20accept%20the%20referral](https://www.cpso.on.ca/Physicians/Policies-Guidance/Policies/Transitions-in-Care#:~:text=Consultant%20physicians%20must%20acknowledge%20referrals,able%20to%20accept%20the%20referral). 

[2][https://www.priv.gc.ca/en/privacy-topics/privacy-laws-in-canada/the-personal-information-protection-and-electronic-documents-act-pipeda/](https://www.priv.gc.ca/en/privacy-topics/privacy-laws-in-canada/the-personal-information-protection-and-electronic-documents-act-pipeda/) 

[3][https://medical-dictionary.thefreedictionary.com/clinic](https://medical-dictionary.thefreedictionary.com/clinic)

[4][https://machinelearningmastery.com/k-fold-cross-validation/](https://machinelearningmastery.com/k-fold-cross-validation/)


## 
1.5 Document Overview

	The remainder of this document contains three chapters and appendices. Chapter two, Product Overview provides an overview of the system and its functionality, describing the system’s origin, functionality, constraints, and users. It also lays the groundwork for the technical requirements that will be detailed later on.

	Chapter three, Requirements provides detailed technical specifications for the system including all of its interfaces. It is written primarily with developers in mind and as such describes the design and functionality of the system in technical terms.

	Chapter four, Verification provides the verification approaches and methods planned to qualify the software and ensure that it meets the requirements set out in this document.

	It should be noted that we are working with an external client to derive the requirements and so there are significant delays between emails and meetings due to their schedule. Therefore in some cases we are awaiting confirmation on some emails.


# 



# 
2. Product Overview


## 
2.1 Product Perspective

	The system is a standalone application which will replace the existing method of determining time slot reservations.The system is standalone in the sense that it will not be integrating with any pre-existing hospital software. It will also replace the existing method of manually guessing the number of slots needed so our system is not going to be replacing any software or hardware currently in use by the hospital.

The system contains four core components: an interface consisting of a front-end interface for user input and a back-end API for communicating with the rest of the system, a utility service that allows the various parts of the system to work together, a prediction module consisting of a prediction model and a simulation module, and a data module. For further information see [figure 1](#bookmark=id.fkz5qiu1a5w5). Part of this system is external, and the other part internal.

The external system can be described in the following example: suppose a user wants to see how many slots are needed to service 95% of the expected critical patients within 2 weeks. The user interface will take user input of 95 and 2, send it via an HTTP request to the API which invokes a utility service. The utility service forwards the request to the simulation module which then returns the final output of the number of slots available. The API returns this result.

Secondly, the internal division of the system can be described as follows: the model takes the data from the datastore module and analyzes it to come up with aggregate information which is then used by the simulation module to create a final prediction for the number of time slots needed which it sends to the user interface via the API. Lastly, the data module is used to store the data inputted by the user so that it can later be used by the model to self-train.


## 
2.2 Product Functions

This section will outline the functions the system will perform, organized by component. 


### 2.2.1 Context Diagram



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.png "image_tooltip")


Figure 1: Context Diagram


### 2.2.2 UI

**Brief description: **the user interface allows users to interact with the system, handling input and output.

**List of functions:**



*   Input necessary data
*   Select parameters for the final result
    *   % of urgent patients
    *   Numbers of weeks to forecast for
*   Ask for a result based on the inputted data
*   View the final prediction


### 2.2.3 Interface API

**Brief description: **the Interface API accepts the input from the user, and sends it to the data module. It also accepts the final result from the model and transforms it so that it can be presented to the user via the UI.

**List of functions:**



*   Transform user input into a format usable by the utility service
*   Send the parameters the user selected to the utility service
*   Receive a final result from the model
*   Transform the final result into a method that can be displayed by the UI
*   Send the transformed result to the UI


### 2.2.4 Utility Service

**Brief description: **allows various parts of the system to communicate with each other.

**List of functions:**



*   Verify the data sent by the API is correct
*   Transform the user input into a form that can be stored by the data module
*   Send the transformed data to the data module.


### 2.2.5 Prediction Model

**Brief description: **the Model receives data and uses it to perform calculations and analyses to get a final prediction that satisfies the parameters set by the user.

**List of functions:**



*   Receive data from the data module
*   Analyze the data 
*   Perform calculations 
*   Generate aggregate scores for data. For example, % of urgent patients out of total patients
*   Send prediction to simulation module


### 2.2.6 Simulation Module

**Brief description: **This module runs a simulation based on the data from the prediction model in order to generate a final result which it then sends to the API

**List of functions: **



*   Receive prediction from model
*   Run a simulation based on result provided by the prediction module in order to get a final result
*   Send the final prediction to the API


### 2.2.7 Data Module

**Brief description: **the Data Module stores all of the data both inputted by the user and historical data for use by the prediction model.

**List of functions:**



*   Insert new data
*   Delete outdated/improper data
*   Store hundreds/thousands of rows of data

## 
2.3 Product Constraints


This subsection should provide a general description of any other items that will limit the developer’s options. These may include:



*   Interfaces to users, other applications or hardware.
*   Quality of service constraints.
*   Standards compliance.
*   Constraints around design or implementation.


### 2.3.1 Data constraints: 

The accuracy and certainty of the final prediction generated by the model is limited by the amount of data we can give the model. The more data we can give the system the better, however there is a minimum threshold of data required for the model to generate a result that the McMaster Children’s Hospital could comfortably trust.

	The amount of data that can be given to the model is constrained by how much access to data McMaster Children’s Hospital and the associated clinics can provide. This is limited by how well they have been tracking this data on their own as well as how much access they can legally provide which is limited by The Personal Information Protection and Electronic Document Act.(PIPEDA [2]) PIPEDA protects the privacy of patient information including limiting access to patient data which is needed for the system to work. 


### 2.3.2 Standards compliance constraints:

	The overall system design must meet the requirements set by the McMaster Children’s Hospital’s IT team in order for it to be deployed to the hospital and maintained by their staff. 


## 
2.4 User Characteristics/Stakeholders

	There are two types of users who will interact with the system; doctors and hospital administrators, and hospital IT staff and developers. These two groups will interact with the system in very different ways discussed below.


### 2.4.1 Doctors and Hospital Administrators

Doctors and hospital administrators will primarily be handling the input of new data into the system and will be the ones requesting predictions from it. They do not have technical expertise with respect to the system so they would not be expected or required to access the API, model or data module directly. Rather they would only be interacting with the web app’s UI to perform data entry, modify parameters, and request time slot predictions. We estimate that they will use the system weekly to generate a prediction and weekly/monthly to do data input depending on who is doing the entry and their schedule.


### 2.4.2 Hospital IT Staff and Developers

The hospital IT staff and/or developers will be doing maintenance and modifications to the system. This includes editing or removing data from the data module, adjusting the model to change how it generates its result, and fixing any errors or issues that might occur. They have technical expertise and thus would have full access to every component including the source code. They will use the system on an ad-hoc basis depending on how often changes are needed or requested by the doctors and hospital administrators. 


## 
2.5 Assumptions and Dependencies

Our first assumption is that the hospital servers will be able to run the application. This includes the servers having enough capacity to store the necessary data and host the user interface, and the server having enough processing capacity so that our model can process all of the data.

	Another assumption is that the system will be supplied with enough data so that the predictions generated by the model will be usable with a high degree of confidence (see 

<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: undefined internal link (link text: "4.2.3 Reserved Slot Predictions"). Did you generate a TOC? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>

[4.2.3 Reserved Slot Predictions](#heading=h.4fn31cv3lucq) for more detail). While the system is constrained by the amount of data it can access and all of the data we need is subject to PIPEDA we assume that there will still be enough anonymized data so that we can generate a trustworthy prediction.


## 
2.6 User Documentation

	For deployment of the system to be successful we will need to prepare a user instruction manual, a demo on how to use the system, and maintenance notes on how to modify and upkeep the model and data module. This will be done via github READMEs.

	

	The user instruction manual will provide a detailed walkthrough of how the user interface works. It will detail how to do proper data input, how to select parameters and what those parameters might affect, and how to obtain and understand the result generated by the model. 

	To go along with the instruction manual we will also prepare a video demonstration providing step by step instructions on the process of working with the system.

	Lastly, maintenance notes will be essential for the hospital IT team so that they modify the system, fix any issues or make any updates. These notes will help them understand how to access error logs, make changes to the source code, and understand how the model works.

Documents to prepare are:


## 
2.7 Apportioning of Requirements

Each requirement is apportioned according to what component it belongs to in the specific requirements detailed in the next section.

Automating data entry and integration with other hospital systems may be apportioned to future versions. Future versions may also increase the scope of the system to cover other departments in the hospital or other hospitals outside of the McMaster Children’s Hospital.


# 3. Requirements


## 
3.1 External Interfaces


### 
3.1.1 User interfaces


#### 
3.1.1.1 Software Components

The users of the externally-facing UI are hospital medical staff excluding IT. We will have the following pages defined by the following UI components:


##### Universal Layout

All pages will have the following features


<table>
  <tr>
   <td>Feature
   </td>
   <td>Description
   </td>
  </tr>
  <tr>
   <td>Navbar
   </td>
   <td>
<ul>

<li>Link to home in the top left

<li>Link to profile on the top right

<li>Question icon which links to the help page
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Collapsible hamburger menu
   </td>
   <td>Contains the following links to pages:
<ul>

<li>Settings

<li>Predictions

<li>Help
</li>
</ul>
   </td>
  </tr>
</table>


With this navbar and collapsible hamburger menu, and the fact that there will not be any nested routes it should be really easy to navigate backwards. Using these common UI elements will add to the ease-of-use and ease-of-learning. Any further questions can be seen in the Help section of the hamburger menu.


##### Prediction Page

The prediction page will be the default load page and will be used to display to the user the number of appointments needed over the next specified amount of time. It will be composed of 4 sub-components:



1. A collapsible form which contains configurable options: X% service threshold; Y weeks that the prediction is made over. The form will by default be open and have a button to create predictions. When the make prediction button is clicked, an asynchronous request to the backend to make a prediction given the user configurations will be made. The backends response will be sent to the front-end and it will be loaded component (2)
2. The results in text of the number of urgent patients seen, the configuration options selected, and the number of appointment slots needed. We will also display the confidence interval to show the range in which we expect the required slots to be within, as well as an info button to describe what it means and how it was calculated. By default this section is hidden, and after filling out the form this section will be populated.
3. A button to clear the form and the page contents. This will be displayed only when (2) is displayed

Alternatively, if the server is down then the form should not be loaded and instead an error page should be displayed. Therefore, when clicking the prediction page, a non-blocking asynchronous call should be made to check if the server is alive. If it is down, the prediction page should be replaced with an error screen instructing the user to contact the Hospital IT staff, and a hyperlink to the FAQ page. This will help the ease-of-use in the event of the asynchronous requests not working.


##### Settings page

This would be to enable colour modes, font adjustments for readability, and user administration.


##### User page

The user page will be a per-clinic-based user system. It will display the clinic name and address by fetching the clinic name and address fields from the Profile table.


##### Help Page

The help page will list common FAQs on how to use the product, what to do if an error occurs or if the server is down, etc. This makes the product much easier to use.


### 
3.1.2 Hardware interfaces

Describe the logical and physical characteristics of each interface between the software product and the hardware components of the system. This may include the supported device types, the nature of the data and control interactions between the software and the hardware, and communication protocols to be used.



*   GPUs will be used for model training, but not a part of the deployed product

### 
3.1.3 Software interfaces


Components:



*   Server host
*   Data training
*   Web client host
*   Database host


#### 3.1.3.1 HTTP Server Host (Flask, latest version)

Encapsulates the predictive model with a REST API via flask. It communicates with both the web client and the database server


<table>
  <tr>
   <td>Communicates with
   </td>
   <td><strong>Web client</strong>
   </td>
  </tr>
  <tr>
   <td>Nature of communication
   </td>
   <td>HTTP. HTTPS is not needed since it is on an internal network.
   </td>
  </tr>
  <tr>
   <td>Responds with (/)
   </td>
   <td>A default health page for the API
   </td>
  </tr>
  <tr>
   <td>Responds with (/calculate)
   </td>
   <td>Number of slots for urgent patients needed given X% service level of critical patients within Y time frame
   </td>
  </tr>
</table>



<table>
  <tr>
   <td>Communicates with
   </td>
   <td><strong>Database</strong>
   </td>
  </tr>
  <tr>
   <td>Nature of communication
   </td>
   <td>Asynchronous inter-process communication
   </td>
  </tr>
  <tr>
   <td>Requests
   </td>
   <td>A query result for a table in the database
   </td>
  </tr>
  <tr>
   <td>Receives
   </td>
   <td>The response to the query, or the PostgresQL error
   </td>
  </tr>
</table>


Libraries used:



*   Flask - latest
*   Psycopg2 - latest
*   Swagger.io - latest


#### 3.1.3.2 Predictive Model Training

Used to train a predictive model it will use the following libraries:



*   Tensorflow - latest
*   Keras - latest
*   Numpy - latest
*   Pandas - latest
*   Matplotlib - latest


#### 3.1.3.3 Web client host (Angular, latest version)

Displays appointment slot predictions to hospital medical staff.


<table>
  <tr>
   <td>Communicates with
   </td>
   <td><strong>Server</strong>
   </td>
  </tr>
  <tr>
   <td>Nature of communication
   </td>
   <td>HTTP. HTTPS is not needed since it is on an internal network.
   </td>
  </tr>
  <tr>
   <td>Requests
   </td>
   <td>Prediction given X% service level of critical patients within Y time frame
   </td>
  </tr>
  <tr>
   <td>Receives
   </td>
   <td>Number of slots for urgent patients, or an error.
   </td>
  </tr>
</table>


Libraries



*   Typescript - latest
*   TsLint - latest
*   Angular - latest
*   Node - latest


#### 3.1.3.4 Database host (PostgreSQL, latest version)

Store hospital clinic information. Further details on what precisely we will be storing needs to be provided by Dr. Belostotsky. It communicates with:


<table>
  <tr>
   <td>Communicates with
   </td>
   <td><strong>Server</strong>
   </td>
  </tr>
  <tr>
   <td>Nature of communication
   </td>
   <td>Asynchronous inter-process communication
   </td>
  </tr>
  <tr>
   <td>Receives
   </td>
   <td>A query to a table in the database
   </td>
  </tr>
  <tr>
   <td>Responds with
   </td>
   <td>The response to the query, or the PostgresQL error
   </td>
  </tr>
</table>



## 
3.2 Functional


### 
3.2.1 Interface Module


#### 
3.2.1.1 Web Application Module


<table>
  <tr>
   <td><strong>ID: INT-1</strong>
   </td>
   <td><strong>Type: Functional Requirements (Web App Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>A form for user inputs taking in a configurable X% of critical patients seen within the needed Y time interval.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>According to the client, ideally over 95% of critical patients should be seen within 2 weeks, but it should be configurable if the healthcare worker wants an alternative deadline.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>A user-friendly form. On error return 500 server error.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: INT-2</strong>
   </td>
   <td><strong>Type: Functional Requirements (Web App Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The form has a send button. On send, a request is sent to the API server for a prediction on how many slots for urgent patients are needed. The data must be received by storing the API call in a variable that is returned by some function.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>This is needed so that the front-end can call a black-box oracle which will suggest the number of urgent slots. (The rationale as to why the call is needed can also be answered by the benefits of microservicing.)
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>It returns the number of urgent patients needed, and the confidence interval in a JSON-able format. On error return 500 server error.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: INT-3</strong>
   </td>
   <td><strong>Type: Functional Requirements (Web App Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Displaying the needed appointment slots and its CI. On error, display a pop-up indicating that there is an error in calculation due to the error response message sent from the API.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>This is needed to fulfill the user-requirements.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>It must be legible. On error, display a pop-up indicating that it could not be calculated due the reason indicated in the API’s error emssage.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



#### 
3.2.1.1 HTTP Backend API Module


<table>
  <tr>
   <td><strong>ID: INT-4</strong>
   </td>
   <td><strong>Type: Functional Requirements (Backend API)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Create a default route with path “/&lt;baseurl>/” that returns the basic information of the endpoint
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>It is standard API practice
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Does not need authentication, and returns the endpoint name.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: INT-5</strong>
   </td>
   <td><strong>Type: Functional Requirements (Backend API)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Create the baseroute with a version i.e. “/v1”
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>API versioning helps with release notes
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Must be “v1”
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: INT-6</strong>
   </td>
   <td><strong>Type: Functional Requirements (Backend API)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Create a path that when called as a HTTP GET and passed the following parameters: X% of urgent patients to be serviced within Y weeks, the API invokes a subroutine that returns the number of slots needed for the urgent patients
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Customer requirement
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>GET, takes at least 2 parameters, returns a number in a JSON response. On error return 500 internal server error.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: INT-6</strong>
   </td>
   <td><strong>Type: Functional Requirements (Backend API)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Create a subroutine invoked by the API that returns the number of slots needed (by invoking the simulation model) for urgent patients given X% of urgent patients to be serviced within Y weeks.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Customer requirement
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Takes at least 2 parameters: X, Y, passes these along to the simulation model which returns a number. Return the value returned by the simulation model. On error, throw and log error message.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: INT-7</strong>
   </td>
   <td><strong>Type: Functional Requirements (Backend API)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Create a path that when called as a HTTP POST and passed a CSV file in the body it invokes a subroutine to load the CSV into the database
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Customer requirement
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>POST request that takes the csv and loads it to a subroutine
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



### 
3.2.2 Datastore Module


#### 
3.2.2.1 Data Acquisition


<table>
  <tr>
   <td><strong>ID: DAT-1</strong>
   </td>
   <td><strong>Type: Functional Requirements (Datastore Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Create a subroutine that is invoked by the API to load a CSV into the database
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Will reduce manual labour of loading data to the training model
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Successfully writes all relevant fields of the CSV fields to the database in the same table
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



#### 
3.2.2.2 Data Injection


<table>
  <tr>
   <td><strong>ID: DAT-2</strong>
   </td>
   <td><strong>Type: Functional Requirements (Datastore Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Data from the datastore module is injected into the prediction model
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The prediction model needs to use data to make the predictions
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Data is able to be injected into the prediction model in a format that is compatible with the model
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: DAT-3</strong>
   </td>
   <td><strong>Type: Functional Requirements (Datastore Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Data from the data module is injected into the prediction module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The training module needs to use data to perform prediction and validation
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Data is able to be injected into the training prediction in a format compatible with the training process
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



#### 
3.2.2.3 Data Storing


<table>
  <tr>
   <td><strong>ID: DAT-4</strong>
   </td>
   <td><strong>Type: Functional Requirements (Datastore Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Storage of anonymized patient data
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The patient data used for prediction must be available
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The data is available in the data module or through a HHS provided API
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: DAT-5</strong>
   </td>
   <td><strong>Type: Functional Requirements (Datastore Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Storage of weights for trained prediction models
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Easily accessible model weights will aid in simple deployments
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Trained model weights are accessible in the data module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Low
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: DAT-6</strong>
   </td>
   <td><strong>Type: Functional Requirements (Datastore Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Storage of program logs
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Storing logs enables better debugging when errors occur
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Logs for all Triage services for the past 14 days are accessible in the data module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: DAT-4</strong>
   </td>
   <td><strong>Type: Functional Requirements (Datastore Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Storage of client information
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Information about the client clinics is needed to organize our system
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Data about the clinics is available in the data module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



### 
3.2.3 Prediction Module


#### 
3.2.3.1 Model Training


<table>
  <tr>
   <td><strong>ID: MOD-1</strong>
   </td>
   <td><strong>Type: Functional Requirements (Prediction Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Prediction module can take in data from data module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The training process relies on data stored in the data module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The training data must be injected in a format compatible with the training process
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: MOD-2</strong>
   </td>
   <td><strong>Type: Functional Requirements (Prediction Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Prediction module can perform training (likely SGD) to converge on meaningful results
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The training process must produce useful results
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The model converges on a non-trivial local minimum
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: MOD-3</strong>
   </td>
   <td><strong>Type: Functional Requirements (Prediction Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Prediction module can write the model weights to disk
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The trained model must be stored for use in the model module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The trained model can be written and loaded from disk
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



#### 
3.2.3.2 Model Validation


<table>
  <tr>
   <td><strong>ID: MOD-4</strong>
   </td>
   <td><strong>Type: Functional Requirements (Prediction Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Kristie
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The prediction model can receive validation data from the datastore module
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The validation data is used to validate the model
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The data must be received from the datastore module and be in a format compatible with the validation process
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: MOD-5</strong>
   </td>
   <td><strong>Type: Functional Requirements (Prediction Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The validation data is used to test the performance of the trained model
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The results of training must be measured to ensure convergence has occurred
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>A numeric score of the model is produced, allowing comparisons between versions of the model
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



#### 
3.2.3.3 Model Prediction


<table>
  <tr>
   <td><strong>ID: MOD-7</strong>
   </td>
   <td><strong>Type: Functional Requirements (Prediction Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Data is used to produce a prediction by feeding it into the model
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>The prediction is used to estimate the projected volume of patients
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Data is able to be sent to the prediction model module and a prediction is returned
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



### 
3.2.4 Simulation Module


<table>
  <tr>
   <td><strong>ID: SIM-1</strong>
   </td>
   <td><strong>Type: Functional Requirements (Simulation Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Predictions from the prediction model module is used to predict the number of reserved slots per triage class
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>This result is used by doctors to create a safe schedule
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The prediction is computed
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: SIM-2</strong>
   </td>
   <td><strong>Type: Functional Requirements (Simulation Module)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The simulation module can be backtested on old data and an accuracy can be computed
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>It is important for the accuracy of the results to be verifiable
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The proportion of times the predicted reserve slots are sufficient for the actual patient inflow should be returned
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



## 
3.3 Quality of Service


### 
3.3.1 Performance


<table>
  <tr>
   <td><strong>ID: PRF-1</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Performance)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The web application is able to return results relatively quickly.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Users may require quick schedule estimations to make on the spot decisions.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Users are able to retrieve their results within 10 seconds.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: PRF-2</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Performance)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The model is able to train on data as it becomes available.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Scheduling decisions should be made taking into account recent historical data.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Model training should automatically run on new data as it becomes available.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



### 
3.3.2 Security


<table>
  <tr>
   <td><strong>ID: SEC-1</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Security)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Data is stored securely, such that patient privacy is upheld.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Patient information could become publicly available failing to meet the PIPEDA compliance
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Ensure that data storage follows PIPEDA compliance guidelines.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Very High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: SEC-2</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Security)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Data is cautiously utilized such that patient privacy is upheld.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Patient information could become publicly available failing to meet the PIPEDA compliance
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Ensure that data usage follows PIPEDA compliance guidelines.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Very High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: SEC-3</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Security)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The web application is only accessible by trusted users, which is assumed to be hospital staff.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Users that are not authorized to access the project services should not be able to utilize the web interface.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The web application is not accessible to users that do not work for the hospital (Hamilton Health Sciences)
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Very High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: SEC-4</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Security)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>The API is only accessible by client applications.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Unauthorized users should not be able to access the API to communicate with the model.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The API is not accessible to users that do not work for the hospital (Hamilton Health Sciences)
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



### 
3.3.3 Reliability


<table>
  <tr>
   <td><strong>ID: REL-1</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Reliability)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Sam
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>All model predictions shown to the end users must be quantified with a confidence interval.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>HHS needs to know how accurate the predictions are in order to justify their use for scheduling patients
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>An accuracy measure is always displayed to the user alongside any predictions
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



### 
3.3.4 Availability


<table>
  <tr>
   <td><strong>ID: AVA-1</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Availability)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Backups of model weights should be stored for recovery purposes.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Training the model should be reversible in case of unexpected circumstances.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The model training is reversible in case of emergency.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>High
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: AVA-2</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Availability)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Detailed logs of usage and error occurrence must be stored.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Tracing errors to investigate downtime causes should be possible.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>Logs can be used to identify availability difficulties.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Medium
   </td>
  </tr>
</table>



<table>
  <tr>
   <td><strong>ID: AVA-2</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Availability)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>Services should be monitored with automated restart functionalities.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>Without these features, the system would require constant manual monitoring resulting in longer downtime periods.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>The project is able to monitor and maintain uptime automatically.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Low
   </td>
  </tr>
</table>



## 
3.4 Compliance


<table>
  <tr>
   <td><strong>ID: CMP-1</strong>
   </td>
   <td><strong>Type: Non-Functional Requirements (Compliance)</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Related PUC:</strong> N/A
   </td>
   <td><strong>Originator:</strong> Daniel/Shawn
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Description:</strong></p>

   </td>
   <td>All usage/storage of data must follow PIPEDA data standards.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Rationale:</strong></p>

   </td>
   <td>This is a strict legal requirement.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Fit Criterion:</strong></p>

   </td>
   <td>All data is stored, protected, and analyzed in a manner compliant with PIPEDA guidelines.
   </td>
  </tr>
  <tr>
   <td><p style="text-align: right">
<strong>Priority:</strong></p>

   </td>
   <td>Very High
   </td>
  </tr>
</table>



## 
3.5 Design and Implementation


### 
3.5.1 Installation

Installation of the client application will be a fairly streamlined process given that users will access services through a web interface. This web application must be hosted on hospital servers along with the API that allows it to communicate with the simulation model module to make predictions given the users input data. Once these two sub-modules are successfully installed, trusted users, such as doctors and hospital staff, within the network will be able to access the web application through their computer browsers.

The simulation model module as well as any tools for automating the process of training and validating the model will also be hosted on hospital owned servers. This module will only require an initial setup.

All data and data manipulation services will be hosted on hospital owned servers. Depending on the preferences of the IT department at the McMaster Children’s Hospital, data will either be stored within a dedicated database for the project, or will be pulled from a view of hospital database tables exposing only the necessary data for training / validating the module. This module will only require an initial setup.

We therefore will have every repository manually creating a Dockerfile which can be installed on the corresponding servers. Therefore it is expected of users to have Docker installed on their environment. The entire setup will be documented in GitHub README.md files.


### 
3.5.2 Distribution

Given that users access the services of this project through a web application, distribution of the product will be accomplished through sharing the various user interface endpoints with doctors after the software has been installed and deployed on the hospital network. We will guarantee support for the latest Google Chrome.


### 
3.5.3 Maintainability

Specify attributes of software that relate to the ease of maintenance of the software itself. These may include requirements for certain modularity, interfaces, or complexity limitation. Requirements should not be placed here just because they are thought to be good design practices.

By ensuring separation of concerns through modularity (see the description of our 3 major modules and their sub-modules above), the project should have a clear structure allowing for easier maintainability.

Utilizing CI/CD tools to build automated testing (unit and integration) and deployment pipelines would further increase the ease of developing and maintaining the project. Developers will be able to modify, add, and deploy changes to the project with ease while ensuring the correctness and reliability of the software.

Lastly, by developing clear and concise documentation outlining not only features of the project but also best practices and code standards (ex. Code review practices, testing requirements, etc.) the team should ensure that development methods are formally established to promote efficient modification and maintenance of the codebase.


### 
3.5.4 Reusability

By ensuring clear modularity, we ensure that various aspects of the project are reusable for similar use cases.

The web application connects to a backend API that runs model prediction and analyzes or modifies the results to display them to the user. This communication will be well defined in order to allow for the web application to be easily modified to connect to any API that follows the communication design. 

The data and model modules will communicate to provide the model with data for training and validation. This infrastructure can similarly be reused with various datasets and models given that their communication conforms to the standards set out by these modules.

Given that the schema of data used for training the model and as inputs for the web application will be formalized, the application will be reusable for any clinic that is able to provide this information.


### 
3.5.5 Portability

Portability of the application can be broken down into two categories, front end portability and back end portability. For front end portability, the application should be portable to any machine with a modern compatible web browser and appropriate security permissions by default. This will ensure the application is portable to any hospital staff using the application. This web application should also be easily deployable to any server that is ready to accept a modern front end framework such as angular.  The backend of the application may require specific computing resources such as computing power not easily portable to other platforms other than the selected deployment service. However, the application will be OS-agnostic since it will run on Docker.


### 
3.5.6 Cost


#### 3.5.6.1 Software Development Costs:


<table>
  <tr>
   <td><strong>Factor</strong>
   </td>
   <td><strong>Description</strong>
   </td>
   <td><strong>Cost</strong>
   </td>
  </tr>
  <tr>
   <td>Software Licensing
   </td>
   <td>All software tools used will be free.
   </td>
   <td>None
   </td>
  </tr>
  <tr>
   <td>Development Time
   </td>
   <td>8 Months (5 developers) working at a rate of $0/hour. $0 total development cost.
   </td>
   <td>8 Months of Development Time
<p>
$0
   </td>
  </tr>
</table>



#### 3.5.6.2 Software Maintenance Costs:


<table>
  <tr>
   <td><strong>Factor</strong>
   </td>
   <td><strong>Description</strong>
   </td>
   <td><strong>Cost</strong>
   </td>
  </tr>
  <tr>
   <td>Software Licensing
   </td>
   <td>Given that all software tools are free, no licensing must be renewed. We will use the standard open source Apache 2.0 license
   </td>
   <td>None
   </td>
  </tr>
  <tr>
   <td>Application / Data Monitoring
   </td>
   <td>The application and data will be hosted and monitored from tools on the hospital servers mitigating all costs.
   </td>
   <td>None
   </td>
  </tr>
  <tr>
   <td>Development Time
   </td>
   <td>Dependant on maintenance requirements (i.e. debugging, increasing functionality, etc.)
   </td>
   <td>None
   </td>
  </tr>
</table>



#### 3.5.6.2 IT Infrastructure and Services Costs:


<table>
  <tr>
   <td><strong>Factor</strong>
   </td>
   <td><strong>Details</strong>
   </td>
   <td><strong>Cost</strong>
   </td>
  </tr>
  <tr>
   <td>Application / Data Hosting
   </td>
   <td>All application modules and data will be hosted on hospital servers mitigating all costs.
   </td>
   <td>None
   </td>
  </tr>
  <tr>
   <td>Infrastructure Monitoring
   </td>
   <td>Monitoring of the infrastructure itself will be the responsibility of the IT department of the hospital.
   </td>
   <td>None
   </td>
  </tr>
</table>



### 
3.5.7 Deadlines


<table>
  <tr>
   <td><strong>Due Date</strong>
   </td>
   <td><strong>Milestone</strong>
   </td>
  </tr>
  <tr>
   <td>October 2, 2020
   </td>
   <td>Problem Statement
   </td>
  </tr>
  <tr>
   <td>October 19, 2020
   </td>
   <td>Requirements Document (SRS)
   </td>
  </tr>
  <tr>
   <td>October 30, 2020
   </td>
   <td>Prototype 1
   </td>
  </tr>
  <tr>
   <td>November 13, 2020
   </td>
   <td>Design
   </td>
  </tr>
  <tr>
   <td>February 1, 2021*
   </td>
   <td>Prototype 2
   </td>
  </tr>
  <tr>
   <td>March 1, 2021*
   </td>
   <td>Internal and External Testing
   </td>
  </tr>
  <tr>
   <td>April 1, 2021*
   </td>
   <td>Final Project Submission
   </td>
  </tr>
  <tr>
   <td>April 1, 2021*
   </td>
   <td>Internal Project Demo
   </td>
  </tr>
  <tr>
   <td>April 5, 2021
   </td>
   <td>Faculty of Engineering Capstone Day Materials
   </td>
  </tr>
</table>


*Subject to 4ZP6 schedule changes


# 
4. Verification


## 4.1 Code Verification


### 4.1.1 Hospital Audit

As a health care application, Triage may be subject to a verification process by the McMaster Children’s Hospital to ensure correct behaviour and handling of patient data. The exact scope of a hospital audit still remains to be determined and is tentative on the Hospital’s confirmation. We are actively working with an IT specialist at Hamilton Health Sciences to ensure we are meeting their guidelines and requirements. As part of our planning process to meet deadlines, this overhead is considered.


### 4.1.2 Automated Testing

Automated testing automatically ensures the correctness of software in defined situations. Triage will include “smoke tests” to quickly ensure the software is functioning normally after code changes. It will also include unit and integration testing in areas of high complexity or where bugs are uncovered during development. These tests will be focused primarily on our backend API, models, and simulations.


### 4.1.3 Manual Testing

Manual testing is an important part of development and will be the main method used to verify the Triage web interface. Part of the manual testing process involves the end user. While developers can take steps to ensure the product matches the agreed upon requirements, it is impossible to specify requirements perfectly, making the solicitation of feedback from clients an important part of the development process. By letting clients run through the Triage critical user journeys, we can gauge the usability of the product and take steps to adapt it to work best for them.


## 4.2 Model Verification


### 4.2.1 Triage Result Proportions

The proportion of patients being triaged into each category for a given clinic is critical in determining the number of slots to reserve for the high priority categories. For this reason, we will produce a value with a 95% confidence interval. This interval will be determined statistically. 


### 4.2.2 Patient Volume Predictions

The expected volume of patients in the future will be predicted using historical volumes. To verify the results, we will use k-fold cross validation [4] to estimate the accuracy of the predictions on new data. The results of this verification do not provide an absolute measure of accuracy; they provide a relative accuracy we can use to compare different instances of our predicted models.


### 4.2.3 Reserved Slot Predictions

The reserved slot predictions are created by using the triage result and patient volume predictions. These will be combined with the current wait list in a simulation that can be used to backtest the predictions on historical data. This produces an exact proportion of time the predicted reversed slots are sufficient for the actual patients.


# 
5. Appendixes
