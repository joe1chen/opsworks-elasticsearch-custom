# opsworks-elasticsearch-custom-cookbook

This cookbook contains the custom recipes mentioned in Amazon's blog post Deploying Elasticsearch with OpsWorks - (http://blogs.aws.amazon.com/application-management/post/Tx3MEVKS0A4G7R5/Deploying-Elasticsearch-with-OpsWorks)

The esplugin recipe (/recipes/esplugins.rb) automates the installation of the elasticsearch-head plugin to add a nice GUI for monitoring the state of the cluster. The community cookbook version we tested had an issue with the installation of the plugins which is why we built our own custom recipe.

A custom recipe was also required to configure monit so that it automatically restarts the elasticsearch service if it fails. The way the community cookbook sets up monit is not compatible with the OpsWorks configuration (OpsWorks itself uses monit to ensure its own agent is running). So our custom recipe simply copies the cookbook’s monit configuration file for the elasticsearch process to /etc/monit.d (/recipes/esmonit.rb).

Another custom recipe (/recipes/allocation-awareness.rb) configures Elasticsearch so that it is aware of each instance’s availability zone and spreads shards and their replicas so that even an Availability Zone disruption does not eliminate both a shard and its replica(s). This uses Opsworks’ Stack configuration JSON to retrieve the availability zone in which the particular instance is deployed and passes this information to the Elasticsearch configuration.

Finally we created a custom recipe (/recipes/cloudwatch-custom.rb) that pushes custom metrics into Amazon CloudWatch. In the example we simply send the number of Elasticsearch data nodes in the cluster. Based on that, we could create an alarm to notify us when this falls below the expected value. It would be simple to expand on this and add additional metrics.

## Usage

### opsworks-elasticsearch-custom::default

Include the following recipes in your layer's Custom Chef Recipes:

```json
opsworks-elasticsearch-custom::esplugins
opsworks-elasticsearch-custom::allocation-awareness
opsworks-elasticsearch-custom::esmonit
opsworks-elasticsearch-custom::cloudwatch-custom
```

## Contributing

1. Fork the repository on Github
2. Create a named feature branch (i.e. `add-new-recipe`)
3. Write you change
4. Write tests for your change (if applicable)
5. Run the tests, ensuring they all pass
6. Submit a Pull Request

## License and Authors

Author:: Joseph Chen (joseph.chen@gmail.com)
