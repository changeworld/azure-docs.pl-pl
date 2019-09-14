---
title: Włącz automatyczne tworzenie tematów w Apache Kafka — Azure HDInsight
description: Dowiedz się, jak skonfigurować Apache Kafka w usłudze HDInsight, aby automatycznie tworzyć tematy. Kafka można skonfigurować przez ustawienie opcji Auto. Create. temats. Enable to true przez Ambari lub podczas tworzenia klastra za pomocą programu PowerShell lub szablonów Menedżer zasobów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 5d990a1c39495090fed1c78f1ddf5e879490e6c4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960652"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Jak skonfigurować Apache Kafka w usłudze HDInsight w celu automatycznego tworzenia tematów

Domyślnie usługa [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight nie umożliwia automatycznego tworzenia tematu. Możesz włączyć tworzenie opcji autotemat dla istniejących klastrów przy użyciu platformy [Apache Ambari](https://ambari.apache.org/). Podczas tworzenia nowego klastra Kafka przy użyciu szablonu Azure Resource Manager można również włączyć funkcję tworzenia autotematu.

## <a name="apache-ambari-web-ui"></a>Interfejs użytkownika sieci Web Apache Ambari

Aby włączyć automatyczne tworzenie tematów w istniejącym klastrze za pomocą interfejsu użytkownika sieci Web Ambari, wykonaj następujące czynności:

1. Na [Azure Portal](https://portal.azure.com)wybierz klaster Kafka.

2. W obszarze __Przegląd klastra__wybierz pozycję __pulpit nawigacyjny klastra__. 

    ![Obraz portalu z wybranym pulpitem nawigacyjnym klastra](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Następnie wybierz pozycję __pulpit nawigacyjny klastra usługi HDInsight__. Po wyświetleniu monitu Uwierzytelnij się przy użyciu poświadczeń logowania (administratora) dla klastra.

    ![Obraz wpisu pulpitu nawigacyjnego klastra usługi HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Wybierz usługę Kafka z listy znajdującej się po lewej stronie.

    ![Lista usług](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. Wybierz pozycję konfiguracje w środku strony.

    ![Karta Konfiguracja usługi](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. W polu Filtr wprowadź wartość `auto.create`. 

    ![Obraz pola filtru](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Spowoduje to przefiltrowanie listy właściwości i wyświetlenie `auto.create.topics.enable` ustawienia.

6. Zmień wartość `auto.create.topics.enable` na `true`, a następnie wybierz pozycję Zapisz. Dodaj notatkę, a następnie wybierz pozycję Zapisz ponownie.

    ![Obraz przedstawiający wpis Auto. Create. temats. Enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Wybierz usługę Kafka, wybierz pozycję __Uruchom ponownie__, a następnie wybierz pozycję __Uruchom ponownie wszystkie uwzględnione__. Po wyświetleniu monitu wybierz pozycję __Potwierdź ponowne uruchomienie wszystkich__.

    ![Obraz zaznaczenia ponownego uruchomienia](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Możesz również ustawić Ambari wartości za pomocą interfejsu API REST Ambari. Jest to zazwyczaj trudniejsze, ponieważ trzeba wykonać wiele wywołań REST, aby pobrać bieżącą konfigurację, zmodyfikować ją itp. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu dokumentu interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Podczas tworzenia klastra Kafka przy użyciu szablonu Azure Resource Manager można bezpośrednio ustawić `auto.create.topics.enable` przez dodanie go `kafka-broker`w. Poniższy fragment kodu JSON pokazuje, jak ustawić tę wartość na `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób włączania automatycznego tworzenia tematu dla Apache Kafka w usłudze HDInsight. Aby dowiedzieć się więcej na temat pracy z usługą Kafka, zobacz następujące linki:

* [Analyze Apache Kafka logs](apache-kafka-log-analytics-operations-management.md) (Analizowanie dzienników platformy Apache Kafka)
* [Replikowanie danych między klastrami Apache Kafka](apache-kafka-mirroring.md)
