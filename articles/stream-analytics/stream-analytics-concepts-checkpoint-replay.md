---
title: Koncepcje odzyskiwania punktów kontrolnych i powtarzania w Azure Stream Analytics
description: W tym artykule opisano punkt kontrolny i powtarzanie koncepcjach odzyskiwania zadania w usłudze Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426402"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Punkt kontrolny i powtarzanie pojęcia dotyczące zadań usługi Azure Stream Analytics
W tym artykule opisano wewnętrzny punkt kontrolny i powtarzanie pojęcia w usłudze Azure Stream Analytics oraz wpływ, że te na zadania odzyskiwania. Każdym uruchomieniu zadania usługi Stream Analytics, informacje o stanie jest zachowywana wewnętrznie. Czy informacje o stanie zostanie zapisany w punkt kontrolny okresowo. W niektórych scenariuszach informacje punktu kontrolnego są używane dla zadania odzyskiwania, jeśli wystąpi błąd zadania lub uaktualnienie. W innych okolicznościach punktu kontrolnego nie może zostać użyty do odzyskiwania, a powtarzania jest konieczne.

## <a name="stateful-query-logicin-temporal-elements"></a>Logiki zapytania stanowych w elementach danych czasowych
Jednym z unikatowych możliwości zadania usługi Azure Stream Analytics jest wykonanie stanowych przetwarzania, takich jak okresowymi danych czasowych sprzężeń i funkcji analitycznych danych czasowych. Każda z tych operatorów przechowuje informacje o stanie, gdy zadanie zostanie uruchomione. Maksymalny rozmiar okna dla tych elementów zapytania wynosi siedem dni. 

Koncepcja okno danych czasowych pojawia się w kilku elementów zapytań usługi Stream Analytics:
1. Okna wartości zagregowanych (grupy przez z wirowania, przeskokiem i przesuwanie systemu windows)

2. Sprzężenia danych czasowych (POŁĄCZ za pomocą funkcji DATEDIFF)

3. Funkcje analityczne danych czasowych (ISFIRST, LAST i OPÓŹNIENIEM przy użyciu LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Zadanie odzyskiwania po awarii węzła, w tym uaktualnienia systemu operacyjnego
Każdym uruchomieniu zadania usługi Stream Analytics, wewnętrznie go jest skalowana w poziomie do pracy w wielu węzłach procesu roboczego. Stan każdego węzła procesu roboczego jest utworzono punkt kontrolny co kilka minut, co pomaga systemu, odzyskiwania, jeśli wystąpi błąd.

Czasami węzłem danego procesu roboczego może zakończyć się niepowodzeniem lub uaktualnienie systemu operacyjnego mogą wystąpić dla tego węzła procesu roboczego. Aby odzyskać automatycznie, Stream Analytics uzyskuje się nowy węzeł dobrej kondycji, a stan węzła poprzedniego procesu roboczego jest przywracany z najnowszy dostępny punkt kontrolny. Aby wznowić pracę, małą ilością powtórzeń jest niezbędne do przywrócenia stanu od momentu, gdy punkt kontrolny jest tworzony. Zazwyczaj przerwa przywracania jest tylko kilka minut. Po wybraniu wystarczającej liczby jednostek przesyłania strumieniowego dla zadania powtarzania powinna być sporządzona szybko. 

W pełni równoległe zapytania czas, jaki zajmuje nadążyć po awarii węzła procesu roboczego jest proporcjonalna do:

[zdarzenie wejściowe szybkość, z] x [długość przerwy] / [liczba partycji]

Jeśli kiedykolwiek zaobserwować opóźnienie przetwarzania z powodu awarii węzła i uaktualnienia systemu operacyjnego należy wziąć pod uwagę, dzięki czemu zapytania w pełni równoległe i skalowanie zadania do przydzielenia większej liczby jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [skalować zadania usługi Azure Stream Analytics, w celu zwiększenia przepływności](stream-analytics-scale-jobs.md).

Bieżący Stream Analytics nie wyświetla raport po odbywa się ten rodzaj procesu odzyskiwania.

## <a name="job-recovery-from-a-service-upgrade"></a>Zadanie odzyskiwania z uaktualniania usługi 
Microsoft od czasu do czasu uaktualnia pliki binarne, które uruchamianie zadania usługi Stream Analytics w usłudze Azure service. W tych godzinach użytkowników uruchomionych zadań zostały zaktualizowane do nowszej wersji, a zadanie zostanie automatycznie ponownie uruchomiona. 

Obecnie format punktu kontrolnego odzyskiwania nie są zachowywane między uaktualnienia. Co w efekcie Stan przesyłania strumieniowego zapytania należy przywrócić wyłącznie przy użyciu techniki powtarzania. Aby umożliwić zadania usługi Stream Analytics do powtarzania dokładnie takie same dane wejściowe z wcześniej, jest ważne, aby ustawić zasady przechowywania dla źródła danych do co najmniej okna o rozmiarach w zapytaniu. Kończy się niepowodzeniem to zrobić może spowodować niepoprawne lub częściowe wyniki podczas uaktualniania usługi, ponieważ źródło danych nie mogą być przechowywane wystarczająco daleko obejmujący rozmiar okna pełną.

Ogólnie rzecz biorąc ilość powtórzeń, potrzebne jest proporcjonalny do rozmiaru okna pomnożona przez średni czas występowania zdarzeń. Na przykład dla zadania o częstotliwości wejściowych 1000 zdarzeń na sekundę rozmiar okna większy niż jedna godzina jest uważany za powtarzania duży rozmiar. Maksymalnie jedną godzinę danych może być konieczne należy ponownie przetworzony zainicjować stan, dzięki czemu można tworzyć pełne i poprawne wyniki, które mogą spowodować opóźnienie danych wyjściowych (Brak danych wyjściowych) dla niektórych dłuższy czas. Zapytania z nie systemu windows lub inne operatory czasowe, takich jak `JOIN` lub `LAG`, będzie mieć zero powtarzania.

## <a name="estimate-replay-catch-up-time"></a>Czas zapoznać się ze zmianami powtarzania szacowania
Aby oszacować długość opóźnienia z powodu uaktualnienia usługi, możesz wykonać tej techniki:

1. Ładowanie danych wejściowych Centrum zdarzeń z ilością danych wystarczającą do pokrycia największy rozmiar okna w zapytaniu, stawki oczekiwanego zdarzenia. Sygnatura czasowa zdarzenia powinny być zbliżone do czas zegarowy przez cały ten okres czasu, jako Jeśli jest na żywo dane wejściowe, źródła danych. Na przykład jeśli masz 3-dniowe okno w zapytaniu, wysyłanie zdarzeń do Centrum zdarzeń dla trzech dni i kontynuować wysyłanie zdarzeń. 

2. Uruchom zadania za pomocą polecenia **teraz** jako czas rozpoczęcia. 

3. Służy do pomiaru czasu między czasem rozpoczęcia i kiedy jest generowane wyjście pierwszego. Czas jest nierównej ile opóźnienie zadania, zostałyby naliczone podczas uaktualniania usługi.

4. Jeśli opóźnienie jest zbyt długa, spróbuj partycje zadania i zwiększyć liczbę SUs, dzięki czemu obciążenia są rozmieszczone na większą liczbę węzłów. Alternatywnie Rozważ zmniejszenie rozmiarów okna w zapytaniu i wykonać dalszą część agregacji lub innych stanowe, przetwarzanie danych wyjściowych wytworzonych przez zadania usługi Stream Analytics w podrzędnym ujściu (na przykład przy użyciu usługi Azure SQL database).

Usługi ogólne zaniepokoić stabilność podczas uaktualniania misji krytyczne zadania, należy wziąć pod uwagę uruchomionych zadań zduplikowane w sparowanych regionach platformy Azure. Aby uzyskać więcej informacji, zobacz [gwarancji Stream Analytics zadania niezawodność podczas aktualizacji usługi](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Zainicjowano zadania odzyskiwania przez użytkownika, Zatrzymaj i uruchom
Aby edytować składnia zapytania w zadaniu przesyłania strumieniowego lub dostosować dane wejściowe i wyjściowe, zadanie musi zostać zatrzymana, aby wprowadzić zmiany i uaktualnić projekt zadania. W takich scenariuszach gdy użytkownik Zatrzymuje zadanie przesyłania strumieniowego i uruchamia go ponownie scenariusza odzyskiwania jest podobny do uaktualniania usługi. 

Dane punktu kontrolnego nie można użyć do ponownego uruchomienia zadania inicjowane przez użytkownika. Aby oszacować opóźnienie danych wyjściowych, podczas ponownego uruchamiania, użyj tej samej procedury, zgodnie z opisem w poprzedniej sekcji i stosowane podobne środki zaradcze w przypadku, gdy opóźnienie jest za długa.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat niezawodności i skalowalności zobacz następujące artykuły:
- [Samouczek: Konfigurowanie alertów dotyczących zadań usługi Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Skalować zadania usługi Azure Stream Analytics, w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)
- [Zagwarantowania niezawodności zadania usługi Stream Analytics podczas aktualizacji usługi](stream-analytics-job-reliability.md)
