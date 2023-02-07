# frozen_string_literal: true

require "bundler/gem_tasks"
require "rake/testtask"
require "syntax_tree/rake_tasks"

Rake::TestTask.new(:test) do |t|
  t.libs << "test"
  t.libs << "test/suites"
  t.libs << "lib"

  # These are our own tests.
  test_files = FileList["test/**/*_test.rb"]

  # This is a big test file from the parser gem that tests its functionality.
  parser_test_file = "test/suites/parser/test/test_parser.rb"
  test_files << parser_test_file if File.exist?(parser_test_file)

  t.test_files = test_files
end

task default: :test

configure = ->(task) do
  task.source_files =
    FileList[%w[Gemfile Rakefile syntax_tree.gemspec lib/**/*.rb test/*.rb]]

  # Since Syntax Tree supports back to Ruby 2.7.0, we need to make sure that we
  # format our code such that it's compatible with that version. This actually
  # has very little effect on the output, the only change at the moment is that
  # Ruby < 2.7.3 didn't allow a newline before the closing brace of a hash
  # pattern.
  task.target_ruby_version = Gem::Version.new("2.7.0")
end

SyntaxTree::Rake::CheckTask.new(&configure)
SyntaxTree::Rake::WriteTask.new(&configure)

desc "Run mspec tests using YARV emulation"
task :spec do
  Dir["./spec/ruby/language/**/*_spec.rb"].each do |filepath|
    sh "exe/yarv ./spec/mspec/bin/mspec-tag #{filepath}"
  end
end
