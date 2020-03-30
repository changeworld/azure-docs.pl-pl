---
title: Niestandardowa baza danych Apache Ambari w usłudze Azure HDInsight
description: Dowiedz się, jak tworzyć klastry HDInsight za pomocą własnej niestandardowej bazy danych Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240159"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Konfigurowanie klastrów USŁUGI HDInsight przy niestandardowych bazach danych Ambari DB

Apache Ambari upraszcza zarządzanie i monitorowanie klastra Apache Hadoop. Ambari zapewnia łatwy w użyciu interfejs użytkownika sieci web i interfejs API REST. Ambari jest dołączony do klastrów HDInsight i służy do monitorowania klastra i wprowadzania zmian konfiguracji.

W normalnym tworzeniu klastra, zgodnie z opisem w innych artykułach, takich jak [Konfigurowanie klastrów w programie HDInsight,](hdinsight-hadoop-provision-linux-clusters.md)Ambari jest wdrażany w [bazie danych SQL platformy Azure S0,](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) która jest zarządzana przez usługę HDInsight i nie jest dostępna dla użytkowników.

Niestandardowa funkcja Ambari DB umożliwia wdrożenie nowego klastra i konfigurację Ambari w zewnętrznej bazie danych, którą zarządzasz. Wdrożenie odbywa się za pomocą szablonu usługi Azure Resource Manager. Ta funkcja ma następujące zalety:

- Dostosowywanie — można wybrać rozmiar i zdolność przetwarzania bazy danych. Jeśli masz duże klastry przetwarzania intensywnych obciążeń, bazy danych Ambari z niższych specyfikacji może stać się wąskim gardłem dla operacji zarządzania.
- Elastyczność — można skalować bazę danych zgodnie z potrzebami, aby spełnić twoje wymagania.
- Kontrola — można zarządzać kopiami zapasowymi i zabezpieczeniami bazy danych w sposób, który odpowiada wymaganiom organizacji.

Poniżej dalsza część artykułu Omówiono następujące kwestie:

- wymagania dotyczące korzystania z niestandardowej funkcji Ambari DB
- kroki niezbędne do aprowizowania klastrów HDInsight przy użyciu własnej zewnętrznej bazy danych dla Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Niestandardowe wymagania ambari DB

Niestandardowe ambari DB można wdrożyć we wszystkich typach i wersjach klastra. Wiele klastrów nie może używać tej samej bazy danych Ambari.

Niestandardowy ambari DB ma następujące inne wymagania:

- Musisz mieć istniejący serwer i bazę danych usługi Azure SQL DB.
- Baza danych, która jest podasz dla instalacji Ambari musi być pusta. Nie powinno być żadnych tabel w domyślnym schemacie dbo.
- Użytkownik używany do łączenia się z bazą danych powinien mieć uprawnienia SELECT, CREATE TABLE i INSERT w bazie danych.
- Włącz opcję [Zezwalaj na dostęp do usług platformy Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) na serwerze SQL platformy Azure, na którym będzie hostować Ambari.
- Zarządzanie adresami IP z usługi HDInsight muszą być dozwolone w programie SQL Server. Zobacz [adresy IP zarządzania hdinsight,](hdinsight-management-ip-addresses.md) aby uzyskać listę adresów IP, które muszą zostać dodane do zapory serwera SQL.

Podczas hostowania bazy danych Apache Ambari w zewnętrznej bazie danych należy pamiętać o następujących kwestiach:

- Ponosisz odpowiedzialność za dodatkowe koszty usługi Azure SQL DB, która przechowuje Ambari.
- Okresowo dzmów swoje niestandardowe Ambari DB. Usługa Azure SQL Database generuje kopie zapasowe automatycznie, ale przedział czasu przechowywania kopii zapasowych jest różny. Aby uzyskać więcej informacji, zobacz [Dowiedz się więcej o automatycznych kopiach zapasowych bazy danych SQL](../sql-database/sql-database-automated-backups.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Wdrażanie klastrów przy niestandardowym ambari DB

Aby utworzyć klaster HDInsight, który używa własnej zewnętrznej bazy danych Ambari, użyj [niestandardowego szablonu Szybkiego startu Ambari DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Edytuj parametry w `azuredeploy.parameters.json` celu określenia informacji o nowym klastrze i bazie danych, która będzie zawierać Ambari.

Wdrożenie można rozpocząć przy użyciu interfejsu wiersza polecenia platformy Azure. Zamień `<RESOURCEGROUPNAME>` ją na grupę zasobów, w której chcesz wdrożyć klaster.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Następne kroki

- [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight](hdinsight-use-external-metadata-stores.md)