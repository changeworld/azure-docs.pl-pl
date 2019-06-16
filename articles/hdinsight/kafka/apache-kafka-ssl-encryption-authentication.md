---
title: Konfigurowanie szyfrowania SSL i uwierzytelniania dla platformy Apache Kafka w usłudze Azure HDInsight
description: Konfigurowanie szyfrowania SSL dla komunikacji między klientami platformy Kafka i brokerów platformy Kafka, jak również między brokerów platformy Kafka. Skonfiguruj uwierzytelnianie klientów SSL.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5d567074a0038915cc43a585b34c9c71ccf3eb1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464991"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurowanie szyfrowania Secure Sockets Layer (SSL) i uwierzytelniania dla platformy Apache Kafka w usłudze Azure HDInsight

W tym artykule pokazano, jak skonfigurować szyfrowanie SSL między klientami platformy Apache Kafka i brokerów platformy Apache Kafka. On również dowiesz się, jak skonfigurować uwierzytelnianie klientów (czasami określane jako dwukierunkowe SSL).

> [!Important]
> Istnieją dwa klientów, których można użyć w przypadku aplikacji platformy Kafka: klienta Java, jak i klienta konsoli. Tylko klient Java `ProducerConsumer.java` można użyć protokołu SSL dla tworzenia i używania. Konsola klienta producenta `console-producer.sh` nie działa przy użyciu protokołu SSL.

## <a name="apache-kafka-broker-setup"></a>Instalator programu Apache Kafka brokera

Konfiguracja brokera połączenia SSL z platformą Kafka użyje cztery maszyny wirtualne klastra HDInsight w następujący sposób:

* węzłem głównym 0 - urząd certyfikacji (CA)
* węzeł procesu roboczego 0, 1 i 2 - brokerzy

> [!Note] 
> Ten przewodnik będzie używać certyfikatów z podpisem własnym, ale najbardziej bezpieczne rozwiązaniem jest użycie certyfikatów wystawionych przez zaufanych urzędów certyfikacji.

Podsumowanie procesu konfiguracji brokera jest następująca:

1. Poniższe kroki są powtarzane na każdym z węzłów procesu roboczego trzy:

    1. Wygeneruj certyfikat.
    1. Utworzenie certyfikatu podpisywania żądania.
    1. Wyślij certyfikatu podpisywania żądania urząd certyfikacji (CA).
    1. Zaloguj się do urzędu certyfikacji i podpisania żądania.
    1. Punkt połączenia usługi certyfikatu z podpisem z powrotem do węzła procesu roboczego.
    1. Punkt połączenia usługi certyfikat publiczny urzędu certyfikacji z węzłem procesu roboczego.

1. Gdy masz wszystkie certyfikaty, umieść certyfikatów z magazynu certyfikatów.
1. Przejdź do narzędzia Ambari i zmień konfigurację.

Użyj poniższych instrukcji szczegółowe, aby ukończyć instalację brokera:

> [!Important]
> W następujących fragmentach kodu wnX stanowi skrót od jednego z węzłów procesu roboczego trzy i powinien zostać zastąpiony `wn0`, `wn1` lub `wn2` odpowiednio. `WorkerNode0_Name` i `HeadNode0_Name` powinny być zastąpione przy użyciu nazwy odpowiednich maszyn, takie jak `wn0-abcxyz` lub `hn0-abcxyz`.

1. Początkowej konfiguracji należy wykonać w węźle głównym 0, która w przypadku HDInsight będzie pełnić rolę urząd certyfikacji (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Wykonaj tę samą konfigurację początkową na każdym z brokerów (węzły procesu roboczego 0, 1 i 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Na każdym z węzłów procesu roboczego wykonaj następujące czynności za pomocą fragmencie kodu poniżej.
    1. Tworzenie magazynu kluczy i wypełnić ją przy użyciu nowego certyfikatu prywatnego.
    1. Utwórz żądanie podpisania certyfikatu.
    1. Punkt połączenia usługi żądania podpisywania certyfikatu do urzędu certyfikacji (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Zmień na komputerze urzędu certyfikacji i zarejestrować wszystkie odebrane certyfikat podpisywania żądań:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Wysyłać podpisanych certyfikatów do węzłów procesu roboczego z urzędu certyfikacji (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Wyślij certyfikat publiczny urzędu certyfikacji do każdego węzła procesu roboczego.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. W każdym węźle procesu roboczego należy dodać certyfikatu publicznego urzędów certyfikacji do truststore i magazyn kluczy. Następnie należy dodać certyfikat z podpisem tego węzła procesu roboczego do magazynu kluczy

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Zaktualizuj konfigurację platformy Kafka używają protokołu SSL, a następnie ponownie uruchomić brokerów

Masz teraz skonfigurować każdy broker platformy Kafka z magazynu kluczy i truststore i zaimportowaniu certyfikatów poprawny. Następnie zmodyfikuj powiązane właściwości konfiguracji platformy Kafka, przy użyciu narzędzia Ambari, a następnie ponownie uruchom brokerów platformy Kafka.

Aby ukończyć modyfikacji konfiguracji, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal i wybierz klaster usługi Azure HDInsight Apache Kafka.
1. Przejdź do interfejsu użytkownika Ambari, klikając **Ambari macierzystego** w obszarze **pulpity nawigacyjne klastra**.
1. W obszarze **brokera platformy Kafka** ustaw **odbiorników** właściwości `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. W obszarze **zaawansowane brokera platformy kafka** ustaw **security.inter.broker.protocol** właściwości `SSL`

    ![Edytowanie właściwości konfiguracji protokołu ssl platformy Kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. W obszarze **brokera platformy kafka niestandardowe** ustaw **ssl.client.auth** właściwość `required`. Ten krok jest tylko wymagane, jeśli konfigurujesz uwierzytelniania i szyfrowania.

    ![Edytowanie właściwości konfiguracji protokołu ssl platformy kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Dodawanie właściwości konfiguracji do Kafka `server.properties` pliku anonsowanie adresów IP zamiast w pełni kwalifikowanej domeny nazwę (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Aby sprawdzić, czy poprawnie wprowadzono zmian poprzednim, można opcjonalnie sprawdzić, że następujące wiersze są obecne w Kafka `server.properties` pliku.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Uruchom ponownie wszystkie brokerów platformy Kafka.
1. Uruchom klienta administratora przy użyciu opcji producentów i konsumentów, aby sprawdzić, czy na porcie 9093 działają zarówno producentów i konsumentów.

## <a name="client-setup-with-authentication"></a>Instalacja klienta (przy użyciu uwierzytelniania)

> [!Note]
> Poniższe kroki są wymagane tylko wtedy, gdy konfigurujesz zarówno szyfrowania SSL **i** uwierzytelniania. Jeśli tylko konfigurujesz szyfrowania, przejdź do [Instalatora klienta bez uwierzytelniania](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Wykonanie poniższych kroków w celu ukończenia instalacji klienta:

1. Zaloguj się do komputera klienckiego (hn1).
1. Utwórz magazyn kluczy java i uzyskać podpisany certyfikat dla brokera. Następnie skopiuj certyfikat do maszyny Wirtualnej, w którym jest uruchomiony urząd certyfikacji.
1. Przełącz się do komputera urzędu certyfikacji (hn0) do podpisywania certyfikatu klienta.
1. Przejdź do komputera klienckiego (hn1) i przejdź do `~/ssl` folderu. Skopiuj certyfikatu z podpisem na komputerze klienckim.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Na koniec Wyświetl plik `client-ssl-auth.properties` za pomocą polecenia `cat client-ssl-auth.properties`. Powinien mieć następujące wiersze:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Instalacja klienta (bez uwierzytelniania)

Jeśli nie potrzebujesz uwierzytelniania są kroki, aby skonfigurować tylko szyfrowanie SSL:

1. Zaloguj się do komputera klienckiego (hn1) i przejdź do `~/ssl` folderu
1. Skopiuj certyfikatu z podpisem na komputerze klienckim, na komputerze urzędu certyfikacji (wn0).
1. Zaimportuj certyfikat urzędu certyfikacji do truststore
1. Zaimportuj certyfikat urzędu certyfikacji do magazynu kluczy

Te kroki są wyświetlane w poniższy fragment kodu.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Na koniec Wyświetl plik `client-ssl-auth.properties` za pomocą polecenia `cat client-ssl-auth.properties`. Powinien mieć następujące wiersze:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest platforma Apache Kafka w HDInsight?](apache-kafka-introduction.md)
