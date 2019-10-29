---
title: Włącz automatyczne tworzenie tematów w Apache Kafka — Azure HDInsight
description: Dowiedz się, jak skonfigurować Apache Kafka w usłudze HDInsight, aby automatycznie tworzyć tematy. Kafka można skonfigurować przez ustawienie opcji Auto. Create. temats. Enable to true przez Ambari lub podczas tworzenia klastra za pomocą programu PowerShell lub szablonów Menedżer zasobów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 1bcb433230af856e92c7e418fc81b35bea549ddf
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987979"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Jak skonfigurować Apache Kafka w usłudze HDInsight w celu automatycznego tworzenia tematów

Domyślnie [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight nie włącza automatycznego tworzenia tematu. Możesz włączyć tworzenie opcji autotemat dla istniejących klastrów przy użyciu platformy [Apache Ambari](https://ambari.apache.org/). Podczas tworzenia nowego klastra Kafka przy użyciu szablonu Azure Resource Manager można również włączyć funkcję tworzenia autotematu.

## <a name="apache-ambari-web-ui"></a>Interfejs użytkownika sieci Web Apache Ambari

Aby włączyć automatyczne tworzenie tematów w istniejącym klastrze za pomocą interfejsu użytkownika sieci Web Ambari, wykonaj następujące czynności:

1. Na [Azure Portal](https://portal.azure.com)wybierz swój klaster Kafka.

1. Z **pulpitów nawigacyjnych klastra**wybierz pozycję **Ambari Home**.

    ![Obraz portalu z wybranym pulpitem nawigacyjnym klastra](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Po wyświetleniu monitu Uwierzytelnij się przy użyciu poświadczeń logowania (administratora) dla klastra. Alternatywnie można nawiązać połączenie z usługą Amabri bezpośrednio z poziomu `https://CLUSTERNAME.azurehdinsight.net/`, gdzie `CLUSTERNAME` jest nazwą klastra Kafka.

1. Wybierz usługę Kafka z listy znajdującej się po lewej stronie.

    ![Karta listy usługi Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Wybierz pozycję konfiguracje w środku strony.

    ![Karta konfiguracje usługi Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. W polu Filtr wprowadź wartość `auto.create`.

    ![Pole filtru wyszukiwania Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Filtruje listę właściwości i wyświetla ustawienia `auto.create.topics.enable`.

1. Zmień wartość `auto.create.topics.enable` na `true`, a następnie wybierz pozycję **Zapisz**. Dodaj notatkę, a następnie wybierz pozycję **Zapisz** ponownie.

    ![Obraz przedstawiający wpis Auto. Create. temats. Enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Wybierz usługę Kafka, wybierz pozycję __Uruchom ponownie__, a następnie wybierz pozycję __Uruchom ponownie wszystkie uwzględnione__. Po wyświetleniu monitu wybierz pozycję __Potwierdź ponowne uruchomienie wszystkich__.

    ![System Apache Ambari](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Możesz również ustawić Ambari wartości za pomocą interfejsu API REST Ambari. Jest to zazwyczaj trudniejsze, ponieważ trzeba wykonać wiele wywołań REST, aby pobrać bieżącą konfigurację, zmodyfikować ją itp. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu dokumentu interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Szablony Menedżera zasobów

Podczas tworzenia klastra Kafka przy użyciu szablonu Azure Resource Manager można bezpośrednio ustawić `auto.create.topics.enable` przez dodanie go w `kafka-broker`. Poniższy fragment kodu JSON pokazuje, jak ustawić tę wartość na `true`:

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
