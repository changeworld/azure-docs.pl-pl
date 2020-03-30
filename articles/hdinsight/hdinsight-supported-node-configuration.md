---
title: Konfiguracje węzłów obsługiwanych przez usługę Azure HDInsight
description: Poznaj minimalne i zalecane konfiguracje dla węzłów klastra HDInsight.
keywords: rozmiary maszyn wirtualnych, rozmiary klastra, konfiguracja klastra
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484790"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Jakie są domyślne i zalecane konfiguracje węzłów dla usługi Azure HDInsight?

W tym artykule omówiono domyślne i zalecane konfiguracje węzłów dla klastrów usługi Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Domyślna i minimalna zalecana konfiguracja węzłów i rozmiary maszyn wirtualnych dla klastrów

W poniższych tabelach przedstawiono domyślne i zalecane rozmiary maszyny wirtualnej (VM) dla klastrów usługi HDInsight.  Te informacje są niezbędne do zrozumienia rozmiarów maszyn wirtualnych do użycia podczas tworzenia skryptów interfejsu wiersza polecenia programu PowerShell lub platformy Azure w celu wdrożenia klastrów usługi HDInsight.

Jeśli potrzebujesz więcej niż 32 węzłów procesu roboczego w klastrze, wybierz rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM. 

Jedynymi typami klastrów, które mają dyski danych, są klastry Platformy Kafka i HBase z włączoną funkcją Przyspieszone zapisy. Program HDInsight obsługuje rozmiary dysków P30 i S30 w tych scenariuszach.

Specyfikacje wszystkich minimalnych zalecanych typów maszyn wirtualnych używanych w tym dokumencie są podsumowane w poniższej tabeli.

| Rozmiar              | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna karta sieciowa / oczekiwana przepustowość sieci (Mb/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16x500           | 4 / 3000                                       |
| Standardowa_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32x500           | 8 / 6000                                       |
| Standardowa_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standardowa_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standardowa_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standardowa_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standardowa_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |

Aby uzyskać więcej informacji na temat specyfikacji każdego typu maszyny Wirtualnej, zobacz następujące dokumenty:

* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia: Seria Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci: Seria Dv2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Rozmiary maszyn wirtualnych ogólnego przeznaczenia: seria Av2 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Wszystkie obsługiwane regiony z wyjątkiem Brazylii na południu i Japonii zachodniej

> [!Note]
> Aby uzyskać identyfikator jednostki SKU do użycia w programie `Standard_` PowerShell i innych skryptów, dodaj na początku wszystkich jednostek SKU maszyn wirtualnych w poniższych tabelach. Na przykład, `D12_v2` `Standard_D12_v2`może stać się .

| Typ klastra | Hadoop | HBase | Zapytanie interakcyjne | Storm | platforma Spark | Serwer ML | Kafka |
|---|---|---|---|---|---|---|---|
| Głowa: domyślny rozmiar maszyny Wirtualnej | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Głowica: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Pracownik: domyślny rozmiar maszyny Wirtualnej | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 z 2 dyskami S30 na brokera |
| Pracownik: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: domyślny rozmiar maszyny Wirtualnej |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: minimalne zalecane rozmiary maszyn wirtualnych |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Usługi ML: domyślny rozmiar maszyny Wirtualnej |  |  |  |  |  | D4_v2 |  |
| Usługi ML: minimalny zalecany rozmiar maszyny Wirtualnej |  |  |  |  |  | D4_v2 |  |

\*= Rozmiary maszyn wirtualnych dla klastrów pakietu zabezpieczeń przedsiębiorstwa Spark (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Brazylia tylko na południu i w Japonii na zachód

| Typ klastra | Hadoop | HBase | Zapytanie interakcyjne | Storm | platforma Spark | Usługi ML |
|---|---|---|---|---|---|---|
| Głowa: domyślny rozmiar maszyny Wirtualnej | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Głowica: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Pracownik: domyślny rozmiar maszyny Wirtualnej | D4 | D4 | D14 | D3 | D13 | D4 |
| Pracownik: minimalne zalecane rozmiary maszyn wirtualnych | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: domyślny rozmiar maszyny Wirtualnej |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: minimalne zalecane rozmiary maszyn wirtualnych |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Usługi ML: domyślne rozmiary maszyn wirtualnych |  |  |  |  |  | D4 |
| Usługi ML: minimalne zalecane rozmiary maszyn wirtualnych |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Głowa jest znana jako *Nimbus* dla typu klastra Burza.
> - Pracownik jest znany jako *nadzorca* dla typu klastra Burza.
> - Pracownik jest znany jako *Region* dla typu klastra HBase.

## <a name="next-steps"></a>Następne kroki

* [Jakie są komponenty i wersje Apache Hadoop z HDInsight?](hdinsight-component-versioning.md)
