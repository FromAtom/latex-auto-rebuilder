# -*- coding: utf-8 -*-
require 'rake'
require 'rake/clean'
require 'yaml'

config = YAML.load_file(File.dirname(__FILE__) + '/config.yml')

PAPER   = config['target']
LATEX   = config['tex_cmd']
BIBTEX  = config['bib_cmd']
DVIPDFM = config['dvi_cmd']
OPEN    = config['open_cmd']
XBB     = config['xbb_cmd']
IMG_DIR_PATH = config['images_dir_path']

CLEAN.include(%w{aux log dvi bbl blg toc lof lot fdb_latexmk fls out bb xbb DS_Store}.map{|e|"**/*.#{e}"}+['**/.DS_Store'])
CLOBBER.include("#{PAPER}.pdf")

img_require_xbb = FileList["#{IMG_DIR_PATH}/*.png", "#{IMG_DIR_PATH}/*.jpg", "#{IMG_DIR_PATH}/*.pdf"]
img_xbb = img_require_xbb.ext('xbb')

task :default do
  Rake::Task[:xbb].invoke
  Rake::Task[:latex].execute
  Rake::Task[:bibtex].execute
  Rake::Task[:latex].execute
  Rake::Task[:latex].execute
  Rake::Task[:dvipdfm].execute
end

desc "Generate .xbb file"
task :xbb => img_xbb do
  TermColor.blue
  print "==> "
  TermColor.reset
  puts "Generate .xbb file is DONE"
end

desc "Compile .tex file"
task :latex do
  TermColor.blue
  print "==> "
  TermColor.reset
  puts "Compile .tex file"

  sh "#{LATEX} #{PAPER}"
end

desc "Compile .bib file"
task :bibtex do
  if FileTest.exist?("#{PAPER}.bib")
    TermColor.blue
    print "==> "
    TermColor.reset
    puts "Compile .bib file"

    sh "#{BIBTEX} #{PAPER}"
  else
    TermColor.red
    print "==> "
    TermColor.reset
    puts "BIB file not exist"
  end
end

desc "Create PDF from .dvi file"
task :dvipdfm do
  TermColor.blue
  print "==> "
  TermColor.reset
  puts "Create PDF from .dvi file"

  sh "#{DVIPDFM} #{PAPER}"

  TermColor.green
  print "==> "
  TermColor.reset
  puts "Compile complete!"
end

desc "Start watch [.tex .bib] and regenerate PDF"
task :watch do
  require 'watchr'
  require 'digest/md5'

  executor = Class.new {
    def submit(path, &task)
      current_hash = Digest::MD5.file(path).to_s
      @hashes ||= {}
      if @hashes[path] != current_hash
        task.call()
        @hashes[path] = current_hash
      end
    end
  }.new

  script = Watchr::Script.new

  script.watch "#{PAPER}.tex" do
    executor.submit "#{PAPER}.tex" do
      Rake::Task[:latex].execute
      executor.submit "#{PAPER}.aux" do
        Rake::Task[:bibtex].execute
        Rake::Task[:latex].execute
        Rake::Task[:latex].execute
      end
      Rake::Task[:dvipdfm].execute
    end
  end

  script.watch "#{PAPER}.bib" do
    executor.submit "#{PAPER}.bib" do
      Rake::Task[:latex].execute
      Rake::Task[:bibtex].execute
      Rake::Task[:latex].execute
      Rake::Task[:latex].execute
      Rake::Task[:dvipdfm].execute
    end
  end

  Watchr::Controller.new(script, Watchr.handler.new).run
end

# .xbb用のrules
rule '.xbb' => '.png' do |t|
  p "convert PNG"
  sh "#{XBB} #{t.source}"
end

rule '.xbb' => '.jpg' do |t|
  p "convert JPG"
  sh "#{XBB} #{t.source}"
end

rule '.xbb' => '.pdf' do |t|
  p "convert PDF"
  sh "#{XBB} #{t.source}"
end

# 標準出力時に色をつけるためのクラス
class TermColor
  class << self
    # 色を解除
    def reset   ; c 0 ; end

    # 各色
    def red     ; c 31; end
    def green   ; c 32; end
    def yellow  ; c 33; end
    def blue    ; c 34; end
    def magenta ; c 35; end
    def cyan    ; c 36; end
    def white   ; c 37; end

    # カラーシーケンスを出力する
    def c(num)
      print "\e[#{num.to_s}m"
    end
  end
end
