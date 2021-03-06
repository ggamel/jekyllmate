require 'rubygems'
require 'rake'

#################################################################################
# Site-specific Config variables
#################################################################################
## -- Rsync Deploy config -- ##
# Be sure your public key is listed in your server's ~/.ssh/authorized_keys file
ssh_user       = "user@domain.com"
ssh_port       = "22"
document_root  = "~/website.com/"
rsync_delete   = false
rsync_args     = ""  # Any extra arguments to pass to rsync
deploy_default = "rsync"
#################################################################################


#################################################################################
#
# Site Tasks
#
# Usage: "rake site:task"
#
# Options:
#   rake site:generate    "Generate Jekyll site"
#   rake site:preview     "Generate and view Jekyll site locally w/server mode"
#   rake site:publish     "Commit/Push _site/ to local/remote gh-pages branches
#
#   rake site:task
#   rake site:task
#   rake site:task
#   rake site:task
#################################################################################

namespace :site do

  desc "Generate Jekyll site"
  task :generate do
    puts "Generating Site with Jekyll"
    system "jekyll --no-server --no-auto"
  end

  desc "Generate and view Jekyll site locally w/server mode"
  task :preview do
    require "launchy"

    # This uses the launchy gem to open our web browser.
    # While not necessary, it's a nicety.
    Thread.new do
      sleep 4
      puts "Opening in browser..."
      Launchy.open("http://localhost:4000")
    end

    # Generate the site in server mode.
    puts "Running Jekyll..."
    sh "jekyll --server --auto"
  end

  desc "Commit the local site to the gh-pages branch and publish to GitHub Pages"
  task :publish do

    # Ensure the gh-pages dir exists so we can generate into it.
    puts "Checking for gh-pages dir..."
    unless File.exist?("./gh-pages")
      puts "No gh-pages directory found. Run the following commands first:"
      puts "  `git clone git://github.com/ggamel/jekyllplate.git gh-pages"
      puts "  `cd gh-pages"
      puts "  `git checkout gh-pages`"
      puts "  `git branch -d master`"
      puts "  `git branch`" # Ensure only gh-pages branch remains
      exit(1)
    end

    # Ensure _site has been generated
    Rake::Task['site:generate'].invoke

    # Ensure gh-pages branch is up to date.
    # Dir.chdir('gh-pages') do
    #   sh "git pull origin gh-pages"
    # end

    # Copy to gh-pages dir.
    puts "Copying site to gh-pages branch..."
    sh "cp -R _site/ gh-pages/"
    sh "rm -R _site/"

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

  # Taken from https://github.com/imathis/octopress
  # TODO: replace variables
  desc "Deploy website via rsync"
  task :rsync do
    exclude = ""
    if File.exists?('./rsync-exclude')
      exclude = "--exclude-from '#{File.expand_path('./rsync-exclude')}'"
    end
    puts "## Deploying website via Rsync"
    ok_failed system("rsync -avze 'ssh -p #{ssh_port}' #{exclude} #{rsync_args} #{"--delete" unless rsync_delete == false} #{public_dir}/ #{ssh_user}:#{document_root}")
  end

end