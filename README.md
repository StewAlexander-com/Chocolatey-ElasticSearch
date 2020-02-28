# Chocolatey-ElasticSearch
How to get Chocolatey parsed by Elastic Search ELK stack...

---
* First, Chocolatey is an open source package manager for MS Windows, to install it see [here](https://chocolatey.org/install)
* Why would you want to expose Chocolatey logs to ElasticSearch's ELK stack?
> We are using Chocolatey to update packages on several thousand machines through a local repository, using Jenkins and Salt in the background, and we noticed once in awhile a nuspec or other meta-file would get hung up, causing something not to not be updated correctly. The issue was we didn't notice this until we went looking.

* In order to fix the issue of knowing whether a meta-file or some other issue was causing systems not to update, we decided to add chocolatey to the repo (thereby it essentially updating itself), where it collected the same files as the other systems (Google Chrome and so forth), and expose the Chocolatey logs via ***ElasticSearch FileBeat***

#### Here's How:
