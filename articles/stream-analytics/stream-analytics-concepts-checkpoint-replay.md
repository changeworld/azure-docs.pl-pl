---
title: Pojęcia dotyczące odzyskiwania punktów kontrolnych i powtarzania w usłudze Azure Stream Analytics
description: W tym artykule opisano pojęcia odzyskiwania zadań punktu kontrolnego i odtwarzania w usłudze Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426402"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Pojęcia dotyczące punktów kontrolnych i powtarzania w zadaniach usługi Azure Stream Analytics
W tym artykule opisano wewnętrzne pojęcia punktu kontrolnego i powtarzania w usłudze Azure Stream Analytics oraz ich wpływ na odzyskiwanie zadań. Za każdym razem, gdy uruchamia się zadanie usługi Stream Analytics, informacje o stanie są obsługiwane wewnętrznie. Te informacje o stanie są okresowo zapisywane w punkcie kontrolnym. W niektórych scenariuszach informacje o punkcie kontrolnym są używane do odzyskiwania zadania, jeśli wystąpi niepowodzenie lub uaktualnienie zadania. W innych okolicznościach punkt kontrolny nie może służyć do odzyskiwania i jest konieczne powtórzenie.

## <a name="stateful-query-logicin-temporal-elements"></a>Logika kwerendy stanowej w elementach czasowych
Jedną z unikatowych możliwości zadania usługi Azure Stream Analytics jest wykonywanie przetwarzania stanowego, takiego jak agregaty okienne, sprzężenia czasowe i funkcje analityczne czasowe. Każdy z tych operatorów przechowuje informacje o stanie po uruchomieniu zadania.Maksymalny rozmiar okna dla tych elementów kwerendy wynosi siedem dni. 

Koncepcja okna czasowego pojawia się w kilku elementach zapytania usługi Stream Analytics:
1. Agregaty w oknie (GRUPA PRZEZ okna tumbling, hopping i przesuwne)

2. Sprzężenia czasowe (DOŁĄCZ z DATEDIFF)

3. Czasowe funkcje analityczne (ISFIRST, LAST i LAG z LIMITEM CZASU TRWANIA)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Odzyskiwanie zadania po awarii węzła, w tym uaktualnienie systemu operacyjnego
Za każdym razem, gdy uruchamia się zadanie usługi Stream Analytics, wewnętrznie jest skalowane w poziomie, aby wykonać pracę w wielu węzłach procesu roboczego. Stan każdego węzła procesu roboczego jest punkt kontrolny co kilka minut, co pomaga systemowi odzyskać, jeśli wystąpi awaria.

Czasami dany węzeł procesu roboczego może zakończyć się niepowodzeniem lub może wystąpić uaktualnienie systemu operacyjnego dla tego węzła procesu roboczego. Aby odzyskać automatycznie, usługa Stream Analytics uzyskuje nowy węzeł w dobrej kondycji, a stan węzła poprzedniego procesu roboczego zostanie przywrócony z najnowszego dostępnego punktu kontrolnego. Aby wznowić pracę, niewielka ilość odtwarzania jest konieczne, aby przywrócić stan od momentu, gdy punkt kontrolny jest pobierana. Zwykle przerwa przywracania wynosi tylko kilka minut. Gdy dla zadania zostanie wybrana wystarczająca liczba jednostek przesyłania strumieniowego, powtórka powinna zostać szybko zakończona. 

W kwerendzie w pełni równoległe czas potrzebny do nadrobienia zaległości po awarii węzła procesu roboczego jest proporcjonalny do:

[szybkość zdarzenia wejściowego] x [długość odstępu] / [liczba partycji przetwarzania]

Jeśli kiedykolwiek zauważysz znaczne opóźnienie przetwarzania z powodu awarii węzła i uaktualnienia systemu operacyjnego, należy rozważyć uczynienie kwerendy w pełni równoległą i skaluj zadanie, aby przydzielić więcej jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Skalowanie zadania usługi Azure Stream Analytics w celu zwiększenia przepływności.](stream-analytics-scale-jobs.md)

Bieżąca usługa Stream Analytics nie pokazuje raportu, gdy odbywa się tego rodzaju proces odzyskiwania.

## <a name="job-recovery-from-a-service-upgrade"></a>Odzyskiwanie zadań po uaktualnieniu usługi 
Firma Microsoft od czasu do czasu uaktualnia pliki binarne, które uruchamiają zadania usługi Stream Analytics w usłudze Platformy Azure. W tych czasach uruchomione zadania użytkowników są uaktualniane do nowszej wersji, a zadanie uruchamia się automatycznie. 

Obecnie format punktu kontrolnego odzyskiwania nie jest zachowywany między uaktualnieniami. W rezultacie stan kwerendy przesyłania strumieniowego musi zostać przywrócony całkowicie przy użyciu techniki powtarzania. Aby umożliwić zadania usługi Stream Analytics odtwarzać dokładnie te same dane wejściowe sprzed, ważne jest, aby ustawić zasady przechowywania danych źródłowych na co najmniej rozmiary okien w zapytaniu. Niezastosowanie się do tego może spowodować nieprawidłowe lub częściowe wyniki podczas uaktualniania usługi, ponieważ dane źródłowe mogą nie być przechowywane wystarczająco daleko wstecz, aby uwzględnić pełny rozmiar okna.

Ogólnie rzecz biorąc ilość potrzebnej powtórki jest proporcjonalna do rozmiaru okna pomnożonego przez średnią szybkość zdarzenia. Na przykład dla zadania z szybkością wejściową 1000 zdarzeń na sekundę, rozmiar okna większy niż jedna godzina jest uważany za duży rozmiar odtwarzania. Może być konieczne ponowne przetworzenie danych do jednej godziny w celu zainicjowania stanu, aby można było uzyskać pełne i poprawne wyniki, co może spowodować opóźnienie produkcji wyjściowej (bez danych wyjściowych) przez dłuższy okres. Zapytania bez okien lub innych operatorów `JOIN` `LAG`czasowych, takich jak lub , będzie miał zero odtwarzania.

## <a name="estimate-replay-catch-up-time"></a>Oszacowanie czasu nadrabiania zaległości powtórki
Aby oszacować długość opóźnienia spowodowanego uaktualnieniem usługi, można wykonać tę technikę:

1. Załaduj wejściowe Centrum zdarzeń z wystarczającą ilością danych, aby pokryć największy rozmiar okna w zapytaniu, z oczekiwaną szybkością zdarzeń. Sygnatura czasowa zdarzeń powinna być zbliżona do czasu zegara ściennego przez cały ten okres czasu, tak jakby była to transmisja danych wejściowych na żywo. Jeśli na przykład w kwerendzie jest 3-dniowe okno, wyślij zdarzenia do Centrum zdarzeń na trzy dni i kontynuuj wysyłanie zdarzeń. 

2. Uruchom zadanie przy użyciu **teraz** jako czas rozpoczęcia. 

3. Zmierz czas między czasem rozpoczęcia a wygenerowaniem pierwszego wyjścia. Czas jest przybliżony, ile opóźnienia zadanie poniesie podczas uaktualniania usługi.

4. Jeśli opóźnienie jest zbyt długie, spróbuj podzielić zadanie na partycje i zwiększyć liczbę sus, więc obciążenie jest rozłożone na więcej węzłów. Alternatywnie należy rozważyć zmniejszenie rozmiarów okien w kwerendzie i wykonać dalsze agregacji lub innych stanowych przetwarzania na dane wyjściowe produkowane przez zadanie usługi Stream Analytics w ujściu podrzędnym (na przykład przy użyciu bazy danych SQL platformy Azure).

Aby uzyskać ogólne obawy dotyczące stabilności usług podczas uaktualniania zadań o znaczeniu krytycznym, należy rozważyć uruchomienie zduplikowanych zadań w sparowanych regionach platformy Azure. Aby uzyskać więcej informacji, zobacz [Niezawodność zadań usługi Guarantee Stream Analytics podczas aktualizacji usługi.](stream-analytics-job-reliability.md)

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Odzyskiwanie zadania z zainicjowanego przez użytkownika zatrzymania i uruchomienia
Aby edytować składnię kwerendy w zadaniu przesyłania strumieniowego lub dostosować dane wejściowe i wyjściowe, zadanie musi zostać zatrzymane, aby wprowadzić zmiany i uaktualnić projekt zadania. W takich scenariuszach, gdy użytkownik zatrzymuje zadanie przesyłania strumieniowego i uruchamia go ponownie, scenariusz odzyskiwania jest podobny do uaktualnienia usługi. 

Dane punktu kontrolnego nie mogą być używane do ponownego uruchamiania zadania inicjowanego przez użytkownika. Aby oszacować opóźnienie danych wyjściowych podczas takiego ponownego uruchomienia, należy użyć tej samej procedury, jak opisano w poprzedniej sekcji i zastosować podobne środki zaradcze, jeśli opóźnienie jest zbyt długie.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat niezawodności i skalowalności, zobacz następujące artykuły:
- [Samouczek: Konfigurowanie alertów dla zadań usługi Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Skalowanie zadania usługi Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)
- [Gwarancja niezawodności zadań usługi Stream Analytics podczas aktualizacji usług](stream-analytics-job-reliability.md)
