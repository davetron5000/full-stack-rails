require 'rake/clean'

include FileUtils
ENV["PATH"] = "#{ENV['PATH']}:node_modules/.bin"
BUILD_DIR = "build"

CLEAN.include(BUILD_DIR)
CLOBBER.include("index.html")

mkdir_p BUILD_DIR

CSS_LIBS           = [ "basscss/css/basscss.css" ]
MY_CSS             = [ "styles.css" ]
MINIFIED_CLEAN_CSS =   "build/s.css.min"
CONSOLIDATED_CSS   =   "build/s.css"
UN_CSS             =   "build/s.un.css"
HTML_SOURCE        =   "html/index.html"
CSS_SRC            = CSS_LIBS.map { |_| "node_modules/#{_}" } + MY_CSS.map { |_| "css/#{_}" }

file CONSOLIDATED_CSS => CSS_SRC do |task|
  rm_f task.name
  task.prerequisites.each do |file|
    sh! "cat #{file} >> #{task.name}"
  end
end

file "dev.html" => [HTML_SOURCE, CONSOLIDATED_CSS] do |task|
  mk_html(src: HTML_SOURCE, dest: "dev.html", css: CONSOLIDATED_CSS, typekit: false)
end

file MINIFIED_CLEAN_CSS => UN_CSS do |task|
  sh! "minify #{UN_CSS} > #{MINIFIED_CLEAN_CSS}"
end

file UN_CSS => [ CONSOLIDATED_CSS, HTML_SOURCE ] do |task|
  HTML_FOR_UNCSS = "build/index.html"
  mk_html(src: HTML_SOURCE, dest: HTML_FOR_UNCSS, css: CONSOLIDATED_CSS.gsub(/^#{BUILD_DIR}\//,''))
  sh! "uncss #{HTML_FOR_UNCSS} > #{UN_CSS}"
end

file "index.html" => [MINIFIED_CLEAN_CSS, HTML_SOURCE ] do |task|
  html_minifier_options = [
    "remove-comments",
    "remove-comments-from-cdata",
    "remove-cdatasections-from-cdata",
    "collapse-whitespace",
    "conservative-collapse",
    "preserve-line-breaks",
    "collapse-boolean-attributes",
    "remove-attribute-quotes",
    "remove-redundant-attributes",
    "remove-empty-attributes",
    "remove-script-type-attributes",
    "remove-optional-tags",
    "remove-ignored",
  ].map { |_|
    "--#{_}"
  }
  unminified_html = "build/index_#{$$}.html"
  mk_html(src: HTML_SOURCE, dest: unminified_html, css: :inline, typekit: false)
  sh! "html-minifier #{html_minifier_options.join(' ')} -o #{task.name} #{unminified_html}"
end

desc "Build the production index.html"
task :default => "index.html"

desc "Build dev.html, which is more suitable for debugging and modifying content"
task :dev => "dev.html"

def sh!(command)
  sh command do |ok, res|
    fail "`#{command}` failed: #{res.inspect}" unless ok
  end
end

def mk_html(src: , dest: , typekit: false, css: :inline)
  puts "Creating #{dest} from #{src}"
  File.open(dest,"w") do |file|
    File.open(src) do |html|
      html.readlines.each do |line|
        if line =~ /<\/head>/
          if css == :inline
            file.puts "<style>"
            file.puts File.read("build/s.css.min")
            file.puts "</style>"
          else
            file.puts "<link href=\"#{css}\" rel=\"stylesheet\">"
          end
          if typekit
            file.puts %{
              <script src="//use.typekit.net/tzz0owm.js"></script>
              <script>try{Typekit.load();}catch(e){}</script>
            }
          end
        else
          file.puts line
        end
      end
    end
  end
end
