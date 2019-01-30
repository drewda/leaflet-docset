require 'sqlite3'
require 'nokogiri'

FILE_NAME = "reference-1.4.0.html"
DOC_URL = "http://leafletjs.com/#{FILE_NAME}"

task :default => [
  :fetch_docs,
  :remove_annoying_parts_from_docs,
  :fetch_icon,
  :index,
  :add_info_plist,
  :tar
]

task :fetch_docs do
  target_dir = Dir.getwd + '/dist/leaflet.docset/Contents/Resources/Documents'
  system "wget --convert-links --page-requisites --directory-prefix=#{target_dir} #{DOC_URL}"
end

task :remove_annoying_parts_from_docs do
  doc_to_modify = doc.clone
  doc_to_modify.css('.ext-links').remove
  doc_to_modify.css('.nav').remove
  doc_to_modify.css('#toc-copy').remove
  doc_to_modify.xpath('//script[@src="docs/js/docs.js"]').remove # no TOC script
  File.open html_file_path, 'w' do |f|
    f.puts doc_to_modify.to_html
  end
end

task :fetch_icon do
  target_file = Dir.getwd + '/dist/leaflet.docset/icon.png'
  system "wget http://leafletjs.com/docs/images/favicon.ico -O #{target_file}"
end

task :index do
  db_path = Dir.getwd + '/dist/leaflet.docset/Contents/Resources/docSet.dsidx'
  db = SQLite3::Database.new db_path
  create_docset_table(db)
  parse_doc_into_db(doc, db)
end

task :add_info_plist do
  info_plist = Dir.getwd + '/Info.plist'
  FileUtils.cp info_plist, docset_contents_path
end

task :tar do
  system "cd dist && tar --exclude='.DS_Store' -cvzf Leaflet.tgz leaflet.docset"
end

private
  def docset_contents_path
    Dir.getwd + '/dist/leaflet.docset/Contents/'
  end

  def html_file_path
    docset_contents_path + "Resources/Documents/leafletjs.com/#{FILE_NAME}"
  end

  def open_html_file
    file ||= File.open(html_file_path)
  end

  def doc
    doc = Nokogiri::HTML(open_html_file)
    open_html_file.close
    doc
  end

  def create_docset_table(db)
    db.execute <<-SQL
      CREATE TABLE IF NOT EXISTS searchIndex(id INTEGER PRIMARY KEY, name TEXT, type TEXT, path TEXT);
      CREATE UNIQUE INDEX anchor ON searchIndex (name, type, path);
    SQL
  end

  def parse_doc_into_db(doc, db)
    file_name = "./leafletjs.com/#{FILE_NAME}"
    doc.css('h2').each do |heading2|
      name = heading2.content
      type = determine_type(heading2)
      path = "#{file_name}##{heading2.attr('id')}"
      db.execute <<-SQL
        INSERT OR IGNORE INTO searchIndex(name, type, path) VALUES ('#{name}', '#{type}', '#{path}');
      SQL
    end
  end

  def determine_type(heading)
    # NOTE: This mapping is somewhat arbitrary; it could use improvement.
    case heading
      when /Event methods/
        'Method'
      when /Event objects/
        'Object'
      when /L.Browser/
        'Namespace'
      when /Util/
        'Function'
      when /L.DomEvent/
        'Function'
      when /IHandler/
        'Interface'
      when /ILayer/
        'Interface'
      when /IControl/
        'Interface'
      when /IProjection/
        'Object'
      when /Global Switches/
        'Property'
      when /L.noConflict()/
        'Method'
      when /L.version/
        'Constant'
      else
        'Class'
      end
  end
