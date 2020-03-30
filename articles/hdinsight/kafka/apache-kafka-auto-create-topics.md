---
title: Włączanie automatycznego tworzenia tematów w apache kafka — Usługa Azure HDInsight
description: Dowiedz się, jak skonfigurować apache kafka w programie HDInsight do automatycznego tworzenia tematów. Program Kafka można skonfigurować, ustawiając funkcję auto.create.topics.enable do wartości true za pośrednictwem programu Ambari lub podczas tworzenia klastra za pomocą szablonów programu PowerShell lub Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242365"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Jak skonfigurować Apache Kafka w programie HDInsight do automatycznego tworzenia tematów

Domyślnie [apache Kafka](https://kafka.apache.org/) w programie HDInsight nie umożliwia automatycznego tworzenia tematów. Automatyczne tworzenie tematów dla istniejących klastrów można włączyć za pomocą [programu Apache Ambari](https://ambari.apache.org/). Można również włączyć automatyczne tworzenie tematów podczas tworzenia nowego klastra platformy Kafka przy użyciu szablonu usługi Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interfejs użytkownika sieci Apache Ambari

Aby włączyć automatyczne tworzenie tematów w istniejącym klastrze za pośrednictwem interfejsu użytkownika sieci Web Ambari, należy wykonać następujące kroki:

1. Z [witryny Azure portal](https://portal.azure.com)wybierz klaster platformy Kafka.

1. Z **pulpitów nawigacyjnych klastra**wybierz **ambari home**.

    ![Obraz portalu z zaznaczonym pulpitem nawigacyjnym klastra](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Po wyświetleniu monitu uwierzytelnij się przy użyciu poświadczeń logowania (administratora) dla klastra. Alternatywnie można połączyć się z Amabri bezpośrednio z `https://CLUSTERNAME.azurehdinsight.net/` miejsca, gdzie `CLUSTERNAME` jest nazwa klastra platformy Kafka.

1. Wybierz usługę Kafka z listy po lewej stronie.

    ![Karta listy usług Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Wybierz configs w środku strony.

    ![Karta konfiguracji usługi Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. W polu Filtr wprowadź wartość `auto.create`.

    ![Apache Ambari szukaj pola filtru](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Spowoduje to filtrowanie listy właściwości `auto.create.topics.enable` i wyświetlenie tego ustawienia.

1. Zmień wartość `auto.create.topics.enable` na `true`, a następnie wybierz pozycję **Zapisz**. Dodaj notatkę, a następnie wybierz pozycję **Zapisz** ponownie.

    ![Obraz wpisu auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Wybierz usługę Kafka, wybierz pozycję __Uruchom ponownie__, a następnie wybierz pozycję Uruchom __ponownie wszystkie, których dotyczy problem.__ Po wyświetleniu monitu wybierz pozycję __Potwierdź ponownie wszystkie__.

    ![Apache Ambari ponownie uruchomić wszystkie dotknięte](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Wartości Ambari można również ustawić za pomocą interfejsu API AMbari REST. Jest to na ogół trudniejsze, ponieważ trzeba wykonać wiele wywołań REST, aby pobrać bieżącą konfigurację, zmodyfikować ją itp. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami HDInsight przy użyciu dokumentu interfejsu API Apache Ambari REST.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Podczas tworzenia klastra platformy Kafka przy użyciu szablonu `auto.create.topics.enable` usługi Azure Resource `kafka-broker`Manager można ustawić bezpośrednio, dodając go w pliku . Poniższy fragment kodu JSON pokazuje, jak ustawić `true`tę wartość na:

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

W tym dokumencie dowiesz się, jak włączyć automatyczne tworzenie tematów dla apache kafka w programie HDInsight. Aby dowiedzieć się więcej o pracy z platformą Kafka, zobacz następujące łącza:

* [Analyze Apache Kafka logs](apache-kafka-log-analytics-operations-management.md) (Analizowanie dzienników platformy Apache Kafka)
* [Replikowanie danych między klastrami Apache Kafka](apache-kafka-mirroring.md)
