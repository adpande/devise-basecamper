# Devise::Basecamper

Devise-Basecamper was built to allow users of [Devise](https://github.com/plataformatec/devise) to implement "Basecamp" style subdomain scoped authentication with 
support for multiple users.  There are a lot of [great tutorials](https://github.com/RailsApps/rails3-subdomains) out 
there on doing subdomain authentication with devise, but none of them seemed to fit my particular use cases.  So I took
a stab at extending the functionality of [Devise](https://github.com/plataformatec/devise), which has been a great 
Gem, and community, to work with.

### Use Case
User authentication that is scoped to an account, which is identified by the subdomain of the URL.  This allows for better
multi-tenancy, as well as for re-use of usernames under different accounts.

## Installation

Add this line to your application's Gemfile:

    gem 'devise-basecamper'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install devise-basecamper

## Usage

To make Devise-Basecamper work properly, there are several steps that need to be taken to adjust the "out-of-the-box" 
behavior of Devise.  None of the changes require doing any "hacking" of Devise, as they are all steps/actions and 
configuration options that are already a part of Devise itself.  

### Devise Configuration
Open the Devise initializer file, which can be found in `config/initializers/devise.rb`.  Add `:subdomain` to the 
`config.request_keys` array like below.

    config.request_keys = [:subdomain]

This will make sure to pass the subdomain value from the request to the appropriate Devise methods.

### Configuring models

Which ever model you would like to have subdomain based authentication scoping on, just add `:basecamper` to your 
included devise modules.

```
class User
	include Mongoid::Document
	include Mongoid::Timestamps
	
	devise	:database_authenticatable,
		:recoverable,
		:trackable,
		:validatable,
		:basecamper
	
	...
end
```

By default, Devise-Basecamper assumes that your devise model "belongs to" an Account and has a field called `account_id`
as the foreign key to that table.  Devise-Basecamper also assumes that the subdomain field exists in your Account model
and is called `subdomain`.  Now that's a lot of assumptions, but never fear...they can be changed. 

If you need to change any of these assumptions, you can do so by calling the `devise_basecamper` method in your devise
model.

```
class User
	include Mongoid::Document
	include Mongoid::Timestamps
	
	devise	:database_authenticatable,
		:recoverable,
		:trackable,
		:validatable,
		:basecamper
		
	devise_basecamper :subdomain_class => :my_parent_class,
			  :subdomain_field => :my_field_name,
			  :scope_field     => :field_to_scope_against
			  
	...
end
```

The `devise_basecamper` method has 3 options that can be set: subdomain_class, subdomain_field and scope_field.  

**subdomain_class**

This option allows you to specify which model your subdomains are defined in.  By default, devise_basecamper assumes this
to be an `Account` object.

**subdomain_field**

This option allows you to specify the name of the field within the `subdomain_class` that the subdomain string is stored 
in.  By default, devise_basecamper assumes this to be `subdomain`.

**scope_field**

This option allows you to specify the name of the field within the devise model (e.g. - User) stores the ID of the account
you want to create a scope against.  By default, devise_basecamper assumes that this is a field called `account_id`.

If your application follows the assumptions, you DO NOT need to define any of these options.

### Configuring multiple models

You can configure multiple models accordingly just as you can in Devise.  If you have a Devise model that does not need
the additional features offered by Devise-Basecamper, simple do not include the module.  Devise will work just as expected.

### ORM Compatability

Devise-Basecamper has very minimal interaction with your data layer, however it uses the same `orm_adapter` gem as Devise
and should work just fine with all of the ORM's supported by Devise.

MORE TO COME

## License

MIT License.  Copyright &copy; 2012 Digital Opera, LLC. [www.digitalopera.com](http://www.digitalopera.com/ "Digital Opera, LLC")
