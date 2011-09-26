require 'rake'

namespace :install do

  desc "Install everything"
  task :all do
    Rake::Task["pathogen:install"].invoke
    Rake::Task[:install].invoke
  end

end

desc "Hook our dotfiles into system-standard positions."
task :install do
  linkables = Dir.glob( "*/{*.symlink,.*.symlink}" )

  skip_all = false
  overwrite_all = false
  backup_all = false

  linkables.each do |linkable|
    overwrite = false
    backup = false

    file = linkable.split('/').last.split('.symlink').last
    target = "#{ENV["HOME"]}/#{file}"

    if File.exists?(target) || File.symlink?(target)
      unless skip_all || overwrite_all || backup_all
        puts "File already exists: #{target}, what do you want to do? [s]kip, [S]kip all, [o]verwrite, [O]verwrite all, [b]ackup, [B]ackup all"
        case STDIN.gets.chomp
        when 'o' then overwrite = true
        when 'b' then backup = true
        when 'O' then overwrite_all = true
        when 'B' then backup_all = true
        when 'S' then skip_all = true
        end
      end
      FileUtils.rm_rf(target) if overwrite || overwrite_all
      `mv "$HOME/#{file}" "$HOME/#{file}.backup"` if backup || backup_all
    end
    `ln -s "$PWD/#{linkable}" "#{target}"`
  end
end
task :default => :install

namespace :pathogen do

  desc "Install Vim Pathogen Bundles ... Inspired By - https://github.com/tsaleh/dotfiles/blob/master/vim/update_bundles"
  task :install do

    git_bundles = [
      "git://github.com/ervandew/supertab.git",
      "git://github.com/kchmck/vim-coffee-script.git",
      "git://github.com/michaeljsmith/vim-indent-object.git",
      "git://github.com/mileszs/ack.vim.git",
      "git://github.com/pangloss/vim-javascript.git",
      "git://github.com/scrooloose/nerdtree.git",
      "git://github.com/scrooloose/syntastic.git",
      "git://github.com/tpope/vim-abolish.git",
      "git://github.com/tpope/vim-endwise.git",
      "git://github.com/tpope/vim-fugitive.git",
      "git://github.com/tpope/vim-markdown.git",
      "git://github.com/tpope/vim-rails.git",
      "git://github.com/tpope/vim-repeat.git",
      "git://github.com/tpope/vim-surround.git",
      "git://github.com/tpope/vim-unimpaired.git",
      "git://github.com/tsaleh/vim-align.git",
      "git://github.com/vim-scripts/searchfold.vim.git",
      "git://github.com/vim-scripts/ZoomWin.git"
    ]

    vim_org_scripts = []

    require 'fileutils'
    require 'open-uri'

    bundles_dir = File.join(File.dirname(__FILE__), "vim/.vim.symlink/bundle")
    FileUtils.cd(bundles_dir)

    puts "trashing everything (lookout!)"
    Dir["#{ bundles_dir }/*"].each {|d| FileUtils.rm_rf d }

    git_bundles.each do |url|
      dir = url.split('/').last.sub(/\.git$/, '')
      puts "unpacking #{url} into #{dir}"
      `git clone #{url} #{dir}`
      FileUtils.rm_rf(File.join(dir, ".git"))
    end

    vim_org_scripts.each do |name, script_id, script_type|
      puts "downloading #{name}"
      local_file = File.join(name, script_type, "#{name}.vim")
      FileUtils.mkdir_p(File.dirname(local_file))
      File.open(local_file, "w") do |file|
        file << open("http://www.vim.org/scripts/download_script.php?src_id=#{script_id}").read
      end
    end

  end

end

