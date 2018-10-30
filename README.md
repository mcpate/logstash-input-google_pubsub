# THIS IS JUST A POC

Basically https://github.com/logstash-plugins/logstash-input-google_pubsub forked and hacked a bit 
so that it replicates the functionality of https://github.com/logstash-plugins/logstash-input-s3sqs  
**but** in GCP environment.

Once you've set up Google Storage events so that they land in a PubSub queue, this plugin is going to consume them and 
whenever object creation message is published - it will automatically download the file to destination specified in plugin 
config.

**Note: Please make sure your credentials file grants access to both PubSub and Storage services.**

Build the plugin:
```
rake vendor
gem build logstash-input-google_pubsub.gemspec
```

Clone Logstash and build it. Plugin installation and configuration looks like this:
```
bin/logstash-plugin install ~/logstash-input-google_pubsub-1.2.1.gem

# run
bin/logstash -e 'input { 
                    google_pubsub { 
                        project_id => "PROJECT_ID"  
                        topic => "logstash-input-dev" 
                        subscription => "logstash-test-subscription" 
                        json_key_file => "~/key_file.json" 
                        file_destination_folder => "~/Downloads"
                        include_metadata => "true" 
                     } 
                   } 
                 output { 
                    stdout { 
                        codec => rubydebug 
                    } 
                 }'

```