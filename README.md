# GraphQL_JWT-Authentication
All about Authentication using GraphQl


Pick Specific Data

https://docs.github.com/en/graphql/overview/explorer

Python  -> Graphene

Type/objects -Sort of like serializers
Schema - container of all  graphql types
Resolver - the func that is exec to give back the data for a single attribute of a type/object
Query - what u use to get or set data
Mutations - Subset of queries that is used to change or modify

Int: A signed 32‐bit integer.
Float: A signed double-precision floating-point value.
String: A UTF‐8 character sequence.
Boolean: true or false.
ID: The ID scalar type represents a unique identifier, often used to refetch an object or as the key for a cache. The ID type is serialized in the same way as a String; however, defining it as an ID signifies that it is not intended to be human‐readable.
Graphql exposes a single endpoint
A query Language for api
Fetching data with queries

https://pypi.org/project/graphene-django/

pip install graphene-django
settings-> apps
'graphene_django',

Urls 
from graphene_django.views import GraphQLView
from books.schema import schema
 
urlpatterns = [
   path("graphql/", GraphQLView.as_view(graphiql=True, schema=schema)),
]


At Schema
class Query(graphene.ObjectType):
   all_books = graphene.List(BooksType)
 
   def resolve_all_books(root, info):
       return Books.objects.all()



Important parts






Passing Arguments Example



CRUD operations

class CategoryMutation(graphene.Mutation):
   class Arguments:
       name = graphene.String(required=True)
   category = graphene.Field(CategoryType)
 
   @classmethod
   def mutate(cls, root, info, name):
       category = Category(name=name)
       category.save()
       return CategoryMutation(category=category)
 
class Mutation(graphene.ObjectType):
   update_category = CategoryMutation.Field()
 
schema = graphene.Schema(query=Query, mutation=Mutation)



It will save the new category name.


We can set the dropdown value like this

Dropdown is select by id
Everything we get is an object.
Quiz = obj
Quiz.category_id = id


class QuizzesMutation(graphene.Mutation):  
 class Arguments:
       title = graphene.String(required=True)
       id = graphene.Int(required=True)
 
   quiz = graphene.Field(QuizzesType)
 
   @classmethod
   def mutate(cls, root, info, title, id):
       quiz = Quizzes(title=title)
       quiz.category_id = id
       # category = Category(name=name)
 
       print(quiz)
       import pdb ; pdb.set_trace()
       quiz.save()
       return QuizzesMutation(quiz=quiz)




Delete

What i trying to make is i created a deleted_id on arguments.
Its not a mandatory argument.
Default is 0
If it is not 0 check the object with the given id if it get.
We can delete.



class QuizzesMutation(graphene.Mutation):
   class Arguments:
       title = graphene.String(required=True)
       id = graphene.Int(required=True)
       delete_id = graphene.Int()
 
   quiz = graphene.Field(QuizzesType)
 
   @classmethod
   def mutate(cls, root, info, title, id, delete_id=0):
       if delete_id != 0:
           quiz = Quizzes.objects.get(id=delete_id)
           import pdb ; pdb.set_trace()
           quiz.delete()
           print(quiz)
           if quiz.id is None:
               return
           import pdb ; pdb.set_trace()
          
           # return f'Deleted {delete_id}'
       else:
           quiz = Quizzes(title=title)
           quiz.category_id = id
           # category = Category(name=name)
           quiz.save()
           return QuizzesMutation(quiz=quiz)



JWT AUTHENTICATION


Login
Logout
Authentication
Signup with email confirmation
Change password
Forgotten password -> email
Delete account
Update account



pip3 install django
django-admin startproject core
python3 manage.py startapp users
pip3 install graphene-django
pip3 install django-graphql-jwt
pip3 freeze > requirements.txt
pip3 install django-graphql-auth

INSTALLED_APPS = [
   'django.contrib.admin',
   'django.contrib.auth',
   'django.contrib.contenttypes',
   'django.contrib.sessions',
   'django.contrib.messages',
   'django.contrib.staticfiles',
   'users.apps.UsersConfig',
   'graphene_django',
   'graphql_jwt.refresh_token.apps.RefreshTokenConfig',
   "graphql_auth"
]
 

Add JSONWebTokenMiddleware middleware to your GRAPHENE settings and Add JSONWebTokenBackend backend to your AUTHENTICATION_BACKENDS:

GRAPHENE = {
   'SCHEMA' : 'users.schema.schema'
   'MIDDLEWARE': [
       'graphql_jwt.middleware.JSONWebTokenMiddleware',
   ],
}
 
AUTHENTICATION_BACKENDS = [
   'graphql_jwt.backends.JSONWebTokenBackend',
   'django.contrib.auth.backends.ModelBackend',
]
 


 Django-graphql-auth version and things will give a lot of headache.
Version installed python3.9

If we install one by one graphql dependencies problem will happen
Only way to overcome is after activating virtual environment 

pip install django-graphql-auth
It will install all .include django

https://django-graphql-auth.readthedocs.io/en/latest/installation/

Edges: help the take all the detail
Node: we get the defined info.




MeQuery example
For front end developer to get the current data of user.



Register the user 
register = mutations.Register.Field()



Verified the user by usertoken


After verification you can use Login
verify_account = mutations.VerifyAccount.Field()


Update
update_account = mutations.UpdateAccount.Field()






update_account = mutations.UpdateAccount.Field()


POSTMAN


If we get unauthenticated message go to Headers 

This way we can get authtoken


Set like this in Headers


After set the body and send as Post

Success = True ,then its change the firstname

Resend Confirm Email

class AuthMutation(graphene.ObjectType):
   register = mutations.Register.Field()
   verify_account = mutations.VerifyAccount.Field()
   # login
   token_auth = mutations.ObtainJSONWebToken.Field()
   update_account = mutations.UpdateAccount.Field()
   resend_activation_email = mutations.ResendActivationEmail.Field()
 
class Query(UserQuery, MeQuery, graphene.ObjectType):
   pass
 
class Mutation(AuthMutation, graphene.ObjectType):
   pass
 
schema = graphene.Schema(query=Query, mutation=Mutation)
 
 

It ll look if the user email is in the database and its there it will send the email.


mutation{
  resendActivationEmail(email: "loot@gmail.com"){
    errors
  }
}


Forgotten Password(send password reset email)
class AuthMutation(graphene.ObjectType):
   register = mutations.Register.Field()
   verify_account = mutations.VerifyAccount.Field()
   # login
   token_auth = mutations.ObtainJSONWebToken.Field()
   update_account = mutations.UpdateAccount.Field()
   resend_activation_email = mutations.ResendActivationEmail.Field()
   send_password_reset_email = mutations.SendPasswordResetEmail.Field()

In Graphql 

mutation{
  sendPasswordResetEmail(email:"test1@gmail.com"){
    success
    errors
  }
}




Put password reset field in schema

password_reset = mutations.PasswordReset.Field()

Take the token and use like this 



All details : Reference(https://django-graphql-auth.readthedocs.io/en/latest/)
youtube(https://www.youtube.com/watch?v=pyV2_F9wlk8)

