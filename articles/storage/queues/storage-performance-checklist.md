---
title: Lista kontrolna wydajności i skalowalności magazynu kolejek — usługa Azure Storage
description: Lista kontrolna sprawdzonych praktyk do użycia z magazynem kolejek w opracowywaniu aplikacji o wysokiej wydajności.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75750496"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Lista kontrolna wydajności i skalowalności magazynu kolejek

Firma Microsoft opracowała szereg sprawdzonych rozwiązań dotyczących tworzenia aplikacji o wysokiej wydajności z magazynem kolejek. Ta lista kontrolna identyfikuje kluczowe praktyki, które deweloperzy mogą stosować w celu optymalizacji wydajności. Należy pamiętać o tych praktykach podczas projektowania aplikacji i w trakcie całego procesu.

Usługa Azure Storage ma cele dotyczące skalowalności i wydajności dla pojemności, szybkości transakcji i przepustowości. Aby uzyskać więcej informacji na temat celów skalowalności usługi Azure Storage, zobacz [Cele skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) oraz cele [skalowalności i wydajności magazynu kolejek](scalability-targets.md).

## <a name="checklist"></a>Lista kontrolna

W tym artykule organizuje sprawdzone praktyki dotyczące wydajności do listy kontrolnej, którą można wykonać podczas tworzenia aplikacji magazynu kolejki.

| Gotowe | Kategoria | Zagadnienie dotyczące projektu |
| --- | --- | --- |
| &nbsp; |Cele skalowalności |[Czy można zaprojektować aplikację do używania nie więcej niż maksymalnej liczby kont magazynu?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cele skalowalności |[Czy unikasz zbliżania się do limitów pojemności i transakcji?](#capacity-and-transaction-targets) |
| &nbsp; |Obsługa sieci |[Czy urządzenia po stronie klienta mają wystarczająco wysoką przepustowość i małe opóźnienia, aby osiągnąć wymaganą wydajność?](#throughput) |
| &nbsp; |Obsługa sieci |[Czy urządzenia po stronie klienta mają wysokiej jakości łącze sieciowe?](#link-quality) |
| &nbsp; |Obsługa sieci |[Czy aplikacja kliencka znajduje się w tym samym regionie co konto magazynu?](#location) |
| &nbsp; |Bezpośredni dostęp do klienta |[Czy używasz sygnatur dostępu współdzielonego (SAS) i współużytkowania zasobów między źródłami (CORS), aby umożliwić bezpośredni dostęp do usługi Azure Storage?](#sas-and-cors) |
| &nbsp; |Konfiguracja .NET |[Czy używasz programu .NET Core 2.1 lub nowszego w celu uzyskania optymalnej wydajności?](#use-net-core) |
| &nbsp; |Konfiguracja .NET |[Czy klient skonfigurował do używania wystarczającej liczby równoczesnych połączeń?](#increase-default-connection-limit) |
| &nbsp; |Konfiguracja .NET |[Czy w przypadku aplikacji platformy .NET skonfigurowano program .NET do używania wystarczającej liczby wątków?](#increase-minimum-number-of-threads) |
| &nbsp; |Równoległości prostych |[Czy upewnij się, że równoległość jest ograniczona odpowiednio, tak aby nie przeciążać możliwości klienta lub podejście do celów skalowalności?](#unbounded-parallelism) |
| &nbsp; |Narzędzia |[Czy korzystasz z najnowszych wersji bibliotek i narzędzi klienckich dostarczonych przez firmę Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Ponowne próby |[Czy używasz zasad ponawiania próby z wykładniczym wycofywania dla błędów ograniczania przepustowości i limitów czasu?](#timeout-and-server-busy-errors) |
| &nbsp; |Ponowne próby |[Czy aplikacja unika ponownych prób dla błędów, których nie można ponowić?](#non-retryable-errors) |
| &nbsp; |Konfigurowanie |[Czy wyłączyłeś algorytm Nagle'a, aby poprawić wydajność małych żądań?](#disable-nagle) |
| &nbsp; |Rozmiar komunikatu |[Czy wiadomości są kompaktowe, aby poprawić wydajność kolejki?](#message-size) |
| &nbsp; |Pobieranie zbiorcze |[Czy pobierasz wiele wiadomości w jednej operacji GET?](#batch-retrieval) |
| &nbsp; |Częstotliwość sondowania |[Czy sondowanie wystarczająco często, aby zmniejszyć postrzegane opóźnienie aplikacji?](#queue-polling-interval) |
| &nbsp; |Aktualizuj wiadomość |[Czy używasz update message operacji do przechowywania postępu w przetwarzaniu wiadomości, dzięki czemu można uniknąć konieczności ponownego przetwarzania całego komunikatu, jeśli wystąpi błąd?](#use-update-message) |
| &nbsp; |Architektura |[Czy używasz kolejek, aby cała aplikacja była bardziej skalowalna, utrzymując długotrwałe obciążenia poza ścieżką krytyczną i skalować, a następnie niezależnie?](#application-architecture) |

## <a name="scalability-targets"></a>Cele skalowalności

Jeśli aplikacja zbliża się lub przekracza dowolny z celów skalowalności, może wystąpić zwiększone opóźnienia transakcji lub ograniczania przepustowości. Gdy usługa Azure Storage ogranicza aplikację, usługa zaczyna zwracać kody błędów 503 (serwer zajęty) lub 500 (limit czasu operacji). Unikanie tych błędów przez pozostawanie w granicach docelowych skalowalności jest ważną częścią zwiększenia wydajności aplikacji.

Aby uzyskać więcej informacji na temat celów skalowalności usługi Kolejka, zobacz [Cele skalowalności i wydajności usługi Azure Storage](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maksymalna liczba kont magazynu

Jeśli zbliżasz się do maksymalnej liczby kont magazynu dozwolonych dla określonej kombinacji subskrypcji/regionu, czy używasz wielu kont magazynu do niezależnego fragmentu w celu zwiększenia ruchu przychodzącego, ruchu wychodzącego, operacji we/wy na sekundę (IOPS) lub pojemności? W tym scenariuszu firma Microsoft zaleca korzystanie ze zwiększonych limitów dla kont magazynu, aby zmniejszyć liczbę kont magazynu wymaganych dla obciążenia, jeśli to możliwe. Skontaktuj się z [pomocą techniczną platformy Azure,](https://azure.microsoft.com/support/options/) aby zażądać zwiększenia limitów dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Ogłaszanie większych, bardziej zakrojonych kont magazynu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Cele dotyczące zdolności produkcyjnych i transakcji

Jeśli aplikacja zbliża się do celów skalowalności dla jednego konta magazynu, należy rozważyć przyjęcie jednego z następujących metod:  

- Jeśli cele skalowalności dla kolejek są niewystarczające dla aplikacji, należy użyć wielu kolejek i rozpowszechniać wiadomości między nimi.
- Ponowne rozważenie obciążenia, które powoduje, że aplikacja zbliża się lub przekracza cel skalowalności. Czy można zaprojektować go inaczej, aby zużywać mniej przepustowości lub pojemności lub mniej transakcji?
- Jeśli aplikacja musi przekraczać jeden z celów skalowalności, a następnie utworzyć wiele kont magazynu i partycjonować dane aplikacji na tych wielu kont magazynu. Jeśli używasz tego wzorca, a następnie należy zaprojektować aplikację, dzięki czemu można dodać więcej kont magazynu w przyszłości do równoważenia obciążenia. Same konta magazynowe nie mają żadnych kosztów innych niż użycie danych przechowywanych, dokonanych transakcji lub przesyłanych danych.
- Jeśli aplikacja zbliża się do docelowych przepustowości, należy wziąć pod uwagę kompresji danych po stronie klienta, aby zmniejszyć przepustowość wymaganą do wysyłania danych do usługi Azure Storage.
    Kompresja danych może oszczędzać przepustowość i poprawiać wydajność sieci, ale może mieć również negatywny wpływ na wydajność. Oceń wpływ dodatkowych wymagań dotyczących przetwarzania dodatkowych danych dotyczących kompresji i dekompresji danych po stronie klienta. Należy pamiętać, że przechowywanie skompresowanych danych może utrudnić rozwiązywanie problemów, ponieważ wyświetlanie danych przy użyciu standardowych narzędzi może być trudniejsze.
- Jeśli aplikacja zbliża się do celów skalowalności, upewnij się, że używasz wykładniczego wycofywania ponownych prób. Najlepiej jest unikać osiągnięcia docelowych skalowalności, implementując zalecenia opisane w tym artykule. Jednak przy użyciu wykładniczego wycofywania dla ponownych prób uniemożliwi aplikacji ponawianie próby szybko, co może pogorszyć ograniczanie przepustowości. Aby uzyskać więcej informacji, zobacz sekcję ["Timeout and Server Busy errors](#timeout-and-server-busy-errors)"

## <a name="networking"></a>Obsługa sieci

Ograniczenia sieci fizycznej aplikacji mogą mieć znaczący wpływ na wydajność. W poniższych sekcjach opisano niektóre ograniczenia, które użytkownicy mogą napotkać.  

### <a name="client-network-capability"></a>Możliwości sieci klienta

Przepustowość i jakość łącza sieciowego odgrywają ważną rolę w wydajności aplikacji, zgodnie z opisem w poniższych sekcjach.

#### <a name="throughput"></a>Przepływność

W przypadku przepustowości problemem często są możliwości klienta. Większe wystąpienia platformy Azure mają karty sieciowe o większej pojemności, więc należy rozważyć użycie większego wystąpienia lub większej liczby maszyn wirtualnych, jeśli potrzebujesz wyższych limitów sieciowych z jednego komputera. Jeśli uzyskujesz dostęp do usługi Azure Storage z aplikacji lokalnej, stosuje się tę samą regułę: poznaj możliwości sieciowe urządzenia klienckiego i łączność sieciową z lokalizacją usługi Azure Storage i ulepsz je w razie potrzeby lub zaprojektuj aplikacji do pracy w ramach swoich możliwości.

#### <a name="link-quality"></a>Jakość łącza

Podobnie jak w przypadku każdego użycia sieci, należy pamiętać, że warunki sieciowe powodujące błędy i utratę pakietów spowolnią efektywną przepływność.  Korzystanie z WireShark lub NetMon może pomóc w diagnozowaniu tego problemu.  

### <a name="location"></a>Lokalizacja

W każdym środowisku rozproszonym umieszczenie klienta w pobliżu serwera zapewnia najlepszą wydajność. Aby uzyskać dostęp do usługi Azure Storage z najniższym opóźnieniem, najlepsza lokalizacja dla klienta znajduje się w tym samym regionie platformy Azure. Na przykład jeśli masz aplikację sieci web platformy Azure, która korzysta z usługi Azure Storage, a następnie zlokalizować je zarówno w jednym regionie, takich jak US West lub Azji Południowo-Wschodniej. Współlokowanie zasobów zmniejsza opóźnienie i koszty, ponieważ użycie przepustowości w jednym regionie jest bezpłatne.  

Jeśli aplikacje klienckie będą uzyskiwać dostęp do usługi Azure Storage, ale nie są hostowane na platformie Azure, takiej jak aplikacje urządzeń przenośnych lub lokalne usługi dla przedsiębiorstw, lokalizowanie konta magazynu w regionie w pobliżu tych klientów może zmniejszyć opóźnienia. Jeśli twoi klienci są szeroko rozpowszechnione (na przykład niektóre w Ameryce Północnej, a niektóre w Europie), a następnie należy rozważyć użycie jednego konta magazynu na region. Takie podejście jest łatwiejsze do zaimplementowania, jeśli dane przechowywane przez aplikację są specyficzne dla poszczególnych użytkowników i nie wymagają replikowania danych między kontami magazynu.

## <a name="sas-and-cors"></a>SAS i CORS

Załóżmy, że musisz autoryzować kod, taki jak JavaScript, który jest uruchomiony w przeglądarce sieci Web użytkownika lub w aplikacji na telefon komórkowy, aby uzyskać dostęp do danych w usłudze Azure Storage. Jednym z podejść jest tworzenie aplikacji usługi, która działa jako serwer proxy. Urządzenie użytkownika uwierzytelnia się za pomocą usługi, która z kolei autoryzuje dostęp do zasobów usługi Azure Storage. W ten sposób można uniknąć ujawniania kluczy konta magazynu na niezabezpieczonych urządzeniach. Jednak to podejście nakłada znaczne obciążenie na aplikację usługi, ponieważ wszystkie dane przesyłane między urządzeniem użytkownika i usługi Azure Storage musi przejść przez aplikację usługi.

Można uniknąć przy użyciu aplikacji usługi jako serwera proxy dla usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SYGNAŁÓW DOSTĘPU WSPÓŁDZIELONEGO). Korzystając z usługi SAS, można włączyć urządzenia użytkownika do żądania bezpośrednio do usługi Azure Storage przy użyciu tokenu ograniczonego dostępu. Na przykład jeśli użytkownik chce przekazać zdjęcie do aplikacji, aplikacja usługi może wygenerować sygnatury dostępu Współdzielonego i wysłać go do urządzenia użytkownika. Token sygnatury dostępu Współdzielonego może udzielić uprawnień do zapisu w zasobie usługi Azure Storage przez określony interwał czasu, po którym token sygnatury dostępu Współdzielonego wygasa. Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../common/storage-sas-overview.md)  

Zazwyczaj przeglądarka internetowa nie zezwala javascript na stronie, która jest obsługiwana przez witrynę sieci Web w jednej domenie do wykonywania niektórych operacji, takich jak operacje zapisu, do innej domeny. Zasady te, znane jako zasady tego samego źródła, uniemożliwiają złośliwemu skryptowi na jednej stronie uzyskanie dostępu do danych na innej stronie sieci Web. Jednak zasady tego samego źródła może być ograniczenie podczas tworzenia rozwiązania w chmurze. Udostępnianie zasobów między źródłami (CORS) to funkcja przeglądarki, która umożliwia domenie docelowej komunikowanie się z przeglądarką, w którym ufa żądań pochodzących z domeny źródłowej.

Załóżmy na przykład, że aplikacja sieci web uruchomiona na platformie Azure wywołuje żądanie zasobu do konta usługi Azure Storage. Aplikacja sieci web jest domeną źródłową, a konto magazynu jest domeną docelową. Usługę CORS można skonfigurować dla dowolnej usługi usługi Azure Storage do komunikowania się z przeglądarką sieci Web, zgodnie z którą żądania z domeny źródłowej są zaufane przez usługę Azure Storage. Aby uzyskać więcej informacji na temat usługi CORS, zobacz [obsługa udostępniania zasobów między źródłami (CORS) dla usługi Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Zarówno SAS, jak i CORS mogą pomóc uniknąć niepotrzebnego obciążenia aplikacji sieci web.  

## <a name="net-configuration"></a>Konfiguracja .NET

Jeśli używasz programu .NET Framework, w tej sekcji wymieniono kilka szybkich ustawień konfiguracji, których można użyć do znacznych ulepszeń wydajności.  Jeśli używasz innych języków, sprawdź, czy podobne pojęcia mają zastosowanie w wybranym języku.  

### <a name="use-net-core"></a>Użyj programu .NET Core

Tworzenie aplikacji usługi Azure Storage za pomocą platformy .NET Core 2.1 lub nowszej w celu skorzystania z ulepszeń wydajności. W miarę możliwości zalecane jest użycie programu .NET Core 3.x.

Aby uzyskać więcej informacji na temat poprawy wydajności w programie .NET Core, zobacz następujące wpisy w blogu:

- [Poprawa wydajności w programie .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Poprawa wydajności w programie .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zwiększ domyślny limit połączeń

W .NET następujący kod zwiększa domyślny limit połączenia (który jest zwykle 2 w środowisku klienckim lub 10 w środowisku serwera) do 100. Zazwyczaj należy ustawić wartość w przybliżeniu liczbę wątków używanych przez aplikację.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Przed otwarciem jakichkolwiek połączeń ustaw limit połączenia.  

W przypadku innych języków programowania zobacz dokumentację tego języka, aby określić sposób ustawiania limitu połączeń.  

Aby uzyskać więcej informacji, zobacz wpis w blogu [Usługi sieci Web: Połączenia współbieżne](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Zwiększenie minimalnej liczby wątków

Jeśli używasz wywołań synchronicznych wraz z zadaniami asynchronialnymi, można zwiększyć liczbę wątków w puli wątków:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Aby uzyskać więcej informacji, zobacz [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) metody.  

## <a name="unbounded-parallelism"></a>Niezwiązany równoległość

Podczas równoległości może być wielki dla wydajności, należy uważać na przy użyciu równoległości nieograniczony, co oznacza, że nie ma limitu wymuszane na liczbę wątków lub żądań równoległych. Należy ograniczyć równoległe żądania przekazywania lub pobierania danych, aby uzyskać dostęp do wielu partycji na tym samym koncie magazynu lub uzyskać dostęp do wielu elementów w tej samej partycji. Jeśli równoległość jest nieograniczona, aplikacja może przekroczyć możliwości urządzenia klienckiego lub cele skalowalności konta magazynu, co powoduje dłuższe opóźnienia i ograniczanie przepustowości.  

## <a name="client-libraries-and-tools"></a>Biblioteki i narzędzia klienckie

Aby uzyskać najlepszą wydajność, należy zawsze używać najnowszych bibliotek klienckich i narzędzi dostarczonych przez firmę Microsoft. Biblioteki klientów usługi Azure Storage są dostępne dla różnych języków. Usługa Azure Storage obsługuje również usługi PowerShell i interfejsu wiersza polecenia platformy Azure. Firma Microsoft aktywnie opracowuje te biblioteki i narzędzia klienckie z myślą o wydajności, aktualizuje je z najnowszymi wersjami usług i zapewnia, że wewnętrznie obsługują wiele sprawdzonych praktyk wydajności. Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną usługi Azure Storage](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Obsługa błędów usługi

Usługa Azure Storage zwraca błąd, gdy usługa nie może przetworzyć żądania. Zrozumienie błędów, które mogą być zwracane przez usługę Azure Storage w danym scenariuszu jest przydatne do optymalizacji wydajności.

### <a name="timeout-and-server-busy-errors"></a>Błędy limitu czasu i zajętości serwera

Usługa Azure Storage może ograniczyć aplikację, jeśli zbliży się do limitów skalowalności. W niektórych przypadkach usługa Azure Storage może nie być w stanie obsłużyć żądania z powodu niektórych stanów przejściowych. W obu przypadkach usługa może zwrócić błąd 503 (Zajęty serwerem) lub 500 (Limit czasu). Błędy te mogą również wystąpić, jeśli usługa jest równoważenie partycji danych, aby umożliwić wyższą przepływność. Aplikacja kliencka zazwyczaj należy ponowić próbę operacji, która powoduje jeden z tych błędów. Jednak jeśli usługa Azure Storage jest ograniczanie aplikacji, ponieważ przekracza cele skalowalności lub nawet jeśli usługa nie może obsługiwać żądania z innego powodu, agresywne ponownych prób może pogorszyć problem. Zaleca się użycie wykładniczej zasady ponawiania prób, a biblioteki klienckie domyślnie to zachowanie. Na przykład aplikacja może ponowić próbę po 2 sekundach, następnie 4 sekundy, następnie 10 sekund, a następnie 30 sekund, a następnie całkowicie zrezygnować. W ten sposób aplikacja znacznie zmniejsza jego obciążenie usługi, a nie zaostrzanie zachowanie, które może prowadzić do ograniczania przepustowości.  

Błędy łączności można ponowić natychmiast, ponieważ nie są one wynikiem ograniczania przepustowości i oczekuje się, że są przejściowe.  

### <a name="non-retryable-errors"></a>Błędy, które nie można ponowić

Biblioteki klienta obsługują ponownych prób ze świadomością, które błędy mogą być ponowione, a które nie. Jednak jeśli wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, istnieją pewne błędy, które nie należy ponowić próbę. Na przykład błąd 400 (Złe żądanie) wskazuje, że aplikacja kliencka wysłała żądanie, które nie mogło zostać przetworzone, ponieważ nie było w oczekiwanej formie. Ponowne wysłanie tego żądania powoduje taką samą odpowiedź za każdym razem, więc nie ma sensu ponawiać jej próby. Jeśli wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, należy pamiętać o potencjalnych błędów i czy powinny one być ponowione.

Aby uzyskać więcej informacji na temat kodów błędów usługi Azure Storage, zobacz [Stan i kody błędów](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagle"></a>Wyłącz Nagle

Algorytm Nagle'a jest szeroko wdrażany w sieciach TCP/IP jako środek poprawy wydajności sieci. Jednak nie jest optymalny we wszystkich okolicznościach (takich jak środowiska wysoce interaktywne). Algorytm Nagle'ego ma negatywny wpływ na wydajność żądań do usługi Azure Table i należy ją wyłączyć, jeśli to możliwe.

## <a name="message-size"></a>Rozmiar komunikatu

Wydajność kolejki i skalowalność zmniejszają się wraz ze wzrostem rozmiaru wiadomości. Umieść w wiadomości tylko informacje potrzebne odbiorcy.  

## <a name="batch-retrieval"></a>Pobieranie partii

Można pobrać maksymalnie 32 wiadomości z kolejki w jednej operacji. Pobieranie wsadowe można zmniejszyć liczbę w obie strony z aplikacji klienckiej, co jest szczególnie przydatne w środowiskach, takich jak urządzenia przenośne, z dużym opóźnieniem.  

## <a name="queue-polling-interval"></a>Interwał sondowania kolejki

Większość aplikacji sondowania wiadomości z kolejki, która może być jednym z największych źródeł transakcji dla tej aplikacji. Wybierz interwał sondowania mądrze: sondowanie zbyt często może spowodować, że aplikacja zbliży się do celów skalowalności dla kolejki. Jednak przy 200 000 transakcji za 0,01 USD (w momencie pisania) ankietowanie pojedynczego procesora raz na sekundę przez miesiąc będzie kosztować mniej niż 15 centów, więc koszt nie jest zazwyczaj czynnikiem wpływającym na wybór interwału sondowania.  

Aby uzyskać aktualne informacje o kosztach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Użyj wiadomości aktualizującej

Można użyć **operacji Aktualizuj wiadomość,** aby zwiększyć limit czasu niewidzialności lub zaktualizować informacje o stanie wiadomości. Za pomocą **wiadomości aktualizacji** może być bardziej wydajne podejście niż o przepływ pracy, który przekazuje zadanie z jednej kolejki do następnej, jak każdy krok zadania jest zakończona. Aplikacja może zapisać stan zadania w wiadomości, a następnie kontynuować pracę, zamiast ponownie wysuwać wiadomość do następnego kroku zadania za każdym razem, gdy krok zostanie ukończony. Należy pamiętać, że każda operacja **wiadomości aktualizacji** liczy się do celu skalowalności.

## <a name="application-architecture"></a>Architektura aplikacji

Użyj kolejek, aby architektura aplikacji była skalowalna. Poniżej wymieniono kilka sposobów użycia kolejek w celu zwiększenia skalowalności aplikacji:  

- Kolejki służy do tworzenia zaległości pracy do przetwarzania i wygładzić obciążeń w aplikacji. Na przykład można kolejkować żądania od użytkowników do wykonywania pracy intensywnie korzystającej z procesora, takich jak zmiana rozmiaru przekazanych obrazów.
- Kolejki służy do oddzielenia części aplikacji, dzięki czemu można skalować je niezależnie. Na przykład frontołów sieci web może umieścić wyniki ankiety od użytkowników w kolejce do późniejszej analizy i przechowywania. Można dodać więcej wystąpień roli procesu roboczego do przetwarzania danych kolejki zgodnie z wymaganiami.  

## <a name="next-steps"></a>Następne kroki

- [Cele dotyczące skalowalności i wydajności magazynu kolejek](scalability-targets.md)
- [Cele dotyczące skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Kody stanu i błędów](/rest/api/storageservices/Status-and-Error-Codes2)
