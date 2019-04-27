---
title: Omówienie modelu programowania usługi Reliable Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o modelu programowania usługi Reliable Service usługi Service Fabric i rozpocząć pisanie własnych usług.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 1789c7489e58df09dccfde3e7ab106ef54b5c1ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727016"
---
# <a name="reliable-services-overview"></a>Omówienie usług Reliable Services
Usługa Azure Service Fabric upraszcza zapisywanie i zarządzanie nimi bezstanowych i stanowych usług Reliable Services. W tym temacie omówiono:

* Model programowania usług Reliable Services usługi stanowe i bezstanowe.
* Opcje, które należy podjąć podczas zapisywania niezawodnej usługi.
* Niektóre scenariusze i przykłady oraz kiedy należy używać usług Reliable Services i jak są one napisane.

Usług Reliable Services jest jednym z modeli programowania, która jest dostępna w usłudze Service Fabric. Drugi to modelu programowania Reliable Actors, który oferuje wirtualny aktora model programowania na podstawie modelu usług Reliable Services. Aby uzyskać więcej informacji o modelu programowania Reliable Actors, zobacz [wprowadzenie do usługi Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Usługa Service Fabric zarządza czasem istnienia usług, od zainicjowania obsługi i wdrażania, uaktualniania i usuwania, za pośrednictwem [zarządzania aplikacjami usługi Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Co to są usługi Reliable Services?
Usług Reliable Services zapewnia prosty, zaawansowany, najwyższego poziomu modelu programowania, aby pomóc lepiej wyrazić, co to jest ważna dla aplikacji. Przy użyciu usług Reliable Services modelu programowania zapewnia następujące korzyści:

* Dostęp do pozostałej części programowania interfejsów API usługi Service Fabric. W przeciwieństwie do usług w usłudze Service Fabric formę [pliki wykonywalne gościa](service-fabric-guest-executables-introduction.md), usług Reliable Services nawiązać połączenia z bezpośrednio korzystać z pozostałej części interfejsów API usługi Service Fabric. Dzięki temu usługi, aby:
  * kwerendy systemie
  * Raport kondycji dotyczące jednostki w klastrze
  * Otrzymywanie powiadomień o zmianach dotyczących konfiguracji i kodu
  * Wyszukiwanie i komunikowanie się z innymi usługami
  * (opcjonalnie) użyj [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * .. .i, dając im dostęp do wielu innych możliwości wszystkie z pierwszej klasy modelu programowania w kilku językach programowania.
* Prostym modelem uruchamianie własnego kodu, który wygląda jak modeli, które są używane do programowania. Twój kod ma punktu wejścia dobrze zdefiniowany i łatwe w zarządzaniu cyklem życia.
* Model podłączanych komunikacji. Użyj transportu wybranych przez użytkownika, takich jak HTTP za pomocą [interfejsu API sieci Web](service-fabric-reliable-services-communication-webapi.md), funkcja WebSockets, protokoły TCP niestandardowe lub cokolwiek innego. Usług Reliable Services zapewniają kilka wspaniałych poza pole opcji można użyć lub możesz podać własne.
* Dla usług stanowych modelu programowania usług Reliable Services umożliwia spójne i niezawodne przechowywanie stanu użytkownika bezpośrednio w usłudze przy użyciu [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Niezawodne kolekcje to prosty zestaw wysoce dostępnych i niezawodnych klas kolekcji, które nie będą niczym nowym dla każdego, kto został użyty C# kolekcji. Tradycyjnie usługi potrzebne systemy zewnętrzne dla zarządzania stanem niezawodne. Z elementami Reliable Collections można przechowywać swój stan obok zasobów obliczeniowych, przy użyciu tego samego wysoka dostępność i niezawodność, których jesteś w dobrym można oczekiwać od o wysokiej dostępności, zewnętrzne magazyny. Ten model również zwiększa opóźnienia, ponieważ kolokowania moc obliczeniową i stanu, musi ona działać.

## <a name="what-makes-reliable-services-different"></a>Czym różni się usług Reliable Services?
Usług Reliable Services w usłudze Service Fabric różnią się od usług, które zostały napisane przed. Usługa Service Fabric udostępnia niezawodności, dostępności, spójności i skalowalności.

* **Niezawodność** — Twoja usługa pozostaje się nawet w zawodnych środowiskach, gdzie maszynach zakończyć się niepowodzeniem, lub kliknij przycisk problemy z siecią lub w przypadkach, gdy występują błędy i awarii lub się nie powieść w samych usług. W przypadku usług stanowych swój stan są zachowywane nawet w obecności sieci lub inne błędy.
* **Dostępność** — usługa jest osiągalny i działa prawidłowo. Usługi Service Fabric obsługuje odpowiednią liczbę uruchomione kopii.
* **Skalowalność** — usług są całkowicie niezależni od sprzętu, a ich może rosnąć lub maleć zgodnie z potrzebami poprzez dodawanie i usuwanie sprzętu lub innych zasobów. Usługi są łatwo podzielone na partycje (szczególnie w przypadku stanowego) aby upewnić się, że usługi można skalować i usuwanie częściowej awarii. Usługi mogą być tworzone i usuwane dynamicznie za pośrednictwem kodu, umożliwiając więcej wystąpień, aby być uruchamiane zgodnie z potrzebami, powiedz w odpowiedzi na żądania klientów. Na koniec usługi Service Fabric zachęca usług LDS. Usługa Service Fabric umożliwia tysięcy usług do aprowizowania w ramach pojedynczego procesu, a nie całego wystąpień systemu operacyjnego wymaganie lub przypisywanie lub procesów w ramach pojedynczego wystąpienia usługi.
* **Spójność** — wszystkie informacje przechowywane w tej usłudze można musi być zgodne. Ta zasada obowiązuje nawet w przypadku wielu elementów reliable collections w ramach usługi. We wszystkich kolekcjach w ramach usługi zmian w sposób transakcyjnie atomic.

## <a name="service-lifecycle"></a>Usługi cyklu życia
Usługi stanowe i bezstanowe, czy usług Reliable Services zapewniają prosty cyklu życia, który pozwala szybko podłączyć swój kod i rozpocząć pracę.  Istnieje tylko jeden lub dwa metody, które należy zaimplementować można pobrać usługi działanie.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** — ta metoda jest, gdzie usługa definiuje stack(s) komunikacji, który chce użyć. Stos komunikacji, takich jak [interfejsu API sieci Web](service-fabric-reliable-services-communication-webapi.md), definiuje punkcie końcowym nasłuchiwania lub punkty końcowe usługi (w jaki sposób klienci dotarcia do usługi). Definiuje również, jak komunikaty, które pojawiają się wchodzić w interakcje z pozostałą częścią kodu usługi.
* **RunAsync** — ta metoda jest gdzie usługa jest uruchomiona jej logiki biznesowej i gdzie czy uruchamiał dowolnego zadania w tle, które powinno być uruchamiane przez okres istnienia usługi. Token anulowania, który jest dostarczany jest sygnałem dla kiedy powinna zostać przerwana w tej pracy. Na przykład jeśli usługa wymaga ściągają komunikaty z niezawodna kolejka i przetwarzać je, to gdy tego praca będzie wykonywana.

Jeśli masz poznawania usług reliable services po raz pierwszy, przeczytaj na! Jeśli szukasz szczegółowy przewodnik dotyczący cyklu życia usług reliable Services możesz można przejść do [w tym artykule](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Przykład usługi
Ten model programowania, wiedząc, Weźmy rzut oka na dwóch różnych usług, aby zobaczyć, jak te elementy współdziałają ze sobą.

### <a name="stateless-reliable-services"></a>Bezstanowych usług Reliable Services
Usługa bezstanowa jest takie, gdzie występuje bez stanu przechowywanych w ramach usługi w wielu wywołań. Każdy stan, który jest obecny jest całkowicie jednorazowe i nie wymaga synchronizacji, replikacji, trwałości i wysokiej dostępności.

Rozważmy na przykład Kalkulator, Brak pamięci i odbiera wszystkie warunki i operacji do wykonania na raz.

W tym przypadku `RunAsync()` (C#) lub `runAsync()` (Java) usługi może być pusty, ponieważ nie ma żadnych zadań przetwarzania w tle, usługa potrzebuje do wykonywania. Po utworzeniu usługi Kalkulator, zwraca `ICommunicationListener` (C#) lub `CommunicationListener` (Java) (na przykład [interfejsu API sieci Web](service-fabric-reliable-services-communication-webapi.md)) które otwiera się w punkcie końcowym nasłuchiwania na porcie niektóre. Tym punkcie końcowym nasłuchiwania punkty zaczepienia różne metody obliczania (przykład: "Dodaj (n1, n2)"), zdefiniuj publiczny interfejs API kalkulatora.

Podczas rozmowy w kliencie odpowiednią metodę wywoływaną, a usługa Kalkulator wykonuje operacje na danych udostępnionych i zwraca wynik. Go nie przechowuje stan.

Nie są przechowywane stanów wewnętrznych sprawia, że ten Kalkulator przykładzie prostego. Jednak większość usług nie są naprawdę bezstanowe. Zamiast tego Zapisz ich stanu do niektórych innych magazynu. (Na przykład dowolnej aplikacji sieci web, która opiera się na przechowywanie stanu sesji w pamięci podręcznej lub magazyn zapasowy nie jest bezstanowa).

Typowym przykładem sposób bezstanowej usługi są używane w usłudze Service Fabric jest jako frontonu, który uwidacznia interfejs API publicznie dostępnych dla aplikacji sieci web. Usługa frontonu następnie rozmawia z usług stanowych na ukończenie żądania przez użytkownika. W takim przypadku wywołania od klientów są kierowane do znanego portu, np. 80, którym nasłuchuje usługa bezstanowa. Tę usługę bezstanową odbiera połączenie i określa, czy wywołanie pochodzi z zaufany i który do usługi jest przeznaczony dla.  Następnie przekazuje wywołanie do właściwej partycji usługi stanowej usługi bezstanowej i czeka na odpowiedź. Gdy usługi bezstanowej odbiera odpowiedź, w odpowiedzi oryginalny klient. Przykładem takiej usługi znajduje się w naszym próbnym [ C# ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). To tylko jeden przykład tego wzorca w próbkach, inne osoby w są także inne przykłady.

### <a name="stateful-reliable-services"></a>Stanowych usług Reliable Services
Usługa stanowa to taki, który musi mieć część stanu przechowywane spójne i znajdują się w kolejności dla usługi do funkcji. Należy wziąć pod uwagę to usługa, która stale oblicza średnią kroczącą niektóre wartości na podstawie aktualizacji, który odbiera. Aby to zrobić, musi on mieć bieżący zestaw żądań przychodzących, potrzebne do procesu, a bieżąca średnią. Wszystkie usługi, która pobiera, przetwarza i przechowuje informacje w magazynie zewnętrznych (takich jak Azure blob Storage czy table magazyn już dziś) jest stanowych. Przechowuje tylko jego stan w magazynie stanów zewnętrznych.

Większość usług już dziś przechowywania ich stan na zewnątrz, ponieważ jest magazynu zewnętrznego, co zapewnia niezawodność, dostępność, skalowalność i spójności dla tego stanu. W usłudze Service Fabric usługi nie są wymagane do przechowywania ich stan na zewnątrz. Usługa Service Fabric zajmuje się te wymagania zarówno kod usługi, jak i stan usługi.

Załóżmy, że chcemy zapisać to usługa, która przetwarza obrazy. Aby to zrobić, usługa pobiera obraz i serii konwersje do wykonania na tym obrazie. Ta usługa zwraca odbiornika komunikacji (teraz załóżmy, że jest WebAPI) czy uwidacznia interfejs API, takich jak `ConvertImage(Image i, IList<Conversion> conversions)`. Po odebraniu żądania, usługa zapisuje go w `IReliableQueue`i zwraca niektóre identyfikator do klienta, dzięki czemu można śledzić, żądanie.

W tej usłudze `RunAsync()` może być bardziej skomplikowane. Usługa ma pętlę wewnątrz jego `RunAsync()` która ściąga żądania z `IReliableQueue` i wykonuje konwersje żądanie. Wyniki pobrać przechowywane w `IReliableDictionary` tak, że kiedy klient wróci będą oni mogli uzyskać przekonwertowanego obrazów. Aby upewnić się, że nawet jeśli coś się nie powiedzie obraz nie zostanie utracony, tej usługi Reliable Service będzie ściąganie z kolejki, wykonywać konwersje i przechowuje wynik w ramach jednej transakcji. W takim przypadku komunikat zostanie usunięty z kolejki, a wyniki są przechowywane w słowniku wynik tylko wtedy, gdy spełniono konwersje. Alternatywnie usługa można ściągnąć obraz z kolejki i natychmiast przechowywania w magazynie zdalnym. Zmniejsza to ilość stanu, w których usługa ma zarządzać, ale zwiększa złożoność, ponieważ usługa ma zachować metadane potrzebne do zarządzania zdalnego magazynu. Każda z tych metod jeśli coś nie udało się w środku żądania nie będzie w kolejce oczekujących na przetworzenie.

Jedno uwagi na temat tej usługi jest to brzmi, takich jak usługi .NET w normalnym! Jedyną różnicą jest to, że struktury danych, używane (`IReliableQueue` i `IReliableDictionary`) są dostarczane przez usługę Service Fabric i są wysoce niezawodne, dostępne i spójne.

## <a name="when-to-use-reliable-services-apis"></a>Kiedy należy używać interfejsów API usług Reliable Services
Jeśli któregoś z następujących scharakteryzowania wymagania w zakresie usług aplikacji, należy rozważyć interfejsy API usług Reliable Services:

* Aby kod usługi (i opcjonalnie stanu) jako wysoko dostępnych i niezawodnych
* Należy w zakresie transakcji w wielu jednostkach stanu (na przykład zamówień i pozycje zamówienia).
* Stan aplikacji mogą być naturalnie modelowane jako niezawodnej słowników i kolejki.
* Z kodu aplikacji lub stanu musi być wysoce dostępna z małych opóźnień operacji odczytu i zapisu.
* Twoja aplikacja potrzebuje do kontroli współbieżności lub stopień szczegółowości Operacje transakcyjne na jeden lub więcej elementów Reliable Collections.
* Chcesz zarządzać komunikacji lub kontrolować schematu partycjonowania dla usługi.
* Kod wymaga środowiska bezwątkowy.
* Twoja aplikacja potrzebuje do dynamicznie tworzy ani nie niszczy niezawodne słowników albo w kolejkach lub całej usługi w czasie wykonywania.
* Należy programowo kontrolować dostarczone do usługi Service Fabric kopii zapasowej i przywracania funkcji dla usługi kondycji.
* Twoja aplikacja potrzebuje do zachowania historii zmian stanu wszystkich jej jednostek.
* Chcesz opracować lub używanie dostawców niestandardowych, opracowane przez strony trzecie stanu.

## <a name="next-steps"></a>Kolejne kroki
* [Reliable Services — szybki start](service-fabric-reliable-services-quick-start.md)
* [Elementy Reliable collections](service-fabric-reliable-services-reliable-collections.md)
* [Modelu programowania Reliable Actors](service-fabric-reliable-actors-introduction.md)
