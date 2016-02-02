

pwd=File.dirname(__FILE__)

def build_module
    # propagate relavent environment variables
    env_globals = []
    [
      'PUPPET_VERSION',
      'STRICT_VARIABLES',
      'FUTURE_PARSER',
      'TRUSTED_NODE_DATA',
      'TRAVIS',
      'CI',
    ].each do |v|
      env_globals << %Q(#{v}="#{ENV[v]}") if ENV.key?( v )
    end
    env_globals_line = env_globals.join(' ')

    Bundler.with_clean_env do
      [
       'bundle --without development system_tests',
       'bundle exec rake clean',
       'bundle exec rake build'
      ].each do |cmd|
        line = "#{env_globals_line} #{cmd}"
        puts "==== EXECUTING: #{line}"
        exit 1 unless system(line)
      end
    end
end

# modules-under-test
mut_path = File.join( pwd, 'forge_modules' )
raise "ERROR: Couldn't find directory at '#{mut_path}'" unless File.directory? mut_path

desc 'builds tar.gz from modules in directories'
task :build do
  Dir["#{mut_path}/[^.]*/"].each do |dir|
    Dir.chdir dir
    build_module
    FileUtils.cp Dir['pkg/*.tar.gz'].first, File.expand_path('../forge_modules/'), :verbose => true
    Dir.chdir pwd
  end
end
