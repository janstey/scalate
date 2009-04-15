# -*- ruby -*-
#
# This is a sample Rakefile to which you can add tasks to manage your website. For example, users
# may use this file for specifying an upload task for their website (copying the output to a server
# via rsync, ftp, scp, ...).
#
# It also provides some tasks out of the box, for example, rendering the website, clobbering the
# generated files, an auto render task,...
#

require 'webgen/webgentask'
require 'webgen/website'

# TODO couldn't figure out how to use the autoload stuff! :)
require 'ext/fuse/sitecopy_rake.rb'

# TODO must change this to the actual project!
# one day it would be nice to find this from the src/metainfo file
project_id = "CHANGEME"

task :default => :webgen
task :upload => ["sitecopy:clobber", "sitecopy:upload"]
task :auto => :auto_webgen

Webgen::WebgenTask.new do |website|
  website.clobber_outdir = true
  website.config_block = lambda do |config|
    # you can set configuration options here
  end
end

desc "Render the website automatically on changes"
task :auto_webgen do
  puts 'Starting auto-render mode'
  time = Time.now
  abort = false
  old_paths = []
  Signal.trap('INT') {abort = true}

  while !abort
    # you may need to adjust the glob so that all your sources are included
    paths = Dir['src/**/*'].sort
    if old_paths != paths || paths.any? {|p| File.mtime(p) > time}
      Rake::Task['webgen'].execute({})
    end
    time = Time.now
    old_paths = paths
    sleep 2
  end
end


# lets parse a file from ~/.fuseforge.rc
#   username anonymous
#   password foo@bar.example
def get_username_pwd
  userpwd = ""
  userpwd_file_name = File.expand_path("~/.fuseforge.rc")
  #puts "Looking for file #{userpwd_file_name}"
  #userpwd_file_name = "/Users/jstrachan/.fuseforge.rc"
  if File.file?(userpwd_file_name) 
    userpwd = IO.readlines(userpwd_file_name).join("")
    #puts "User file is #{userpwd}"
  end
  userpwd.strip
end

# lets not use safe mode due to timestamp wierdness
#safe
#state checksum

Fuse::SitecopyTask.new("forgesite", <<-SITECOPYRC)
  server fusesource.com
  protocol http
  #{get_username_pwd}  
  local out
  remote /forge/dav/#{project_id}/site
  
  exclude /maven
  ignore /maven
  ignore /.htaccess
  exclude /.htaccess
    
SITECOPYRC

