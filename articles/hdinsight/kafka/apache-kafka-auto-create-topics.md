---
title: Włącz tworzenie automatycznego tematu w Apache Kafka - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować Apache Kafka na HDInsight, aby automatycznie utworzyć tematy. Możesz skonfigurować Kafka przez ustawienie auto.create.topics.enable na wartość true, za pomocą narzędzia Ambari lub podczas tworzenia klastra za pomocą szablonów programu PowerShell lub Menedżera zasobów.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: larryfr
ms.openlocfilehash: fa5dd7533259c794671cd16231fd3f530173bfa3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Jak skonfigurować Apache Kafka na HDInsight, aby automatycznie utworzyć tematy

Domyślnie Kafka w usłudze HDInsight nie obsługuje tworzenia automatyczne tematu. Można włączyć automatyczne tworzenie tematu dla istniejących klastrów za pomocą narzędzia Ambari. Można również włączyć automatyczne tworzenie tematu, podczas tworzenia nowego klastra Kafka przy użyciu szablonu usługi Azure Resource Manager.

## <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web Ambari

Aby włączyć tworzenie tematu automatyczne istniejącego klastra za pomocą interfejsu użytkownika sieci Web Ambari, użyj następujące czynności:

1. Z [portalu Azure](https://portal.azure.com), wybierz klaster Kafka.

2. Z __Omówienie klastra__, wybierz pozycję __pulpit nawigacyjny klastra__. 

    ![Obraz portalu z pulpitu nawigacyjnego klastra wybrane](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Następnie wybierz __pulpit nawigacyjny klastra usługi HDInsight__. Po wyświetleniu monitu uwierzytelniania przy użyciu poświadczeń logowania (Administrator) dla klastra.

    ![Obraz wpis pulpit nawigacyjny klastrów usługi HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Wybierz usługę Kafka z listy po lewej stronie.

    ![Lista usług](./media/apache-kafka-auto-create-topics/service-list.png)

4. Wybierz Configs środku strony.

    ![Karta konfiguracji usługi](./media/apache-kafka-auto-create-topics/service-config.png)

5. W polu filtru wpisz wartość `auto.create`. 

    ![Obraz w polu filtru](./media/apache-kafka-auto-create-topics/filter.png)

    To filtruje listę właściwości i wyświetla `auto.create.topics.enable` ustawienie.

6. Zmień wartość `auto.create.topics.enable` do `true`, a następnie wybierz opcję Zapisz. Dodanie uwagi, a następnie wybierz Zapisz ponownie.

    ![Obraz wprowadzania auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Wybierz usługę Kafka, wybierz pozycję __Uruchom ponownie__, a następnie wybierz __ponowne uruchomienie wszystkich odpowiednich__. Po wyświetleniu monitu wybierz __Potwierdź Uruchom ponownie wszystkie__.

    ![Obraz zaznaczenia ponownego uruchomienia](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> Można również ustawić wartości Ambari za pośrednictwem interfejsu API REST Ambari. Jest to zazwyczaj bardziej trudne, konieczne będzie wprowadzenie wielu wywołań REST, aby pobrać bieżącą konfigurację, zmodyfikuj go, np. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentu.

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Podczas tworzenia klastra Kafka przy użyciu szablonu usługi Azure Resource Manager, można bezpośrednio ustawić `auto.create.topics.enable` przez dodanie go w `kafka-broker`. Poniższy fragment kodu JSON pokazano, jak ustawić wartość `true`:

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

W tym dokumencie przedstawiono sposób umożliwiają tworzenie automatycznego tematu dla Kafka w usłudze HDInsight. Aby dowiedzieć się więcej o pracy z Kafka, zobacz następujące linki:

* [Analizowanie dzienników Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicate data between Kafka clusters (Replikowanie danych między klastrami Kafka)](apache-kafka-mirroring.md)