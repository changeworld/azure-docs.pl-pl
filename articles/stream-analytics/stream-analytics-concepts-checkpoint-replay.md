---
title: Punkt kontrolny i powtórzeń zadania odzyskiwania pojęcia dotyczące usługi Azure Stream Analytics
description: W tym artykule opisano punktu kontrolnego i powtórzeń zadania odzyskiwania pojęcia dotyczące usługi Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 1a7cb6c5d9c3383b127ce38ae21bb2dc811e1f2e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31529485"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Pojęcia dotyczące punktu kontrolnego i powtórzeń zadania usługi analiza strumienia Azure
W tym artykule opisano wewnętrznego punktu kontrolnego i powtarzania pojęcia związane z usługi Azure Stream Analytics i wpływu, że te na zadania odzyskiwania. Zawsze uruchamia zadanie usługi Stream Analytics, informacje o stanie jest obsługiwany wewnętrznie. Czy informacje o stanie jest zapisywany w punkt kontrolny okresowo. W niektórych scenariuszach zostanie użyty dla zadania odzyskiwania informacji punktu kontrolnego, jeśli zadania przed wystąpieniem awarii lub uaktualnienia. W innych warunkach punktu kontrolnego nie może zostać użyty do odzyskiwania, a powtórzeń jest konieczne.

## <a name="stateful-query-logic-in-temporal-elements"></a>Logika stanowe zapytania w elementach danych czasowych
Jednym z unikatowych możliwości zadania usługi analiza strumienia Azure jest przetwarzania stanowe, takie jak agregacjami, sprzężenia danych czasowych i funkcje analityczne danych czasowych. Każdy z tych operatorów przechowuje informacje o stanie po uruchomieniu zadania. Maksymalny rozmiar okna dla tych elementów zapytania wynosi siedem dni. 

Pojęcie okno danych czasowych pojawia się w kilku elementach zapytań usługi Stream Analytics:
1. Agregacjami (grupy przez z wirowania, skaczące i przedłużanie systemu windows)

2. Sprzężenia danych czasowych (połączenie z DATEDIFF)

3. Funkcje analityczne danych czasowych (ISFIRST, LAST i opóźnienie z LIMIT czasu trwania)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Zadania odzyskiwania po awarii węzła, w tym uaktualnienia systemu operacyjnego
Każdym uruchomieniu zadania usługi analiza strumienia wewnętrznie on jest skalowana w poziomie do pracy na wielu węzłach procesu roboczego. Stan każdego węzła procesu roboczego jest sprawdzany za co kilka minut, co pomaga systemu, odzyskiwania, jeśli wystąpi błąd.

W czasie węzłem danego procesu roboczego może zakończyć się niepowodzeniem lub uaktualnienia systemu operacyjnego może wystąpić dla tego węzła procesu roboczego. Aby odzyskać automatycznie, Stream Analytics nabędzie nowy węzeł dobrej kondycji i z najnowszy dostępny punkt kontrolny jest przywracany stan węzła poprzedniego procesu roboczego. Aby wznowić pracę, niezbędne do przywrócenia stanu od momentu, gdy punkt kontrolny jest tworzony jest mała ilość powtórzeń. Zazwyczaj przerwa przywracania jest tylko kilka minut. Po wybraniu wystarczającej liczby jednostek przesyłania strumieniowego dla zadania, powtarzania powinna zostać ukończona szybko. 

W pełni równoległe zapytania czas, jaki zajmuje nadążyć po awarii węzła procesu roboczego jest proporcjonalny do:

[częstotliwość zdarzenia wejściowego] x [długość luki] / [liczba przetwarzania partycji]

Jeśli kiedykolwiek obserwować przetwarzania znaczne opóźnienia z powodu awarii węzła i uaktualnienia systemu operacyjnego należy rozważyć zmianę zapytania w pełni równoległe i skalowanie zadania można przydzielić więcej jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [skalować zadanie usługi analiza strumienia Azure w celu zwiększenia przepływności](stream-analytics-scale-jobs.md).

Bieżący Stream Analytics nie są wyświetlane raportu podczas odbywa się tego rodzaju procesu odzyskiwania.

## <a name="job-recovery-from-a-service-upgrade"></a>Zadanie odzyskiwania z uaktualnienia usługi 
Microsoft od czasu do czasu uaktualnia pliki binarne uruchamiane zadania usługi analiza strumienia w usłudze Azure. W tych godzinach użytkowników uruchomionych zadań uaktualnienia do nowszej wersji i automatycznie uruchamia ponownie zadanie. 

Obecnie format punktu kontrolnego odzyskiwania nie są zachowywane między uaktualnienia. W związku z tym stan zapytania przesyłania strumieniowego musi zostać przywrócona całkowicie przy użyciu techniki powtarzania. Aby umożliwić zadania usługi analiza strumienia powtarzania dokładnie takie same dane wejściowe przed ważne jest, aby ustawić zasady przechowywania dla źródła danych do co najmniej okna rozmiary w zapytaniu. Przeciwnym razie może spowodować nieprawidłowe lub częściowe wyniki podczas uaktualniania usługi, ponieważ źródło danych nie mogą być przechowywane daleko wstecz do uwzględnienia rozmiar okna pełna.

Ogólnie rzecz biorąc ilość powtórzeń potrzebne jest proporcjonalny do rozmiaru okna pomnożona przez średni czas występowania zdarzeń. Na przykład dla zadania szybkość wejściowych wynosi 1000 zdarzeń na sekundę rozmiar okna jest większa niż jedna godzina została uznana za rozmiar dużych powtarzania. Dla zapytań o rozmiarze dużych polegającym na odtwarzaniu można zaobserwować opóźnione danych wyjściowych (żadne dane wyjściowe) niektórych dłuższy okres. 

## <a name="estimate-replay-catch-up-time"></a>Czas wyrównującej szacowania powtórzeń
Aby oszacować długość opóźnienia z powodu uaktualnienia usługi, można wykonać tej metody:

1. Załaduj wejściowych Centrum zdarzeń z wystarczającą ilość danych, aby pokrywał największy rozmiar okna w kwerendzie szybkością oczekiwanego zdarzenia. Zdarzenia sygnatura czasowa powinna mieć bliski czas zegarowy całego okresu czasu, jako Jeśli jest wprowadzania na żywo, źródła danych. Na przykład jeśli masz okna 3 dni w kwerendzie wysyłania zdarzeń do Centrum zdarzeń trzy dni i kontynuować wysyłanie zdarzeń. 

2. Uruchom zadania za pomocą polecenia **teraz** jako czas rozpoczęcia. 

3. Zmierz czas między godziną uruchomienia a wygenerowano pierwsze dane wyjściowe. Czas jest nierównej ile opóźnienie zadania może pociągnąć za sobą podczas uaktualniania usługi.

4. Jeśli opóźnienie jest za długa, spróbuj zadania partycji i zwiększyć liczbę SUs, więc obciążenia są rozmieszczone na więcej węzłów. Ewentualnie Rozważ zmniejszenie rozmiar okna w zapytaniu i wykonać dalsze agregacji lub innych stateful przetwarzania na dane wyjściowe, generowane przez zadanie usługi analiza strumienia w podrzędnym zbiornika (na przykład przy użyciu bazy danych Azure SQL).

Usługi ogólne stabilności problem podczas uaktualniania misji krytyczne zadania, rozważ uruchomionych zadań zduplikowane w parach regiony platformy Azure. Aby uzyskać więcej informacji, zobacz [gwarancji Stream Analytics zadania niezawodności podczas aktualizacji usługi](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Zadanie odzyskiwania z zatrzymania zainicjowanej przez użytkownika i rozpocząć
Aby edytować składni zapytania w zadaniu przesyłania strumieniowego lub dopasować wejściach i wyjściach, zadanie wymaga zatrzymanie zmian i uaktualniania projektu zadania. W takich sytuacjach gdy użytkownik Zatrzymuje zadanie przesyłania strumieniowego i uruchamia go ponownie, scenariuszu odzyskiwania jest podobny do uaktualniania usługi. 

Dane punktu kontrolnego nie może służyć do ponownego uruchomienia zadania inicjowane przez użytkownika. Aby oszacować opóźnienie danych wyjściowych podczas ponownego uruchamiania, użyj tej samej procedury, zgodnie z opisem w poprzedniej sekcji, a zastosować podobne środki zaradcze, jeśli opóźnienie jest zbyt długa.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na skalowalność i niezawodność zobacz następujące artykuły:
- [Samouczek: Ustawianie alertów dla zadania usługi analiza strumienia Azure](stream-analytics-set-up-alerts.md)
- [Zadanie usługi analiza strumienia Azure w celu zwiększenia przepływności skalowania](stream-analytics-scale-jobs.md)
- [Zagwarantować niezawodność zadania usługi analiza strumienia podczas aktualizacji usługi](stream-analytics-job-reliability.md)
