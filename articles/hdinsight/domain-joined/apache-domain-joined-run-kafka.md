---
title: Samouczek — Konfigurowanie zasad usługi Apache Kafka HDInsight z pakietem Enterprise Security - Azure
description: Samouczek — Dowiedz się, jak skonfigurować zasady platformy Apache Ranger dla platformy Kafka w usłudze Azure HDInsight z pakietem Enterprise Security.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: ba16a975aa3b1e60393006ef49a7e422c572931e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441369"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Samouczek: Konfigurowanie zasad platformy Apache Kafka w usłudze HDInsight przy użyciu pakietu Enterprise Security (wersja zapoznawcza)

Dowiedz się, jak skonfigurować zasady platformy Apache Ranger na potrzeby klastrów Apache Kafka pakietów Enterprise Security Package (ESP). Klastry ESP są łączone z domeną, co pozwala użytkownikom na uwierzytelnianie przy użyciu poświadczeń domeny. Korzystając z tego artykułu, utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do tematów `sales` i `marketingspend`.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie użytkowników domeny
> * Tworzenie zasad platformy Ranger
> * Tworzenie tematów w klastrze platformy Kafka
> * Testowanie zasad platformy Ranger

## <a name="prerequisite"></a>Wymagania wstępne

A [HDInsight Kafka klastra z pakietem Enterprise Security](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger

1. W przeglądarce połącz się z interfejsem użytkownika administratora platformy Ranger przy użyciu adresu URL `https://ClusterName.azurehdinsight.net/Ranger/`. Pamiętaj, aby zmienić wartość `ClusterName` na nazwę klastra platformy Kafka. Poświadczenia platformy Ranger są inne niż poświadczenia klastra Hadoop. Aby zapobiec używaniu w przeglądarce buforowanych poświadczeń usługi Hadoop, należy połączyć się z interfejsem użytkownika administratora platformy Ranger w nowym oknie przeglądarki InPrivate.

2. Zaloguj się przy użyciu poświadczeń administratora usługi Azure Active Directory (AD). Poświadczenia administratora usługi Azure AD są inne niż poświadczenia klastra HDInsight i poświadczenia protokołu SSH węzła usługi HDInsight w systemie Linux.

   ![Interfejs użytkownika administratora platformy Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny

Zapoznaj się z sekcją dotyczącą [tworzenia klastra usługi HDInsight przy użyciu pakietu Enterprise Security](./apache-domain-joined-configure-using-azure-adds.md), aby dowiedzieć się, jak utworzyć użytkowników domeny **sales_user** i **marketing_user**. W scenariuszu produkcyjnym użytkownicy domeny pochodzą z dzierżawy usługi Active Directory.

## <a name="create-ranger-policy"></a>Tworzenie zasad platformy Ranger

Utwórz zasady platformy Ranger dla użytkowników **sales_user** i **marketing_user**.

1. Otwórz **interfejs użytkownika administratora platformy Ranger**.

2. Wybierz  **\<Nazwa_klastra > _kafka** w obszarze **Kafka**. Lista może zawierać tylko jedne wstępnie skonfigurowane zasady.

3. Wybierz **— Dodawanie nowych zasad** i wprowadź następujące wartości:

   |Ustawienie  |Sugerowana wartość  |
   |---------|---------|
   |Nazwa zasad  |  hdi sales* policy   |
   |Temat   |  sales* |
   |Select User (Wybierz użytkownika)  |  sales_user1 |
   |Uprawnienia  | publish, consume, create |

   W nazwie tematu można uwzględnić następujące symbole wieloznaczne:

   * „*” oznacza zero lub więcej wystąpień znaków.
   * „?” oznacza pojedynczy znak.

   ![Interfejs użytkownika administratora platformy Apache Ranger — tworzenie zasad](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Zaczekaj kilka minut na zsynchronizowanie platformy Ranger z usługą Azure AD, jeśli użytkownik domeny nie zostanie automatycznie wypełniony dla ustawienia **Select User** (Wybierz użytkownika).

4. Wybierz **Dodaj** zapisać zasady.

5. Wybierz **— Dodawanie nowych zasad** , a następnie wprowadź następujące wartości:

   |Ustawienie  |Sugerowana wartość  |
   |---------|---------|
   |Nazwa zasad  |  hdi marketing policy   |
   |Temat   |  marketingspend |
   |Select User (Wybierz użytkownika)  |  marketing_user1 |
   |Uprawnienia  | publish, consume, create |

   ![Interfejs użytkownika administratora platformy Apache Ranger — tworzenie zasad](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Wybierz **Dodaj** zapisać zasady.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Tworzenie tematów w klastrze platformy Kafka przy użyciu pakietu ESP

Aby utworzyć dwa tematy — `salesevents` i `marketingspend`:

1. Użyj następującego polecenia w celu otwarcia połączenia protokołu SSH z klastrem:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Zastąp ciąg `DOMAINADMIN` za pomocą użytkownika będącego administratorem dla klastra skonfigurowanego podczas [tworzenia klastra](./apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp), a ciąg `CLUSTERNAME` zastąp nazwą klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika będącego administratorem. Aby uzyskać więcej informacji na temat używania polecenia `SSH` w usłudze HDInsight, zobacz [Korzystanie z protokołu SSH w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następujących poleceń, aby zapisać nazwę klastra w zmiennej i zainstalować narzędzie do analizy JSON, `jq`. Po wyświetleniu monitu wprowadź nazwę klastra platformy Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Użyj następujących poleceń, aby uzyskać hosty brokera platformy Kafka. Po wyświetleniu monitu wprowadź hasło konta administratora klastra.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Zanim przejdziesz dalej, może być konieczne konfigurowanie środowiska projektowego, jeśli nie zostały już wykonane. Konieczne będzie składników, takich jak Java JDK, Apache Maven i klienta SSH za pomocą punktu połączenia usługi. Aby uzyskać więcej informacji, zobacz [instrukcje instalacji](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Pobierz [przykłady odbiorców i producentów przyłączonych do domeny na platformie Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Wykonaj kroki 2 i 3 w sekcji **Kompilowanie i wdrażanie przykładu** w artykule [Samouczek: Korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Uruchom następujące polecenia:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testowanie zasad platformy Ranger

W oparciu o skonfigurowane zasady platformy Ranger użytkownik **sales_user** może tworzyć/wykorzystywać temat `salesevents`, ale nie temat `marketingspend`. Z drugiej strony użytkownik **marketing_user** może tworzyć/wykorzystywać temat `marketingspend`, ale nie temat `salesevents`.

1. Otwórz nowe połączenie SSH z klastrem. Użyj następującego polecenia, aby zalogować się jako użytkownik **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Wykonaj następujące polecenie:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Użyj nazw brokera z poprzedniej sekcji, aby ustawić następujące zmienne środowiskowe:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Przykład: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Wykonaj krok 3 w sekcji **Kompilowanie i wdrażanie przykładu** w artykule [Samouczek: korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example), aby zapewnić dostępność pliku `kafka-producer-consumer.jar` także dla użytkownika **sales_user**.

5. Upewnij się, że użytkownik **sales_user1** może tworzyć do tematu `salesevents`, wykonując następujące polecenie:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Wykonaj następujące polecenie, aby korzystać z elementów tematu `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Sprawdź, czy możesz odczytywać komunikaty.

7. Upewnij się, że użytkownik **sales_user1** nie może tworzyć do tematu `marketingspend`, wykonując następujące polecenie w tym samym oknie protokołu SSH:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Wystąpi błąd autoryzacji, który można zignorować.

8. Zauważ, że użytkownik **marketing_user1** nie może korzystać z elementów tematu `salesevents`.

   Powtórz kroki 1–4 powyżej, ale tym razem jako użytkownik **marketing_user1**.

   Wykonaj następujące polecenie, aby korzystać z elementów tematu `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Poprzednie komunikaty nie będą widoczne.

9. Wyświetl zdarzenia dostępu inspekcji z poziomu interfejsu użytkownika platformy Ranger.

   ![Inspekcja zasad interfejsu użytkownika platformy Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, aby usunąć klastra platformy Kafka, który został utworzony wykonując następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.
1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.
1. Na wyświetlonej liście klastrów usługi HDInsight kliknij symbol **...** obok klastra utworzonego na potrzeby tego samouczka. 
1. Kliknij przycisk **Usuń**. Kliknij przycisk **Yes** (Tak).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Bring your own key to Apache Kafka (Używanie własnego klucza na platformie Apache Kafka)](../kafka/apache-kafka-byok.md)