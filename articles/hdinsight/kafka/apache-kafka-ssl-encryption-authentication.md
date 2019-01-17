---
title: Konfigurowanie szyfrowania SSL i uwierzytelniania dla platformy Apache Kafka w usłudze Azure HDInsight
description: Konfigurowanie szyfrowania SSL dla komunikacji między klientami platformy Kafka i brokerów platformy Kafka, jak również między brokerów platformy Kafka. Konfiguracja uwierzytelniania klientów SSL.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355655"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurowanie szyfrowania Secure Sockets Layer (SSL) i uwierzytelniania dla platformy Apache Kafka w usłudze Azure HDInsight

W tym artykule opisano sposób konfigurowania szyfrowania SSL z platformy Apache Kafka klienci platformy Apache Kafka brokerów, a także między brokerów platformy Apache Kafka. Daje ona również kroki niezbędne do uwierzytelniania konfiguracji klientów (czasami określane jako dwukierunkowe SSL).

## <a name="server-setup"></a>Konfiguracja serwera

Pierwszym krokiem jest skonfigurować magazyn kluczy i truststore na brokerów Kafka i zaimportuj certyfikat urzędu certyfikacji i certyfikatów brokera do tych magazynów.

> [!Note] 
> Ten przewodnik będzie używać certyfikatów z podpisem własnym, ale najbardziej bezpieczne rozwiązaniem jest użycie certyfikatów wystawionych przez zaufanych urzędów certyfikacji.

1. Utwórz folder o nazwie protokołu ssl, a następnie wyeksportować hasło do serwera jako zmienną środowiskową. W pozostałej części tego przewodnika, można zastąpić `<server_password>` hasłem rzeczywiste administratora serwera.

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. Następnie należy utworzyć magazyn kluczy java (kafka.server.keystore.jks) i certyfikat urzędu certyfikacji.

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. Następnie utwórz żądanie podpisywania, aby pobrać certyfikat utworzony w poprzednim kroku, które są podpisane przez urząd certyfikacji.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. Teraz wysyłać żądania podpisywania urzędu certyfikacji i Pobierz ten certyfikat z podpisem. Ponieważ firma Microsoft korzysta z certyfikatu z podpisem własnym, firma Microsoft podpisanie certyfikatu przy użyciu naszej certyfikacji `openssl` polecenia.

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. Utwórz magazyn zaufania i zaimportuj certyfikat urzędu certyfikacji.

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. Zaimportuj certyfikat publiczny urzędu certyfikacji do magazynu kluczy.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. Certyfikat z podpisem należy zaimportować do magazynu kluczy.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

Importowanie certyfikatu z podpisem do magazynu kluczy jest ostatnim kroku, które są wymagane do skonfigurowania truststore i magazyn kluczy dla brokera platformy Kafka.

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>Zaktualizuj konfigurację do używania protokołu SSL, a następnie ponownie uruchom brokerów

Mamy instalacji każdej Kafka brokera z magazynu kluczy i truststore i zaimportowaniu certyfikatów poprawny.  Następnie zmodyfikuj powiązane właściwości konfiguracji platformy Kafka, przy użyciu narzędzia Ambari, a następnie ponownie uruchom brokerów platformy Kafka.

1. Zaloguj się do witryny Azure portal i wybierz klaster usługi Azure HDInsight Apache Kafka.
1. Przejdź do interfejsu użytkownika Ambari, klikając **Ambari macierzystego** w obszarze **pulpity nawigacyjne klastra**.
1. W obszarze **brokera platformy Kafka** ustaw **odbiorników** właściwości `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. W obszarze **zaawansowane brokera platformy kafka** ustaw **security.inter.broker.protocol** właściwości `SSL`

    ![Edytowanie właściwości konfiguracji protokołu ssl platformy kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. W obszarze **brokera platformy kafka niestandardowe** ustaw **ssl.client.auth** właściwość `required`. Ten krok jest tylko wymagane, jeśli konfigurujesz uwierzytelniania, a także szyfrowanie.

    ![Edytowanie właściwości konfiguracji protokołu ssl platformy kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Dodawanie właściwości konfiguracji do Kafka `server.properties` pliku anonsowanie adresów IP zamiast w pełni kwalifikowanej domeny nazwę (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Aby sprawdzić, czy poprawnie wprowadzono zmian poprzednim, można opcjonalnie sprawdzić, że następujące wiersze są obecne w Kafka `server.properties` pliku.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Uruchom ponownie wszystkie brokerów platformy Kafka.

## <a name="client-setup-with-authentication"></a>Instalacja klienta (przy użyciu uwierzytelniania)

> [!Note]
> Poniższe kroki są wymagane tylko wtedy, gdy konfigurujesz zarówno szyfrowania SSL **i** uwierzytelniania. Jeśli tylko konfigurujesz szyfrowania, przejdź do [Instalatora klienta bez uwierzytelniania](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

1. Wyeksportuj hasła klienta. Zastąp `<client_password>` hasłem rzeczywiste administratora na komputerze klienckim platformy Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Utwórz magazyn kluczy java i uzyskać podpisany certyfikat dla brokera. Następnie skopiuj certyfikat do maszyny Wirtualnej, w którym jest uruchomiony urząd certyfikacji.

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. Przełącz się do komputera urzędu certyfikacji (wn0) do podpisywania certyfikatu klienta.

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. Przejdź do komputera klienckiego (hn1) i przejdź do `~/ssl` folderu. Skopiuj certyfikatu z podpisem na komputerze klienckim.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Wyświetl plik `client-ssl-auth.properties` za pomocą polecenia `$cat client-ssl-auth.properties`. Powinien mieć następujące wiersze:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>Instalacja klienta (bez uwierzytelniania)

1. Wyeksportuj hasła klienta. Zastąp `<client_password>` hasłem rzeczywiste administratora na komputerze klienckim platformy Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Przejdź do komputera klienckiego (hn1) i przejdź do `~/ssl` folderu. Skopiuj certyfikatu z podpisem na komputerze klienckim.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Wyświetl plik `client-ssl-auth.properties` za pomocą polecenia `$cat client-ssl-auth.properties`. Powinien mieć następujące wiersze:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest platforma Apache Kafka w HDInsight?](/../azure/hdinsight/kafka/apache-kafka-introduction)