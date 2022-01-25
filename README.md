# Google Next 2018 San Francisco (24-26th July)

## Overview
Demo of the American Financing use case integration with Google CC API and Dialogflow. Shows Bot interaction with a customer answering questions, hand off to a Live Agent and an Agent Assist web page with information about the Customer and the Bot conversation.

### Demo Video
[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/ctfh9JSJBM0/0.jpg)](https://www.youtube.com/watch?v=ctfh9JSJBM0)

### How it works
Calls come in through different channels and different numbers. We get leads in through:

* Main Line (303) XXX-XXXX and (800) XXX-XXXX
* Vanity Numbers for specific endorsers
* Online Leads (these call out through Velocify so we are trying to contact them within a couple minutes of the lead coming in)

The Customer Service Rep (CSR) is the first to make contact with the potential borrower. The CSR is only allowed to ask certain questions before transferring to one of our Mortgage Consultants (MC’s). The most identifying questions asked are:

* Is this a Purchase or Refinance?
* What state is the property located?

There are other questions as well such as how you heard about us:

* Which Source?
* Which Endorser?

Based on how the potential borrower answers we route the call to a blast group. Our numbers are divided by Licensed Mortgage Consultants in specific states and by whether they are refinance or purchase loans.

From here the MC will get the call and ask the qualifying questions to the borrower with the ultimate goal being to get the data needed to make a complete application and pull credit to qualify for a loan. The MC will then set the appointment to call back the borrower or have them come to the office where they will bring us their documentation for underwriting and we get their disclosure documents taken care of. At this point we will have a Sign Off for the loan.


## Services Used
- Google's Contact Center AI APIs
- Dialogflow ML engine
- Nexmo's Comms Router (skill based routing app)
- Nexmo's Voice APIs

## Documentation
Complete documentation can be found here: [Google Docs](https://docs.google.com/document/d/1joa__7w1CWHCygXKD8Qb2Cgr5D0o7N23Ff6i869hKOA/edit#)
![Google Next demo flow diagram](google_ccai_demo_flow.png)

### Components for build
* Vonage Voice API:
  * Nexmo Voice Application
* NCCO
* Vonage Voice virtual number (required for use with API):
  * Proxy may be required to forward calls to existing AF Contact Center numbers
* Open sourced Contact Center App (extends current demo app for Google Next):
  * Manages Voice call, transfers and connections with either Google CC AI or inContact for routing to live Agent
  * Integrates with Salesforce CRM, this could also be achieved through gUnify (Integration Suite) for simplicity this integrated with Contact Center App
* Open sourced Agent Assist App:
  * Agent Assist could also be integrated with gUnify (Integration Suite) for simplicity this is currently a stand alone application
  * Includes content management for modelling AI e.g. upload, run modeler, etc.
* Google Cloud Platform account to manage hosting of applications:
  * Contact Center App
  * Agent Assist App
  * NCCO (answer_url, event_url)
  * Dialogflow webhooks


## Integrated Solution
How to implement integration with Nexmo APIs.
![Nexmo API integration](google_ccai_nexmo_api_integration.png)

## Future State Architecture
Possible end-to-end architecture of a contact center solution.

### Potential Architecture
![Platform architecture](google_ccai_platform_architecture.png)

### Potential Workflow
1. PSTN call initiated from phone number displayed on marketing material or website.
2. Call gets routed through proxy and conference call is created in order to connect Customer with Google AI bot via the Contact Center Call App.
3. Contact Center Call App (extends current demo app) connects with Salesforce via API to map user phone number with existing customer information, returns key customer data to augment bot interaction “Hi Bryan, thanks for calling American Financing...”.
4. Contact Center Call App connects with Dialogflow to manage intents in order to enable AI bot to respond to Customer questions and capture Customer information.
5. Google CC AI bot interacts with Customer to respond to questions.
6. Contact Center Call App passes metadata to inContact via `SENDSIPINFO` to help enable routing to correct Agent. https://help.incontact.com/spring18/Content/MAX/MAXOverview.htm
7. Contact Center Call App creates new conference to connect Customer with live Agent based on metadata routing.
8. Live Agent connects to Agent Assist screen in the Contact Center UI. Contact Center App pulls Customer call information (name, product, state, etc.), transcript and suggestions through Contact Center Call App and Google CC AI.
9. Contact Center App saves key call information back into Salesforce e.g. transcription thread and files (url), top suggestions (hit values from sentiment analysis), any user information updates (name, product, state, etc.).
10. Manage modeling data in the Agent Assist screen Contact Center UI in order to save back in GCP and run model reprocessing rather than having to do this manually from development environment.
