$LOAD_PATH.unshift File.join(Dir.pwd, 'lib')

begin
    require 'hoe'
    namespace 'dist' do
        config = Hoe.spec 'autoproj' do
            self.developer "Sylvain Joyeux", "sylvain.joyeux@dfki.de"

            self.summary = 'Easy installation and management of robotics software'
            self.description = paragraphs_of('README.txt', 0..1).join("\n\n")
            self.changes     = paragraphs_of('History.txt', 0..1).join("\n\n")

            extra_deps << 
                ['autobuild',   '>= 1.3.0'] <<
                ['rmail',   '>= 1.0.0'] <<
                ['utilrb', '>= 1.3.3'] <<
                ['nokogiri', '>= 1.3.3'] <<
                ['highline', '>= 1.5.0']

            extra_dev_deps <<
                ['webgen', '>= 0.5.9']
        end

        desc "generate the bootstrap script"
        task 'bootstrap' do
            osdeps_code = File.read(File.join(Dir.pwd, 'lib', 'autoproj', 'osdeps.rb'))
            bootstrap_code = File.read(File.join(Dir.pwd, 'bin', 'autoproj_bootstrap.in')).
                gsub('OSDEPS_CODE', osdeps_code)
            File.open(File.join(Dir.pwd, 'doc', 'guide', 'src', 'autoproj_bootstrap'), 'w') do |io|
                io.write bootstrap_code
            end
        end
    end

    # Define our own documentation handling. Rake.clear_tasks is defined by Hoe
    Rake.clear_tasks(/dist:(re|clobber_|)docs/)

rescue LoadError
    STDERR.puts "cannot load the Hoe gem. Distribution is disabled"
rescue Exception => e
    STDERR.puts "cannot load the Hoe gem, or Hoe fails. Distribution is disabled"
    STDERR.puts "error message is: #{e.message}"
end

do_doc = begin
             require 'webgen/webgentask'
             require 'rdoc/task'
             true
         rescue LoadError => e
             STDERR.puts "ERROR: cannot load webgen and/or RDoc, documentation generation disabled"
             STDERR.puts "ERROR:   #{e.message}"
         end

if do_doc
    task 'doc' => 'doc:all'
    task 'clobber_docs' => 'doc:clobber'
    task 'redocs' do
        Rake::Task['doc:clobber'].invoke
        Rake::Task['doc'].invoke
    end

    namespace 'doc' do
        task 'all' => %w{guide api}
        task 'clobber' => 'clobber_guide'
        Webgen::WebgenTask.new('guide') do |website|
            website.clobber_outdir = true
            website.directory = File.join(Dir.pwd, 'doc', 'guide')
            website.config_block = lambda do |config|
                config['output'] = ['Webgen::Output::FileSystem', File.join(Dir.pwd, 'doc', 'html')]
            end
        end
        task 'guide' => 'dist:bootstrap'
        RDoc::Task.new("api") do |rdoc|
            rdoc.rdoc_dir = 'doc/html/api'
            rdoc.title    = "oroGen"
            rdoc.options << '--show-hash'
            rdoc.rdoc_files.include('lib/**/*.rb')
        end
    end
end


