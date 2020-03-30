---
title: Uwierzytelnianie & szyfrowania SSL platformy Apache Kafka — Usługa Azure HDInsight
description: Skonfiguruj szyfrowanie SSL do komunikacji między klientami platformy Kafka a brokerami platformy Kafka, a także między brokerami platformy Kafka. Konfigurowanie uwierzytelniania SSL klientów.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 4a363caf61046cf39c31ae2d5f35622b7b9109f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129991"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurowanie szyfrowania i uwierzytelniania warstwy SSL (Secure Sockets Layer) dla platformy Apache Kafka w usłudze Azure HDInsight

W tym artykule pokazano, jak skonfigurować szyfrowanie SSL między klientami Apache Kafka i brokerami Apache Kafka. Pokazuje również, jak skonfigurować uwierzytelnianie klientów (czasami nazywane dwukierunkowym protokołem SSL).

> [!Important]
> Istnieją dwa klienci, których można użyć w aplikacjach platformy Kafka: klient Java i klient konsoli. Tylko klient `ProducerConsumer.java` Java może używać SSL zarówno do produkcji, jak i konsumpcji. Klient `console-producer.sh` producenta konsoli nie działa z SSL.

> [!Note] 
> Producent konsoli HDInsight Kafka w wersji 1.1 nie obsługuje ssl.
## <a name="apache-kafka-broker-setup"></a>Apache Kafka konfiguracji brokera

Konfiguracja brokera SSL platformy Kafka będzie używać czterech maszyn wirtualnych klastra HDInsight w następujący sposób:

* headnode 0 - Urząd certyfikacji (CA)
* węzeł pracownika 0, 1 i 2 - brokerzy

> [!Note] 
> W tym przewodniku będą używane certyfikaty z podpisem własnym, ale najbezpieczniejszym rozwiązaniem jest użycie certyfikatów wystawionych przez zaufane urzędy certyfikacji.

Podsumowanie procesu konfiguracji brokera jest następujące:

1. Następujące kroki są powtarzane w każdym z trzech węzłów procesu roboczego:

    1. Wygeneruj certyfikat.
    1. Utwórz żądanie podpisywania certyfikatu.
    1. Wyślij żądanie podpisywania certyfikatu do urzędu certyfikacji(CA).
    1. Zaloguj się do urzędu certyfikacji i podpisz żądanie.
    1. Protokół SCP podpisany certyfikat z powrotem do węzła procesu roboczego.
    1. SCP certyfikat publiczny urzędu certyfikacji do węzła procesu roboczego.

1. Po uzyskaniu wszystkich certyfikatów umieść certyfikaty w magazynie certyfikatów.
1. Przejdź do Ambari i zmień konfiguracje.

Aby ukończyć konfigurację brokera, wykonaj następujące szczegółowe instrukcje:

> [!Important]
> W poniższych fragmentach kodu wnX jest skrótem dla jednego z `wn0`trzech węzłów procesu `wn1` roboczego i powinien być zastąpiony , lub `wn2` w stosownych przypadkach. `WorkerNode0_Name`i `HeadNode0_Name` powinny być zastąpione nazwami odpowiednich maszyn.

1. Wykonaj początkową konfigurację w węźle głównym 0, który dla usługi HDInsight wypełni rolę urzędu certyfikacji (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Wykonaj tę samą konfigurację początkową dla każdego brokera (węzły procesu roboczego 0, 1 i 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. W każdym z węzłów procesu roboczego wykonaj następujące kroki przy użyciu fragmentu kodu poniżej.
    1. Utwórz magazyn kluczy i wypełnij go nowym certyfikatem prywatnym.
    1. Utwórz żądanie podpisywania certyfikatów.
    1. SCP żądanie podpisywania certyfikatów do urzędu certyfikacji (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na komputerze urzędu certyfikacji uruchom następujące polecenie, aby utworzyć pliki ca-cert i ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Zmień na maszynę urzędu certyfikacji i podpisz wszystkie odebrane żądania podpisywania certyfikatów:

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

1. Wyślij publiczny certyfikat urzędu certyfikacji do każdego węzła procesu roboczego.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. W każdym węźle procesu roboczego dodaj certyfikat publiczny urzędów certyfikacji do magazynu zaufania i magazynu kluczy. Następnie dodaj własny podpisany certyfikat węzła procesu roboczego do magazynu kluczy

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aktualizowanie konfiguracji platformy Kafka w celu używania usług SSL i ponowne uruchamianie brokerów

Teraz skonfigurowano każdego brokera platformy Kafka z magazynem kluczy i magazynem zaufania i zaimportowano poprawne certyfikaty. Następnie zmodyfikuj powiązane właściwości konfiguracji platformy Kafka przy użyciu programu Ambari, a następnie uruchom ponownie brokerów platformy Kafka.

Aby ukończyć modyfikację konfiguracji, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal i wybierz klaster platformy Azure HDInsight Apache Kafka.
1. Przejdź do interfejsu użytkownika Ambari, klikając pozycję **Ambari home** w obszarze **Pulpity nawigacyjne klastra**.
1. W obszarze **Kafka Broker** ustaw właściwość **dla słuchaczy** na`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. W obszarze **Zaawansowane kafka-broker** ustaw właściwość **security.inter.broker.protocol** na`SSL`

    ![Edytowanie właściwości konfiguracji ssl platformy Kafka w ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. W obszarze **Niestandardowy kafka-broker** ustaw właściwość **ssl.client.auth** na `required`. Ten krok jest wymagany tylko w przypadku konfigurowania uwierzytelniania i szyfrowania.

    ![Edytowanie właściwości konfiguracji kafka ssl w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Dodaj nowe właściwości konfiguracji do pliku server.properties.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
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

1. Przejdź do interfejsu użytkownika konfiguracji Ambari i sprawdź, czy nowe właściwości są wyświetlane w obszarze **Zaawansowane kafka-env** i właściwości **szablonu kafka-env.**

    Dla HDI w wersji 3.6:

    ![Edytowanie właściwości szablonu kafka-env w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Dla HDI w wersji 4.0:

     ![Edytowanie właściwości szablonu kafka-env w Ambari cztery](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. Uruchom ponownie wszystkich brokerów platformy Kafka.
1. Uruchom klienta administracyjnego z opcji producenta i konsumenta, aby sprawdzić, czy zarówno producenci, jak i konsumenci pracują nad portem 9093.

## <a name="client-setup-without-authentication"></a>Konfiguracja klienta (bez uwierzytelniania)

Jeśli nie potrzebujesz uwierzytelniania, podsumowanie czynności konfigurowania tylko szyfrowania SSL to:

1. Zaloguj się do urzędu certyfikacji (aktywnego węzła głównego).
1. Skopiuj certyfikat urzędu certyfikacji do komputera klienckiego z komputera urzędu certyfikacji (wn0).
1. Zaloguj się do komputera klienckiego (hn1) i przejdź do folderu. `~/ssl`
1. Zaimportuj certyfikat urzędu certyfikacji do magazynu zaufania.
1. Zaimportuj certyfikat urzędu certyfikacji do magazynu kluczy.

Te kroki są szczegółowo opisane w poniższych fragmentach kodu.

1. Zaloguj się do węzła urzędu certyfikacji.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Kopiowanie ca-cert do komputera klienckiego

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Zaloguj się do komputera klienckiego (węzeł główny rezerwy).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Zaimportuj certyfikat urzędu certyfikacji do magazynu zaufania.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Zaimportuj certyfikat urzędu certyfikacji do magazynu kluczy.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Utwórz `client-ssl-auth.properties`plik . Powinien mieć następujące wiersze:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Konfiguracja klienta (z uwierzytelnianiem)

> [!Note]
> Poniższe kroki są wymagane tylko wtedy, gdy konfigurujesz szyfrowanie SSL **i** uwierzytelnianie. Jeśli konfigurujesz tylko szyfrowanie, zobacz [Konfiguracja klienta bez uwierzytelniania](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Następujące cztery kroki podsumowują zadania potrzebne do ukończenia konfiguracji klienta:

1. Zaloguj się do komputera klienckiego (węzeł główny rezerwy).
1. Utwórz magazyn kluczy java i uzyskaj podpisany certyfikat dla brokera. Następnie skopiuj certyfikat do maszyny Wirtualnej, na której jest uruchomiony urząd certyfikacji.
1. Przełącz się na komputer urzędu certyfikacji (aktywny węzeł główny), aby podpisać certyfikat klienta.
1. Przejdź do komputera klienckiego (węzeł główny `~/ssl` rezerwy) i przejdź do folderu. Skopiuj podpisany certyfikat na komputerze klienckim.

Szczegóły każdego kroku podano poniżej.

1. Zaloguj się do komputera klienckiego (węzeł główny rezerwy).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Usuń istniejący katalog Ssl.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Utwórz magazyn kluczy java i utwórz żądanie podpisywania certyfikatów. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Kopiowanie żądania podpisywania certyfikatu do urzędu certyfikacji

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Przełącz się na komputer urzędu certyfikacji (aktywny węzeł główny) i podpisz certyfikat klienta.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Kopiowanie podpisanego certyfikatu klienta z urzędu certyfikacji (aktywnego węzła głównego) do komputera klienckiego.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Kopiowanie ca-cert do komputera klienckiego

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Utwórz magazyn klienta z podpisanym certyfikatem i zaimportuj ca cert do magazynu kluczy i magazynu zaufania:

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. Tworzenie pliku `client-ssl-auth.properties`. Powinien mieć następujące wiersze:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Weryfikacja

> [!Note]
> Jeśli są zainstalowane standardy HDInsight 4.0 i Kafka 2.1, można użyć producenta/konsumentów konsoli, aby zweryfikować konfigurację. Jeśli nie, uruchom producenta platformy Kafka na porcie 9092 i wysyłaj wiadomości do tematu, a następnie użyj konsumenta platformy Kafka w porcie 9093, który używa SSL.

### <a name="kafka-21-or-above"></a>Kafka 2.1 lub wyższa

1. Utwórz temat, jeśli jeszcze nie istnieje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Uruchom producenta konsoli i `client-ssl-auth.properties` podaj ścieżkę jako plik konfiguracyjny dla producenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  Otwórz inne połączenie ssh do komputera klienckiego `client-ssl-auth.properties` i uruchom konsumenta konsoli i podaj ścieżkę jako plik konfiguracyjny dla konsumenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Utwórz temat, jeśli jeszcze nie istnieje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Uruchom producenta konsoli i podaj ścieżkę do client-ssl-auth.properties jako plik konfiguracyjny dla producenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  Otwórz inne połączenie ssh do komputera klienckiego `client-ssl-auth.properties` i uruchom konsumenta konsoli i podaj ścieżkę jako plik konfiguracyjny dla konsumenta.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Następne kroki

* [Co to jest platforma Apache Kafka w usłudze HDInsight?](apache-kafka-introduction.md)
