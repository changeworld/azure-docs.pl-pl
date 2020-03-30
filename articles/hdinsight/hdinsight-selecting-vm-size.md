---
title: Jak wybrać odpowiedni rozmiar maszyny Wirtualnej dla klastra usługi Azure HDInsight
description: Dowiedz się, jak wybrać odpowiedni rozmiar maszyny Wirtualnej dla klastra HDInsight.
keywords: rozmiary maszyn wirtualnych, rozmiary klastra, konfiguracja klastra
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682203"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Wybieranie odpowiedniego rozmiaru maszyny Wirtualnej dla klastra usługi Azure HDInsight

W tym artykule omówiono sposób wyboru odpowiedniego rozmiaru maszyny Wirtualnej dla różnych węzłów w klastrze HDInsight. 

Rozpocznij od zrozumienia, jak właściwości maszyny wirtualnej, takie jak przetwarzanie procesora CPU, rozmiar pamięci RAM i opóźnienie sieci, wpłynie na przetwarzanie obciążeń. Następnie zastanów się nad aplikacją i tym, jak pasuje do tego, dla czego zoptymalizowane są różne rodziny maszyn wirtualnych. Upewnij się, że rodzina maszyn wirtualnych, której chcesz użyć, jest zgodna z typem klastra, który zamierzasz wdrożyć. Aby uzyskać listę wszystkich obsługiwanych i zalecanych rozmiarów maszyn wirtualnych dla każdego typu klastra, zobacz [Konfiguracje węzłów obsługiwanych przez usługę Azure HDInsight](hdinsight-supported-node-configuration.md). Nareszcie można użyć procesu analizy porównawczej, aby przetestować niektóre przykładowe obciążenia i sprawdzić, która jednostka SKU w tej rodzinie jest dla Ciebie odpowiednia.

Aby uzyskać więcej informacji na temat planowania innych aspektów klastra, takich jak wybieranie typu magazynu lub rozmiaru klastra, zobacz [Planowanie pojemności klastrów HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Właściwości maszyn wirtualnych i duże zbiory danych

Rozmiar i typ maszyny Wirtualnej zależy od mocy obliczeniowej procesora, rozmiaru pamięci RAM i opóźnienia sieci:

- Procesor: Rozmiar maszyny Wirtualnej decyduje o liczbie rdzeni. Im więcej rdzeni, tym większy stopień obliczeń równoległych może osiągnąć każdy węzeł. Ponadto niektóre typy maszyn wirtualnych mają szybsze rdzenie.

- Pamięć RAM: Rozmiar maszyny Wirtualnej określa również ilość pamięci RAM dostępnej na maszynie Wirtualnej. W przypadku obciążeń, które przechowują dane w pamięci do przetwarzania, a nie odczytu z dysku, upewnij się, że węzły procesu roboczego mają wystarczającą ilość pamięci, aby dopasować dane.

- Sieć: W przypadku większości typów klastrów dane przetwarzane przez klaster nie znajdują się na dysku lokalnym, ale raczej w zewnętrznej usłudze magazynu, takiej jak Data Lake Storage lub Azure Storage. Należy wziąć pod uwagę przepustowość sieci i przepływność między węzłem Maszyny Wirtualnej i usługi magazynu. Przepustowość sieci dostępna dla maszyny Wirtualnej zazwyczaj zwiększa się przy większych rozmiarach. Aby uzyskać szczegółowe informacje, zobacz [omówienie rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Opis optymalizacji maszyn wirtualnych

Rodziny maszyn wirtualnych na platformie Azure są zoptymalizowane pod kątem różnych przypadków użycia. W poniższej tabeli można znaleźć niektóre z najbardziej popularnych przypadków użycia i rodzin maszyn wirtualnych, które pasują do nich.

| Typ                     | Rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Entry-level](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Mieć wydajność procesora i konfiguracje pamięci najlepiej nadaje się do obciążeń na poziomie podstawowym, takich jak rozwój i testowanie. Są ekonomiczne i zapewniają tanią opcję, aby rozpocząć korzystanie z platformy Azure. |
| [Zastosowania ogólne](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Zrównoważony stosunek procesora do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](../virtual-machines/linux/sizes-compute.md)        | F           | Wysoki współczynnik procesora cpu do pamięci. Dobre dla serwerów sieci web o średnim natężeniu ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji.        |
| [Optymalizacja pod kątem pamięci](../virtual-machines/linux/sizes-memory.md)         | Esv3, Ev3  | Wysoki współczynnik pamięci do procesora. Opcja bardzo dobra w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |

- Aby uzyskać informacje na temat cen dostępnych wystąpień maszyn wirtualnych w regionach obsługiwanych przez system HDInsight, zobacz [Cennik hdinsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Ekonomiczne typy maszyn wirtualnych dla lekkich obciążeń

Jeśli masz wymagania dotyczące przetwarzania światła, [seria F](https://azure.microsoft.com/blog/f-series-vm-size/) może być dobrym wyborem, aby rozpocząć pracę z HDInsight. Oferując niższą cenę za godzinę, seria F zapewnia najlepszy w portfolio platformy Azure stosunek ceny do wydajności, określany na podstawie liczby jednostek ACU (Azure Compute Unit) na procesor wirtualny vCPU.

W poniższej tabeli opisano typy klastrów i typy węzłów, które można utworzyć za pomocą maszyn wirtualnych z serii Fsv2.

| Typ klastra | Wersja | Węzeł pracownika | Węzeł główny | Węzeł zookeepera |
|---|---|---|---|---|
| platforma Spark | Wszystkie | F4 i powyżej | nie | nie |
| Hadoop | Wszystkie | F4 i powyżej | nie | nie |
| Kafka | Wszystkie | F4 i powyżej | nie | nie |
| HBase | Wszystkie | F4 i powyżej | nie | nie |
| LLAP (WŁASJ) | wyłączone | nie | nie | nie |
| Storm | wyłączone | nie | nie | nie |
| Usługa ML | TYLKO HDI 3.6 | F4 i powyżej | nie | nie |

Aby zapoznać się ze specyfikacjami każdej jednostki SKU serii F, zobacz [Rozmiary maszyn wirtualnych serii F](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Benchmarking

Analiza porównawcza to proces uruchamiania symulowanych obciążeń na różnych maszynach wirtualnych w celu zmierzenia, jak dobrze będą one działać dla obciążeń produkcyjnych. 

Aby uzyskać więcej informacji na temat analizy porównawczej jednostek SKU maszyn wirtualnych i rozmiarów klastrów, zobacz [Planowanie pojemności klastra w usłudze Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Następne kroki

- [Konfiguracje węzłów obsługiwanych przez usługę Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/linux/sizes.md)
