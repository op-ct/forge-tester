


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

pwd=ENV.fetch( 'FORGE_TEST_TOP_DIR', Dir.pwd)
Dir.chdir pwd

# modules-under-test
mut_path = File.join( pwd, 'forge_modules' )

# tarballs-under-test ?
tut_path = File.join( pwd, 'forge_tarballs' )


FileUtils.mkdir_p mut_path
raise "ERROR: Couldn't find directory at '#{mut_path}'" unless File.directory? mut_path

desc 'pulls down modules in Puppetfile to set up forge_modules'
task :install do
  sh 'r10k puppetfile install --verbose'
end


desc 'builds tar.gz from module directories'
task :build do
  FileUtils.mkdir_p tut_path
  Dir["#{mut_path}/[^.]*/"].each do |dir|
    Dir.chdir dir
    build_module
    file = Dir.glob('pkg/*.tar.gz').shift
    FileUtils.cp(file, File.expand_path("#{tut_path}/#{File.basename file}"), :verbose => true)
    Dir.chdir pwd
  end
end

