---
title: Konfigurowanie zasad platformy Apache Kafka w usłudze HDInsight przy użyciu pakietu Enterprise Security — Azure
description: Dowiedz się, jak skonfigurować zasady platformy Apache Ranger dla platformy Kafka w usłudze Azure HDInsight przy użyciu pakietu Enterprise Security.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: aa6702ccf00faa3d63d5458cfbd77ac15fbfbeaa
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633052"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Samouczek: konfigurowanie zasad platformy Apache Kafka w usłudze HDInsight przy użyciu pakietu Enterprise Security (wersja zapoznawcza)

Dowiedz się, jak skonfigurować zasady platformy Apache Ranger na potrzeby klastrów Apache Kafka pakietów Enterprise Security Package (ESP). Klastry ESP są łączone z domeną, co pozwala użytkownikom na uwierzytelnianie przy użyciu poświadczeń domeny. Korzystając z tego artykułu, utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do tematów `sales*` i `marketingspend`.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie użytkowników domeny
> * Tworzenie zasad platformy Ranger
> * Tworzenie tematów w klastrze platformy Kafka
> * Testowanie zasad platformy Ranger

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Utwórz [klaster platformy Kafka usługi HDInsight, używając pakietu Enterprise Security](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger

1. W przeglądarce połącz się z interfejsem użytkownika administratora platformy Ranger przy użyciu adresu URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Pamiętaj, aby zmienić wartość `<ClusterName>` na nazwę klastra platformy Kafka.

    > [!NOTE] 
    > Poświadczenia platformy Ranger są inne niż poświadczenia klastra Hadoop. Aby zapobiec używaniu w przeglądarce buforowanych poświadczeń usługi Hadoop, należy połączyć się z interfejsem użytkownika administratora platformy Ranger w nowym oknie przeglądarki InPrivate.

2. Zaloguj się przy użyciu poświadczeń administratora usługi Azure Active Directory (AD). Poświadczenia administratora usługi Azure AD są inne niż poświadczenia klastra HDInsight i poświadczenia protokołu SSH węzła usługi HDInsight w systemie Linux.

   ![Interfejs użytkownika administratora platformy Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny

Zapoznaj się z sekcją dotyczącą [tworzenia klastra usługi HDInsight przy użyciu pakietu Enterprise Security](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-domain-joined-hdinsight-cluster), aby dowiedzieć się, jak utworzyć użytkowników domeny **sales_user** i **marketing_user**. W scenariuszu produkcyjnym użytkownicy domeny pochodzą z dzierżawy usługi Active Directory.

## <a name="create-ranger-policy"></a>Tworzenie zasad platformy Ranger 

Utwórz zasady platformy Ranger dla użytkowników **sales_user** i **marketing_user**.

1. Otwórz **interfejs użytkownika administratora platformy Ranger**.

2. Kliknij pozycję **\<Nazwa_klastra>_kafka** w obszarze **Kafka**. Lista może zawierać tylko jedne wstępnie skonfigurowane zasady.

3. Kliknij pozycję **Add New Policy** (Dodaj nowe zasady) i wprowadź następujące wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Policy Name (Nazwa zasad)  |  hdi sales* policy   |
   |Temat   |  sales* |
   |Select User (Wybierz użytkownika)  |  sales_user1 |
   |Uprawnienia  | publish, consume, create |

   W nazwie tematu można uwzględnić następujące symbole wieloznaczne:

   * „*” oznacza zero lub więcej wystąpień znaków.
   * „?” oznacza pojedynczy znak.

   ![Interfejs użytkownika administratora platformy Apache Ranger — tworzenie zasad](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE] 
   >Zaczekaj kilka minut na zsynchronizowanie platformy Ranger z usługą Azure AD, jeśli użytkownik domeny nie zostanie automatycznie wypełniony dla ustawienia **Select User** (Wybierz użytkownika).

4. Kliknij pozycję **Add** (Dodaj), aby zapisać zasady.

5. Kliknij pozycję **Add New Policy** (Dodaj nowe zasady), a następnie wprowadź następujące wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Policy Name (Nazwa zasad)  |  hdi marketing policy   |
   |Temat   |  marketingspend |
   |Select User (Wybierz użytkownika)  |  marketing_user1 |
   |Uprawnienia  | publish, consume, create |

   ![Interfejs użytkownika administratora platformy Apache Ranger — tworzenie zasad](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Kliknij pozycję **Add** (Dodaj), aby zapisać zasady.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Tworzenie tematów w klastrze platformy Kafka przy użyciu pakietu ESP

Aby utworzyć dwa tematy, **salesevents** i **marketingspend**:

1. Użyj następującego polecenia w celu otwarcia połączenia protokołu SSH z klastrem:

   ```bash
   ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Zamień ciąg `SSHUSER` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH. Aby uzyskać więcej informacji na temat używania polecenia `scp` w usłudze HDInsight, zobacz [Korzystanie z protokołu SSH w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

   W scenariuszu produkcyjnym użytkownicy domeny skonfigurowani podczas tworzenia klastra mogą połączyć się z klastrem za pośrednictwem protokołu SSH.

2. Użyj następujących poleceń, aby zapisać nazwę klastra w zmiennej i zainstalować narzędzie do analizy JSON, `jq`. Po wyświetleniu monitu wprowadź nazwę klastra platformy Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Użyj następujących poleceń, aby uzyskać hosty brokera platformy Kafka i hosty usługi ZooKeeper. Po wyświetleniu monitu wprowadź hasło konta administratora klastra.

   ```bash
   export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```
> [!Note]
> Zanim przejdziesz dalej, może być konieczne skonfigurowanie środowiska projektowego, jeśli nie wykonano tego wcześniej. Potrzebne będą Ci następujące składniki: zestaw Java JDK, narzędzie Apache Maven i klient SSH z punktem połączenia z usługą. Aby uzyskać więcej informacji, zobacz te [instrukcje instalacji](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
1. Pobierz [przykłady odbiorców i producentów przyłączonych do domeny na platformie Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Wykonaj kroki 2 i 3 w sekcji **Kompilowanie i wdrażanie przykładu** samouczka [Korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Uruchom następujące polecenia:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

   >[!NOTE] 
   >Tylko właściciel procesu usługi Kafka, taki jak katalog główny, może zapisywać w węzłach usługi Zookeeper `/config/topics`. Zasady platformy Ranger nie są wymuszane, gdy użytkownik bez uprawnień tworzy temat. Dzieje się tak, ponieważ skrypt `kafka-topics.sh` komunikuje się bezpośrednio z usługą Zookeeper w celu utworzenia tematu. Wpisy są dodawane do węzłów usługi Zookeeper, a obserwatorzy po stronie brokera odpowiednio monitorują i tworzą tematy. Nie można przeprowadzić autoryzacji za pomocą wtyczki platformy Ranger. Powyższe polecenie jest wykonywane przy użyciu elementu `sudo` za pośrednictwem brokera platformy Kafka.


## <a name="test-the-ranger-policies"></a>Testowanie zasad platformy Ranger

W oparciu o skonfigurowane zasady platformy Ranger użytkownik **sales_user** może tworzyć/wykorzystywać temat **salesevents**, ale nie temat **marketingspend**. Z drugiej strony użytkownik **marketing_user** może tworzyć/wykorzystywać temat **marketingspend**, ale nie temat **salesevents**.

1. Otwórz nowe połączenie SSH z klastrem. Użyj następującego polecenia, aby zalogować się jako użytkownik **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Wykonaj następujące polecenie:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Użyj nazw brokera i usługi z poprzedniej sekcji, aby ustawić następujące zmienne środowiskowe:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092 
   ```

   Przykład: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

   ```bash
   export KAFKAZKHOSTS=<zklist>:2181
   ```

   Przykład: `export KAFKAZKHOSTS=zk1-khdicl.contoso.com:2181,zk2-khdicl.contoso.com:2181`

4. Upewnij się, że użytkownik **sales_user1** może tworzyć elementy tematu **salesevents**.
   
   Wykonaj następujące polecenie, aby uruchomić producenta konsoli dla tematu **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic salesevents --security-protocol SASL_PLAINTEXT
   ```

   Następnie wprowadź kilka komunikatów w obrębie konsoli. Naciśnij klawisze **Ctrl + C**, aby zamknąć producenta konsoli.

5. Wykonaj następujące polecenie, aby korzystać z elementów tematu **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic salesevents --security-protocol PLAINTEXTSASL --from-beginning
   ```
 
6. Sprawdź, czy pojawią się komunikaty wprowadzone w poprzednim kroku oraz czy użytkownik **sales_user1** nie może tworzyć elementów tematu **marketingspend**.

   Z poziomu tego samego okna protokołu SSH, co powyżej, wykonaj następujące polecenie, aby tworzyć elementy tematu **marketingspend**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic marketingspend --security-protocol SASL_PLAINTEXT
   ```

   Wystąpi błąd autoryzacji, który można zignorować. 

7. Zauważ, że użytkownik **marketing_user1** nie może korzystać z elementów tematu **salesevents**.

   Powtórz kroki 1–3 powyżej, ale tym razem jako użytkownik **marketing_user1**.

   Wykonaj następujące polecenie, aby korzystać z elementów tematu **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic marketingspend --security-protocol PLAINTEXTSASL --from-beginning
   ```

   Poprzednie komunikaty nie będą widoczne.

8. Wyświetl zdarzenia dostępu inspekcji z poziomu interfejsu użytkownika platformy Ranger.

   ![Inspekcja zasad interfejsu użytkownika platformy Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Następne kroki

* [Bring your own key to Kafka (Używanie własnego klucza na platformie Kafka)](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [An introduction to Hadoop security with Enterprise Security Package (Wprowadzenie do zabezpieczeń usługi Hadoop z pakietem Enterprise Security)](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
