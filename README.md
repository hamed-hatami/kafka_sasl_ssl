# kafka_sasl_ssl

    This Docker-Compose is to create a SASL_SSL protocol with mechanism SCRAM-SHA-256 enabled kafka instance for local testing purposes


## How to generate keystore and truststore for SSL

    openssl genrsa -out root.key
    openssl req -new -x509 -key root.key -out root.crt  (*** Common Name value should be kafka ***)
    keytool -keystore kafka.truststore.jks -alias CARoot -import -file root.crt
    keytool -keystore kafka.keystore.jks -alias kafka -validity 365 -genkey -keyalg RSA -ext SAN=DNS:kafka
    keytool -keystore kafka.keystore.jks -alias kafka -certreq -file kafka.unsigned.crt
    openssl x509 -req -CA root.crt -CAkey root.key -in kafka.unsigned.crt -out kafka.signed.crt -days 365 -CAcreateserial
    keytool -keystore kafka.keystore.jks -alias CARoot -import -file root.crt
    keytool -keystore kafka.keystore.jks -alias kafka -import -file kafka.signed.crt


## In order to start 
    
    docker-compose -f docker-compose-ssl.yml up -d
    

## In order to stop    
    
    docker-compose -f docker-compose-ssl.yml down

## In order to connect to Kafka securely

    You have to configure Offset Explorer as follows : 

    Properties tab :   
       Zookeeper Host : localhost
       Zookeeper Port : 2181
    Security tab :
       Type : SASL_SSL
    Advanced tab :
       Bootstrap servers : localhost:9093
       SASL Mechanism : SCRAM-SHA-256
    JAAS Config tab :
       org.apache.kafka.common.security.scram.ScramLoginModule required  username="admin" password="admin_secret";

