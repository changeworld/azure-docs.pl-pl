---
title: Włącz tworzenie tematu automatyczne na platformie Apache Kafka — Azure HDInsight
description: Dowiedz się, jak skonfigurować platformy Apache Kafka w HDInsight w celu automatycznego tworzenia tematów. Można skonfigurować platformy Kafka, ustawiając auto.create.topics.enable na wartość true, za pomocą systemu Ambari lub podczas tworzenia klastra za pomocą szablonów programu PowerShell lub Menedżera zasobów.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: af26bcee08ded8eb66d640f954113be3e7672e1b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709138"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Jak skonfigurować platformy Apache Kafka w HDInsight w celu automatycznego tworzenia tematów

Domyślnie [platformy Apache Kafka](https://kafka.apache.org/) na HDInsight nie umożliwia tworzenie automatycznych tematu. Można włączyć automatycznego tworzenia tematów dla istniejących klastrów za pomocą [Apache Ambari](https://ambari.apache.org/). Można również włączyć automatycznego tworzenia tematów, podczas tworzenia nowego klastra platformy Kafka, przy użyciu szablonu usługi Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Aby włączyć Tworzenie automatycznego tematu w istniejącym klastrze przy użyciu interfejsu użytkownika sieci Web Ambari, użyj następujące czynności:

1. Z [witryny Azure portal](https://portal.azure.com), wybierz klaster platformy Kafka.

2. Z __Omówienie klastra__, wybierz opcję __pulpit nawigacyjny klastra__. 

    ![Obraz przedstawiający portalu z wybrany pulpit nawigacyjny klastra](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Następnie wybierz pozycję __pulpit nawigacyjny klastra HDInsight__. Po wyświetleniu monitu uwierzytelniania przy użyciu poświadczeń logowania (administratora) dla klastra.

    ![Obraz wpisu pulpit nawigacyjny klastra HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Wybierz usługę platformy Kafka na liście po lewej części strony.

    ![Lista usług](./media/apache-kafka-auto-create-topics/service-list.png)

4. Wybierz konfiguracje pośrodku strony.

    ![Karta Konfiguracja usługi](./media/apache-kafka-auto-create-topics/service-config.png)

5. W polu filtru wprowadź wartość `auto.create`. 

    ![Obraz przedstawiający pole filtru](./media/apache-kafka-auto-create-topics/filter.png)

    Filtruje listę właściwości i wyświetla `auto.create.topics.enable` ustawienie.

6. Zmień wartość właściwości `auto.create.topics.enable` do `true`, a następnie wybierz pozycję Zapisz. Dodanie uwagi, a następnie wybierz pozycję Zapisz ponownie.

    ![Obraz wpisu auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Wybierz usługę platformy Kafka, wybierz pozycję __ponowne uruchomienie__, a następnie wybierz pozycję __ponownego uruchomienia, wszystkie objęte__. Po wyświetleniu monitu wybierz __Potwierdź ponowne uruchomienie wszystkich__.

    ![Obraz przedstawiający wybór ponownego uruchomienia](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Można również ustawić wartości Ambari za pomocą interfejsu API REST Ambari. Jest to zwykle trudniejsze, należy podjąć wiele wywołań REST, aby pobrać bieżącą konfigurację, zmodyfikować go, itp. Aby uzyskać więcej informacji, zobacz [HDInsight Zarządzanie klastrami przy użyciu interfejsu API Apache Ambari REST](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentu.

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Podczas tworzenia klastra platformy Kafka, przy użyciu szablonu usługi Azure Resource Manager, można ustawić bezpośrednio `auto.create.topics.enable` , dodając ją w `kafka-broker`. Poniższy fragment kodu JSON pokazuje, jak ustawić tę wartość na `true`:

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

W tym dokumencie przedstawiono sposób włączyć automatyczne tematu Tworzenie klastra Apache kafka w HDInsight. Aby dowiedzieć się więcej na temat pracy z platformą Kafka, zobacz następujące linki:

* [Analyze Apache Kafka logs](apache-kafka-log-analytics-operations-management.md) (Analizowanie dzienników platformy Apache Kafka)
* [Replikowanie danych między klastrami Apache Kafka](apache-kafka-mirroring.md)
