require 'rake'

desc "Hook our dotfiles into system-standard positions."
task :install => [:submodules] do
  puts
  puts "======================================================"
  puts "Welcome to YADR Installation. I'll ask you a few"
  puts "questions about which files to install. Nothing will"
  puts "be overwritten without your consent."
  puts "======================================================"
  puts
  # this has all the runcoms from this directory.
  file_operation(Dir.glob('git/*')) if want_to_install?('git')
  file_operation(Dir.glob('irb/*')) if want_to_install?('irb/pry')
  file_operation(Dir.glob('ruby/*')) if want_to_install?('ruby (gems)')
  file_operation(Dir.glob('ctags/*')) if want_to_install?('ctags config (better js/ruby support)')
  file_operation(Dir.glob('vimify/*')) if want_to_install?('vimification of mysql/irb/command line')
  file_operation(Dir.glob('{vim,vimrc}')) if want_to_install?('vim')
  file_operation(Dir.glob('zsh/prezto/runcoms/z*'), :copy)

  success_msg("installed")
end

desc "Init and update submodules."
task :submodules do
  sh('git submodule update --init')
end

task :default => 'install'


private
def run(cmd)
  puts
  puts "[Installing] #{cmd}"
  `#{cmd}` unless ENV['DEBUG']
end

def want_to_install? (section)
  puts "Would you like to install configuration files for: #{section}? [y]es, [n]o"
  STDIN.gets.chomp == 'y'
end

def file_operation(files, method = :symlink)
  skip_all = false
  overwrite_all = false
  backup_all = false

  files.each do |f|
    file = f.split('/').last
    source = "#{ENV["PWD"]}/#{f}"
    target = "#{ENV["HOME"]}/.#{file}"

    puts "--------"
    puts "file:   #{file}"
    puts "source: #{source}"
    puts "target: #{target}"

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
      run %{ mv "$HOME/.#{file}" "$HOME/.#{file}.backup" } if backup || backup_all
    end

    if method == :symlink
      run %{ ln -s "#{source}" "#{target}" }
    else
      run %{ cp -f "#{source}" "#{target}" }
    end

    # Temporary solution until we find a way to allow customization
    if file == 'zshrc'
      run %{ ln -s "~/.yadr/zsh/prezto" "$HOME/.oh-my-zsh" }
      File.open(target, 'a') do |f|
        f.write('for config_file (~/.yadr/zsh/*.zsh) source $config_file')
      end
    end

    if file == 'zshenv'
      text = File.read(target)
      text.gsub!('export PDIR="$ZDOTDIR/.zsh.d"', 'export PDIR="$HOME/.yadr/zsh/prezto"')
      File.open(target, 'w') { |f| f.puts text }
    end
  end
end

def success_msg(action)
  puts ""
  puts "   _     _           _         "
  puts "  | |   | |         | |        "
  puts "  | |___| |_____  __| | ____   "
  puts "  |_____  (____ |/ _  |/ ___)  "
  puts "   _____| / ___ ( (_| | |      "
  puts "  (_______\_____|\____|_|      "
  puts ""
  puts "YADR has been #{action}. Please restart your terminal and vim."
end
