## Console Lab

For this lab, we'd like you to strengthen your Rails console skills. This lab is going to be very familiar to the SQL lab, where we'll ask you to create a model and then write out the console commands you would use to execute these queries

### To Start

1. Create a model called Student, that has a first_name, last_name and age

Macintosh-00264a0fbef6:console_lab rasbabbo$ rails generate model Student first_name:string last_name:string age:integer
Warning: Running `gem pristine --all` to regenerate your installed gemspecs (and deleting then reinstalling your bundle if you use bundle --path) will improve the startup performance of Spring.
      invoke  active_record
      create    db/migrate/20140819231837_create_students.rb
      create    app/models/student.rb

2. Don't forget to run your migrations

### Tasks to create

1. Using the new/save syntax, create a student, first and last name and an age 

  SQL (1.4ms)  INSERT INTO "students" ("age", "created_at", "first_name", "last_name", "updated_at") VALUES ($1, $2, $3, $4, $5) RETURNING "id"  [["age", 23], ["created_at", "2014-08-19 23:33:34.983006"], ["first_name", "Matt"], ["last_name", "Chavez"], ["updated_at", "2014-08-19 23:33:34.983006"]]
   (0.9ms)  COMMIT

2. Save the student to the database

[5] pry(main)> me.save

3. Using the find/set/save syntax update the student's first name to 

[1] pry(main)> student = Student.find(1)
  Student Load (0.9ms)  SELECT  "students".* FROM "students"  WHERE "students"."id" = $1 LIMIT 1  [["id", 1]]
=> #<Student id: 1, first_name: "Matt", last_name: "Chavez", age: 23, created_at: "2014-08-19 23:33:34", updated_at: "2014-08-19 23:33:34">
[2] pry(main)> student.first_name="Taco"
=> "Taco"
[3] pry(main)> student
=> #<Student id: 1, first_name: "Taco", last_name: "Chavez", age: 23, created_at: "2014-08-19 23:33:34", updated_at: "2014-08-19 23:33:34">
[4] pry(main)> student.save
   (0.5ms)  BEGIN
  SQL (1.2ms)  UPDATE "students" SET "first_name" = $1, "updated_at" = $2 WHERE "students"."id" = 1  [["first_name", "Taco"], ["updated_at", "2014-08-19 23:39:34.090540"]]
   (0.9ms)  COMMIT

4. Delete the student (where first_name is taco)

[10] pry(main)> student.destroy
   (0.4ms)  BEGIN
  SQL (0.4ms)  DELETE FROM "students" WHERE "students"."id" = $1  [["id", 1]]
   (0.8ms)  COMMIT

5. Validate that every Student's last name is unique
6. Validate that every Student has a first and last name that is longer than 4 characters
7. Validate that every first and last name cannot be empty
7. Combine all of these individual validations into one validation (using validate and a hash) 

class Student < ActiveRecord::Base
	validates :first_name, :last_name, 

										:presence => true, 
										:length => {minimum: 4}
										:last_name, :uniqueness => true
										
end

8. Using the create syntax create a student named John Doe who is 33 years old

[11] pry(main)> student = Student.create(:first_name=> "John", :last_name=>"Doe", :age=>33)
   (0.4ms)  BEGIN
  Student Exists (0.8ms)  SELECT  1 AS one FROM "students"  WHERE "students"."last_name" = 'Doe' LIMIT 1
   (0.4ms)  ROLLBACK

9. Show if this new student entry is valid
10. Show the number of errors for this student instance

ActiveRecord::RecordInvalid: Validation failed: First name is too short (minimum is 4 characters), Last name is too short (minimum is 4 characters)
from /Library/Ruby/Gems/2.0.0/gems/activerecord-4.1.3/lib/active_record/validations.rb:57:in `save!'

11. In one command, Change John Doe's name to Jonathan Doesmith
[19] pry(main)> student.update_attributes(:first_name =>"Johnathen", :last_name => "Doesmith")
   (1.3ms)  BEGIN
  Student Exists (0.8ms)  SELECT  1 AS one FROM "students"  WHERE "students"."last_name" = 'Doesmith' LIMIT 1
  SQL (0.6ms)  INSERT INTO "students" ("age", "created_at", "first_name", "last_name", "updated_at") VALUES ($1, $2, $3, $4, $5) RETURNING "id"  [["age", 33], ["created_at", "2014-08-20 01:00:39.978152"], ["first_name", "Johnathen"], ["last_name", "Doesmith"], ["updated_at", "2014-08-20 01:00:39.978152"]]
   (25.8ms)  COMMIT

12. Clear the errors array
13. Save Jonathan Doesmith

[21] pry(main)> student.save
   (0.4ms)  BEGIN
  Student Exists (37.6ms)  SELECT  1 AS one FROM "students"  WHERE ("students"."last_name" = 'Doesmith' AND "students"."id" != 5) LIMIT 1
   (0.3ms)  COMMIT
=> true

15. Find all of the Students

[24] pry(main)> Student.all
  Student Load (0.6ms)  SELECT "students".* FROM "students"
=> [#<Student id: 2, first_name: "Marlon", last_name: "Brando", age: 34, created_at: "2014-08-20 00:21:37", updated_at: "2014-08-20 00:21:37">,
 #<Student id: 3, first_name: "Bobby", last_name: "Balabushka", age: 34, created_at: "2014-08-20 00:22:00", updated_at: "2014-08-20 00:22:00">,
 #<Student id: 4, first_name: "Engelbert", last_name: "Humperdink", age: 76, created_at: "2014-08-20 00:22:43", updated_at: "2014-08-20 00:22:43">,
 #<Student id: 5, first_name: "Johnathen", last_name: "Doesmith", age: 33, created_at: "2014-08-20 01:00:39", updated_at: "2014-08-20 01:00:39">]

16. Find the student with an ID of 128 and if it does not exist, make sure it returns nil and not an error

[25] pry(main)> Student.find_by(:id => 128)
  Student Load (1.0ms)  SELECT  "students".* FROM "students"  WHERE "students"."id" = 128 LIMIT 1
=> nil

17. Find the first student in the table

[26] pry(main)> Student.first
  Student Load (1.8ms)  SELECT  "students".* FROM "students"   ORDER BY "students"."id" ASC LIMIT 1
=> #<Student id: 2, first_name: "Marlon", last_name: "Brando", age: 34, created_at: "2014-08-20 00:21:37", updated_at: "2014-08-20 00:21:37">

18. Find the last student in the table

[27] pry(main)> Student.last
  Student Load (1.3ms)  SELECT  "students".* FROM "students"   ORDER BY "students"."id" DESC LIMIT 1
=> #<Student id: 5, first_name: "Johnathen", last_name: "Doesmith", age: 33, created_at: "2014-08-20 01:00:39", updated_at: "2014-08-20 01:00:39">

19. Find the student with the last name of Doesmith

[28] pry(main)> Student.find_by(:last_name => "Doesmith")
  Student Load (0.9ms)  SELECT  "students".* FROM "students"  WHERE "students"."last_name" = 'Doesmith' LIMIT 1
=> #<Student id: 5, first_name: "Johnathen", last_name: "Doesmith", age: 33, created_at: "2014-08-20 01:00:39", updated_at: "2014-08-20 01:00:39">

21. Find all of the students and limit the search to 5 students, starting with the 2nd student and finally, order the students in alphabetical 

[32] pry(main)> Student.offset(2).limit(5).order('last_name')
  Student Load (1.2ms)  SELECT  "students".* FROM "students"   ORDER BY last_name LIMIT 5 OFFSET 2
=> [#<Student id: 5, first_name: "Johnathen", last_name: "Doesmith", age: 33, created_at: "2014-08-20 01:00:39", updated_at: "2014-08-20 01:00:39">,
 #<Student id: 4, first_name: "Engelbert", last_name: "Humperdink", age: 76, created_at: "2014-08-20 00:22:43", updated_at: "2014-08-20 00:22:43">]

20. Delete Jonathan Doesmith

[43] pry(main)> student.destroy
   (1.0ms)  BEGIN
  SQL (0.9ms)  DELETE FROM "students" WHERE "students"."id" = $1  [["id", 5]]
   (18.1ms)  COMMIT

   

### Bonus
1. Use the validates_format_of and regex to only validate names that consist of letters (no numbers or symbols) and start with a capital letter
2. Write a custom validation to ensure that no one named Delmer Reed, Tim Licata, Anil Bridgpal or Elie Schoppik is included in the students table


