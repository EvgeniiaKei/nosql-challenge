# nosql-challenge
Module 12

![image](https://github.com/user-attachments/assets/b05cd129-f475-47ee-93af-fa9f1a5ba602)


# Instructions
The UK Food Standards Agency evaluates various establishments across the United Kingdom, and gives them a food hygiene rating. You've been contracted by the editors of a food magazine, Eat Safe, Love, to evaluate some of the ratings data in order to help their journalists and food critics decide where to focus future articles.

##  Part 1: Database and Jupyter Notebook Set Up
Use NoSQL_setup_starter.ipynb for this section of the challenge.
 1. Import the data provided in the establishments.json file from your Terminal. Name the database uk_food and the collection establishments. Copy the text you used to import your data from
    your Terminal to a markdown cell in your notebook.

               mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json

 2. Within your notebook, import the libraries you need: PyMongo and Pretty Print (pprint).

              # Import dependencies
              from pymongo import MongoClient
              from pprint import pprint
            
  3. Create an instance of the Mongo Client.

               mongo = MongoClient(port=27017)

  5. Confirm that you created the database and loaded the data properly:
     - List the databases you have in MongoDB. Confirm that uk_food is listed.
     - List the collection(s) in the database to ensure that establishments is there.
     - Find and display one document in the establishments collection using find_one and display with pprint.
      
  6. Assign the establishments collection to a variable to prepare the collection for use.

               establishments = db['establishments']

## Part 2: Update the Database
Use NoSQL_setup_starter.ipynb for this section of the challenge.
1. The magazine editors have some requested modifications for the database before you can perform any queries or analysis for them. Make the following changes to the establishments collection:
   - An exciting new halal restaurant just opened in Greenwich, but hasn't been rated yet. The magazine has asked you to include it in your analysis. Add the following information to the database:
  
              # Create a dictionary for the new restaurant data
              penang_dict = {"BusinessName":"Penang Flavours",
               "BusinessType":"Restaurant/Cafe/Canteen",
               "BusinessTypeID":"",
               "AddressLine1":"Penang Flavours",
               "AddressLine2":"146A Plumstead Rd",
               "AddressLine3":"London",
               "AddressLine4":"",
               "PostCode":"SE18 7DY",
               "Phone":"",
               "LocalAuthorityCode":"511",
               "LocalAuthorityName":"Greenwich",
               "LocalAuthorityWebSite":"http://www.royalgreenwich.gov.uk",
               "LocalAuthorityEmailAddress":"health@royalgreenwich.gov.uk",
               "scores":{"Hygiene":"",
                         "Structural":"",
                         "ConfidenceInManagement":""},
               "SchemeType":"FHRS",
               "geocode":{"longitude":"0.08384000",
                          "latitude":"51.49014200"},
               "RightToReply":"",
               "Distance":4623.9723280747176,
               "NewRatingPending":True}

2. Find the BusinessTypeID for "Restaurant/Cafe/Canteen" and return only the BusinessTypeID and BusinessType fields.
3. Update the new restaurant with the BusinessTypeID you found.
4. The magazine is not interested in any establishments in Dover, so check how many documents contain the Dover Local Authority. Then, remove any establishments within the Dover Local
   Authority from the database, and check the number of documents to ensure they were deleted.
5. Some of the number values are stored as strings, when they should be stored as numbers.
6. Use update_many to convert latitude and longitude to decimal numbers.

               # Change the data type from String to Decimal for longitude and latitude
               establishments.update_many({}, [{'$set': {'geocode.longitude': {'$toDecimal': '$geocode.longitude'},
                                                'geocode.latitude': {'$toDecimal': '$geocode.latitude'}
                                         }
                                }
                               ]
                          )
   
7. Use update_many to convert RatingValue to integer numbers.

                    # Change the data type from String to Integer for RatingValue
                  convert_rating = establishments.update_many({}, [{"$set": 
                                                                   {"RatingValue": 
                                                                    {"$toInt":"$RatingValue"}}}])
   

# Part 3: Exploratory Analysis
Eat Safe, Love has specific questions they want you to answer, which will help them find the locations they wish to visit and avoid.

Use NoSQL_analysis_starter.ipynb for this section of the challenge.
Some notes to be aware of while you are exploring the dataset:
 - RatingValue refers to the overall rating decided by the Food Authority and ranges from 1-5. The higher the value, the better the rating.
Note: This field also includes non-numeric values such as 'Pass', where 'Pass' means that the establishment passed their inspection but isn't given a number rating. We will coerce non-numeric values to nulls during the database setup before converting ratings to integers.

 - The scores for Hygiene, Structural, and ConfidenceInManagement work in reverse. This means, the higher the value, the worse the establishment is in these areas.

                  # Find the establishments with a hygiene score of 20
                    query = {"scores.Hygiene": {"$eq":20}}

                  # Use count_documents to display the number of documents in the result
                  print("Number of documents in result: ", establishments.count_documents(query))
                  print("-------")
                  # Display the first document in the results using pprint
                  results = establishments.find(query)
                  pprint("First Document:")
                  pprint(results[0])

Use the following questions to explore the database, and find the answers, so you can provide them to the magazine editors.
Unless otherwise stated, for each question:

   - Use count_documents to display the number of documents contained in the result.
   - Display the first document in the results using pprint.
   - Convert the result to a Pandas DataFrame, print the number of rows in the DataFrame, and display the first 10 rows.


1. Which establishments have a hygiene score equal to 20?

              Number of documents in result:  41
              -------
              'First Document:'
              {'AddressLine1': '5-6 Southfields Road',
              'AddressLine2': 'Eastbourne',
              'AddressLine3': 'East Sussex',
              'AddressLine4': '',
              'BusinessName': 'The Chase Rest Home',
              'BusinessType': 'Caring Premises',
              'BusinessTypeID': 5,
              'ChangesByServerID': 0,
              'Distance': 4613.888288172291,
              'FHRSID': 110681,
              'LocalAuthorityBusinessID': '4029',
              'LocalAuthorityCode': '102',
              'LocalAuthorityEmailAddress': 'Customerfirst@eastbourne.gov.uk',
              'LocalAuthorityName': 'Eastbourne',
              'LocalAuthorityWebSite': 'http://www.eastbourne.gov.uk/foodratings',
              'NewRatingPending': False,
              'Phone': '',
              'PostCode': 'BN21 1BU',
              'RatingDate': '2021-09-23T00:00:00',
              'RatingKey': 'fhrs_0_en-gb',
              'RatingValue': 0,
              'RightToReply': '',
              'SchemeType': 'FHRS',
              '_id': ObjectId('66b76056b168aa9536fc37de'),
              'geocode': {'latitude': Decimal128('50.769705'),
              'longitude': Decimal128('0.27694')},
               'links': [{'href': 'https://api.ratings.food.gov.uk/establishments/110681',
                          'rel': 'self'}],
               'meta': {'dataSource': None,
               'extractDate': '0001-01-01T00:00:00',
               'itemCount': 0,
               'pageNumber': 0,
               'pageSize': 0,
               'returncode': None,
               'totalCount': 0,
               'totalPages': 0},
               'scores': {'ConfidenceInManagement': 20, 'Hygiene': 20, 'Structural': 20}}

3. Which establishments in London have a RatingValue greater than or equal to 4?
Hint: The London Local Authority has a longer name than "London" so you will need to use $regex as part of your search.

              Number of documents in result:  33
              --------
              {'AddressLine1': 'Oak Apple Farm Building 103 Sheernes Docks'...**'BusinessName': "Charlie's",**}

5. What are the top 5 establishments with a RatingValue of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?
Hint: You will need to compare the geocode to find the nearest locations. Search within 0.01 degree on either side of the latitude and longitude.

                  Result:
                  1. [{'AddressLine1': '101 Plumstead High Street'....**'BusinessName': 'Lucky Food & Wine',**},
                  2. {'AddressLine1': '112 Plumstead High Street'....**'BusinessName': 'Fineway Cash & Carry'**},
                  3. {'AddressLine1': '104 Plumstead High Street'...** 'BusinessName': 'Everest Stores Ltd',**},
                  4. {'AddressLine1': '102 Plumstead High Street'....** 'BusinessName': 'Premier Express',**},
                  5.  {'AddressLine1': '152 Plumstead High Street'...**'BusinessName': 'TIWA N TIWA African Restaurant Ltd',**}]

7. How many establishments in each Local Authority area have a hygiene score of 0? Sort the results from highest to lowest, and print out the top ten local authority areas.
Hint: You will need to use the aggregation method to answer this.

<img width="244" alt="image" src="https://github.com/user-attachments/assets/4661d25c-e463-4b16-bac8-e19e8c99de60">


 




     
