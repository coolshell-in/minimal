require "jekyll"
require "listen"

task :preview do
  base = Pathname.new('.').expand_path
  options = {
    "source"        => base.to_s,
    "destination"   => base.join('_site').to_s,
    "force_polling" => false,
    "serving"       => true,
    "theme"         => "minimal-mistakes-jekyll"
  }

  options = Jekyll.configuration(options)

  listener = Listen.to(
    base.join("_data"),
    base.join("_includes"),
    base.join("_layouts"),
    base.join("_sass"),
    base.join("assets"),
    options["source"],
    ignore: [
      /_config\.ya?ml/,
      /_site/,
      /\.jekyll-metadata/
    ],
    force_polling: options['force_polling']
  ) do |modified, added, removed|
    t = Time.now
    c = modified + added + removed
    relative_paths = c.map { |p| Pathname.new(p).relative_path_from(base).to_s }
    print Jekyll.logger.message("Regenerating:", "#{relative_paths.join(", ")} changed... ")
    begin
      Jekyll::Commands::Build.process(options)
      puts "regenerated in #{Time.now - t} seconds."
    rescue => e
      puts "error:"
      Jekyll.logger.warn "Error:", e.message
      Jekyll.logger.warn "Error:", "Run jekyll build --trace for more information."
    end
  end

  listener.start
  puts "Listening for changes..."
  sleep
end

