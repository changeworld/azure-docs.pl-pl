---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — motywacja i korzyści
description: Dowiedz się motywacją i korzyści w przypadku migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: efaedccde854d8cd7fa777aa9457db7203ce833a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221945"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — motywacja i korzyści

Ten artykuł jest pierwszy w serii na najlepsze rozwiązania dotyczące migracji w środowisku lokalnym wdrożeń ekosystemu Apache Hadoop do usługi Azure HDInsight. Ta seria artykułów jest dla osób, które odpowiadają za projektowanie, wdrażanie i migracja rozwiązania Apache Hadoop w usłudze Azure HDInsight. Role, które mogą korzystać z tych artykułów obejmują architektów chmury, Administratorzy usługi Hadoop i inżynierom DevOps. Deweloperzy oprogramowania, inżynierami danych i analitykom danych powinien również korzystać z wyjaśnienie sposobu różnych typów klastrów pracy w chmurze.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Dlaczego do migracji do usługi Azure HDInsight

Usługa Azure HDInsight jest dystrybucją w chmurze składniki platformy Hadoop z [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). Usługa Azure HDInsight ułatwia i przyspiesza przetwarzanie ogromnych ilości danych przy jednoczesnej minimalizacji kosztów. HDInsight obejmuje najbardziej popularnych struktur open source, takie jak:

- Apache Hadoop
- Apache Spark
- Apache Hive z opcją LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="advantages-that-azure-hdinsight-offers-over-on-premises-hadoop"></a>Korzyści oferowanych przez usługi Azure HDInsight za pośrednictwem lokalnej usługi Hadoop

- **Niski koszt** — można zmniejszyć koszty przez [tworząc klastry na żądanie](../hdinsight-hadoop-create-linux-clusters-adf.md) i płacąc wyłącznie za rzeczywiste użycie. Oddzielenie obliczeń i magazynu zapewnia elastyczność, przechowując ilość danych niezależnie od rozmiaru klastra.
- **Zautomatyzowane tworzenie klastra** — zautomatyzowane tworzenie klastra wymaga minimalnej instalacji i konfiguracji. Automatyzacja może służyć do klastrów na żądanie.
- **Zarządzane sprzętu i konfiguracji** — nie trzeba już martwić się o sprzętu fizycznego lub infrastruktury z klastrem usługi HDInsight. Wystarczy określić konfigurację klastra, a Azure ustawia go.
- **Łatwe skalowanie** — HDInsight umożliwia [skalowania](../hdinsight-administer-use-portal-linux.md) obciążeń w górę lub w dół. Platforma Azure zajmie się rozpowszechnianie danych i obciążenia ponowne równoważenie bez przerywania zadań przetwarzania danych.
- **Globalna dostępność** — HDInsight jest dostępna w większej liczbie [regionów](https://azure.microsoft.com/regions/services/) niż inne oferty do analizy danych big data. Usługa Azure HDInsight jest również dostępna w ramach chmur Azure Government, Azure (Chiny) i Azure (Niemcy), dzięki czemu odpowiada na potrzeby Twojego przedsiębiorstwa w najważniejszych obszarach suwerenności.
- **Bezpieczeństwo i zgodność** — HDInsight umożliwia ochronę zasobów danych przedsiębiorstwa przy użyciu [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [szyfrowania](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)oraz integracji z usługą [usługi Azure Active Katalog](../domain-joined/apache-domain-joined-introduction.md). HDInsight spełnia również najpopularniejsze branżowe i rządowe [standardami](https://azure.microsoft.com/overview/trusted-cloud).
- **Uproszczone zarządzanie wersjami** — Azure HDInsight zarządza wersja składników ekosystemu Hadoop, a jednocześnie dbając o ich aktualności. Aktualizacje oprogramowania są zwykle złożony proces w przypadku wdrożeń lokalnych.
- **Mniejszych klastrów zoptymalizowanych pod kątem konkretnych obciążeń na mniejszej liczbie zależności między składnikami** -konfigurację usługi Hadoop typowe lokalnych korzysta z jednego klastra, która służy do wielu celów. Za pomocą usługi Azure HDInsight można utworzyć klastry specyficznego dla obciążenia. Tworzenie klastrów dla konkretnych obciążeń eliminuje złożoność obsługi pojedynczy klaster z wzrostem złożoności.
- **Produktywność** — można użyć różnych narzędzi dla usług Hadoop i Spark w preferowanym środowisku programistycznym.
- **Rozszerzalność przy użyciu niestandardowego narzędzia lub aplikacje innych producentów** -klastrów HDInsight można rozszerzyć za pomocą zainstalowanych składników i mogą również być zintegrowane z innymi rozwiązaniami danych big data przy użyciu [jednym kliknięciem](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  wdrożenia z poziomu portalu Azure Marketplace.
- **Łatwe zarządzanie, administrowanie oraz monitorowanie** — Azure HDInsight integruje się z [usługi Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) , zapewniając pojedynczy interfejs, za pomocą którego można monitorować wszystkie swoje klastry.
- **Integracja z innymi usługami Azure** — HDInsight można łatwo zintegrować z innymi popularnymi usługami platformy Azure, takich jak następujące:

    - Usługi Azure Data Factory (ADF)
    - Azure Blob Storage
    - Usługa Azure Data Lake Storage 2. generacji
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Samonaprawiania procesów i składniki** — HDInsight nieustannie analizuje składników infrastruktury i typu open source, za pomocą własnej infrastruktury monitorowania. Również automatycznie odzyskuje błędy krytyczne, takie jak niedostępność składników typu open source i węzłów. Alerty są wyzwalane w Ambari, jeśli dowolny składnik OSS nie powiodło się.

Aby uzyskać więcej informacji, zobacz artykuł [co to jest Azure HDInsight oraz stosu technologii Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces planowania migracji

Poniższe kroki są zalecane w przypadku planowania migracji lokalnych klastrów Hadoop do usługi Azure HDInsight:

1. Dowiedz się, bieżącego wdrożenia w środowisku lokalnym i topologii.
2. Dowiedz się, bieżącego zakresu projektu, osie czasu i doświadczenia zespołu.
3. Zapoznaj się z wymaganiami platformy Azure.
4. Do skompilowania szczegółowego planu, w oparciu o najlepsze rozwiązania.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następny artykuł w tej serii:

- [Najlepsze rozwiązania architektury dla lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)