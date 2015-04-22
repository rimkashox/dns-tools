#dns-tools
Tools for studying DNS Registrations; used and developed at the BYU Internet Research Lab.

For more information, visit the [BYU Internet Research Lab homepage](http://www.fht.byu.edu/)

---
##Parsing
###About
This toolset was developed to analyze the `.COM` and `.NET` zone files downloaded daily from Verisign's FTP servers (more info [here](http://www.verisigninc.com/en_US/channel-resources/domain-registry-products/zone-file/index.xhtml)). The directory structure assumed is:
```
dns-registration
├── 2013-12-31
│   ├── com.zone.gz
│   ├── master.name.zone.gz
│   ├── net.zone.gz
│   └── root.zone.gz
├── 2014-1-1
│   ├── com.zone.gz
│   └── master.name.zone.gz
└── 2014-1-2
    ├── com.zone.gz
    └── master.name.zone.gz
``` 
Note that single-digit months and days are not zero-padded on the left.


###Pre-Requisites
It is assumed that the linux OS already has MySQL Server (and user/password/database/permissions already set up), g++, and virtualenv installed. To speed up the process, it is recommended that a RAM disk be created for temporary usage. This can be setup with a line in `/etc/fstab`:
```
tmpfs /data/untarplane		tmpfs size=12G 0 0
```

### Installation
The python installation depends on g++ being installed. It is recommended to setup and activate a virtual environment for this installation:
```
virtualenv ~/.virtualenvs/dns-tools
source ~/.virtualenvs/dns-tools/bin/activate
```
Now install the requirements:
```
pip install -r requirements.txt
```
### Configuration
Begin by specifying the database details in `zonefile_parse.h` and `dbConfig.py`. 

The `autoparse.sh` script allows us to use an index file to specify which dates to parse. This is useful, for example, if you encounter an error during the parsing (out of storage, etc). You can recover by generating an new index file and starting the `autoparse.sh` script again, and it will pick up where it left off. Specify source and destination directories for parsing in the `autoparse.sh` script:
```
SOURCE_DIR=/path/to/dns-registration 		# the root directory of the tree above
RAM_DRIVE_DIR=/data/untarplane				# the RAM disk created above in /etc/fstab
INDEX_FILE=index_file.txt 					# the index file generated by Date_Indexer.py
PARSE_SCRIPT_DIR=$RAM_DRIVE_DIR/zonefile 	# the destination for the parser executable
```
### Usage
Begin by generating an index file for the `autoparse.sh` script to use:
```
python Date_Indexer.py --help
python Date_Indexer.py -v -d /path/to/dns-registration
```
And start the `autoparse.sh` script:
```
./autoparse.sh &> dns-results.txt
```

Since the parsing takes a long time (~2 weeks for 1.5 years of daily downloads), it is recommended to background the process or use a `screen` session. 

Also, since new zone files are typically downloaded daily, this parsing can easily be set up with a `cron` job to parse each new daily download; just generate a new index file and start the `autoparse.sh` script again. 

##Study
Run the study to generate graphs for 
* Domains added and removed over time
* Net gain of domains over time
* Quantity of Domains over time

The study can be run with the following:
```
source ~/.virtualenvs/dns-study/bin/activate
python dns-study.py
```

Here are some example graphs generated:

###.COM
![.COM Domains Added/Removed]()
![.COM Domains Net Gain]()
![.COM Domains Quantity]()

###.NET
![.NET Domains Added/Removed]()
![.NET Domains Net Gain]()
![.NET Domains Quantity]()