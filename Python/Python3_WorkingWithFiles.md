# Working with Files in Python 3

- Course URL: <https://app.pluralsight.com/ilx/video-courses/python-3-working-files/course-overview>

## Finding Files

- `os.listdir(<folder-path>)` --> basic listing
  - The results are the filename, but without the "folder-path" params
  - To filter --> use string match, or `fnmatch` for more complex criteria (such as "*1*_file.csv")
- Use `glob` to search for file using pattern matching: `matched_files = Path(fld).glob(search)`

## Working with Files & Folders

- `f.stat()`: return file attributes
- `os.walk(<folder-path>)`: to traverse the folder tree
- `shutil`: module for file operations
  - `shutil.copy(src_file, dest_folder)`: copy a file
  - `shutil.copytree(src_folder, dest_folder)`: copy a folder
  - `shutil.move(src_file, dest_folder)`: for moving file or folder
- Rename file in two ways:
  - `os.rename(src, dest)`
  - `Path(src).rename(dest)`
- Deleting file: `os.remove(fn)` --> may throw `OSError`

## Archiving Files

- Zipfile module: `zipfile`
  - Open zip file: `with zipfile.ZipFile(zip_filename, options, allowZip64=True) as zip_file:`
- Create zipfile:
  - For new zipfile, options is often "w": write
  - After creation, have to write files into zipfile: `new_zip_file.write(f)`
- Add new file to existing zip file: `zip_file.write(f)`
  - Options is "a": append mode
  - Have to check if the file already in the zip file via `zip_file.namelist()`
- Reading info of files within zip file:
  - Options is "r": read mode
  - Loop through the files in `namelist()` --> information can be retrieve with `zip_file.getinfo(l)`
- Extract
  - Option is also "r": read mode
  - To extract file "fn": `zip_file.extract(fn, path=dest_path)`
  - To extract all: `zip_file.extractall(path=dest_path)`

## Reading & Writing Files

### Text file

- Start with `with open(fn) as f:`
- Reading: either `read()` or `readline()`
- Writing: open with extra option: `open(fn, 'w', encoding='utf-8')` --> replace "w" with "a" for appending
  - use `write(str)` to write the content

### CSV file

- Using `csv` module
- Reading
  - Start with `open(fn)` as normal
  - Reader: `csv.reader(csv_file, delimiter=delimiter)` --> iterator to rows of column as array
- Writing:
  - Start with `open(fn, mode='w', newline='') as csv_file:`
  - Writer: `writer = csv.writer(csv_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)`
  - To write, use `writer.writerow(string_array)`

### XML file

- Using xml.etree.ElementTree: `import xml.etree.ElementTree as ET`
- Reading:
  - Parsing the XML doc: `ET.parse(file_name)`
  - Get root: `tree.getroot()`
  - Get attribute: `root.attrib['<attribute-name>']`
  - Child node: iterate through each node
- Writing:
  - First: Getting the tree (via parsing), then the root node as with reading
  - Node creation: `ET.Element("<element-name">)` --> set attribute via `attrib`
    - Append the node to parent node as needed
  - Node update: `root.find("<xpath-expression>")` --> update node attribute via `attrib` as with creation
  - To finish, write out the tree: `tree.write(file_name)`

### JSON file

- Using `json` package
- Reading:
  - First, open file and load the json via the file handle: `json.load(json_file_handle)`
- Writing:
  - May first load the json from file handle
  - Update its using dictionary syntax: `data[<first_key>][<array_pos>][<second_key>] = value`
  - Write it out: `json.dump(json_data, write_file_handle)`
- Printing JSON data: `json.dumps(json_data, sort_keys=True, indent=4)`

### Persisting Objects

- Using `pickle` package
- Serialize an object: `serialized_obj = pickle.dumps(obj, protocol=pickle.HIGHEST_PROTOCOL)`
  - Serialize to file: `pickle.dumps(obj, file_handle, protocol=pickle.HIGHEST_PROTOCOL)`
- Deserialize an object: `obj = pickle.load(serialized_obj)` --> use a file-handle instead
