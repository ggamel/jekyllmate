#############################################################################
#
# Site tasks  // taken from http://jekyllrb.com
#
#############################################################################

namespace :site do
  desc "Generate and view the site locally"
  task :preview do
    require "launchy"

    # Yep, it's a hack! Wait a few seconds for the Jekyll site to generate and
    # then open it in a browser. Someday we can do better than this, I hope.
    Thread.new do
      sleep 4
      puts "Opening in browser..."
      Launchy.open("http://localhost:4000")
    end

    # Generate the site in server mode.
    puts "Running Jekyll..."
    Dir.chdir("site") do
      sh "#{File.expand_path('bin/jekyll', File.dirname(__FILE__))} serve --watch"
    end
  end

  desc "Commit the local site to the gh-pages branch and publish to GitHub Pages"
  task :publish do
    # Failsafe. Remove this once it has been done.
    puts "Make sure to merge #583 into gh-pages before deploying."
    exit(1)

    # Ensure the gh-pages dir exists so we can generate into it.
    puts "Checking for gh-pages dir..."
    unless File.exist?("./gh-pages")
      puts "No gh-pages directory found. Run the following commands first:"
      puts "  `git clone git@github.com:mojombo/jekyll gh-pages"
      puts "  `cd gh-pages"
      puts "  `git checkout gh-pages`"
      exit(1)
    end

    # Ensure gh-pages branch is up to date.
    Dir.chdir('gh-pages') do
      sh "git pull origin gh-pages"
    end

    # Copy to gh-pages dir.
    puts "Copying site to gh-pages branch..."
    Dir.glob("site/*") do |path|
      next if path == "_site"
      sh "cp -R #{path} gh-pages/"
    end

    # Commit and push.
    puts "Committing and pushing to GitHub Pages..."
    sha = `git log`.match(/[a-z0-9]{40}/)[0]
    Dir.chdir('gh-pages') do
      sh "git add ."
      sh "git commit -m 'Updating to #{sha}.'"
      sh "git push origin gh-pages"
    end
    puts 'Done.'
  end
end
