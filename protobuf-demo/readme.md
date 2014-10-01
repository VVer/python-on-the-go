python下使用protobuf
====
1.下载protobuf编译器protoc<br/>
<p>如果是windows平台，可以直接到http://code.google.com/p/protobuf/downloads/ 下载编译好的版本,如protoc-2.6.0-win32.zip，将protoc解压到目的目录，然后将该目录加到环境变量中去。</p>

2.python安装protobuf库
```Bash
    easy_install protobuf
```
3.定义一个protobuf文件，如下所示,定义了addressbook.proto：
```c
message Person {
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phone = 4;
}

message AddressBook {
  repeated Person person = 1;
}
```
4.编译，生成对应的py文件
```Bash
protoc -I=./ --python_out=./   addressbook.proto
```
编译完成之后，将会生成一个py文件，如本例中生成的py文件是addressbook_pb2.py

5.读写protobuf示例
```Python
#! /usr/bin/python
 
import addressbook_pb2
import sys

# This function fills in a Person message based on user input.
def PromptForAddress(person):
  person.id = int(raw_input("Enter person ID number: "))
  person.name = raw_input("Enter name: ").decode('gb2312') 
  

  email = raw_input("Enter email address (blank for none): ")
  if email != "":
    person.email = email

  while True:
    number = raw_input("Enter a phone number (or leave blank to finish): ")
    if number == "":
      break

    phone_number = person.phone.add()
    phone_number.number = number

    type = raw_input("Is this a mobile, home, or work phone? ")
    if type == "mobile":
      phone_number.type = addressbook_pb2.Person.MOBILE
    elif type == "home":
      phone_number.type = addressbook_pb2.Person.HOME
    elif type == "work":
      phone_number.type = addressbook_pb2.Person.WORK
    else:
      print "Unknown phone type; leaving as default value."
  

# Main procedure:  Reads the entire address book from a file,
#   adds one person based on user input, then writes it back out to the same
#   file.
if len(sys.argv) != 2:
  print "Usage:", sys.argv[0], "ADDRESS_BOOK_FILE"
  sys.exit(-1)

address_book = addressbook_pb2.AddressBook()

# Read the existing address book.
try:
  f = open(sys.argv[1], "rb")
  address_book.ParseFromString(f.read())
  size=len(address_book.person)
  if size >0 :
  	print "id\tname"
  for one in address_book.person:
  	print one.id,"\t",one.name.encode('gb2312')
  f.close()
except IOError:
	print sys.argv[1] + ": File not found.  Creating a new file."

# Add an address.
while True:
	answer = raw_input("Add a person (yes or no [y/n])?: ")
	if (answer.lower()== 'n'):
		print 'bye'
		break
	elif answer.lower() =='y':
		PromptForAddress(address_book.person.add())

# Write the new address book back to disk.
f = open(sys.argv[1], "wb")
f.write(address_book.SerializeToString())
f.close()


```
