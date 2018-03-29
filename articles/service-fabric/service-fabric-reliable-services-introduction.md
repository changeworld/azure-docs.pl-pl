---
title: Omówienie modelu programowania usług sieci szkieletowej niezawodnej usługi | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o modelu programowania niezawodnej usługi Service Fabric i rozpocząć pisanie własnych usług.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider;
ms.openlocfilehash: 66e3939a59f09650ce76488c38eb46699ab9f63f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="reliable-services-overview"></a>Omówienie usług Reliable Services
Sieć szkieletowa usług Azure upraszcza zapisywanie i bezstanowe i stanowe niezawodne usługi zarządzania. W tym temacie omówiono:

* Niezawodne usługi model programowania usług bezstanowych i stanowych.
* Opcje, które należy podjąć podczas zapisywania niezawodnej usługi.
* Niektóre scenariusze i przykłady, kiedy należy używać usługi niezawodnego i jak są one napisane.

Niezawodne usługi jest jednym z modele programowania dostępne w sieci szkieletowej usług. Druga to modelu programowania niezawodnego aktora, który zapewnia wirtualnego model programowania aktora na podstawie modelu niezawodne usługi. Aby uzyskać więcej informacji o modelu programowania Reliable Actors, zobacz [wprowadzenie do usługi sieci szkieletowej Reliable Actors](service-fabric-reliable-actors-introduction.md).

Usługa Service Fabric zarządza czasem istnienia usług, od aprowizacji i wdrożenia w ramach uaktualniania i usuwania, za pośrednictwem [zarządzania aplikacjami sieci szkieletowej usług](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Co to są niezawodne usługi?
Niezawodne usługi zapewnia prosty, zaawansowane, najwyższego poziomu model programowania ułatwiające express, co jest ważne do tej aplikacji. Z usługami Reliable Services modelu programowania można uzyskać:

* Dostęp do pozostałej części sieci szkieletowej usług programowania interfejsów API. W przeciwieństwie do usługi sieci szkieletowej usług formę [pliki wykonywalne gościa](service-fabric-guest-executables-introduction.md), Pobierz niezawodne usługi do użycia bezpośrednio z resztą interfejsów API usługi Service Fabric. Dzięki temu usługi do:
  * wykonywanie zapytań względem systemu
  * Raport kondycji temat jednostek w klastrze
  * odbieranie powiadomień o zmianach dotyczących konfiguracji i kod
  * Wyszukiwanie i komunikowanie się z innymi usługami
  * (opcjonalnie) użyć [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md)
  * .. .a udzieleniem im dostępu do wielu innych funkcji, wszystkie z pierwszej klasie model programowania w wielu językach programowania.
* Prostego modelu do uruchamiania z własnego kodu, który wygląda jak modeli, które są używane do programowania. Kod ma punktu wejścia dobrze zdefiniowany i łatwe do zarządzania cyklem życia.
* Model podłączany komunikacji. Użyj transportu wybranych przez użytkownika, takich jak HTTP z [interfejsu API sieci Web](service-fabric-reliable-services-communication-webapi.md), Websocket, niestandardowe protokoły TCP lub innych elementów. Niezawodne usługi zapewniają dużą niektóre opcje poza pole można użyć lub Podaj własny.
* Dla stanowych usług modelu programowania usług niezawodnej pozwala na spójne i niezawodne przechowywanie swój stan wewnątrz usługi przy użyciu [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md). Niezawodne kolekcje są prostym zestawem typu klas kolekcji wysokiej dostępności i niezawodne, znanych dla każdego, kto został użyty kolekcje C#. Tradycyjnie usług niezbędnych systemów zewnętrznych dla zarządzania stanem niezawodne. Z kolekcjami niezawodne można przechowywać swój stan obok obliczeniowych o tej samej wysokiej dostępności i niezawodności, które zostały NAS oczekiwać sklepach zewnętrznych wysokiej dostępności. Ten model również poprawia czas oczekiwania, ponieważ kolokacja obliczeniowych i stan, jaki musi działać.

Obejrzyj ten film Microsoft Virtual Academy omówienie niezawodne usługi: <center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>Niezawodne usługi dzięki czemu różne?
Niezawodne usługi w sieci szkieletowej usług różnią się od usługi, które zostały zapisane przed. Usługa Service Fabric realizuje niezawodności, dostępności, spójność i skalowalność.

* **Niezawodność** — Twoja usługa pozostaje się nawet w środowiskach zawodnych gdzie maszynach się nie powieść lub kliknij przycisk problemy z siecią lub w przypadkach, gdy uwierzytelnienia usługi wystąpić błędów i awarii lub się nie powieść. Dla stanowych usług swój stan są zachowywane nawet obecności sieci lub inne błędy.
* **Dostępność** -usługa jest osiągalny i szybko reagowały. Sieć szkieletowa usług przechowuje żądaną liczbę kopii uruchomiona.
* **Skalowalność** — usługi są całkowicie niezależna od konkretnego sprzętu i ich można zwiększać i zmniejszać w razie potrzeby poprzez dodawanie lub usuwanie sprzętu lub inne zasoby. Usługi łatwo są dzielone (szczególnie w przypadku stanowego) aby upewnić się, że można skalować usługi oraz obsługi błędów częściowej. Usługi mogą być tworzone i usuwane dynamicznie za pośrednictwem kodu, włączanie więcej wystąpień do przejścia można w razie potrzeby powiedzieć w odpowiedzi na żądania klientów. Ponadto sieci szkieletowej usług zachęca usług jako lightweight. Sieć szkieletowa usług umożliwia tysięcy usługi udostępniane w ramach jednego procesu, zamiast całego wymagające lub przypisywanie wystąpień systemu operacyjnego lub procesów do pojedynczego wystąpienia usługi.
* **Spójność** -wszystkie informacje przechowywane w tej usłudze można zagwarantować aby było spójne. Dotyczy to nawet przez wiele kolekcji niezawodnej w ramach usługi. Zmiany w kolekcjach, w ramach usługi można podjąć w sposób transakcyjnie atomic.

## <a name="service-lifecycle"></a>Usługi cyklu życia
Czy usługa jest stanowego lub bezstanowego, niezawodne usługi zapewniają prosty cykl pozwala szybko podłączenia w kodzie i rozpocząć pracę.  Istnieje tylko jedna lub dwie metody, które należy wdrożyć można pobrać usługi do pracy.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** — ta metoda jest, gdzie usługa definiuje stack(s) komunikacji, który chce używać. Komunikacja stosu, takich jak [interfejsu API sieci Web](service-fabric-reliable-services-communication-webapi.md), jest definiuje co punktu końcowego nasłuchiwania lub punktów końcowych usługi (jak klienci dotarcia do usługi). Definiuje również, jak komunikaty wyświetlane interakcję z resztą kodu usługi.
* **RunAsync** — ta metoda jest usługą, w którym odbywa się jej logiki biznesowej i gdzie może rozpocząć Wyłącz wszystkie zadania w tle, które powinny być uruchamiane przez czas ich istnienia usługi. Token anulowania, który został dostarczony jest sygnał dla podczas pracy ma zostać zatrzymana. Na przykład jeśli usługa wymaga ściągać komunikaty poza kolejka niezawodnych i przetwarzanie ich, to gdy się stanie, które współpracują.

Jeśli zapoznawania się o usługach reliable services po raz pierwszy, przeczytaj na! Jeśli szukasz szczegółowy przewodnik życia niezawodne usługi użytkownik może przejdź do [w tym artykule](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Przykład usług
Znajomość tego modelu programowania, Spójrzmy szybki w dwóch różnych usług, aby zobaczyć, jak te dopasowania.

### <a name="stateless-reliable-services"></a>Bezstanowej niezawodnej usługi
Usługi bezstanowej jest jeden, gdy stan jest niedostępny przechowywanych w ramach usługi w wielu wywołań. Stan jest obecny jest całkowicie jednorazowe i nie wymaga synchronizacji, replikacji, trwałości i wysokiej dostępności.

Rozważmy na przykład Kalkulator, Brak pamięci i odbiera wszystkie warunki i czynności do wykonania na raz.

W takim przypadku `RunAsync()` (C#) lub `runAsync()` (Java) usługi może być pusta, ponieważ nie istnieje żadne zadania — przetwarzania w tle wymagającym czy usługa. Po utworzeniu usługi Kalkulator zwraca `ICommunicationListener` (C#) lub `CommunicationListener` (Java) (na przykład [interfejsu API sieci Web](service-fabric-reliable-services-communication-webapi.md)) która otwiera punktu końcowego nasłuchiwania na porcie niektórych. Tego punktu końcowego nasłuchiwania przechwytuje do metody innej obliczania (przykład: "Dodaj (n1, n2)") definiującą Kalkulator publiczny interfejs API.

Gdy wywołanie z klienta odpowiedniej metody i Kalkulator wykonuje operacje na danych udostępnionych i zwraca wynik. Go nie przechowuje jakikolwiek stan.

Nie są przechowywane wszystkie stan wewnętrzny upraszcza Kalkulator tego przykładu. Jednak większość usług nie są naprawdę bezstanowych. Zamiast tego należy ich oddzielenie ich stanu do niektórych innym magazynie. (Na przykład dowolnej aplikacji sieci web, która opiera się na przechowywanie stanu sesji w sklepie zapasowy lub w pamięci podręcznej nie jest bezstanowych.)

Typowym przykładem sposobu usługi bezstanowej są używane w sieci szkieletowej usług jest jako frontonu, która uwidacznia interfejs API publicznych dla aplikacji sieci web. Usługi frontonu następnie komunikuje się stanowych usług do ukończenia żądania użytkownika. W takim przypadku wywołania z klientów są przekierowywane do znanego port, np. 80, którym nasłuchuje usługa bezstanowa. Tej usługi bezstanowej odbiera połączenie i określa, czy wywołanie pochodzi z zaufanego firm i której usługi jest przeznaczony do.  Następnie usługi bezstanowej przesłanie wywołania do poprawnej partycji usługi stanowej i czeka na odpowiedź. Gdy usługa bezstanowa odbiera odpowiedź, w odpowiedzi klienta oryginalnym. Przykładem takiej usługi znajduje się w naszej próbki [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService). To jest tylko jeden przykład tego wzorca w próbkach, inne osoby w istnieją także inne przykłady.

### <a name="stateful-reliable-services"></a>Stanowe niezawodne usługi
Usługi stanowej to taki, który musi mieć część stanu zachowano spójności i znajdują się w kolejności dla usługi funkcji. Należy wziąć pod uwagę to usługa, która stale oblicza średnią kroczącą pewnej wartości na podstawie aktualizacji, który odbiera. Aby to zrobić, musi mieć bieżącego zestawu żądań przychodzących, wymaganych do przetworzenia oraz aktualny średni. Wszystkie usługi, która pobiera, procesów i zapisuje informacje w zewnętrznym sklepie (np. Azure blob lub tabeli magazynu dzisiaj) jest obiektem stanowym. Śledzi tylko jego stanu w magazynie stanów zewnętrznych.

Większość usług dzisiaj przechowywane zewnętrznie, ich stanie, ponieważ jest zewnętrznym sklepie, co zapewnia niezawodność, dostępność, skalowalność i spójności dla tego stanu. W sieci szkieletowej usług usługi nie są wymagane do przechowywania ich stanu zewnętrznie. Sieć szkieletowa usług zapewnia obsługę tych wymagań dla kodu usługi i stan usługi.

Załóżmy, że jeśli chcesz zapisać to usługa, która przetwarza obrazów. Aby to zrobić, usługa przyjmuje obrazu i serii konwersje do wykonania w tym obrazie. Ta usługa zwraca odbiornik komunikacji (teraz załóżmy, że jest WebAPI) czy ujawnia interfejs API, takich jak `ConvertImage(Image i, IList<Conversion> conversions)`. Po odebraniu żądania usługa zapisze go w `IReliableQueue`i zwraca identyfikator niektórych do klienta, więc można śledzić żądania.

W tej usłudze `RunAsync()` może być bardziej złożonych. Usługa ma pętlę wewnątrz jego `RunAsync()` który ściąga żądań z `IReliableQueue` i wykonuje konwersje żądanie. Wyniki pobrać przechowywane w `IReliableDictionary` tak, aby po wróci klient może uzyskać przekonwertowanego obrazów. Do upewnij się, że nawet jeśli coś nie powiedzie się obraz nie zostanie utracony, ta usługa niezawodnej będzie pobierać z kolejki, wykonywania konwersji i zapisać wynik w ramach pojedynczej transakcji. W takim przypadku wiadomość zostanie usunięta z kolejki i wyniki są przechowywane w słowniku wynik tylko po ukończeniu konwersji. Alternatywnie usługa można ściągnąć obraz z kolejki i natychmiast przechowywania w magazynie zdalnym. Zmniejsza ilość stanu do zarządzania przez usługę, ale zwiększa złożoności, ponieważ usługa musi zachować metadane potrzebne do zarządzania zdalnego magazynu. Z obu podejście jeśli coś nie powiodła się w środku żądanie pozostaje w kolejce oczekujących na przetworzenie.

Należy pamiętać o tej usługi jest to wydaje, takich jak usługi .NET w normalnym! Jedyną różnicą jest to, że struktury danych używane (`IReliableQueue` i `IReliableDictionary`) są dostarczane przez sieć szkieletowa usług i są wysoce niezawodnych, dostępna i spójne.

## <a name="when-to-use-reliable-services-apis"></a>Kiedy należy używać niezawodnej usługi interfejsów API
Jedną z następujących czynności scharakteryzowania wymagań usługi aplikacji, należy rozważyć niezawodnej usługi interfejsów API:

* Kod z usługą (i opcjonalnie stanu) być wysokiej dostępności i niezawodnych
* Należy transakcyjne gwarancje w wielu jednostkach stanu (na przykład zleceń i kolejność pozycji).
* Stan aplikacji mogą być naturalnie modelowane jako wiarygodnych słowniki i kolejek.
* Stan lub kodu aplikacji musi być wysokiej dostępności z niskim opóźnieniem odczyty i zapisy.
* Aplikacja wymaga kontrola współbieżności lub szczegółowości Operacje transakcyjne na co najmniej jednej kolekcji wiarygodne.
* Chcesz zarządzać komunikatów lub kontrolować schemat partycjonowania dla usługi.
* Kod wymaga środowiska uruchomieniowego bezwątkowy.
* Aplikacja musi zostać dynamicznie utworzyć lub zniszczyć niezawodnej słowniki lub kolejek lub całej usługi w czasie wykonywania.
* Należy programowo kontrolować wprowadzone do usługi Service Fabric kopii zapasowej i przywracania funkcji stanu usługi.
* Aplikacja wymaga przechowywania historii zmian dla jednostki jego stanu.
* Chcesz rozwijać lub zużywać dostawców innych firm — rozwinięte, niestandardowe stanu.

## <a name="next-steps"></a>Kolejne kroki
* [Niezawodne usługi szybki start](service-fabric-reliable-services-quick-start.md)
* [Kolekcje niezawodnej](service-fabric-reliable-services-reliable-collections.md)
* [Model programowania Reliable Actors](service-fabric-reliable-actors-introduction.md)
