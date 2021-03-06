# Encoding: utf-8

require 'rake'
require 'rake/testtask'

# Rainbow require is due to incompatibilities between version of
# rubocop and rainbow breaking the build. May be able to be removed
# at some future time
require 'rainbow/ext/string'
require 'rubocop/rake_task'
require 'bundler/setup'

desc 'Run RuboCop on the lib directory'
RuboCop::RakeTask.new(:rubocop) do |task|
  task.fail_on_error = true
end

desc 'Foodcritic linter'
task :foodcritic do
  sh 'foodcritic -f any -t ~FC007 .'
end

desc 'Run Test Kitchen integration tests'
namespace :integration do
  desc 'Run integration tests with kitchen-vagrant'
  task :vagrant do
    require 'kitchen'
    Kitchen.logger = Kitchen.default_file_logger
    Kitchen::Config.new.instances.each { |instance| instance.test(:always) }
  end

  desc 'Run integration tests with kitchen-docker'
  task :docker, [:instance] do |_t, args|
    args.with_defaults(instance: 'default-ubuntu-1204')
    require 'kitchen'
    Kitchen.logger = Kitchen.default_file_logger
    loader = Kitchen::Loader::YAML.new(local_config: '.kitchen.docker.yml')
    instances = Kitchen::Config.new(loader: loader).instances
    # Travis CI Docker service does not support destroy:
    instances.get(args.instance).verify
  end
end

task default: [:rubocop, :foodcritic]
