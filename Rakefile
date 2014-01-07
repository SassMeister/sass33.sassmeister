require 'yaml'
require 'thor'

desc "Update bundled gems. Use this in place of bundle update"
task "bundle:update" do
  plugins = YAML.load_file("config/plugins.yml")
  gemfile = File.new('Gemfile').read

  plugins.each do |plugin, info|
    if ! gemfile.match(/^gem '#{info[:gem]}'/)
      puts "Adding #{info[:gem]} to Gemfile..."
      Utilities.new.append('Gemfile', "\ngem '#{info[:gem]}'")
    end
  end

  stdout = `bundle update`

  puts stdout

  plugins.each do |plugin, info|
    version = stdout.scan(/#{info[:gem]} \((.+)\)/)[0][0].to_s

    sass_input_list.push "<li><a data-import=\"#{info[:import].to_s.gsub(/(\"|\[|\]|\s*)/, '')}\">#{plugin}</a>&nbsp;&nbsp;(v#{version})</li>"
  end

  Utilities.new.update_plugin_list('views/extensions.erb', sass_input_list)
end


class Utilities < Thor
  include Thor::Actions

  no_tasks do
    def append(file, string)
       append_file file, string, {:verbose => false}
    end

    def update_plugin_list(file, list)
      gsub_file file, /<ol>\s*(<li>.+?<\/li>\s*)+<\/ol>/, "<ol>\n\t\t#{list.join("\n\t\t")}\n\t</ol>"
    end
    end
  end
end