---
title: Kompromisy dotyczące spójności, dostępności i wydajności usługi Azure Cosmos DB
description: Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 7cdaa9699b15000359c438bcc410e300415b759a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379957"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisy w zakresie spójności, dostępności i wydajności

Rozproszone bazy danych polegające na replikacji do zapewnienia wysokiej dostępności, małych opóźnień lub obu tych charakterystyk muszą iść na pewne kompromisy. Te kompromisy dotyczą spójności odczytu wobec dostępności, opóźnienia i przepływności.

Usługa Azure Cosmos DB podchodzi do spójności danych jako spektrum wyborów. Takie podejście obejmuje więcej opcji niż dwie skrajności silnej i ostatecznej spójności. Możesz wybrać jeden z pięciu dobrze zdefiniowanych poziomów w spektrum spójności. Od najsilniejszych do najsłabszych, poziomy są:

- *Silna*
- *Ograniczona nieaktualność*
- *Sesja*
- *Spójny prefiks*
- *Ostateczna*

Każdy poziom zapewnia dostępność i wydajność kompromisów i jest wspierany przez kompleksowe umowy SLA.

## <a name="consistency-levels-and-latency"></a>Poziomy spójności i opóźnienia

Opóźnienie odczytu dla wszystkich poziomów spójności jest zawsze gwarantowane być mniej niż 10 milisekund przy 99 percentylu. To opóźnienie odczytu jest poparte umowy SLA. Średni czas odczytu, przy 50 percentylu, jest zazwyczaj 4 milisekundy lub mniej.

Opóźnienie zapisu dla wszystkich poziomów spójności jest zawsze gwarantowane być mniej niż 10 milisekund przy 99 percentylu. To opóźnienie zapisu jest wspierany przez umowy SLA. Średnie opóźnienie zapisu, przy 50 percentylu, jest zwykle 5 milisekund lub mniej. Konta usługi Azure Cosmos, które obejmują kilka regionów i są skonfigurowane z silną spójnością są wyjątkiem od tej gwarancji.

### <a name="write-latency-and-strong-consistency"></a>Opóźnienie zapisu i silna spójność

W przypadku kont usługi Azure Cosmos skonfigurowanych z silną spójnością z więcej niż jednym regionem opóźnienie zapisu jest równe dwa razy w obie strony (RTT) między dowolnym z dwóch najdychwszych regionów oraz 10 milisekund przy 99 percentylu. RtT wysokiej sieci między regionami przełoży się na większe opóźnienia dla żądań usługi Cosmos DB, ponieważ silna spójność kończy operację tylko po upewnieniu się, że została zatwierdzona do wszystkich regionów w ramach konta.

Dokładne opóźnienie RTT jest funkcją odległości prędkości światła i topologii sieci platformy Azure. Usługi azure networking nie zapewniają żadnych łasce opóźnienia dla RTT między dowolnymi dwoma regionami platformy Azure. Dla konta usługi Azure Cosmos opóźnienia replikacji są wyświetlane w witrynie Azure portal. Za pomocą witryny Azure Portal (przejdź do bloku Metryki, wybierz kartę Spójność), aby monitorować opóźnienia replikacji między różnymi regionami skojarzonymi z kontem usługi Azure Cosmos.

> [!IMPORTANT]
> Silna spójność kont z regionami obejmującymi więcej niż 5000 mil (8000 kilometrów) jest domyślnie blokowana z powodu dużych opóźnień zapisu. Aby włączyć tę funkcję, skontaktuj się z pomocą techniczną.

## <a name="consistency-levels-and-throughput"></a>Poziomy spójności i przepustowość

- W przypadku silnego i ograniczonego nieaktualności odczyty są wykonywane względem dwóch replik w zestawie czterech replik (kworum mniejszości), aby zapewnić gwarancje spójności. Sesja, spójny prefiks i ewentualne odczyty pojedynczej repliki. W rezultacie dla tej samej liczby jednostek żądań odczytu przepływności dla silnego i ograniczonego nieaktualności jest połowa innych poziomów spójności.

- Dla danego typu operacji zapisu, takich jak wstawianie, zamienianie, upsert i usuwanie, przepływność zapisu dla jednostek żądań jest identyczna dla wszystkich poziomów spójności.

|**Poziom spójności**|**Odczyty kworum**|**Zapisy kworum**|
|--|--|--|
|**Silna**|Mniejszość lokalna|Globalna większość|
|**Powiązana nieaktualność**|Mniejszość lokalna|Większość lokalna|
|**Sesja**|Pojedyncza replika (przy użyciu tokenu sesji)|Większość lokalna|
|**Spójny prefiks**|Pojedyncza replika|Większość lokalna|
|**Ostateczna**|Pojedyncza replika|Większość lokalna|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Poziomy spójności i trwałość danych

W środowisku globalnie rozproszonej bazy danych istnieje bezpośrednia relacja między poziomem spójności a trwałością danych w obecności awarii w całym regionie. Podczas opracowywania planu ciągłości działania należy zrozumieć maksymalny dopuszczalny czas, zanim aplikacja zostanie w pełni odzyskana po przełomowym zdarzeniu. Czas wymagany do pełnego odzyskania przez aplikację jest znany jako **cel czasu odzyskiwania** **(RTO).** Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych aplikacja może tolerować utraty podczas odzyskiwania po zdarzenie zakłócające. Okres aktualizacji, które można stracić, jest znany jako **cel punktu odzyskiwania** **(RPO).**

W poniższej tabeli zdefiniowano relację między modelem spójności a trwałością danych w przypadku awarii w całym regionie. Należy zauważyć, że w systemie rozproszonym, nawet z silną spójnością, niemożliwe jest dysponowanie rozproszoną bazą danych z RPO i RTO zerowym ze względu na wpr. Aby dowiedzieć się więcej o tym, dlaczego, zobacz [Poziomy spójności w usłudze Azure Cosmos DB.](consistency-levels.md)

|**Region(-y)**|**Tryb replikacji**|**Poziom spójności**|**CEL PUNKTU ODZYSKIWANIA**|**Rto**|
|---------|---------|---------|---------|---------|
|1|Pojedynczy lub multi-master|Dowolny poziom spójności|< 240 minut|<1 tydzień|
|>1|Pojedynczy wzorzec|Sesja, Spójny prefiks, Ostateczny|< 15 minut|< 15 minut|
|>1|Pojedynczy wzorzec|Powiązana nieaktualność|*K* & *T*|< 15 minut|
|>1|Pojedynczy wzorzec|Silna|0|< 15 minut|
|>1|Multi-Master|Sesja, Spójny prefiks, Ostateczny|< 15 minut|0|
|>1|Multi-Master|Powiązana nieaktualność|*K* & *T*|0|

*K* = Liczba wersji *"K"* (tj. aktualizacji) elementu.

*T* = przedział czasu *"T"* od ostatniej aktualizacji.

## <a name="strong-consistency-and-multi-master"></a>Silna spójność i multi-master

Kont usługi Cosmos skonfigurowanych dla wielu wzorców nie można skonfigurować dla silnej spójności, ponieważ nie jest możliwe, aby system rozproszony zapewniał znak RPO zero i rto zero. Ponadto nie ma żadnych korzyści opóźnienia zapisu przy użyciu silnej spójności z multi-master jak każdy zapis w dowolnym regionie musi być replikowane i zatwierdzone do wszystkich skonfigurowanych regionów w ramach konta. Powoduje to takie samo opóźnienie zapisu jak jedno konto główne.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o globalnej dystrybucji i ogólnej spójności kompromisów w systemach rozproszonych. Zobacz następujące artykuły:

- [Kompromisy spójności w nowoczesnym projektowaniu rozproszonych systemów baz danych](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Wysoka dostępność](high-availability.md)
- [Usługa Azure Cosmos DB NDLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
