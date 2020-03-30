---
title: Omówienie modelu programowania niezawodnej usługi
description: Dowiedz się więcej o modelu programowania niezawodnej usługi sieci szkieletowej i rozpocznij pisanie własnych usług.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083504"
---
# <a name="reliable-services-overview"></a>Omówienie usług Reliable Services

Usługa Azure Service Fabric upraszcza pisanie i zarządzanie usługami bezstanowymi i stanowymi. W tym temacie omówiono:

* Model programowania niezawodne usługi dla usług bezstanowych i stanowych.
* Wybory, których należy dokonać podczas pisania niezawodnej usługi.
* Niektóre scenariusze i przykłady, kiedy używać niezawodnych usług i jak są one zapisywane.

*Niezawodne usługi* jest jednym z modeli programowania dostępnych w sieci szkieletowej usług. Innym jest model programowania *reliable actor,* który zapewnia platformę aplikacji [virtual actor](https://research.microsoft.com/en-us/projects/orleans/) na modelu niezawodne usługi. Aby uzyskać więcej informacji na temat wiarygodnych aktorów, zobacz [Wprowadzenie do sieci szkieletowej usług Wiarygodnych aktorów.](service-fabric-reliable-actors-introduction.md)

Usługa Fabric zarządza okresem istnienia usług, od inicjowania obsługi administracyjnej i wdrażania przez uaktualnianie i usuwanie, za pośrednictwem [zarządzania aplikacjami sieci szkieletowej usług.](service-fabric-deploy-remove-applications.md)

## <a name="what-are-reliable-services"></a>Co to są niezawodne usługi

Niezawodne usługi zapewnia prosty, wydajny, model programowania najwyższego poziomu, które pomogą Ci wyrazić, co jest ważne dla aplikacji. Dzięki modelowi programowania Reliable Services otrzymujesz:

* Dostęp do interfejsów API sieci szkieletowej usług. W przeciwieństwie do usług sieci szkieletowej usług modelowanych jako [pliki wykonywalne gościa,](service-fabric-guest-executables-introduction.md)niezawodne usługi mogą bezpośrednio używać interfejsów API sieci szkieletowej usług. Dzięki temu usługi mogą:
  * Wykonywanie zapytań względem systemu
  * Raport kondycji jednostek w klastrze
  * Odbieranie powiadomień o zmianach konfiguracji i kodu
  * Znajdowanie i komunikowanie się z innymi usługami,
  * Korzystanie z [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md)
  * Dostęp do wielu innych możliwości, wszystkie z najwyższej klasy modelu programowania w kilku językach programowania.
* Prosty model do uruchamiania własnego kodu, który czuje się jak inne znane modele programowania. Kod ma dobrze zdefiniowany punkt wejścia i łatwo zarządzany cykl życia.
* Podłączalny model komunikacji. Użyj wybranego transportu, takiego jak HTTP z [interfejsem API sieci Web,](service-fabric-reliable-services-communication-webapi.md)WebSockets, niestandardowe protokoły TCP lub cokolwiek innego. Niezawodne usługi zapewniają doskonałe opcje out-of-the-box, których możesz użyć, lub możesz podać własne.
* W przypadku usług stanowych model programowania Reliable Services umożliwia spójne i niezawodne przechowywanie stanu w usłudze przy użyciu [niezawodnych kolekcji.](service-fabric-reliable-services-reliable-collections.md) Niezawodne kolekcje to prosty zestaw klas kolekcji o wysokiej dostępności i niezawodne, które będą znane każdemu, kto użył kolekcji C#. Tradycyjnie usługi potrzebne systemy zewnętrzne dla niezawodnego zarządzania stanem. Dzięki niezawodnym kolekcjom możesz przechowywać stan obok swoich obliczeń z taką samą wysoką dostępnością i niezawodnością, jakiej można oczekiwać od sklepów zewnętrznych o wysokiej dostępności. Ten model zwiększa również opóźnienie, ponieważ są współlokowanie obliczeń i stan musi działać.

## <a name="what-makes-reliable-services-different"></a>Co wyróżnia niezawodne usługi

Niezawodne usługi różnią się od usług, które zostały napisane wcześniej, ponieważ sieci szkieletowej usług zapewnia:

* **Niezawodność** — usługa pozostaje w górę nawet w zawodnych środowiskach, w których maszyny zawodzą lub występują problemy z siecią, lub w przypadkach, gdy same usługi napotykają błędy i ulegają awarii lub awarii. W przypadku usług stanowych stan jest zachowywany nawet w obecności sieci lub innych awarii.
* **Dostępność** — usługa jest osiągalna i responsywna. Usługa Sieci szkieletowej zachowuje żądaną liczbę uruchomionych kopii.
* **Skalowalność** — usługi są oddzielone od określonego sprzętu i mogą rosnąć lub zmniejszać się w razie potrzeby poprzez dodanie lub usunięcie sprzętu lub innych zasobów. Usługi są łatwo podzielone na partycje (szczególnie w przypadku stanowym), aby upewnić się, że usługa może skalować i obsługiwać częściowe awarie. Usługi można tworzyć i usuwać dynamicznie za pomocą kodu, dzięki czemu w razie potrzeby można wywężone więcej wystąpień, na przykład w odpowiedzi na żądania klientów. Na koniec usługa Service Fabric zachęca usługi do lekkich. Sieć szkieletowa usług umożliwia tysiące usług, które mają być aprowizacji w ramach jednego procesu, a nie wymaga lub poświęca całe wystąpienia systemu operacyjnego lub procesów do jednego wystąpienia usługi.
* **Spójność** — wszelkie informacje przechowywane w niezawodnej usłudze mogą być gwarantowane, aby były spójne. Dotyczy to nawet w wielu niezawodnych kolekcji w ramach usługi. Zmiany w kolekcjach w ramach usługi można wyw.

## <a name="service-lifecycle"></a>Cykl życia serwisu

Niezależnie od tego, czy usługa jest stanowa, czy bezstanowa, niezawodne usługi zapewniają prosty cykl życia, który umożliwia szybkie podłączenie kodu i rozpoczęcie pracy.  Uruchomienie nowej usługi wymaga zaimplementowania dwóch metod:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** — ta metoda jest, gdy usługa definiuje stos komunikacji, który chce użyć. Stos komunikacji, takich jak [web API](service-fabric-reliable-services-communication-webapi.md), jest to, co definiuje nasłuchiwanie punktu końcowego lub punktów końcowych dla usługi (jak klienci dotrzeć do usługi). Definiuje również, jak wiadomości, które pojawiają się interakcji z resztą kodu usługi.
* **RunAsync** — ta metoda jest, gdy usługa uruchamia swoją logikę biznesową i gdzie będzie kopać wszystkie zadania w tle, które powinny być uruchamiane przez cały okres istnienia usługi. Token anulowania, który jest dostarczany jest sygnałem, kiedy ta praca powinna zostać zatrzymana. Na przykład jeśli usługa musi pobierać wiadomości z kolejki niezawodne i przetwarzać je, jest to, gdzie ta praca się dzieje.

Jeśli po raz pierwszy uczysz się o niezawodnych usługach, czytaj dalej! Jeśli szukasz szczegółowego przewodnika po cyklu życia niezawodnych usług, zapoznaj się z [omówieniem cyklu życia niezawodnych usług.](service-fabric-reliable-services-lifecycle.md)

## <a name="example-services"></a>Przykładowe usługi

Przyjrzyjmy się bliżej, jak model niezawodnych usług działa z usługami bezstanowymi i stanowymi.

### <a name="stateless-reliable-services"></a>Niezawodne usługi bezstanowe

*Usługa bezstanowa* jest taki, gdzie nie ma stanu utrzymywane w ramach usługi przez wywołania. Każdy stan, który jest obecny jest całkowicie jednorazowe i nie wymaga synchronizacji, replikacji, trwałości lub wysokiej dostępności.

Rozważmy na przykład kalkulator, który nie ma pamięci i odbiera wszystkie warunki i operacje do wykonania jednocześnie.

W takim przypadku `RunAsync()` (C#) `runAsync()` lub (Java) usługi może być pusta, ponieważ nie ma przetwarzania zadań w tle, które usługa musi wykonać. Po utworzeniu usługi kalkulatora zwraca `ICommunicationListener` (C#) `CommunicationListener` lub (Java) (na przykład [Web API),](service-fabric-reliable-services-communication-webapi.md)który otwiera punkt końcowy nasłuchiwania na niektórych portach. Ten punkt końcowy nasłuchiwania łączy się z różnymi metodami obliczania (przykład: "Dodaj(n1, n2)"), które definiują publiczny interfejs API kalkulatora.

Gdy wywołanie jest wykonywane z klienta, wywoływana jest odpowiednia metoda, a usługa kalkulatora wykonuje operacje na dostarczonych danych i zwraca wynik. Nie przechowuje żadnego stanu.

Nie przechowywanie żadnego stanu wewnętrznego sprawia, że ten przykład kalkulator proste. Ale większość usług nie są naprawdę bezpaństwowe. Zamiast tego zewnętrzni ich stan do innego magazynu. (Na przykład każda aplikacja sieci web, która opiera się na zachowaniu stanu sesji w magazynie kopii zapasowej lub pamięci podręcznej nie jest bezstanowa).

Typowym przykładem jak usługi bezstanowe są używane w sieci szkieletowej usług jest fronton, który udostępnia interfejsu API publicznej dla aplikacji sieci web. Następnie usługa front-end rozmawia z usługami stanowymi, aby wypełnić żądanie użytkownika. W takim przypadku wywołania od klientów są kierowane do znanego portu, takiego jak 80, gdzie nasłuchuje usługa bezstanowa. Ta usługa bezstanowa odbiera wywołanie i określa, czy wywołanie pochodzi od zaufanej strony i do której usługi jest przeznaczone.  Następnie usługa bezstanowa przekazuje wywołanie do poprawnej partycji usługi stanowej i czeka na odpowiedź. Gdy usługa bezstanowa odbiera odpowiedź, odpowiada na oryginalnego klienta. Przykładem takiej usługi jest przykład *wprowadzenie do sieci szkieletowej usług* [(C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java),](https://github.com/Azure-Samples/service-fabric-java-getting-started)wśród innych przykładów sieci szkieletowej usług w tym repozytorium.

### <a name="stateful-reliable-services"></a>Niezawodne usługi stanowe

*Usługa stanowa* jest taki, który musi mieć pewną część stanu przechowywane spójne i obecne, aby usługa funkcjonowała. Należy wziąć pod uwagę usługę, która stale oblicza średnią kroczącą niektórych wartości na podstawie aktualizacji, które otrzymuje. Aby to zrobić, musi mieć bieżący zestaw żądań przychodzących, które musi przetworzyć i bieżącą średnią. Każda usługa, która pobiera, przetwarza i przechowuje informacje w magazynie zewnętrznym (na przykład azure blob lub magazynu tabel dzisiaj) jest stanowa. Po prostu zachowuje swój stan w magazynie stanu zewnętrznego.

Większość usług obecnie przechowywać ich stan zewnętrznie, ponieważ magazyn zewnętrzny jest, co zapewnia niezawodność, dostępność, skalowalność i spójność dla tego stanu. W sieci szkieletowej usług usługi nie są wymagane do przechowywania ich stanu zewnętrznie. Sieci szkieletowej usług zajmuje się te wymagania dotyczące kodu usługi i stanu usługi.

Załóżmy, że chcemy napisać usługę, która przetwarza obrazy. Aby to zrobić, usługa wykonuje obraz i serię konwersji do wykonania na tym obrazie. Ta usługa zwraca odbiornik komunikacji (załóżmy, że jest webapi), który `ConvertImage(Image i, IList<Conversion> conversions)`udostępnia interfejs API, takich jak . Po odebraniu żądania usługa przechowuje `IReliableQueue`je w programie i zwraca do klienta identyfikator, aby mógł śledzić żądanie.

W tej `RunAsync()` usłudze może być bardziej skomplikowane. Usługa ma pętlę `RunAsync()` wewnątrz, która pobiera `IReliableQueue` żądania i wykonuje żądane konwersje. Wyniki są przechowywane `IReliableDictionary` w taki sposób, że po powrocie klienta mogą uzyskać ich przekonwertowane obrazy. Aby upewnić się, że nawet jeśli coś nie powiedzie się obraz nie zostanie utracona, ta niezawodna usługa będzie wyciągnąć z kolejki, wykonać konwersje i przechowywać wynik wszystko w jednej transakcji. W takim przypadku wiadomość jest usuwana z kolejki, a wyniki są przechowywane w słowniku wyników tylko po zakończeniu konwersji. Alternatywnie usługa może wyciągnąć obraz z kolejki i natychmiast przechowywać go w magazynie zdalnym. Zmniejsza to ilość stanu, który usługa musi zarządzać, ale zwiększa złożoność, ponieważ usługa musi zachować metadane niezbędne do zarządzania magazynem zdalnym. Z obu podejść, jeśli coś nie powiodło się w środku żądanie pozostaje w kolejce oczekiwania na przetworzenie.

Mimo że ta usługa brzmi jak typowa usługa .NET, różnica`IReliableQueue` `IReliableDictionary`polega na tym, że struktury danych używane ( i) są dostarczane przez sieć szkieletową usług i są wysoce niezawodne, dostępne i spójne.

## <a name="when-to-use-reliable-services-apis"></a>Kiedy używać interfejsów API niezawodnych usług

Należy wziąć pod uwagę interfejsy API niezawodnych usług, jeśli:

* Chcesz, aby kod usługi (i opcjonalnie stan) był wysoce dostępny i niezawodny.
* Potrzebne są gwarancje transakcyjne w wielu jednostkach stanu (na przykład zamówienia i elementy zamówienia).
* Stan aplikacji może być naturalnie modelowane jako niezawodne słowniki i kolejki.
* Kod aplikacji lub stan musi być wysoce dostępny z odczytami i zapisami o małym opóźnieniu.
* Aplikacja musi kontrolować współbieżność lub szczegółowość operacji ujednoliconych w co najmniej jednej niezawodnej kolekcji.
* Chcesz zarządzać komunikacją lub kontrolować schemat partycjonowania dla usługi.
* Kod wymaga środowiska uruchomieniowego zwolną gwintem.
* Aplikacja musi dynamicznie tworzyć lub niszczyć niezawodne słowniki lub kolejki lub całe usługi w czasie wykonywania.
* Należy programowo kontrolować funkcje tworzenia kopii zapasowych i przywracania dostarczonych przez usługę Sieci szkieletowej dla stanu usługi.
* Aplikacja musi zachować historię zmian dla swoich jednostek stanu.
* Chcesz tworzyć lub korzystać z dostawców stanu niestandardowego opracowanych przez innych firm.

## <a name="next-steps"></a>Następne kroki

* [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start.md)
* [Niezawodne kolekcje](service-fabric-reliable-services-reliable-collections.md)
* [Model programowania Reliable Actors](service-fabric-reliable-actors-introduction.md)
