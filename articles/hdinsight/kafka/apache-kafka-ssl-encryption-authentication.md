---
title: Apache Kafka szyfrowanie SSL & Authentication — Azure HDInsight
description: Skonfiguruj szyfrowanie SSL na potrzeby komunikacji między klientami Kafka i brokerami Kafka, a także między brokerami Kafka. Skonfiguruj uwierzytelnianie SSL klientów.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5dd698b28a01ed251492cf34e9da2dda4d0c2580
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241984"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurowanie szyfrowania SSL (SSL) i uwierzytelniania Apache Kafka w usłudze Azure HDInsight

W tym artykule pokazano, jak skonfigurować szyfrowanie SSL między klientami Apache Kafka i brokerami Apache Kafka. Pokazano w nim także, jak skonfigurować uwierzytelnianie klientów (czasami określany jako dwukierunkowy protokół SSL).

> [!Important]
> Istnieją dwa klientów, których można używać do obsługi aplikacji Kafka: klient Java i klient konsoli. Tylko klient Java `ProducerConsumer.java` może korzystać z protokołu SSL zarówno do tworzenia, jak i zużywania. Klient producenta konsoli `console-producer.sh` nie działa z protokołem SSL.

## <a name="apache-kafka-broker-setup"></a>Konfiguracja brokera Apache Kafka

Konfiguracja brokera protokołu SSL Kafka będzie używać czterech maszyn wirtualnych klastra usługi HDInsight w następujący sposób:

* węzła głównego 0 — urząd certyfikacji
* węzeł procesu roboczego 0, 1 i 2-brokerzy

> [!Note] 
> W tym przewodniku zastosowano certyfikaty z podpisem własnym, ale najbardziej bezpiecznym rozwiązaniem jest użycie certyfikatów wystawionych przez zaufane urzędy certyfikacji.

Podsumowanie procesu instalacji brokera przebiega następująco:

1. Następujące kroki są powtórzone w każdym z trzech węzłów procesu roboczego:

    1. Wygeneruj certyfikat.
    1. Utwórz żądanie podpisania certyfikatu.
    1. Wyślij żądanie podpisania certyfikatu do urzędu certyfikacji.
    1. Zaloguj się do urzędu certyfikacji i podpisz żądanie.
    1. Podpunkt połączenia z podpisanym certyfikatem z powrotem do węzła procesu roboczego.
    1. Punkt SCP certyfikatu publicznego urzędu certyfikacji z węzłem procesu roboczego.

1. Po uzyskaniu wszystkich certyfikatów należy umieścić certyfikaty w magazynie certyfikatów.
1. Przejdź do Ambari i Zmień konfiguracje.

Aby ukończyć konfigurowanie brokera, Skorzystaj z poniższych szczegółowych instrukcji:

> [!Important]
> W poniższych fragmentach kodu wnX jest skrót jednego z trzech węzłów procesu roboczego i powinien zostać zastąpiony `wn0`, `wn1` lub `wn2`, zgodnie z potrzebami. `WorkerNode0_Name` i `HeadNode0_Name` powinny zostać zastąpione nazwami odpowiednich maszyn, takimi jak `wn0-abcxyz` lub `hn0-abcxyz`.

1. Wykonaj konfigurację początkową w węźle głównym 0, dla którego Usługa HDInsight wypełni rolę urzędu certyfikacji.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Wykonaj tę samą konfigurację początkową dla każdego z brokerów (węzły procesu roboczego 0, 1 i 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. W każdym z węzłów procesu roboczego wykonaj następujące kroki, korzystając z poniższego fragmentu kodu.
    1. Utwórz magazyn kluczy i wypełnij go nowym certyfikatem prywatnym.
    1. Utwórz żądanie podpisania certyfikatu.
    1. Punkt SCP żądania podpisania certyfikatu do urzędu certyfikacji (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na komputerze urzędu certyfikacji Uruchom następujące polecenie, aby utworzyć pliki kluczy urzędu certyfikacji i urzędu certyfikacji:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Przejdź do komputera urzędu certyfikacji i podpisz wszystkie odebrane żądania podpisania certyfikatu:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Wyślij podpisane certyfikaty z powrotem do węzłów procesu roboczego z urzędu certyfikacji (headnode0).

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

1. W każdym węźle procesu roboczego Dodaj certyfikat publiczny urzędu certyfikacji do truststore i magazynu kluczy. Następnie Dodaj własny podpisany certyfikat węzła procesu roboczego do magazynu kluczy

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Zaktualizuj konfigurację Kafka, aby korzystać z protokołu SSL i brokerów ponownego uruchamiania

Teraz można skonfigurować każdego brokera Kafka z magazynem kluczy i truststore i zaimportować odpowiednie certyfikaty. Następnie zmodyfikuj powiązane właściwości konfiguracji Kafka za pomocą Ambari, a następnie ponownie uruchom brokerów Kafka.

Aby ukończyć modyfikację konfiguracji, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal i wybierz klaster Apache Kafka usługi Azure HDInsight.
1. Przejdź do interfejsu użytkownika Ambari, klikając pozycję **Strona główna** w obszarze **pulpity nawigacyjne klastra**.
1. W obszarze **Broker Kafka** ustaw właściwość **detektory** na `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. W obszarze **Advanced Kafka-Broker** ustaw właściwość **Security. Inter. Broker. Protocol** na `SSL`

    ![Edytowanie właściwości konfiguracji protokołu SSL Kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. W obszarze **Custom Kafka-Broker** ustaw właściwość **SSL. Client. auth** na `required`. Ten krok jest wymagany tylko w przypadku konfigurowania uwierzytelniania i szyfrowania.

    ![Edytowanie właściwości konfiguracji protokołu SSL Kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. W obszarze **Advanced Kafka-ENV** Dodaj następujące wiersze na końcu właściwości **szablonu Kafka-ENV** .

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Edytowanie właściwości szablonu Kafka-ENV w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Uruchom ponownie wszystkie brokerów Kafka.
1. Uruchom klienta administracyjnego z opcjami producent i klient, aby sprawdzić, czy zarówno producenci, jak i konsumenci pracują na porcie 9093.

## <a name="client-setup-with-authentication"></a>Konfiguracja klienta (z uwierzytelnianiem)

> [!Note]
> Poniższe kroki są wymagane tylko w przypadku konfigurowania szyfrowania SSL **i** uwierzytelniania. Jeśli konfigurujesz tylko szyfrowanie, przejdź do [konfiguracji klienta bez uwierzytelniania](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Aby zakończyć instalację klienta, wykonaj następujące kroki:

1. Zaloguj się do komputera klienckiego (hn1).
1. Utwórz magazyn kluczy języka Java i uzyskaj podpisany certyfikat dla brokera. Następnie skopiuj certyfikat na maszynę wirtualną, na której działa urząd certyfikacji.
1. Przejdź do komputera urzędu certyfikacji (hn0), aby podpisać certyfikat klienta.
1. Przejdź do komputera klienckiego (hn1) i przejdź do folderu `~/ssl`. Skopiuj podpisany certyfikat na komputer kliencki.

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

Na koniec Wyświetl plik `client-ssl-auth.properties` przy użyciu polecenia `cat client-ssl-auth.properties`. Powinien on zawierać następujące wiersze:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Instalacja klienta (bez uwierzytelniania)

Jeśli nie potrzebujesz uwierzytelniania, wykonaj czynności wymagane do skonfigurowania tylko szyfrowania SSL:

1. Zaloguj się do komputera klienckiego (hn1) i przejdź do folderu `~/ssl`
1. Skopiuj podpisany certyfikat do komputera klienckiego z komputera urzędu certyfikacji (wn0).
1. Zaimportuj certyfikat urzędu certyfikacji do truststore
1. Zaimportuj certyfikat urzędu certyfikacji do magazynu kluczy

Te kroki przedstawiono w poniższym fragmencie kodu.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Na koniec przejrzyj plik `client-ssl-auth.properties` z poleceniem `cat client-ssl-auth.properties`. Powinien on zawierać następujące wiersze:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Następne kroki

* [Co to jest Apache Kafka w usłudze HDInsight?](apache-kafka-introduction.md)
