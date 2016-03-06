
class FakeForge
  VERBOSITY_LEVELS={
    :quiet    => 0,
    :info     => 1,
    :verbose  => 2,
    :debug    => 3,
  }

  def initialize
    @pwd = ENV.fetch( 'FORGE_TEST_TOP_DIR', Dir.pwd)
    Dir.chdir @pwd

    # modules-under-test
    @mut_path = File.join( @pwd, 'forge_modules' )

    # tarballs-under-test ?
    @tut_path = File.join( @pwd, 'forge_tarballs' )

    FileUtils.mkdir_p @mut_path
    raise "ERROR: Couldn't find directory at '#{@mut_path}'" unless File.directory? @mut_path
  end

  def label verbosity, text
    raise "'#{verbosity}' is not a verbosity_level." unless \
      VERBOSITY_LEVELS.keys.include? verbosity

    
    puts "\n#{'=='*VERBOSITY_LEVELS[verbosity]} #{text}" 
  end


  # build a string to preserve relavent environment variables
  def env_vars_line( list = nil )
    env_globals = []
    list = list || [
      'PUPPET_VERSION',
      'STRICT_VARIABLES',
      'FUTURE_PARSER',
      'TRUSTED_NODE_DATA',
      'TRAVIS',
      'CI',
    ]
    list.each do |v|
      env_globals << %Q(#{v}="#{ENV[v]}") if ENV.key?( v )
    end
    env_globals.join(' ')
  end

  def build_module_tarball(dir)
    label :verbose, "START building module tar files"

    Dir.chdir dir
    Bundler.with_clean_env do
      [
       'bundle --without development system_tests',
       'bundle exec rake clean',
       'bundle exec rake build'
      ].each do |cmd|
        line = "#{env_vars_line} #{cmd}"
        label :verbose, "EXECUTING [#{File.basename dir}]: #{line}"
        exit 1 unless system(line)
      end
    end

    label :verbose, "FINISHED building module tar files"
  end

  def build_module_tarballs
    FileUtils.mkdir_p @tut_path, :verbose => true
    Dir["#{@mut_path}/[^.]*/"].each do |dir|
      build_module_tarball(dir)

      label :info, "COPY tarfiles into mock Puppet Forge"
      file = Dir.glob('pkg/*.tar.gz').shift
      d_file = File.join(@tut_path, File.basename(file))
      FileUtils.cp(file, d_file, :verbose => true)
    end
    Dir.chdir @pwd
  end

  def install
    sh 'r10k puppetfile install --verbose'
  end
end


f = FakeForge.new
desc 'pulls down modules in Puppetfile to set up forge_modules'
task :install do
  f.install
end


desc 'builds tar.gz file from module directories'
task :build do
  f.build_module_tarballs
end

