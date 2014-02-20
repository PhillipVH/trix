require 'fileutils'
require 'json'
require 'shellwords'

def with_server
  begin
    pid = spawn "rackup config.ru"
    sleep 2
    yield
    trap("INT") { Process.kill "INT", pid }
    Process.waitpid pid
  end
end

task :clean do
  FileUtils.rm_rf "dist"
  FileUtils.mkdir_p "dist"
end

task :build do
  `sprockets -I src -o dist src/trix.coffee src/trix.html.erb`
  manifest_file = Dir["dist/manifest*.json"].first
  manifest = JSON.parse(File.read(manifest_file))
  FileUtils.ln_sf manifest["assets"]["trix.html"], "dist/trix.html"
end

task :open do
  with_server do
    `open http://localhost:9292`
  end
end

task :test => :build do
  `sprockets -I src -I test test/trix/test.coffee > test/test.js`
  `open -a Safari test/test.html`
end

task :default => :open

task :loc do
  puts `find src -name '*.coffee' -type f | xargs sed -n '/^ *\\(#.*\\)*\$/!p' | wc -l`
end
