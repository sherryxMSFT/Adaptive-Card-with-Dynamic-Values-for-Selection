# Adaptive Card with Dynamic Values for Selection
This repository shows step-by-step instructions on how to return a list of values dynamically using Power Automate Flow, and display them as a choiceset on an adaptive card in Copilot Studio for users to pick from. 

Imagine you are chatting with a copilot on a hopistal's website, you want to book a doctor appoinment. Copilot retrieves a list of the doctor's availability slots for the next 7 days in the format of "05/09/2024 9-10AM, 5/10/2024 11-12PM, 5/11/2024 3-4PM". Copilot now is waiting for you to enter which slot would you like. Would you like to type the text or pick from a list with one click? I would always prefer the latter. 

One best practice to follow in copilot development is that we don't want the users to type too much. They could make serious typos that derail the whole conversation or simply quit in the middle of it because it is too much to type. 

In this lab, I am sharing how to build a simple adaptive card with choiceset control to display a list of dynamic values for selection. Here we go! Dynamic means the values are different based on different scenarios. It is not a pre-defined static list of value.

1. Prepare the records in Dataverse Account Table. Enter records as follow:

    <img width="584" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/ebfa1be7-f172-40bc-a6f6-02dbe183343b">


2. Create a topic in Copilot Studio with triggers, rename the topic to be "Account Search with Adaptive Card", trigger phrases are "I am looking for a customer.", "I am looking for an account", "search account", "search customer".
   
   <img width="626" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/10fb9860-9849-414f-8978-7066847ec01b">
   
3. Add a Question node asking "What's the name of the customer you're looking for?" and save the user's response into a variable called "organization"

   <img width="164" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/8494b52e-82f7-4938-afc4-ce5072d1b2d1">
   
4. Add a node to call an action to create a flow. This will take you to the Power Automate Flow UI
   <img width="400" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/7c3bae02-2dcf-4803-a669-2fed4c8a6a67">
5. Rename the flow title to "Search Account for ChoiceSet". Add an input parameter named "Organization"
   <img width="581" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/54b03cf4-e8a4-426b-aad8-977e9a761bea">
6. Add an action to trigger "List Rows" action from Dataverse connector. Pick Accounts in the table name field, and enter formula "contains(name, '![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/8796668e-bda1-4fa4-a95e-2f106a794f70)
')", ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/b0b96d64-ecb0-4e41-bce3-1617b81e74d0) is selected from the dynamic content.

   <img width="314" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/9f6089a6-d3f5-4bbd-91b9-c7121c948487">
   
7. Add an action to trigger Parse JSON action. Enter ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/2fedf006-8111-452f-9104-8de910bda599) in the Content field. It is selected from the dynamic content under list rows section. Enter the code below in the Schema field.

   ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/a7b08118-e9fc-4d01-bb58-78cbd3709dfd)

                   {                                                                              
                    "type": "array",
                    "items": {
                        "type": "object",
                        "properties": {
                            "@@search.score": {
                                "type": "number"
                            },
                            "name": {
                                "type": "string"
                            },
                            "address1_city": {
                                "type": "string"
                            },
                            "accountnumber": {
                                "type": "string"
                            }
                        },
                        "required": ["name"]
                    }
                }
8. Add an action to trigger a select under Data Operation connector. Enter ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/35f7b1db-de68-43ec-a6da-f5bf0b8dcc6b) in the From field which could be selected from dynanmic content under Parse JSON section. Map field is as below.
   
    ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/a831e3a3-527a-44ca-81e0-f6ad2274f6ac)

9. Add an action to trigger a compose under Data Operation connector. Enter ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/3941cdc0-3bf9-4fb7-b602-1832546c53f0) in the Inputs field which could be selected from dynamic content under select section.

   ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/45d69fc5-eb0c-4f2c-8424-3d171a719b40)

10. Return the outputs back to Copilot Studio. Add an output parameter, name it "AccountJSON", value is {"items:" ![image](https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/bd645a75-3799-43ac-af09-33c69be559db) }. Outputs is selected from the dynamic content under Compose section.

    <img width="479" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/f382ed87-6fa9-4d3a-a5e0-f2a21d806f7f">

11. Save and test your flow, the final output JSON should look like something in the following. Assume your search keyword is "Fabrikam". 

    {
  "accountjson": "{\"items\": [{\"title\":\"Fabrikam Real Estate - New York\",\"value\":\"FK0001\"},{\"title\":\"Fabrikam LLC - Queens\",\"value\":\"FK0002\"},{\"title\":\"Fabrikam Auto - Bellevue\",\"value\":\"FK0003\"}]}"
    }

12. Go back to the copilot studio and pick Search Account for ChoiceSet flow. Input should be automatically set to the organization variable, save the ourput to AccountJSON vairable of type string.

    <img width="165" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/64bfc984-3f34-49fc-80d6-6b2fca570562">

13. Add a new node to Parse value. Pick "Record" from the Data type dropdown. Edit the schema to match below. Save the parsed value into a variable named "ChoiceSetValue" of type record.

    <img width="167" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/25d77f33-ed0b-4dcc-a34c-65f42ad817b6">
    
    <img width="391" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/a832d868-3fa0-44be-b498-65666b696e64">

14. Add a new node to ask with an adaptive card. Copy the following formula into the "edit formula" window. Two output parameters shuould be created autoamtically. One is actionSubmitId, the other one is selectedaccount. Both are type string.

     <img width="161" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/0d40e440-9297-4efd-a660-6c67bbc99180">

			    {
				    type: "AdaptiveCard",
				    '$schema': "http://adaptivecards.io/schemas/adaptive-card.json",
				    version: "1.0",
				    backgroundImage: {
				    url: "https://images.pexels.com/photos/7130497/pexels-photo-7130497.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1"
			    },
				    body: [{
				    type: "TextBlock",
				    text: "I found following accounts:",
				    wrap: true,
				    weight: "Bolder",
				    color: "Dark"
			    },
			    {
				    type: "Input.ChoiceSet",
				    id: "selectedaccount",
				    style: "expanded",
				    choices: ForAll(Topic.ChoiceSetValue.items,{title: "**"&title&"**", value:title & " - " &value})
			    }
			    ],
			    actions: [
			    {
				    type: "Action.Submit",
				    title: "Submit"
			    }
			    ]
			    }
    
15. Add a new node display a message dispalying "You selected <img width="100" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/e8ecc1e4-54fd-42bf-9835-d23ac97c5c37">" 

	 <img width="164" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/5775e69a-125e-4cda-b5cc-bf646cab74ec">

16. Time to TEST!! Type Search Account to trigger the topic, and then type contoso, expect copilot to present you with a card of options to choose from. Pretty neat! 

    <img width="184" alt="image" src="https://github.com/sherryxMSFT/Adaptive-Card-with-Dynamic-Values-for-Selection/assets/133151558/94cbf04c-82c1-43b4-a43d-b372552443b4">

17. Congratulations, you have finished the lab. If you need source code for reference, you can download the solution file attached and import to any of your Power Platform environment.
18. Drop me an email if you need any help at sherry.qian.xu@gmail.com. Thank you :)


   
	



        
    






    

   



   


   

   
   
   
