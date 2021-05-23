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

Στην περίπτωση που βρεθούν καλούμε την συνάρτηση 'user_uuid=str(create_session(data["username"]))' με παράμετρο το 'username' που εισήγαγε ο χρήστης σε μορφή χαρακτήρων και την αποθυκεύουμε σε μία μεταβλητή 'user_uuid'. Ώς αποτέλσμα  μέσα στην μεταβλητή 'user_uuid' να υπάρχει ο id κωδικός που θα χρησιμοποιηθεί για το authorization. Αυτή η μεταβλητή πήρε την τιμής της απο την συνάρτηση 'create_session()'. Έπειτα εισγχωρεί στην μεταβλητή 'res' τα δεδομένα του 'uuid' και του 'username'. Τέλος γυρνάει τα δεδομένα αυτά μέσο της μεταβλητής 'res'.

Διαφορετικά γυρνάει ένα μήνυμα λάθους.

    if users.find_one({'username': data['username'],'password': data['password']}): 
        user_uuid=str(create_session(data["username"]))
        res = {"uuid": user_uuid, "username": data['username']}
        return Response(json.dumps(res), status=200,mimetype='application/json') 
    else:
        return Response("Wrong username or password.",status=400,mimetype='application/json') 
        
Τα δεδομένα του χρήστη δίνονται με την μορφή:

    {
        "username": "some username", 
        "password": "a very secure password"
    }

#Endpoint 3

Αρχικά πέρνουμε σε μια μεταβλητή 'uuid' το αποτέλεσμα της συνάρτησης 'request.headers.get('authorization')' με παράμετρο ''authorization'' όπου ουσιαστικά πέρνει τον κωδικό του authorization που έχει δημιουργηθεί απο το 'login'. Έπειτα μέσα σε μία δομή επιλογής και μέσα απο την συνάρτηση 'is_session_valid(uuid)' με παράμετρο την μεταβλητή 'uuid' επαληθεύει τον 'user'. Στην περίπτωση που δεν επαληθευτεί γυρνάει μήνυμα λάθους. Διαφορετικά ελέγχει σε μία άλλη δομή επιλογής άμα το 'email' που έχει δώσει ο 'user' υπάρχει μέσα στο collection Students. Στην περίπτωση που υπάρχει περνάει σε μία μεταβλητη 'x' τα δεδομένα του φοιτητή που βρέθηκε, έπειτα με βάση αυτή την μεταβλητή εισγχωρεί όλα τα keys και τις τιμές του συγκεκριμένου φοιτητή στο student και τα επιστρέφει διαφορετικά γυρνάει μήνυμα λάθους.

    uuid = request.headers.get('authorization')
    if not(is_session_valid(uuid)):    
        return Response("Student doesn't exist",status=401,mimetype="application/json")
    else:
        if Students.find_one({'email': data['email']}):
            x=Students.find_one({'email': data['email']})
            student ={"_id": str(x["_id"]),"name": x["name"],"email": x["email"],"yearOfBirth": x["yearOfBirth"],"address":x["address"]}
            return Response(json.dumps(student), status=200, mimetype='application/json')
        else:
            return Response("There isn't student with this email", mimetype='application/json')
            
 Τα δεδομένα του χρήστη δίνονται με την μορφή:
 
    {
        "email":"email που θέλουμε να ψάξουμε"
    }

#Endpoint 4

Αρχικά χρησιμοποιούμε την μεταβλητή 'result' για να εισάγουμε τα αποτελέσματα της τοπικής ώρας μέσο της συνάρτησης 'time.localtime()', έπειτα χρησιμοποιούμε την μεταβλητή 'result' για να απομονώσουμε απο την τοπική ώρα την χρονιά και το αποθηκέυουμε σε μορφή 'string' στην μεταβλητή  'Byear'. Έπειτα αποθηκέυουμε στην μεταβλητη uuid το κωδικό του authorization και καλούμε την συνάρτηση is_session_valid(uuid) με παράμετρο uuid. Στην περίπτωση που επαληυθεί δημιουργούμε μία λίστα students διαφορετικά γυρνάμε status=401 και μήνυμα λάθους. Ελέγχουμε άμα υπάρχει στο collection Students φοιτητής στην χρονολογία που μας ενδιαφέρει, δηλαδή την τωρινή - 30. Άμα αυτό ισχυεί μέσα σε ένα βρόγχο επανάλληψης περνάει μέσα στην λίστα κάθε φοιτητή αυτής της χρονιάς. Τέλος γυρνάει την λίστα ενώ άμα δεν ισχύει η συνθήκη γυρίζει μήνυμα λάθους.

    result=time.localtime()
    Byear= result.tm_year - 30
    Byear= str(Byear)
    uuid = request.headers.get('authorization')
    if is_session_valid(uuid):
        students =[]
        if Students.find({"yearOfBirth": Byear}).count()!=0:
            for i in Students.find({"yearOfBirth": Byear}):
                students.append(i)
                return Response(json.dumps(students), status=200, mimetype='application/json')
        else:
            return Response("empty", mimetype='application/json')
    else:
        return Response("error authorization",status=401,mimetype="application/json")

#Endpoint 5

Αρχικά χρησιμοποιούμε την μεταβλητή 'result' για να εισάγουμε τα αποτελέσματα της τοπικής ώρας μέσο της συνάρτησης 'time.localtime()', έπειτα χρησιμοποιούμε την μεταβλητή 'result' για να απομονώσουμε απο την τοπική ώρα την χρονιά και το αποθηκέυουμε σε μορφή 'string' στην μεταβλητή  'Byear'. Έπειτα αποθηκέυουμε στην μεταβλητη uuid το κωδικό του authorization και καλούμε την συνάρτηση is_session_valid(uuid) με παράμετρο uuid. Στην περίπτωση που επαληυθεί δημιουργούμε μία λίστα students διαφορετικά γυρνάμε status=401 και μήνυμα λάθους.Ελέγχουμε άμα υπάρχει στο collection Students φοιτητής στην χρονολογία που μας ενδιαφέρει, δηλαδή την τωρινή - 30 και πιο παλιά χρησιμοποιόντας την εντολή $lte.Άμα αυτό ισχυεί μέσα σε ένα βρόγχο επανάλληψης περνάει μέσα στην λίστα κάθε φοιτητή αυτής της χρονιάς. Τέλος γυρνάει την λίστα ενώ άμα δεν ισχύει η συνθήκη γυρίζει μήνυμα λάθους.

    result=time.localtime()
    Byear= result.tm_year - 30
    Byear= str(Byear)
    uuid = request.headers.get('authorization')
    if is_session_valid(uuid):
        students =[]
        if db.Students.find({"yearOfBirth": {'$lte' :Byear}}).count()!=0:
            for i in db.Students.find({"yearOfBirth": {'$lte' :Byear}}):
                 students.append(i)
            return Response(json.dumps(students), status=200, mimetype='application/json')
        else:
            return Response("empty", mimetype='application/json')
    else:
        return Response("error authorization",status=401,mimetype="application/json")

#Endpoint 6

Αρχικά πέρνουμε σε μια μεταβλητή 'uuid' το αποτέλεσμα της συνάρτησης 'request.headers.get('authorization')' με παράμετρο ''authorization'' όπου ουσιαστικά πέρνει τον κωδικό του authorization που έχει δημιουργηθεί απο το 'login'. Έπειτα μέσα σε μία δομή επιλογής και μέσα απο την συνάρτηση 'is_session_valid(uuid)' με παράμετρο την μεταβλητή 'uuid' επαληθεύει τον 'user'. Στην περίπτωση που δεν επαληθευτεί γυρνάει μήνυμα λάθους.

    uuid = request.headers.get('authorization')
    if is_session_valid(uuid):
        if Students.find_one({'email': data['email']}):
            iterable = Students.find_one({'email': data['email']})
            if iterable == db.Students.find_one({'email': data['email']}):
                iterable = db.Students.find_one({'email': data['email']})
                student = {"name": iterable["name"]}
                return Response(json.dumps(student), status=200, mimetype='application/json')
            else:
                return Response("There isn't student's address",mimetype="application/json")
        else:
            return Response("There isn't student's email",mimetype="application/json")
    else:
        return Response("error authorization",status=401,mimetype="application/json")
        
Τα δεδομένα του χρήστη δίνονται με την μορφή:
 
    {
        "email":"email που θέλουμε να ψάξουμε"
    }

#Endpoint 7

Αρχικά πέρνουμε σε μια μεταβλητή 'uuid' το αποτέλεσμα της συνάρτησης 'request.headers.get('authorization')' με παράμετρο ''authorization'' όπου ουσιαστικά πέρνει τον κωδικό του authorization που έχει δημιουργηθεί απο το 'login'. Έπειτα μέσα σε μία δομή επιλογής και μέσα απο την συνάρτηση 'is_session_valid(uuid)' με παράμετρο την μεταβλητή 'uuid' επαληθεύει τον 'user'. Στην περίπτωση που δεν επαληθευτεί γυρνάει μήνυμα λάθους.

    uuid = request.headers.get('authorization')
    if is_session_valid(uuid):
        if Students.find_one({'email': data['email']}):
            name = db.Students.find_one({'email': data['email']})
            db.Students.delete_one({'email': data['email']})
            msg = name['name']," was deleted."
            return Response(msg, status=200, mimetype='application/json')
        else: 
            msg = "Nobody deleted."
        return Response(msg, status=200, mimetype='application/json')
    else:
        return Response("error authorization", status=401, mimetype='application/json')

Τα δεδομένα του χρήστη δίνονται με την μορφή:
 
    {
        "email":"email που θέλουμε να ψάξουμε"
    }

#Endpoint 8

Αρχικά πέρνουμε σε μια μεταβλητή 'uuid' το αποτέλεσμα της συνάρτησης 'request.headers.get('authorization')' με παράμετρο ''authorization'' όπου ουσιαστικά πέρνει τον κωδικό του authorization που έχει δημιουργηθεί απο το 'login'. Έπειτα μέσα σε μία δομή επιλογής και μέσα απο την συνάρτηση 'is_session_valid(uuid)' με παράμετρο την μεταβλητή 'uuid' επαληθεύει τον 'user'. Στην περίπτωση που δεν επαληθευτεί γυρνάει μήνυμα λάθους.

    uuid = request.headers.get('authorization')
    if is_session_valid(uuid):
        if Students.find_one({'email': data["email"]}):
           # courses.insert_one({'email': data["email"], 'courses':data["courses"]})
           db.Students.find_and_modify({'email': data["email"]},{"$set" :{'email': data["email"], 'courses':data["courses"]}})
        else:
            msg="There isn't this email"
            return Response(msg, mimetype='application/json')
        msg="authorization succeed"
        return Response(msg, status=200, mimetype='application/json')
    else:
        msg="authorization failed"
        return Response(msg, status=401, mimetype='application/json')

Τα δεδομένα του χρήστη δίνονται με την μορφή:

        {
            email: "an email",
            courses: [
                {'course 1': 10, 
                {'course 2': 3 }, 
                {'course 3': 8},
                ...
            ]
        } 
#Endpoint 9

Αρχικά πέρνουμε σε μια μεταβλητή 'uuid' το αποτέλεσμα της συνάρτησης 'request.headers.get('authorization')' με παράμετρο ''authorization'' όπου ουσιαστικά πέρνει τον κωδικό του authorization που έχει δημιουργηθεί απο το 'login'. Έπειτα μέσα σε μία δομή επιλογής και μέσα απο την συνάρτηση 'is_session_valid(uuid)' με παράμετρο την μεταβλητή 'uuid' επαληθεύει τον 'user'. Στην περίπτωση που δεν επαληθευτεί γυρνάει μήνυμα λάθους.

    uuid = request.headers.get('authorization')
    if is_session_valid(uuid):
        if Students.find_one({'email': data["email"]}):
            student={Students.find_one({'email': data["email"]},{"courses":{'$gte':5}})}
            student=student["courses"]
            name = db.Students.find_one({'email': data['email']})
            msg= name['name'],student
            return Response(msg, status=200, mimetype='application/json')
        else:
            msg="There isn't email or there aren't passed courses"
            return Response(msg,mimetype='application/json')
    else:
        msg="authorization failed"
        return Response(msg, status=401, mimetype='application/json')
        
Τα δεδομένα του χρήστη δίνονται με την μορφή:
 
    {
        "email":"email που θέλουμε να ψάξουμε"
    }
