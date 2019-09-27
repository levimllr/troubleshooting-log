# troubleshooting-log
A log of problems I've faced and how I've solved them.
## Environmental
### Ruby
The following error happened when I tried to change the default version of the concurrent-ruby gem in an attempt to avoid using the `bundle exec` command to run Rake tasks in an old project. I assumed the two instances were related.

```
An error occurred while loading spec_helper.
Failure/Error: require_relative '../config/environment'

LoadError:
  cannot load such file -- concurrent/map
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/inflector/inflections.rb:3:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/inflector/inflections.rb:3:in `<top (required)>'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/inflections.rb:3:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/inflections.rb:3:in `<top (required)>'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/inflector/methods.rb:3:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/inflector/methods.rb:3:in `<top (required)>'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/dependencies/autoload.rb:3:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/dependencies/autoload.rb:3:in `<top (required)>'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support.rb:27:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support.rb:27:in `<top (required)>'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record.rb:26:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record.rb:26:in `<top (required)>'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/bundler-2.0.2/lib/bundler.rb:114:in `require'
# ./config/environment.rb:2:in `<top (required)>'
# ./spec/spec_helper.rb:3:in `require_relative'
# ./spec/spec_helper.rb:3:in `<top (required)>'
```

I tried uninstalling and reinstalling verisons of concurrent-ruby. So I tried the nuclear option. `rvm reinstall all --force`. The process stalled when pulling all of the global gems, which the Internet attributed to DNS. I Ctrl+C'd my way past it to finish the reinstallation, upon which I was greated with the following error:

```
An error occurred while loading spec_helper.
Failure/Error: require_relative '../config/environment'

LoadError:
  Error loading the 'sqlite3' Active Record adapter. Missing a gem it depends on? can't activate sqlite3 (~> 1.4), already activated sqlite3-1.3.13. Make sure all dependencies are added to Gemfile.
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record/connection_adapters/sqlite3_adapter.rb:13:in `<top (required)>'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/dependencies.rb:325:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/dependencies.rb:325:in `block in require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/dependencies.rb:291:in `load_dependency'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activesupport-6.0.0/lib/active_support/dependencies.rb:325:in `require'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record/connection_adapters/connection_specification.rb:170:in `spec'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record/connection_adapters/abstract/connection_pool.rb:1044:in `establish_connection'
# /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record/connection_handling.rb:51:in `establish_connection'
# ./config/environment.rb:4:in `<top (required)>'
# ./spec/spec_helper.rb:3:in `require_relative'
# ./spec/spec_helper.rb:3:in `<top (required)>'
# ------------------
# --- Caused by: ---
# Gem::LoadError:
#   can't activate sqlite3 (~> 1.4), already activated sqlite3-1.3.13. Make sure all dependencies are added to Gemfile.
#   /Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record/connection_adapters/sqlite3_adapter.rb:13:in `<top (required)>'
```

**To fix the issue, I opened `/Users/lmiller4/.rvm/gems/ruby-2.6.1/gems/activerecord-6.0.0/lib/active_record/connection_adapters/sqlite3_adapter.rb` and changed line 13 to `gem "sqlite3", "< 1.4"`, requiring the adapter to use the activated `sqlite3-1.3.13` gem.
