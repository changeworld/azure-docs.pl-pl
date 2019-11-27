---
title: Tagi usług sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight
description: Użyj tagów usługi HDInsight, aby zezwolić na ruch przychodzący do klastra z węzłów usług HDInsight Health i Management Services bez jawnego dodawania adresów IP do sieciowych grup zabezpieczeń.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/19/2019
ms.openlocfilehash: 7e3ce33bdf0773ababe5eb190877a9288c094c5c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187087"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Tagi usług sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight

Tagi usługi HDInsight dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) to grupy adresów IP dla usług kondycji i zarządzania. Te grupy pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń. [Tagi usług](../virtual-network/security-overview.md#service-tags) zapewniają alternatywną metodę zezwalania na ruch przychodzący z określonych adresów IP bez wprowadzania poszczególnych [adresów IP zarządzania](hdinsight-management-ip-addresses.md) w sieciowych grupach zabezpieczeń.

Te Tagi usługi są tworzone i zarządzane przez usługę HDInsight. Nie można utworzyć własnego tagu usługi ani zmodyfikować istniejącego tagu. Firma Microsoft zarządza prefiksami adresów, które pasują do tagu usługi, i automatycznie aktualizuje tag usługi jako adresy.

## <a name="getting-started-with-service-tags"></a>Wprowadzenie do korzystania z tagów usługi

Dostępne są dwie opcje używania tagów usługi w sieciowych grupach zabezpieczeń:

1. Użyj jednego tagu usługi HDInsight — ta opcja spowoduje otwarcie sieci wirtualnej dla wszystkich adresów IP używanych przez usługę HDInsight do monitorowania klastrów we wszystkich regionach. Ta opcja jest najprostszą metodą, ale może nie być odpowiednia, jeśli istnieją restrykcyjne wymagania dotyczące zabezpieczeń.

1. Użyj wielu tagów usługi regionalnej — ta opcja spowoduje otwarcie sieci wirtualnej tylko do adresów IP, których Usługa HDInsight używa w tym konkretnym regionie. Jeśli jednak korzystasz z wielu regionów, musisz dodać wiele tagów usługi do sieci wirtualnej.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Użyj jednego globalnego tagu usługi HDInsight

Najprostszym sposobem rozpoczęcia korzystania z tagów usług w klastrze usługi HDInsight jest dodanie tagu globalnego `HDInsight` do reguły sieciowej grupy zabezpieczeń. Aby uzyskać instrukcje dotyczące dodawania tagów usługi do sieciowej grupy zabezpieczeń, zobacz [grupy zabezpieczeń: Tagi usług](../virtual-network/security-overview.md#service-tags).

Ten tag zawiera adresy IP usług kondycji i zarządzania dla wszystkich regionów, w których Usługa HDInsight jest dostępna, i zapewnia, że klaster będzie mógł komunikować się z niezbędnymi usługami kondycji i zarządzania niezależnie od miejsca, w którym zostały utworzone.

## <a name="use-regional-hdinsight-service-tags"></a>Korzystanie z regionalnych tagów usługi HDInsight

Jeśli opcja jeden nie będzie działała, ponieważ potrzebne są bardziej restrykcyjne uprawnienia, można zezwolić na dostęp tylko do tagów usługi odpowiednich dla danego regionu. Odpowiednie Tagi usług mogą mieć jeden, dwa lub trzy Tagi usługi, w zależności od regionu, w którym jest tworzony klaster.

Aby dowiedzieć się, które Tagi usług dodać do regionu, przeczytaj następujące sekcje dokumentu.

### <a name="use-a-single-regional-service-tag"></a>Użyj jednego tagu usługi regionalnej

Jeśli wolisz używać opcji tagów usługi dwa, a klaster znajduje się w jednym z regionów wymienionych w tej tabeli, wystarczy dodać tylko jeden tag usługi regionalnej do sieciowej grupy zabezpieczeń.

| Kraj | Region | Tag usługi |
| ---- | ---- | ---- |
| Australia | Australia Wschodnia | HDInsight. AustraliaEast |
| &nbsp; | Australia Południowo-Wschodnia | HDInsight. AustraliaSoutheast |
| &nbsp; | Australia Środkowa | HDInsight. AustraliaCentral |
| Chiny | Chiny Wschodnie 2 | HDInsight. ChinaEast2 |
| &nbsp; | Chiny Północne 2 | HDInsight. ChinaNorth2 |
| Stany Zjednoczone | Środkowo-północne stany USA | HDInsight. NorthCentralUS |
| &nbsp; | Zachodnie stany USA 2 | HDInsight. WestUS2 |
| &nbsp; | Środkowo-zachodnie stany USA | HDInsight. WestCentralUS |
| Kanada | Kanada Wschodnia | HDInsight. CanadaEast |
| Brazylia | Brazylia Południowa | HDInsight. BrazilSouth |
| Korea | Korea Środkowa | HDInsight. KoreaCentral |
| &nbsp; | Korea Południowa | HDInsight. KoreaSouth |
| Indie | Indie Środkowe | HDInsight. CentralIndia |
| &nbsp; | Indie Południowe | HDInsight. SouthIndia |
| Japonia | Japonia Zachodnia | HDInsight. JapanWest |
| Francja | Francja Środkowa| HDInsight. FranceCentral |
| Zjednoczone Królestwo | Południowe Zjednoczone Królestwo | HDInsight. UKSouth |
| Azure Government (Fairfax) | USDoD środkowe   | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | UsDoD wschód | HDInsight. USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>Używanie wielu tagów usługi regionalnej

Jeśli wolisz używać opcji tagów usługi dwa, a region, w którym tworzony jest klaster, nie został wymieniony powyżej, musisz zezwolić na wiele tagów usługi regionalnej. Konieczność korzystania z więcej niż jednego jest spowodowana różnicami w rozmieszczeniu dostawców zasobów dla różnych regionów.

Pozostałe regiony są podzielone na grupy w oparciu o używane przez nie Tagi usług regionalnych.

#### <a name="group-1"></a>Grupa 1

Jeśli klaster został utworzony w jednym z regionów w poniższej tabeli, Zezwól na używanie tagów usługi `HDInsight.WestUS` i `HDInsight.EastUS` poza regionalnym numerem znacznika usługi. Regiony w tej sekcji wymagają trzech tagów usługi.

Na przykład jeśli klaster jest tworzony w regionie `East US 2`, należy dodać następujące znaczniki usługi do sieciowej grupy zabezpieczeń:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Kraj | Region | Tag usługi |
| ---- | ---- | ---- |
| Stany Zjednoczone | Wschodnie stany USA 2 | HDInsight. EastUS2 |
| &nbsp; | Środkowe stany USA | HDInsight. środkowe |
| &nbsp; | NorthCentral nam | HDInsight. NorthCentralUS |
| &nbsp; | Środkowo-południowe stany USA | HDInsight. SouthCentralUS |
| &nbsp; | Wschodnie stany USA | HDInsight. wschód |
| &nbsp; | Zachodnie stany USA | HDInsight. Zachodnie |
| Japonia | Japonia Wschodnia | HDInsight. JapanEast |
| Europa | Europa Północna | HDInsight. NorthEurope |
| &nbsp; | Europa Zachodnia| HDInsight. WestEurope |
| Azja | Azja Wschodnia | HDInsight. EastAsia |
| &nbsp; | Azja Południowo-Wschodnia | HDInsight. SoutheastAsia |
| Australia | Australia Wschodnia | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Grupa 2

Klastry w regionach **Chiny Północne** i **Chiny Wschodnie**muszą zezwalać na dwie znaczniki usługi: `HDInsight.ChinaNorth` i `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Grupa 3

Klastry w regionach **US gov Iowa** i **US gov Wirginia**muszą zezwalać na dwie znaczniki usługi: `HDInsight.USGovIowa` i `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Grupa 4

Klastry w regionach **Niemiec środkowe** i **Niemcy**Wschodnie muszą zezwalać na dwie znaczniki usługi: `HDInsight.GermanyCentral` i `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Następne kroki

* [Sieciowe grupy zabezpieczeń — Tagi usług](../virtual-network/security-overview.md#security-rules)
* [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md)
