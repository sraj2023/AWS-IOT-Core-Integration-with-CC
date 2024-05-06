# AWS-IOT-Core-Integration-with-CC

## Prerequisites
- Create a Thing on AWS IOT Core. Download the certificates and store them in the certificates directory under the parent directory.
- Create a Standard Cluster and API Key and Secret on Confluent Cloud.
- Install Python3


## Steps to follow:
- Run these commands to generate keystore.ks and truststore.ks. You will be prompted to create passwords.
```
$ openssl pkcs12 -export -in certificate.pem.crt -inkey private.pem.key -out p12.keystore -name alias

$ openssl pkcs12 -export -out eneCert.pkcs12 -in certificate.pem.crt -inkey private.pem.key

$ keytool -import -v -trustcacerts -alias endeca-ca -file certificate.pem.crt -keystore truststore.ks

$ keytool -v -importkeystore -srckeystore eneCert.pkcs12 -srcstoretype PKCS12 -destkeystore keystore.ks -deststoretype JKS

$ keytool -import -v -trustcacerts -alias endeca-ca -file AmazonRootCA1.pem -keystore truststore.ks
```

- Once the keystore.ks and truststore.ks file gets generated, For the mqtt.ssl.trust.store.file and the mqtt.ssl.key.store.file properties in the connector-config.json file, you encode the truststore or keystore file in base64.

- Take the encoded string, add the data:text/plain:base64 prefix, and then use the entire string as the property entry in the connector-config.json. For example:
> "mqtt.ssl.key.store.file" : "data:text/plain;base64,u3+7QAAAAIAAAACAAAAAQAGY2xpZ...=="

- Update the mqtt.ssl.key.password, mqtt.ssl.key.store.password and mqtt.ssl.trust.store.password with the password you created initally.

- Update the connector-config.json file with Confluent Cloud Cluster API key and secret.

- Update the AWS IoT Core Endpoint and topic to subscribe to.

- Deploy the MQTT Source Connector on Confluent Cloud using [Confluent Cloud CLI](https://docs.confluent.io/cloud/current/connectors/connect-api-section.html#json-file-payload-example).

- Wait for the connector to get provisioned successfully.

- In producer-mqtt.py, update the AWS IoT Core Endpoint, the path for the certs and private key and the topic name to subscribe to. (Same as mqtt.topics value from connector-config.json)

- Once done, run the python file tp produce dummy data to the AWS IoT Thing.

- Test the data being produced to the kafka topic 'mqtt'

