default: test

test:secrets

NAMESPACE := <YOUR_NAMESPACE>
HIGH_SECURITY_ENABLED := <FIPS_MODE>

CLUSTERING_SERVICE_IMAGE := cloudservices-docker-dev-local.bin.pega.io/platform/clustering-service:2.0.0-SNAPSHOT
ENC_KEYSTORE_PASSWORD := mystorePwd
ENC_TRUSTSTORE_PASSWORD := mystorePwd
ENCRYPTION_KEYSTORE_NAME := cluster-keystore
ENCRYPTION_TRUSTSTORE_NAME := cluster-truststore
ALIAS := myalias

ifeq ($(HIGH_SECURITY_ENABLED), true)
secrets:
	docker run --name hazelcast-helm-charts-certs -i -w /tmp \
    		$(CLUSTERING_SERVICE_IMAGE) \
            		/bin/sh -c " \
            		         ./certs.sh HIGH_SECURITY_ENABLED $(ENC_KEYSTORE_PASSWORD) $(ENCRYPTION_KEYSTORE_NAME) $(ENC_TRUSTSTORE_PASSWORD) $(ENCRYPTION_TRUSTSTORE_NAME) $(ALIAS)" &&\
                             docker cp hazelcast-helm-charts-certs:/tmp/$(ENCRYPTION_KEYSTORE_NAME).jks ./ &&\
                             docker cp hazelcast-helm-charts-certs:/tmp/$(ENCRYPTION_TRUSTSTORE_NAME).jks ./ &&\
                             kubectl create secret generic hz-encryption-secrets --from-literal=HZ_SSL_KEYSTORE_PASSWORD=$(ENC_KEYSTORE_PASSWORD) --from-literal=HZ_SSL_TRUSTSTORE_PASSWORD=$(ENC_TRUSTSTORE_PASSWORD) --from-file=$(ENCRYPTION_KEYSTORE_NAME).jks --from-file=$(ENCRYPTION_TRUSTSTORE_NAME).jks --namespace=$(NAMESPACE) &&\
                             rm -f $(ENCRYPTION_KEYSTORE_NAME).jks  $(ENCRYPTION_TRUSTSTORE_NAME).jks
else
secrets:
	docker run --name hazelcast-helm-charts-certs -i -w /tmp \
    		$(CLUSTERING_SERVICE_IMAGE) \
            		/bin/sh -c " \
            		         ./certs.sh SSL $(ENC_KEYSTORE_PASSWORD) $(ENCRYPTION_KEYSTORE_NAME) $(ENC_TRUSTSTORE_PASSWORD) $(ENCRYPTION_TRUSTSTORE_NAME) $(ALIAS)" && \
            		docker cp hazelcast-helm-charts-certs:/tmp/$(ENCRYPTION_KEYSTORE_NAME).jks ./ &&\
            		docker cp hazelcast-helm-charts-certs:/tmp/$(ENCRYPTION_TRUSTSTORE_NAME).jks ./ &&\
                    docker rm -f hazelcast-helm-charts-certs &&\
                    kubectl create secret generic hz-encryption-secrets --from-literal=HZ_SSL_KEYSTORE_PASSWORD=$(ENC_KEYSTORE_PASSWORD) --from-literal=HZ_SSL_TRUSTSTORE_PASSWORD=$(ENC_TRUSTSTORE_PASSWORD) --from-file=$(ENCRYPTION_KEYSTORE_NAME).jks --from-file=$(ENCRYPTION_TRUSTSTORE_NAME).jks --namespace=$(NAMESPACE) &&\
                   rm -f $(ENCRYPTION_KEYSTORE_NAME).jks  $(ENCRYPTION_TRUSTSTORE_NAME).jks

endif