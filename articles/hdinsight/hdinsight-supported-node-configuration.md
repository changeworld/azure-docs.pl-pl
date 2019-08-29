---
title: Konfiguracje węzłów obsługiwane przez usługę Azure HDInsight
description: Poznaj minimalne i zalecane konfiguracje dla węzłów klastra usługi HDInsight.
keywords: rozmiary maszyn wirtualnych, rozmiary klastrów, Konfiguracja klastra
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076214"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Jakie są domyślne i zalecane konfiguracje węzłów dla usługi Azure HDInsight?

W tym artykule omówiono domyślne i zalecane konfiguracje węzłów dla klastrów usługi Azure HDInsight.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Domyślna i zalecana konfiguracja węzłów oraz rozmiary maszyn wirtualnych dla klastrów

W poniższych tabelach przedstawiono domyślne i zalecane rozmiary maszyn wirtualnych dla klastrów usługi HDInsight.  Te informacje są niezbędne, aby zrozumieć rozmiary maszyn wirtualnych używane podczas tworzenia skryptów programu PowerShell lub interfejsu wiersza polecenia platformy Azure w celu wdrożenia klastrów usługi HDInsight. 

Jeśli potrzebujesz więcej niż 32 węzłów procesu roboczego w klastrze, wybierz rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM. 

Jedynymi typami klastrów z dyskami danych są klastry Kafka i HBase z włączoną funkcją przyspieszonego zapisywania. Usługa HDInsight obsługuje rozmiary dysków P30 i S30 w tych scenariuszach.

Aby uzyskać szczegółowe informacje na temat specyfikacji poszczególnych typów maszyn wirtualnych, zobacz następujące dokumenty:

* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia: Seria Dv2 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci: Seria Dv2 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia: Seria Av2 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Wszystkie obsługiwane regiony, z wyjątkiem Brazylii Południowe i Japonia Zachodnia

> [!Note]
> Aby uzyskać identyfikator jednostki SKU do użycia w programie PowerShell i innych skryptach `Standard_` , Dodaj na początku wszystkie jednostki SKU maszyny wirtualnej w poniższych tabelach. Na przykład `D12_v2`. `Standard_D12_v2`

| Typ klastra | Hadoop | HBase | Zapytanie interakcyjne | Storm | platforma Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Nagłówek: domyślny rozmiar maszyny wirtualnej | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Główna: zalecane rozmiary maszyn wirtualnych | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Proces roboczy: domyślny rozmiar maszyny wirtualnej | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 z 2 dyskami S30 na brokera |
| Proces roboczy: zalecane rozmiary maszyn wirtualnych | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| Dozorcy: domyślny rozmiar maszyny wirtualnej |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| Dozorcy: zalecane rozmiary maszyn wirtualnych |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| Usługi ML: domyślny rozmiar maszyny wirtualnej |  |  |  |  |  | D4_v2 |  |
| Usługi w usłudze ML: zalecany rozmiar maszyny wirtualnej |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Brazylia Południowa i Japonia Zachodnia

| Typ klastra | Hadoop | HBase | Zapytanie interakcyjne | Storm | platforma Spark | ML Services |
|---|---|---|---|---|---|---|
| Nagłówek: domyślny rozmiar maszyny wirtualnej | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Główna: zalecane rozmiary maszyn wirtualnych | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Proces roboczy: domyślny rozmiar maszyny wirtualnej | D4 | D4 | D14 | D3 | D13 | D4 |
| Proces roboczy: zalecane rozmiary maszyn wirtualnych | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Dozorcy: domyślny rozmiar maszyny wirtualnej |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Dozorcy: zalecane rozmiary maszyn wirtualnych |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| Usługi ML: domyślne rozmiary maszyn wirtualnych |  |  |  |  |  | D4 |
| Usługi w usłudze ML: zalecane rozmiary maszyn wirtualnych |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - Nagłówek jest znany jako *Nimbus* dla typu klastra burza.
> - Proces roboczy jest znany jako *opiekun* dla typu klastra burza.
> - Proces roboczy jest znany jako *region* dla typu klastra HBase.

## <a name="next-steps"></a>Następne kroki

* [Jakie są składniki platformy Apache Hadoop i wersje dostępne z HDInsight?](hdinsight-component-versioning.md)
