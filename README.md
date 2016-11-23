#Accessing Azure Storage with CLI on Dockers#

##1. Install Azure Command-Line Interface (CLI)##
_Source:_ [CLI Installation](https://docs.microsoft.com/en-us/azure/xplat-cli-install)

The CLI installation guide offers 3 options: 

1. Node package manager, 
2. Installers (Linux and OSX)  
3. Docker Containers 

I have Docker installed on my machine so I chose option 3:

    docker run -it microsoft/azure-cli

which outputs:

![Alt text](./images/DockerResponse.png?raw=true "Optional Title")

I run azure help to see list of commands, and azure --v to check the version of Azure cli on this docker.

-----
##2. Access To Azure Storage##
_Source:_ [Using Azure Storage with CLI](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli)

### 2.1 Copy file onto Docker

This command copies a file into the Docker directory, so that I can upload it onto Azure

    docker cp foo.txt <docker_CONTAINER_ID>:<path_to_save_file>/foo.txt    


-----
### 2.2 Login to Azure

In order to log into my Azure subscription, I run 

    azure login
    
which provides an authentication code. I log into the web address provided (https://aka.ms/devicelogin), enter the code and the terminal output is as follows:

![Alt text](./images/Login.png?raw=true "Optional Title")


-----
### 2.3 Create new storage account

I used this command to create a new storage acount

    azure storage account create teststorageusingcli

and when prompted gave the following inputs. The process fails with the following output

![Alt text](./images/StorageAccountFail.png?raw=true "Optional Title")

I have not found a way to create a new resource group. A resource group defines permissions and access for a set of resources.
Therefore, I am using an existing resource group name (TADAP) that I previously created on Azure Portal. 

![Alt text](./images/StorageAccountOK.png?raw=true "Optional Title")

This works fine.


-----
### 2.4 Get connection string

I need a connection string so that I can create my blob container into this storage account.

    azure storage account connectionstring show <storage_account>

When prompted, I provide the resource group name I used earlier. This returns a connection string to the storage account.

![Alt text](./images/ConnectionString.png?raw=true "Optional Title")

I export the connection string into the variable AZURE_STORAGE_CONNECTION_STRING

    export AZURE_STORAGE_CONNECTION_STRING='DefaultEndpointsProtocol=https;AccountName=teststoragewithcli;AccountKey=MM..XXX'

I check with

	echo $AZURE_STORAGE_CONNECTION_STRING 
	
to see that the variable is correctly set.

-----
### 2.5 Create new storage container

I created the storage container with the following commands. The output has been edited to remove IDs.

	root@c1ee7439398b:/# azure storage container create mytestcontainer
	info:    Executing command storage container create
	+ Creating storage container mytestcontainer                                   
	+ Getting storage container information                                        
	data:    {
	data:        name: 'mytestcontainer',
	data:        metadata: {},
	data:        etag: '"0x8D412DE11264811"',
	data:        lastModified: 'Tue, 22 Nov 2016 13:47:14 GMT',
	data:        lease: { status: 'unlocked', state: 'available' },
	data:        requestId: 'XXXXXXX-0001-002a-XX-XXXX98000000',
	data:        publicAccessLevel: 'Off'
	data:    }
	info:    storage container create command OK


-----
### 2.6 Upload a new blob

I am uploading a pdf onto my storage container, I am using the command as follows:

    azure storage blob upload <path_to_file> <storage_container> <name_for_new_blob>
 
The response is:
 
![Alt text](./images/BlobUpload.png?raw=true "Optional Title")
  
 When I look on the Azure Storage Explorer, I can see the storage account (teststoragewithcli), container (mytestcontainer) and paper (mypaper) that I uploaded.

![Alt text](./images/AzureStorage.png?raw=true "Optional Title")


-----
### 2.7 Download a blob

I used the command

     azure storage blob download <container_name> <blob_name> <path_to_download_to>
 
 and I can see that the file has been deposited in the directory I specified.
 
![Alt text](./images/BlobDownload.png?raw=true "Optional Title")    
 

    