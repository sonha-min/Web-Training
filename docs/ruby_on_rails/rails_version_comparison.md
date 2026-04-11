# Rails Version Comparison

## Rails 4 and Earlier

In Rails 4, the command-line is divided into two parts:

- **rails**: Used for generating code and running applications.

  ```ruby
  rails g model User name:string
  rails g migration AddNameToUsers name:string
  rails server / rails console
  ```

- **rake**: Used for all other tasks.

  ```ruby
  rake db:migrate
  rake db:seed
  rake db:create
  rake test
  rake routes
  rake assets:precompile
  ```

## Rails 5

1. **Proxy Rake tasks through bin/rails**. You can use rails for all tasks.

   ```ruby
   rails db:migrate
   rails db:seed
   rails db:create
   rails test
   rails routes
   rails assets:precompile
   ```

   [Reference](https://guides.rubyonrails.org/5_0_release_notes.html#railties-notable-changes)

2. **Active Record**

- **Model inherits from ApplicationRecord**: Instead of ActiveRecord::Base, `rails g model` now creates models inheriting from `app/models/application_record.rb`.

- Added a `foreign_key` option to `references` while creating the table.

- **belongs_to is required by default**: When you create a model with belongs_to, Rails 5 automatically adds `validation required: true`. You must add `optional: true` if you don't want it.

  ```bash
    # Rails 4:
    rails g model Comment post:references
    # app/models/comment.rb
    # class Comment < ActiveRecord::Base
    #   belongs_to :post
    # end

    # Rails 5+:
    rails g model Comment post:references
    # app/models/comment.rb
    # class Comment < ApplicationRecord
    #   belongs_to :post # (Required by default)
    # end
  ```

  [Reference](https://guides.rubyonrails.org/5_0_release_notes.html#active-record-notable-changes)

## Rails 6: Adding New Frameworks

Rails 6 does not change the core commands, but it introduces new commands:

- [rails db:prepare](https://guides.rubyonrails.org/active_record_migrations.html#preparing-the-database) to create a database if it doesn't exist, and run its migrations

  [Reference](https://blog.saeloun.com/2019/04/11/rails-6-rails-db-prepare)

- [rails db:seed:replant](https://guides.rubyonrails.org/active_record_migrations.html#preparing-the-database) that truncates tables of each database for the current environment and loads the seeds

  [Reference](https://blog.saeloun.com/2019/09/30/rails-6-adds-db-seed-replant-task-and-db-truncate-all)

[Release notes](https://guides.rubyonrails.org/6_0_release_notes.html#active-record-notable-changes)

## Rails 7: Goodbye Webpack, Use Importmaps

The biggest change in Rails 7 is about the front-end. Webpacker is no longer the default.

**rails new changes**:

By default, `rails new my_app` will use Importmaps to manage JavaScript.

You no longer need `rails webpacker:install`.

**New install commands**:

- `rails importmap:install`: Used to install importmap for an old application upgrading to Rails 7.
- `rails turbo:install`: Installs Turbo.
- `rails stimulus:install`: Installs Stimulus.

# Common notes

## Different between bin/rails vs rails and bin/rake vs rake

|                            | `bin/rails` (or `bin/rake`)                                                          | `rails` (or `rake`)                                                                |
| -------------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| What is it?                | A `binstub` (small script) created by Rails/Bundler. (bin/rails ~ bundle exec rails) | An executable in your Ruby environment (managed by RVM, rbenv).                    |
| Which version does it run? | The exact version locked in the project's Gemfile.lock.                              | The "global" version installed in your current Ruby gemset.                        |
| Scope                      | Specific to the current project.                                                     | Entire Ruby environment.                                                           |
| Reliability                | High. Recommended for consistency.                                                   | Low. Prone to errors if you have multiple projects using different Rails versions. |
