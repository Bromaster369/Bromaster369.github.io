---
layout: post
title:      "Active Record Associations"
date:       2021-01-08 19:22:21 +0000
permalink:  active_record_associations
---


An association is a connection between two Active Record models. These associations make operations simpler and easier. For example, in my citizen_report application that includes a model for users and a model for reports. Each user can have many reports and each report belongs to a user.

I will use my project as an example to explain each method. Here’s the schema to have a reference on each attribute for users and reports:

```
ActiveRecord::Schema.define(version: 20201228000300) do

  create_table "reports", force: :cascade do |t|
    t.text     "content"
    t.integer  "user_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.text     "location"
  end

  create_table "users", force: :cascade do |t|
    t.string   "username"
    t.text     "email"
    t.string   "password_digest"
    t.datetime "created_at",      null: false
    t.datetime "updated_at",      null: false
  end

end
```

**The belongs_to Association**

The belongs_to association allow us to set up a connection with another model such that each instance of models belongs_to one instance of the other model. For example, a report belongs to a user.

***.association***

This method returns the associated object, if any. If there’s no associated object, it’ll return nil. 

```
@user = @report.user

=> nil

After we have **built_association ** . @user = @report.user will return: 

=> #<User id: nil, username: "James", email: "james123@gmail.com", password_digest: "$2a$12$YYquOCJo/8iy6QwOCKxKu.VA99ovg8nJ/8Yez9X4Oe9...", created_at: nil, updated_at: nil>

```

***.build_association***

This method returns a new object of the associated type. The object will be instantiated from the passed attributes, and the link through this object’s foreign key will be set, but the associated object will not yet be saved. 

```
@user = @report.build_user(username: "James", email: "james123@gmail.com", password: "james123")

=> #<User id: nil, username: "James", email: "james123@gmail.com", password_digest: "$2a$12$YYquOCJo/8iy6QwOCKxKu.VA99ovg8nJ/8Yez9X4Oe9...", created_at: nil, updated_at: nil>

```

**The has many Association **

The has many Association is a one-to-many connection with another model. For example, a user has many reports. Let's take a look at some of these has many associations:

***.collection***

This method returns a relation of all of the associated objects. If there are none, it’ll return an empty relation. 

```
@reports = @user.reports

=> #<ActiveRecord::Associations::CollectionProxy [#<Report id: 34, content: "I want to report a stray dog", user_id: 24, created_at: "2021-01-08 17:41:56", updated_at: "2021-01-08 17:43:44", location: "123 main st, Queens, NY.">, #<Report id: 35, content: "I want to report a stray cat", user_id: 24, created_at: "2021-01-08 17:41:56", updated_at: "2021-01-08 17:45:09", location: "321 main st, Queens, NY.">, #<Report id: 36, content: nil, user_id: 24, created_at: "2021-01-08 18:17:25", updated_at: "2021-01-08 18:17:25", location: nil>, #<Report id: 37, content: "I want to report a suspicious activity in my schoo...", user_id: 24, created_at: "2021-01-08 18:18:34", updated_at: "2021-01-08 18:18:34", location: nil>, #<Report id: 38, content: "I want to report a suspicious activity in my schoo...", user_id: 24, created_at: "2021-01-08 18:19:58", updated_at: "2021-01-08 18:19:58", location: nil>, #<Report id: 39, content: "I want to report a robery", user_id: 24, created_at: "2021-01-08 18:19:58", updated_at: "2021-01-08 18:19:58", location: nil>, #<Report id: nil, content: nil, user_id: 24, created_at: nil, updated_at: nil, location: nil>, #<Report id: nil, content: nil, user_id: 24, created_at: nil, updated_at: nil, location: nil>, #<Report id: nil, content: nil, user_id: 24, created_at: nil, updated_at: nil, location: nil>, #<Report id: nil, content: "I want to report a stray tiger", user_id: 24, created_at: nil, updated_at: nil, location: nil>, ...]>
```

***.collection.build ***

This method returns a single or array of new objects of the associated type. The object/objects will be instantiated from the passed attributes, and the link through their foreign key will be created, but the associated objects will not yet be saved. 

```

@user.reports.build

=> #<Report id: nil, content: nil, user_id: 24, created_at: nil, updated_at: nil, location: nil>

@user.reports.build(content: "I want to report a stray tiger")

=> #<Report id: nil, content: "I want to report a stray tiger", user_id: 24, created_at: nil, updated_at: nil, location: nil>

@user.reports.build([{content: "I want to report a stray tiger"}, {content: "I want to report a stray lion"}])

=> [#<Report id: nil, content: "I want to report a stray tiger", user_id: 24, created_at: nil, updated_at: nil, location: nil>, #<Report id: nil, content: "I want to report a stray lion", user_id: 24, created_at: nil, updated_at: nil, location: nil>]


@report = @user.reports.build(created_at: @report.created_at, content: "I want to report a stray dog", location: "123 main st, Queens, NY.")

=> #<Report id: nil, content: "I want to report a stray dog", user_id: nil, created_at: nil, updated_at: nil, location: "123 main st, Queens, NY.">


@report = @user.reports.build([{created_at: @report.created_at, content: "I want to report a stray cat", location: "321 main st, Columbia, MD."}, {created_at: @report.created_at, content: "I want to report a noisy neighbor", location: "345 clover st, Owings Mills, MD."}])

[#<Report id: nil, content: "I want to report a stray cat", user_id: nil, created_at: nil, updated_at: nil, location: "321 main st, Columbia, MD.">, #<Report id: nil, content: "I want to report a noisy neighbor", user_id: nil, created_at: nil, updated_at: nil, location: "345 clover st, Owings Mills, MD.">]

```

***.collection.create***

This method returns a single or array of new objects of the associated type. The object/objects will be instantiated from the passed attributes, the link through its foreign key will be created, and once it passes all of the validations specified on the associated model, the associated object will be saved. 

```
@user.reports.create(content: "I want to report a suspicious activity in my school")

=> #<Report id: 37, content: "I want to report a suspicious activity in my schoo...", user_id: 24, created_at: "2021-01-08 18:18:34", updated_at: "2021-01-08 18:18:34", location: nil>

@user.reports.create([{content: "I want to report a suspicious activity in my school"}, {content: "I want to report a robery"}])

=> [#<Report id: 38, content: "I want to report a suspicious activity in my schoo...", user_id: 24, created_at: "2021-01-08 18:19:58", updated_at: "2021-01-08 18:19:58", location: nil>, #<Report id: 39, content: "I want to report a robery", user_id: 24, created_at: "2021-01-08 18:19:58", updated_at: "2021-01-08 18:19:58", location: nil>]


```

**Conclusion**

Active Record associations make our life easier by setting up a connection with another model such that each instance of models belongs_to one instance of the other model or a one-to-many connection with another model. We were able to visualize these methods thanks to our citizen_report app and see how these methods are written and their corresponding return. 




