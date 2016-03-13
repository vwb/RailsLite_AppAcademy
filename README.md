# RailsLite

A functional MVC framework based upon the functionality of Ruby on Rails. 

## Usage

* Download the repository
* In the terminal run `bundle exec bundle install`
* Then enter `ruby server.rb`
* Navigate to localhost:3000

Upon viewing the homepage you will see "Greetings from the controller" this behavior has been defined in the server.rb file.
	
* If you have familiarity with Rails feel free to instantiate your own controller class here and 
simply inherit it from ControllerBase. 

The framework also supports viewing of static assets that may be placed in the /public folder. 

If you navigate to `localhost:3000/public/bla.jpg` you can see a sample image included with the repo.

## Features

#### Router

The framework includes an implementation of a router that implements similar functionality to the rails router itself.

In server.rb we can see how a router may be instantiated:

	router = Router.new
	router.draw do
		get Regexp.new("^/$"), MyController, :go
	end

While this is a relatively simple call, there is alot going on behind the scenes. With the call to to `Router.new` we instantiate a series of methods for each http action using define_method to keep our code dry: 

	[:get, :post, :put, :delete].each do |http_method|
		define_method(http_method) do |pattern, controller_class, action_name|
	 	 add_route(pattern, http_method, controller_class, action_name)
		end
	end

This allows us to have appropriately matching routes when we may receive a new request.

Now in the instantiation of the app within `server.rb`, we call `router.run(req, res)`. This is where the actual matching will occur.

First it will check the if the route has been seen before by the router, and if so will grab the controller tied to the route and invoke the appropriate action: 

	controller = controller_class.new(req, res, route_hash)
	controller.invoke_action(action_name)

For further implementation details please refer to `router.rb`

#### Render Templates

To successfully render templates it is necessary to write our own `ControllerBase#render` function.

This needs to implement the following basic behaviors:

* Read the correct html.erb template based on classname and file name
* Render it using erb
* Write the content into the reponse (if it hasn't been done already)

The first is completed in this manner:

	file = File.read("views/#{self.class.name.underscore}/#{template_name}.html.erb")

Then we render using ERB:

	erb_template = ERB.new(file)
	erb_result = erb_template.result(binding)

And finally write the content into the response:

	if already_built_response?
		raise "Double render!"
	end
	@already_built_response = true
	res.write(content)

For further implementation details on template rendering please refer to `controller_base.rb`

#### Flash


#### Server Exceptions


#### Serve Static Assets




