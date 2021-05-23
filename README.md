# Ergasia_1_E18077_Koronakis_Ioannis

#Endpoint 1

Αρχικά μέσα σε μια δομή επιλογής ελέγχουμε άμα υπάρχει χρήστης με το ίδιο username. Αυτό το επιτυγχάνουμε με την εντολή 'users.find({'username': data['username']}).count()==0' μέσα στην συνθήκη την δομής επιλογής, όπου ουσιαστικά γίνεται η αναζήτηση του key('username') γίνεται στην περίπτωση που το username έχει την τιμή που έχει λάβει απο τον χρήστη. Η συνθήκη επαληθεύεται όταν δεν βρεί κανένα username με την τιμή όπου έθεση ο χρήστης χάρη στην εντολή '.count()==0'.

Στην περίπτωση που η συνθήκη της δομής επιλογής είναι αληθής τότε η εντολή 'users.insert_one(data)' εισάγει τα δεδομένα του χρήστη στο collection users και γυρνάει το όνομα του user και το μηνυμα ότι εισήχθε στην βάση με status=200 διαφορετικά γυρνάει ένα μήνυμα αποτυχίας και status=400.

    if users.find({'username': data['username']}).count()==0:
        users.insert_one(data)
        return Response(data['username']+" was added to the MongoDB", status=200 ,mimetype='application/json') 
    else:
        return Response("A user with the given email already exists", status=400 ,mimetype='application/json')
        
  Τα δεδομένα του χρήστη είναι της μορφής:

    {
        "username": "some username", 
        "password": "a very secure password"
    }
#Endpoint 2

Αρχικά στην συνθήκη μίας δομής επιλογής 'users.find_one({'username': data['username'],'password': data['password']})' ψάχνουμε μέσα στο collection 'users' 'username' και 'password' τα οποία έχουν δωθεί απο τον χρήστη 'data'.

Στην περίπτωση που βρεθούν καλούμε την συνάρτηση 'user_uuid=str(create_session(data["username"]))' με παράμετρο το 'username' που εισήγαγε ο χρήστης σε μορφή χαρακτήρων και την αποθυκεύουμε σε μία μεταβλητή 'user_uuid'. Ώς αποτέλσμα  μέσα στην μεταβλητή 'user_uuid' να υπάρχει ο id κωδικός που θα χρησιμοποιηθεί για το authorization. Αυτή η μεταβλητή πήρε την τιμής της απο την συνάρτηση 'create_session()'. Έπειτα εισγχωρεί στην μεταβλητή

    if users.find_one({'username': data['username'],'password': data['password']}): 
        user_uuid=str(create_session(data["username"]))
        res = {"uuid": user_uuid, "username": data['username']}
        return Response(json.dumps(res), status=200,mimetype='application/json') 
    else:
        return Response("Wrong username or password.",status=400,mimetype='application/json') 
#Endpoint 3

#Endpoint 4

#Endpoint 5

#Endpoint 6

#Endpoint 7

#Endpoint 8

#Endpoint 9
