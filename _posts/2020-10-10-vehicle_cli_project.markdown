---
layout: post
title:      "Vehicle CLI Project"
date:       2020-10-10 13:26:06 -0400
permalink:  vehicle_cli_project
---


It was so interesting doing my CLI project. I chose to make it about vehicles where the user has the opportunity to interact with the interface and search for any make's vehicle. This solves a problem when it comes to finding details about the make you are interesting in and have the chance to find out how many vehicles exist for that specific make.
Details come in handy as they nurture us with information about the make's name, make's model, make id, model id, and even the vehicle type. You will be submerged in a list of your favorite vehicles and will be amazed by the vast variety of vehicles. 

The process was intriguing as I went through it. I had the chance to apply the concepts I learnt and thanks to my cohort lead I was able to unpuzzle my tangled thoughts to finally make my program friendly and useful to my user. I did find situations where I felt frustrated but this is what programming is about right? Solving problems from different angles until you find the right one and yet being open to change it as I will be evaluated. Hopefully won't need many changes as I tried to make it as abstrast as possible. 

At first I ask the user for their input, or make's name. 
Then I display a list of their make's name and make's model by using print_makes(makes) method: 

```
def print_makes(makes)
        puts ""
        puts "Here are #{@make} cars"
        puts ""
        makes.each.with_index(1) do |m, i|
            puts "#{i}. #{m.name} #{m.model}"
        end 
    end  
```

Thanks to my API. I am able to retrieve this information for my user and iterate over it: 

```
  def self.fetch_makes(make)
        url = "https://vpic.nhtsa.dot.gov/api/vehicles/getmodelsformake/#{make}?format=json"
         uri = URI(url) 
            response = Net::HTTP.get(uri)
            
            makes = JSON.parse(response)["Results"]
            
            makes.each do |m|
                
                new_make = Make.new(name: m["Make_Name"], model: m["Model_Name"], make_id: m["Make_ID"], model_id: m["Model_ID"])
                
            end 
        
    end 
```

As you see when the make's information is displayed I give extra details also joining another method to display the vehicle type: 

```
  def self.get_make_details(make)
        
        
        url = "https://vpic.nhtsa.dot.gov/api/vehicles/GetVehicleTypesForMakeId/#{make.make_id}?format=json"
    

        uri = URI(url)
        response = Net::HTTP.get(uri)
        data = JSON.parse(response)["Results"][0]
        
        make.vehicle_type = data["VehicleTypeName"]
       
    end
end 
```

The user is able to choose a list of make's names to do their search again by typing 'list'. They can type 'exit' to exit the program.

All this interaction is allowed thanks to my start method in my CLI class: 

```
 def start 
        puts ""
        puts "Welcome to vehicle finder!"
        puts ""
        puts "Search for makes by entering a valid make's name"
        puts ""
        @make = gets.strip.downcase 
        puts ""
        API.fetch_makes(@make)
        Make.all 
        puts ""
        makes = Make.all 
        print_makes(makes)        
        puts ""
        puts "Type a number listed to see more details or type 'exit' to exit"
        puts ""
        inp = gets.strip.downcase 
        while inp != 'exit' do 
            if inp == 'list'
                print_makes(Make.find_by_make(@make))
                puts ""
                puts "Type a number listed to see more details or type 'exit' to exit"
                puts ""
                
            elsif inp.to_i > 0 && inp.to_i <= Make.find_by_make(@make).length
                
            make =  Make.find_by_make(@make)[inp.to_i - 1]
           
             
                API.get_make_details(make)
                print_make(make)
            end 
   
            prompt
            inp = gets.strip.downcase  

        end 
        puts "Goodbye!"
    end 
```

Again! It was really fun to do this project. :)

