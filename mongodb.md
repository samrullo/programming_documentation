# Mongodb

## Installing mongodb on Mac

You download latest binary of mongodb and add it to the PATH in your ~/.bash_profile or ~/.zsh_profile

```bash
export PATH=$PATH:/usr/local/mongodb/bin
```

To check if you have mongodb installed:

```bash
mongo --version
```

To launch mongodb service

```bash
mongod
```

You will need to make ```/data/db``` directory as root and change directory owner to yourself

```bash
sudo mkdir -p /data/db
sudo chown -R samrullo:samrullo /data/db
```

Sometimes Mac doesn't allow you to make /data/db directory, saying it is read only file system

In which case you can launch mongodb daemon by passing ```-dbPath```

```bash
mongod -dbpath /usr/local/data/db
```

Then you can connect to mongodb from shell and start running queries

```bash
mongo
```

## Navigating mongodb

To view databases

```bash
>show databases
```

To use a specific database

```bash
>use usersDB
```

Once you use a certain database you can start querying it.

To fetch all documents in a collection

```bash
>db.users.find()
```

To query a specific record

```bash
>db.users.find({name:"samrullo"})
```

To query one record

```bash
>db.users.findOne({email:"samrullo@sam.com"})
```

To insert one record

```bash
>db.users.insertOne({name:"myname",email:"myemail",company:"mycompany"})
```

To insert many records

```bash
>db.users.insertMany([{name:"myname",email:"myemail",company:"mycompany"},{name:"anothername",email:"anotheremail",company:"anothercompany"}])
```

To update one record

```bash
>db.users.updateOne({name:"myname"},{$set:{name:"mynewname"}})
```

To delete a record

```bash
>db.users.deleteOne({name:"myname"})
```

