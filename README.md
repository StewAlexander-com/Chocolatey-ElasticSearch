# Chocolatey-ElasticSearch
How to get Chocolatey parsed by the Elastic Search ELK stack...

---
* First, Chocolatey is an open source package manager for MS Windows, to install it see [here](https://chocolatey.org/install)
* Why would you want to expose Chocolatey logs to ElasticSearch's ELK stack?
> We are using Chocolatey to update packages on several thousand machines through a local repository, using Jenkins and Salt in the background, and we noticed once in awhile a nuspec or other meta-file would get hung up, causing something not to not be updated correctly. The issue was, we didn't notice this problem until we went looking.

* In order to fix the issue of knowing whether a meta-file or some other issue was causing systems not to update, we decided to add chocolatey to the repo (thereby essentially updating itself), where it collected and updated the same files as the other systems (Google Chrome and so forth), and expose the created Chocolatey log entries via ***ElasticSearch FileBeat*** to our Elk stack.

#### Here's How:
* As said above first we installed Chocolatey onto our Chocolatey Repo Server (We are using a [Nexus Repository](https://www.sonatype.com/nexus-repository-oss) for our local proxy repo, see how to get this running in your environment here: [Setting up Nexus as a local Chocolatey Repo](https://www.youtube.com/watch?v=UehkG1VHtz0))
* We then downloaded the files we wanted to monitor to our repo server using ```Choco install <filename>```
* This created a log at ```C:\ProgramData\chocolatey\logs```
* In order to get the log data we wanted up to ElasticSearch we used their [FileBeat plugin](https://www.elastic.co/downloads/beats/filebeat) for Windows
* We downloaded the plugin, installed FileBeat using _PowerShell_, and updated the ```filebeat.yml``` file under ```filebeat.inputs:``` 
   * Set ```enabled:``` to **true** 
   * Set ```paths``` to ```C:\ProgramData\chocolatey\logs\chocolatey.log```
   * And for our purposes, set ```exclude_lines:``` to ```['[DEBUG]']```
   * We commented out the ```Elasticsearch Output``` lines, as we wanted to parse the logs via ***LogStash***
   * We then set the _Logstash Output_ ```hosts:``` to our logstash server and port address
* After saving our edits, we started the _filebeat_ service, with the Windows ***Services*** app.
* After correcting some minor syntax errors, and restarting the filebeat service, our _ElasticSearch Kibana_ client started seeing Chocolatey log information
* We expect this should work in your environment also, and is a convenient way (if you are using ElasticSearch already) to get notified if packages aren't updating for some reason.
### What to do if meta files are corrupted in your local Nexus proxy repo:
* Log in as admin into the Nexus repo web GUI
* Click the _gear_ icon that appears up next to the search bar
* Click on the _Repositories_ icon on the left
* Click on the repository you want to fix (such a _chocolatey_group_)
* Click on the ***Invalidate cache*** button 
> This will clear the meta files for the repo, and in the case of a proxy repo, request new meta files from the Chocolatey.org repository, which in our situation cleared the issue.
