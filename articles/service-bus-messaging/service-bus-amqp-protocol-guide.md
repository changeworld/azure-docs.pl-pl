---
title: Przewodnik protokołu USŁUGI AMQP 1.0 w usłudze Azure Service Bus and Event Hubs | Dokumenty firmy Microsoft
description: Przewodnik protokołu do wyrażeń i opis protokołu AMQP 1.0 w usłudze Azure Service Bus i usługi Event Hubs
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d706e9b3351b0693a1f352e15b6b9b0cc5c7a65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086150"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Przewodnik protokołu USŁUGI AMQP 1.0 w usłudze Azure Service Bus and Event Hubs

Protokół Zaawansowanego kolejkowania wiadomości 1.0 to znormalizowany protokół ramowania i transferu dla asynchronicznie, bezpiecznie i niezawodnie przesyłających wiadomości między dwiema stronami. Jest to podstawowy protokół usługi Azure Service Bus Messaging i usługi Azure Event Hubs. Obie usługi obsługują również protokół HTTPS. Zastrzeżony protokół SBMP, który jest również obsługiwany, jest stopniowo wycofywany na rzecz AMQP.

AMQP 1.0 jest wynikiem szerokiej współpracy branżowej, która zgromadziła dostawców oprogramowania pośredniczącego, takich jak Microsoft i Red Hat, z wieloma użytkownikami oprogramowania pośredniczącego wiadomości, takimi jak JP Morgan Chase reprezentujący branżę usług finansowych. Forum normalizacji technicznej dla protokołu AMQP i specyfikacji rozszerzenia jest OASIS, i osiągnęła formalne zatwierdzenie jako międzynarodowy standard iso /IEC 19494.

## <a name="goals"></a>Cele

W tym artykule pokrótce opisano podstawowe pojęcia specyfikacji obsługi wiadomości AMQP 1.0 wraz z małym zestawem specyfikacji rozszerzeń wersji roboczej, które są obecnie finalizowane w komitecie technicznym OASIS AMQP i wyjaśniono, w jaki sposób usługa Azure Service Bus wdraża i opiera się na tych specyfikacjach.

Celem jest dla każdego dewelopera przy użyciu istniejącego stosu klienta AMQP 1.0 na dowolnej platformie, aby móc wchodzić w interakcje z usługą Azure Service Bus za pośrednictwem protokołu AMQP 1.0.

Typowe stosy AMQP 1.0 ogólnego przeznaczenia, takie jak Apache Proton lub AMQP.NET Lite, implementują już wszystkie podstawowe protokoły AMQP 1.0. Te podstawowe gesty są czasami zawijane za pomocą interfejsu API wyższego poziomu; Apache Proton oferuje nawet dwa, imperatywny interfejs API Messenger i reaktywne API reaktora.

W poniższej dyskusji zakładamy, że zarządzanie połączeniami, sesjami i łączami AMQP oraz obsługa transferów ramek i kontroli przepływu są obsługiwane przez odpowiedni stos (np. od deweloperów aplikacji. Abstrakcyjnie zakładamy istnienie kilku prymitywów interfejsu API, takich jak możliwość łączenia się i tworzenia jakiejś formy `send()` `receive()` obiektów abstrakcji *nadawcy* i *odbiorcy,* które następnie mają jakiś kształt i operacje, odpowiednio.

Podczas omawiania zaawansowanych możliwości usługi Azure Service Bus, takich jak przeglądanie wiadomości lub zarządzanie sesjami, te funkcje są wyjaśnione w warunkach PROTOKOŁU AMQP, ale także jako warstwowa pseudo-implementacja na górze tej zakładanej abstrakcji interfejsu API.

## <a name="what-is-amqp"></a>Co to jest AMQP?

AMQP jest protokołem kadrowania i transferu. Kadrowanie oznacza, że zapewnia strukturę dla strumieni danych binarnych, które przepływają w obu kierunkach połączenia sieciowego. Struktura zapewnia wytylinowanie dla różnych bloków danych, zwanych *ramkami,* które mają być wymieniane między połączonymi stronami. Możliwości przekazywania zapewniają, że obie strony przekazujące informacje mogą ustalić wspólne zrozumienie, kiedy ramy zostaną przeniesione i kiedy transfery uznaje się za kompletne.

W przeciwieństwie do wcześniejszych wygasłych wersji roboczych opracowanych przez grupę roboczą AMQP, które są nadal używane przez kilku brokerów wiadomości, ostateczny i standaryzowany protokół AMQP 1.0 grupy roboczej nie przewiduje obecności brokera wiadomości lub żadnej konkretnej topologii dla w brokerze komunikatów.

Protokół może służyć do symetrycznej komunikacji peer-to-peer, do interakcji z brokerami komunikatów, które obsługują kolejki i publikować/subskrybować jednostki, tak jak usługa Azure Service Bus. Może również służyć do interakcji z infrastrukturą obsługi wiadomości, gdzie wzorce interakcji różnią się od zwykłych kolejek, jak w przypadku usługi Azure Event Hubs. Centrum zdarzeń działa jak kolejka, gdy zdarzenia są wysyłane do niego, ale działa bardziej jak usługa magazynu szeregowego, gdy zdarzenia są odczytywane z niego; przypomina napęd taśmowy. Klient wybiera przesunięcie do dostępnego strumienia danych, a następnie jest obsługiwany wszystkie zdarzenia z tego przesunięcia do najnowszych dostępnych.

Protokół AMQP 1.0 został zaprojektowany tak, aby był rozszerzalny, umożliwiając dalsze specyfikacje w celu zwiększenia jego możliwości. Ilustrują to trzy specyfikacje rozszerzenia omówione w tym dokumencie. W przypadku komunikacji za pośrednictwem istniejącej infrastruktury HTTPS/WebSockets konfigurowanie natywnych portów PROTOKOŁU TCP protokołu AMQP może być trudne. Specyfikacja powiązania definiuje sposób warstwy AMQP przez WebSockets. Do interakcji z infrastrukturą obsługi wiadomości w sposób żądania/odpowiedzi do celów zarządzania lub w celu zapewnienia zaawansowanych funkcji, specyfikacja zarządzania AMQP definiuje wymagane podstawowe prymitywy interakcji. W przypadku integracji z modelem autoryzacji federacyjnej specyfikacja zabezpieczeń oparta na oświadczeniach usługi AMQP określa sposób kojarzenia i odnawiania tokenów autoryzacji skojarzonych z łączami.

## <a name="basic-amqp-scenarios"></a>Podstawowe scenariusze AMQP

W tej sekcji opisano podstawowe użycie usługi AMQP 1.0 z usługą Azure Service Bus, która obejmuje tworzenie połączeń, sesji i łączy oraz przesyłanie wiadomości do i z encji usługi Service Bus, takich jak kolejki, tematy i subskrypcje.

Najbardziej miarodajne źródło, aby dowiedzieć się o tym, jak działa AMQP jest specyfikacja AMQP 1.0, ale specyfikacja została napisana dokładnie przypomnienie implementacji, a nie uczyć protokołu. W tej sekcji koncentruje się na wprowadzenie tyle terminologii, ile potrzeba do opisywania, jak usługa Service Bus używa AMQP 1.0. Aby uzyskać bardziej kompleksowe wprowadzenie do AMQP, a także szerszą dyskusję na temat AMQP 1.0, możesz przejrzeć [ten kurs wideo][this video course].

### <a name="connections-and-sessions"></a>Połączenia i sesje

AMQP wywołuje *kontenery*programów komunikacyjnych; zawierają *węzły*, które są jednostkami komunikującymi się wewnątrz tych kontenerów. Kolejka może być takim węzłem. AMQP pozwala na multipleksowanie, więc jedno połączenie może być używane dla wielu ścieżek komunikacyjnych między węzłami; na przykład klient aplikacji może jednocześnie odbierać z jednej kolejki i wysyłać do innej kolejki za pośrednictwem tego samego połączenia sieciowego.

![][1]

Połączenie sieciowe jest zatem zakotwiczone w kontenerze. Jest inicjowany przez kontener w roli klienta, co wychodzące połączenie gniazda TCP do kontenera w roli odbiornika, który nasłuchuje i akceptuje przychodzące połączenia TCP. Uzgadnianie połączenia obejmuje negocjowanie wersji protokołu, deklarowanie lub negocjowanie użycia zabezpieczeń poziomu transportu (TLS/SSL) oraz uzgadnianie uwierzytelniania/autoryzacji w zakresie połączenia opartym na programie SASL.

Usługa Azure Service Bus wymaga użycia protokołu TLS przez cały czas. Obsługuje połączenia za pomocą portu TCP 5671, przy czym połączenie TCP jest najpierw nałożone na TLS przed wprowadzeniem uzgadniania protokołu AMQP, a także obsługuje połączenia za pomocą portu TCP 5672, przy czym serwer natychmiast oferuje obowiązkowe uaktualnienie połączenia do TLS przy użyciu modelu zalecanego przez AMQP. Powiązanie WEBSockets protokołu AMQP tworzy tunel za pośrednictwem portu TCP 443, który jest następnie odpowiednikiem połączeń AMQP 5671.

Po skonfigurowaniu połączenia i protokołu TLS usługa Service Bus oferuje dwie opcje mechanizmu SASL:

* SASL PLAIN jest powszechnie używany do przekazywania poświadczeń nazwy użytkownika i hasła do serwera. Usługa Service Bus nie ma kont, ale nazywa [się reguły zabezpieczeń dostępu współdzielonego,](service-bus-sas.md)które przyznają prawa i są skojarzone z kluczem. Nazwa reguły jest używana jako nazwa użytkownika, a klucz (jako tekst zakodowany base64) jest używany jako hasło. Prawa skojarzone z wybraną regułą regulują operacje dozwolone w połączeniu.
* SASL ANONYMOUS służy do pomijania autoryzacji SASL, gdy klient chce użyć modelu zabezpieczeń opartych na oświadczeniach (CBS), który jest opisany później. Dzięki tej opcji połączenie klienta można ustanowić anonimowo przez krótki czas, podczas którego klient może wchodzić w interakcje tylko z punktem końcowym cbs i uzgadniania CBS musi zostać ukończona.

Po ustanowieniu połączenia transportowego kontenery deklarują maksymalny rozmiar ramki, który są chętni do obsługi, a po upływie limitu czasu bezczynnego będą jednostronnie rozłączać, jeśli nie ma żadnych działań na połączenie.

Deklarują również, ile równoczesnych kanałów są obsługiwane. Kanał jest jednokierunkową, wychodzącą, wirtualną ścieżką transferu na górze połączenia. Sesja pobiera kanał z każdego ze połączonych kontenerów, tworząc dwukierunkową ścieżkę komunikacji.

Sesje mają model kontroli przepływu oparty na oknie; po utworzeniu sesji każda ze stron deklaruje, ile klatek chce zaakceptować w swoim oknie odbioru. Podczas wymiany ramek strony, przeniesione ramki wypełniają to okno, a transfery zatrzymują się, gdy okno jest zapełnione i dopóki okno nie zostanie zresetowane lub rozwinięte przy użyciu *przepływu performatywnego* (*performatywne* jest terminem AMQP dla gestów na poziomie protokołu wymienianych między obiema stronami).

Ten model oparty na oknie jest w przybliżeniu analogiczne do koncepcji TCP kontroli przepływu opartego na oknie, ale na poziomie sesji wewnątrz gniazda. Koncepcja protokołu zezwalania na wiele równoczesnych sesji istnieje tak, że ruch o wysokim priorytecie może być pospiesznie przeszłości ograniczona normalnego ruchu, jak na drodze ekspresowej pasa.

Usługa Azure Service Bus używa obecnie dokładnie jednej sesji dla każdego połączenia. Maksymalny rozmiar ramki usługi Service Bus to 262 144 bajtów (256-K bajtów) dla standardowych i centrów zdarzeń magistrali usług. Jest to 1 048 576 (1 MB) dla usługi Service Bus Premium. Usługa Service Bus nie nakłada żadnych określonych okien ograniczania na poziomie sesji, ale regularnie resetuje okno w ramach kontroli przepływu na poziomie łącza (patrz [następna sekcja).](#links)

Połączenia, kanały i sesje są ulotne. Jeśli podstawowe połączenie zostanie zwinięty, połączenia, tunel TLS, kontekst autoryzacji SASL i sesje muszą zostać przywrócone.

### <a name="amqp-outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących AMQP

Klienci korzystający z połączeń PROTOKOŁU AMQP za pomocą protokołu TCP wymagają otwarcia portów 5671 i 5672 w zaporze lokalnej. Wraz z tymi portami może być konieczne otwarcie dodatkowych portów, jeśli funkcja [EnableLinkRedirect](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) jest włączona. `EnableLinkRedirect`to nowa funkcja obsługi wiadomości, która pomaga pominąć jeden przeskok podczas odbierania wiadomości, pomagając w ten sposób zwiększyć przepływność. Klient rozpocznie komunikację bezpośrednio z usługą zaplecza za pośrednictwem zakresu portów 104XX, jak pokazano na poniższej ilustracji. 

![Lista portów docelowych][4]

Klient platformy .NET zakończy się niepowodzeniem z SocketException ("Podjęto próbę uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu"), jeśli te porty są blokowane przez zaporę. Funkcję można wyłączyć, `EnableAmqpLinkRedirect=false` ustawiając w ciągu połączenia, co zmusza klientów do komunikowania się z usługą zdalną za pomocą portu 5671.


### <a name="links"></a>Linki

AMQP przesyła wiadomości za linki. Łącze to ścieżka komunikacji utworzona za jego przez sesję, która umożliwia przesyłanie wiadomości w jednym kierunku; negocjacji statusu transferu odbywa się za pomocą łącza i dwukierunkowe między połączonymi stronami.

![][2]

Łącza mogą być tworzone przez dowolny kontener w dowolnym momencie i za pośrednictwem istniejącej sesji, co sprawia, że AMQP różni się od wielu innych protokołów, w tym HTTP i MQTT, gdzie inicjowanie transferów i ścieżki transferu jest wyłącznym przywilejem strony tworzącej podłączenie gniazda.

Kontener inicjujący łącza prosi kontenera przeciwnego o zaakceptowanie łącza i wybiera rolę nadawcy lub odbiorcy. W związku z tym kontenera można zainicjować tworzenie jednokierunkowych lub dwukierunkowych ścieżek komunikacji, przy czym ten ostatni modelowane jako pary łączy.

Łącza są nazwane i skojarzone z węzłami. Jak wspomniano na początku, węzły są komunikujące się jednostki wewnątrz kontenera.

W usłudze Service Bus węzeł jest bezpośrednio odpowiednikiem kolejki, tematu, subskrypcji lub podkiełka listy uszczypnienia kolejki lub subskrypcji. Nazwa węzła używana w dodatku AMQP jest zatem względną nazwą jednostki wewnątrz obszaru nazw usługi Service Bus. Jeśli kolejka `myqueue`ma na imię , jest to również jego nazwa węzła AMQP. Subskrypcja tematu jest zgodna z konwencją interfejsu API HTTP, posortowana w kolekcję zasobów "subskrypcje", a tym samym **sub** subskrypcja na temat **mytopic** ma nazwę węzła AMQP **mytopic/subscriptions/sub**.

Klient łączący jest również wymagany do używania nazwy węzła lokalnego do tworzenia łączy; Usługa Service Bus nie jest nakazowa dotycząca tych nazw węzłów i nie interpretuje ich. Stosy klientów usługi AMQP 1.0 zazwyczaj używają schematu, aby zapewnić, że te nazwy węzłów tymczasowych są unikatowe w zakresie klienta.

### <a name="transfers"></a>Transfery

Po ustanowieniu łącza wiadomości można przesyłać za ten link. W udKW transfer jest wykonywany za pomocą jawnego gestu protokołu *(performatywnego transferu),* który przenosi wiadomość z nadawcy do odbiorcy za pomocą łącza. Przeniesienie jest zakończone, gdy zostanie "rozliczone", co oznacza, że obie strony ustaliły wspólne zrozumienie wyniku tego przeniesienia.

![][3]

W najprostszym przypadku nadawca może wybrać wysyłanie wiadomości "wstępnie rozliczone", co oznacza, że klient nie jest zainteresowany wynikiem, a odbiorca nie przekazuje żadnych informacji zwrotnych na temat wyniku operacji. Ten tryb jest obsługiwany przez usługę Service Bus na poziomie protokołu AMQP, ale nie jest widoczna w żadnym z interfejsów API klienta.

W przypadku regularnych jest to, że wiadomości są wysyłane nierozliczenia, a odbiornik następnie wskazuje akceptacji lub odrzucenia przy użyciu *dyspozycji* performative. Odrzucenie występuje, gdy odbiorca nie może zaakceptować wiadomości z jakiegokolwiek powodu, a komunikat odrzucenia zawiera informacje o przyczynie, która jest strukturą błędów zdefiniowaną przez usługę AMQP. Jeśli komunikaty są odrzucane z powodu błędów wewnętrznych wewnątrz usługi Service Bus, usługa zwraca dodatkowe informacje wewnątrz tej struktury, które mogą być używane do dostarczania wskazówek diagnostycznych do obsługi personelu, jeśli są zgłoszenia żądań pomocy technicznej. Więcej szczegółów na temat błędów można znaleźć później.

Szczególną formą odrzucenia jest *zwolnione* państwo, które wskazuje, że syndyk nie ma technicznego sprzeciwu wobec przeniesienia, ale także nie ma interesu w uregulowaniu przeniesienia. Ten przypadek istnieje, na przykład, gdy wiadomość jest dostarczana do klienta usługi Service Bus, a klient zdecyduje się "porzucić" wiadomość, ponieważ nie może wykonać pracy wynikającej z przetwarzania wiadomości; sama dostawa wiadomości nie jest winna. Odmiana tego stanu jest *zmodyfikowany* stan, który umożliwia zmiany w wiadomości, jak jest zwolniony. Ten stan nie jest obecnie używany przez usługę Service Bus.

Specyfikacja AMQP 1.0 definiuje dalszy stan dyspozycji o nazwie *received*, który w szczególności pomaga obsługiwać odzyskiwanie łączy. Odzyskiwanie łączy umożliwia odtworzenie stanu łącza i wszelkich oczekujących dostaw na nowym połączeniu i sesji, gdy poprzednie połączenie i sesja zostały utracone.

Usługa Service Bus nie obsługuje odzyskiwania łączy; jeśli klient utraci połączenie z usługą Service Bus z oczekującym nierozliszczącym transferem wiadomości, ten transfer wiadomości zostanie utracony, a klient musi ponownie połączyć się, ponownie nawiązać połączenie i ponowić próbę przeniesienia.

W związku z tym usługa Service Bus i Event Hubs obsługują transfer "co najmniej raz", w którym nadawca może mieć pewność, że wiadomość została zapisana i zaakceptowana, ale nie obsługuje transferów "dokładnie raz" na poziomie AMQP, gdzie system będzie próbował odzyskać połączenie i kontynuować negocjacje stanu dostawy, aby uniknąć powielania transferu wiadomości.

Aby zrekompensować możliwe zduplikowane wiadomości, usługa Service Bus obsługuje wykrywanie duplikatów jako opcjonalną funkcję w kolejkach i tematach. Wykrywanie duplikatów rejestruje identyfikatory wiadomości wszystkich wiadomości przychodzących w oknie czasu zdefiniowanym przez użytkownika, a następnie po cichu upuszcza wszystkie wiadomości wysyłane z tymi samymi identyfikatorami wiadomości w tym samym oknie.

### <a name="flow-control"></a>Sterowanie przepływem

Oprócz modelu kontroli przepływu na poziomie sesji, który wcześniej omówiono, każde łącze ma swój własny model sterowania przepływem. Kontrola przepływu na poziomie sesji chroni kontener przed koniecznością obsługi zbyt wiele ramek naraz, kontrola przepływu na poziomie łącza stawia aplikację za ile wiadomości chce obsłużyć z łącza i kiedy.

![][4]

W przypadku łącza przelewy mogą się zdarzyć tylko wtedy, gdy nadawca ma wystarczającą ilość *środków na link*. Kredyt łącza jest licznikiem ustawionym przez odbiorcę przy użyciu *przepływu* performatywnego, który jest objęty zakresem łącza. Gdy nadawcy przypisano kredyt łącza, próbuje wykorzystać ten kredyt, dostarczając wiadomości. Każde dostarczenie wiadomości zmniejsza kredyt pozostałego łącza o 1. Gdy kredyt link jest wykorzystywany, dostawy się zatrzymują.

Gdy usługa Service Bus znajduje się w roli odbiorcy, natychmiast zapewnia nadawcy duży kredyt łącza, dzięki czemu wiadomości mogą być wysyłane natychmiast. Ponieważ używany jest kredyt łącza, usługa Service Bus od czasu do czasu wysyła do nadawcy *przepływ* wydajności w celu zaktualizowania salda kredytu łącza.

W roli nadawcy usługa Service Bus wysyła wiadomości w celu wykorzystania wszelkich zaległych kredytów łącza.

Wywołanie "odbierz" na poziomie interfejsu API przekłada się na *przepływ* performative wysyłane do usługi Service Bus przez klienta i service bus zużywa tego kredytu, biorąc pierwszy dostępny, odblokowany komunikat z kolejki, blokowanie go i przenoszenie go. Jeśli nie ma wiadomości łatwo dostępne do dostawy, wszelkie zaległe kredyty przez jakikolwiek związek ustanowiony z danym podmiotem pozostaje rejestrowane w kolejności przyjazdu, a wiadomości są zablokowane i przekazywane, gdy tylko staną się dostępne, aby wykorzystać wszelkie zaległe kredyty.

Blokada wiadomości jest zwalniana, gdy transfer jest rozliczany w jednym z *akceptowanych,* *odrzucanych*lub zwolnionych stanów *terminalowych.* Komunikat zostanie usunięty z usługi Service Bus po zaakceptowaniu stanu *terminala*. Pozostaje w usłudze Service Bus i jest dostarczany do następnego odbiorcy, gdy transfer osiągnie którykolwiek z pozostałych stanów. Usługa Service Bus automatycznie przenosi wiadomość do kolejki utraconych błędów jednostki, gdy osiągnie maksymalną liczbę dostarczenia dozwoloną dla jednostki z powodu powtarzających się odrzuceń lub zwolnień.

Mimo że interfejsy API usługi Service Bus nie udostępniają obecnie bezpośrednio takiej opcji, klient protokołu AMQP niższego poziomu może użyć modelu link-credit, aby przekształcić interakcję "pull-style" wystawiania jednej jednostki kredytu dla każdego żądania odbioru w modelu "push-style" przez wydawanie dużej liczby kredytów link, a następnie odbierać wiadomości, gdy stają się one dostępne bez dalszej interakcji. Wypychanie jest obsługiwane za pośrednictwem [messagingfactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) lub [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) ustawienia właściwości. Gdy są one niezerowe, klient USŁUGI AMQP używa go jako kredytu łącza.

W tym kontekście ważne jest, aby zrozumieć, że zegar wygaśnięcia blokady na komunikat wewnątrz jednostki rozpoczyna się, gdy wiadomość jest pobierana z jednostki, a nie, gdy wiadomość jest umieszczana w sieci. Za każdym razem, gdy klient wskazuje gotowość do odbierania wiadomości przez wystawienie kredytu łącza, w związku z tym oczekuje się, że aktywnie pobiera wiadomości w sieci i być gotowy do ich obsługi. W przeciwnym razie blokada wiadomości może wygasnąć przed dostarczeniem wiadomości. Korzystanie z kontroli przepływu przepływów kredytowych powinny bezpośrednio odzwierciedlać natychmiastową gotowość do radzenia sobie z dostępnymi komunikatami wysyłanych do odbiorcy.

Podsumowując następujące sekcje zawierają schematyczne omówienie przepływu wydajności podczas różnych interakcji interfejsu API. Każda sekcja opisuje inną operację logiczną. Niektóre z tych interakcji mogą być "leniwe", co oznacza, że mogą być wykonywane tylko wtedy, gdy jest to wymagane. Tworzenie nadawcy wiadomości nie może powodować interakcji sieciowej, dopóki nie zostanie wysłana lub zażądana pierwsza wiadomość.

Strzałki w poniższej tabeli przedstawiają kierunek przepływu wydajności.

#### <a name="create-message-receiver"></a>Tworzenie odbiornika wiadomości

| Klient | Service Bus |
| --- | --- |
| --> dołączyć(<br/>nazwa={nazwa łącza},<br/>dojścia={uchwyt numeryczny},<br/>rola =**odbiornik**,<br/>source={nazwa encji},<br/>target={identyfikator łącza klienta}<br/>) |Klient dołącza do jednostki jako odbiorca |
| Odpowiedzi usługi Service Bus dołączające jej koniec łącza |< - załączyć(<br/>nazwa={nazwa łącza},<br/>dojścia={uchwyt numeryczny},<br/>rola =**nadawca**,<br/>source={nazwa encji},<br/>target={identyfikator łącza klienta}<br/>) |

#### <a name="create-message-sender"></a>Tworzenie nadawcy wiadomości

| Klient | Service Bus |
| --- | --- |
| --> dołączyć(<br/>nazwa={nazwa łącza},<br/>dojścia={uchwyt numeryczny},<br/>rola =**nadawca**,<br/>source={identyfikator łącza klienta},<br/>cel={nazwa encji}<br/>) |Brak akcji |
| Brak akcji |< - załączyć(<br/>nazwa={nazwa łącza},<br/>dojścia={uchwyt numeryczny},<br/>rola =**odbiornik**,<br/>source={identyfikator łącza klienta},<br/>cel={nazwa encji}<br/>) |

#### <a name="create-message-sender-error"></a>Utwórz nadawcę wiadomości (błąd)

| Klient | Service Bus |
| --- | --- |
| --> dołączyć(<br/>nazwa={nazwa łącza},<br/>dojścia={uchwyt numeryczny},<br/>rola =**nadawca**,<br/>source={identyfikator łącza klienta},<br/>cel={nazwa encji}<br/>) |Brak akcji |
| Brak akcji |< - załączyć(<br/>nazwa={nazwa łącza},<br/>dojścia={uchwyt numeryczny},<br/>rola =**odbiornik**,<br/>źródło=null,<br/>cel=wartość null<br/>)<br/><br/><- odłączyć(<br/>dojścia={uchwyt numeryczny},<br/>zamknięte =**prawda**,<br/>error={informacje o błędzie}<br/>) |

#### <a name="close-message-receiversender"></a>Zamknij odbiorcę/nadawcę wiadomości

| Klient | Service Bus |
| --- | --- |
| --> odłączyć(<br/>dojścia={uchwyt numeryczny},<br/>zamknięte =**prawda**<br/>) |Brak akcji |
| Brak akcji |<- odłączyć(<br/>dojścia={uchwyt numeryczny},<br/>zamknięte =**prawda**<br/>) |

#### <a name="send-success"></a>Wyślij (sukces)

| Klient | Service Bus |
| --- | --- |
| --> transfer(<br/>identyfikator dostawy={uchwyt numeryczny},<br/>delivery-tag={uchwyt binarny},<br/>rozliczane =**false**,,more=**false**,<br/>stan =**null**,<br/>wznowienie =**false**<br/>) |Brak akcji |
| Brak akcji |<- dyspozycja(<br/>rola = odbiornik,<br/>first={identyfikator dostawy},<br/>last={identyfikator dostawy},<br/>rozliczane =**prawda**,<br/>stan =**zaakceptowany**<br/>) |

#### <a name="send-error"></a>Wyślij (błąd)

| Klient | Service Bus |
| --- | --- |
| --> transfer(<br/>identyfikator dostawy={uchwyt numeryczny},<br/>delivery-tag={uchwyt binarny},<br/>rozliczane =**false**,,more=**false**,<br/>stan =**null**,<br/>wznowienie =**false**<br/>) |Brak akcji |
| Brak akcji |<- dyspozycja(<br/>rola = odbiornik,<br/>first={identyfikator dostawy},<br/>last={identyfikator dostawy},<br/>rozliczane =**prawda**,<br/>stan =**odrzucony**(<br/>error={informacje o błędzie}<br/>)<br/>) |

#### <a name="receive"></a>Odbieranie

| Klient | Service Bus |
| --- | --- |
| --> przepływu(<br/>link-credit=1<br/>) |Brak akcji |
| Brak akcji |< transfer.<br/>identyfikator dostawy={uchwyt numeryczny},<br/>delivery-tag={uchwyt binarny},<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>stan =**null**,<br/>wznowienie =**false**<br/>) |
| --> dyspozycja(<br/>rola =**odbiornik**,<br/>first={identyfikator dostawy},<br/>last={identyfikator dostawy},<br/>rozliczane =**prawda**,<br/>stan =**zaakceptowany**<br/>) |Brak akcji |

#### <a name="multi-message-receive"></a>Odbiór wielu wiadomości

| Klient | Service Bus |
| --- | --- |
| --> przepływu(<br/>link-credit=3<br/>) |Brak akcji |
| Brak akcji |< transfer.<br/>identyfikator dostawy={uchwyt numeryczny},<br/>delivery-tag={uchwyt binarny},<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>stan =**null**,<br/>wznowienie =**false**<br/>) |
| Brak akcji |< transfer.<br/>identyfikator dostawy={uchwyt numeryczny+1},<br/>delivery-tag={uchwyt binarny},<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>stan =**null**,<br/>wznowienie =**false**<br/>) |
| Brak akcji |< transfer.<br/>identyfikator dostawy={uchwyt numeryczny+2},<br/>delivery-tag={uchwyt binarny},<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>stan =**null**,<br/>wznowienie =**false**<br/>) |
| --> dyspozycja(<br/>rola = odbiornik,<br/>first={identyfikator dostawy},<br/>last={identyfikator dostawy+2},<br/>rozliczane =**prawda**,<br/>stan =**zaakceptowany**<br/>) |Brak akcji |

### <a name="messages"></a>Komunikaty

W poniższych sekcjach wyjaśniono, które właściwości ze standardowych sekcji komunikatów USŁUGI AMQP są używane przez usługę Service Bus i jak są mapowane do zestawu interfejsu API usługi Service Bus.

Każda właściwość, którą aplikacja musi zdefiniować, powinna `application-properties` być mapowana na mapę usługi AMQP.

#### <a name="header"></a>nagłówek

| Nazwa pola | Sposób użycia | Nazwa interfejsu API |
| --- | --- | --- |
| Trwałe |- |- |
| priority |- |- |
| czas wygaśnięcia |Czas żyć dla tej wiadomości |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| pierwszy nabywca |- |- |
| licznik dostaw |- |[Licznik dostaw](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Nazwa pola | Sposób użycia | Nazwa interfejsu API |
| --- | --- | --- |
| identyfikator wiadomości |Zdefiniowany przez aplikację identyfikator swobodny dla tej wiadomości. Służy do wykrywania duplikatów. |[Messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identyfikator użytkownika zdefiniowany przez aplikację, nieointerpretowany przez usługę Service Bus. |Niedostępne za pośrednictwem interfejsu API usługi Service Bus. |
| na |Identyfikator miejsca docelowego zdefiniowany przez aplikację, nieointerpretowany przez usługę Service Bus. |[Do](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Temat |Identyfikator celu wiadomości zdefiniowany przez aplikację, nieointerpretowany przez usługę Service Bus. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| odpowiedź na |Zdefiniowany w aplikacji wskaźnik ścieżki odpowiedzi, nieointerpretowany przez usługę Service Bus. |[Replyto](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Identyfikator korelacji zdefiniowany przez aplikację, nieointerpretowany przez usługę Service Bus. |[Correlationid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| typ zawartości |Zdefiniowany w aplikacji wskaźnik zawartości dla treści, nie jest interpretowany przez usługę Service Bus. |[Contenttype](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| kodowanie zawartości |Zdefiniowany w aplikacji wskaźnik kodowania zawartości dla treści, nie jest interpretowany przez usługę Service Bus. |Niedostępne za pośrednictwem interfejsu API usługi Service Bus. |
| bezwzględny czas wygaśnięcia |Deklaruje, w którym bezwzględny komunikat wygasa. Ignorowane na dane wejściowe (nagłówek TTL jest przestrzegane), autorytatywne na wyjściu. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| czas tworzenia |Deklaruje, w którym czasie wiadomość została utworzona. Nie używane przez usługę Service Bus |Niedostępne za pośrednictwem interfejsu API usługi Service Bus. |
| identyfikator grupy |Identyfikator zdefiniowany przez aplikację dla powiązanego zestawu komunikatów. Używane do sesji usługi Service Bus. |[Sessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| sekwencja grupowa |Licznik identyfikujący względny numer sekwencyjny wiadomości wewnątrz sesji. Ignorowane przez usługę Service Bus. |Niedostępne za pośrednictwem interfejsu API usługi Service Bus. |
| identyfikator odpowiedzi na grupę |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Adnotacje wiadomości

Istnieje kilka innych właściwości komunikatu magistrali usług, które nie są częścią właściwości `MessageAnnotations` komunikatu AMQP i są przekazywane zgodnie z komunikatem.

| Klucz mapy adnotacji | Sposób użycia | Nazwa interfejsu API |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time x-opt-scheduled-enqueue-time x-opt-scheduled-enqueue-time x- | Deklaruje, w którym czasie komunikat powinien pojawić się w jednostce |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Klucz zdefiniowany przez aplikację, który decyduje o partycji, w której powinna wylądować wiadomość. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Zdefiniowana przez aplikację wartość klucza partycji, gdy transakcja ma być używana do wysyłania wiadomości za pośrednictwem kolejki transferu. | [ViaPartitionKey (Klucz ViaPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time x-opt-enqueued-time x-opt-enqueued-time x- | Zdefiniowany przez usługę czas UTC reprezentujący rzeczywisty czas wyłudzania wiadomości. Ignorowane na danych wejściowych. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sekwencja-numer | Unikatowy numer zdefiniowany przez usługę przypisany do wiadomości. | [Sequencenumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Zdefiniowany przez usługę numer sekwencyjny wiadomości w kolejce. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-locked-do | Zdefiniowane w usłudze. Data i godzina, do której wiadomość zostanie zablokowana w kolejce/subskrypcji. | [ZablokowaneUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-źródło | Zdefiniowana przez usługę. Jeśli wiadomość zostanie odebrana z kolejki utraconych wiadomości, źródło oryginalnej wiadomości. | [Źródło deadlettersource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Możliwość transakcji

Transakcja grupuje razem co najmniej dwie operacje w zakresie wykonania. Z natury taka transakcja musi zapewnić, że wszystkie operacje należące do danej grupy operacji albo odnieść sukces, albo zakończyć się niepowodzeniem.
Operacje są pogrupowane według `txn-id`identyfikatora .

W przypadku interakcji transakcyjnej `transaction controller` klient działa jako program , który kontroluje operacje, które powinny być zgrupowane. Usługa Service Bus `transactional resource` Service działa jako a i `transaction controller`wykonuje pracę zgodnie z życzeniem .

Klient i usługa komunikują się za pomocą `control link` , który jest ustanowiony przez klienta. Wiadomości `declare` `discharge` i są wysyłane przez kontrolera za pomocą łącza kontroli, aby przydzielić i zakończyć transakcje odpowiednio (nie reprezentują one rozgraniczenia pracy transakcyjnej). Rzeczywiste wysyłanie/odbieranie nie jest wykonywane na tym łączu. Każda żądana operacja transakcyjna jest jawnie identyfikowana z żądanym `txn-id` i dlatego może wystąpić na dowolnym łączu w połączeniu. Jeśli łącze formantu jest zamknięte, gdy istnieją nierozładowane transakcje, które utworzono, wszystkie takie transakcje są natychmiast przywracane, a próby wykonania dalszych prac transakcyjnych na nich doprowadzi do niepowodzenia. Wiadomości na łączu kontrolnym nie mogą być wstępnie rozliczane.

Każde połączenie musi zainicjować własne łącze sterowania, aby móc rozpoczynać i kończyć transakcje. Usługa definiuje specjalny cel, który `coordinator`działa jako . Klient/kontroler ustanawia łącze sterujące do tego obiektu docelowego. Łącze sterowania znajduje się poza granicą jednostki, czyli tego samego łącza kontroli może służyć do inicjowania i rozładowywania transakcji dla wielu jednostek.

#### <a name="starting-a-transaction"></a>Rozpoczynanie transakcji

Aby rozpocząć pracę transakcyjną. kontroler musi uzyskać `txn-id` od koordynatora. Robi to, wysyłając `declare` wiadomość typu. Jeśli deklaracja zakończy się powodzeniem, koordynator odpowiada z wynikiem dyspozycji, która niesie przypisany `txn-id`.

| Klient (kontroler) | | Linia autobusowa (koordynator) |
| --- | --- | --- |
| dołączyć(<br/>nazwa={nazwa łącza},<br/>... ,<br/>rola =**nadawca**,<br/>cel =**Koordynator**<br/>) | ------> |  |
|  | <------ | dołączyć(<br/>nazwa={nazwa łącza},<br/>... ,<br/>target=Koordynator()<br/>) |
| (np.<br/>dostawa-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | dyspozycji( <br/> pierwszy =0, ostatni=0, <br/>stan =**Zadeklarowany**(<br/>**txn-id**={identyfikator transakcji}<br/>))|

#### <a name="discharging-a-transaction"></a>Rozładowywanie transakcji

Administrator kończy pracę transakcyjną, `discharge` wysyłając wiadomość do koordynatora. Kontroler wskazuje, że chce zatwierdzić lub wycofać pracę transakcyjną, ustawiając flagę `fail` na treści absolutorium. Jeżeli koordynator nie jest w stanie ukończyć absolutorium, `transaction-error`wiadomość zostaje odrzucona z tym wynikiem niosąc .

> Uwaga: fail=true odnosi się do wycofywania transakcji, a fail=false odnosi się do commit.

| Klient (kontroler) | | Linia autobusowa (koordynator) |
| --- | --- | --- |
| (np.<br/>dostawa-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | dyspozycji( <br/> pierwszy =0, ostatni=0, <br/>stan=Zadeklarowany(<br/>txn-id={identyfikator transakcji}<br/>))|
| | . . . <br/>Praca transakcyjna<br/>na innych linkach<br/> . . . |
| (np.<br/>dostawa-id=57, ...)<br/>{ AmqpValue (<br/>**Rozładowanie(txn-id=0,<br/>fail=false)**)}| ------> |  |
| | <------ | dyspozycji( <br/> pierwszy =57, ostatni=57, <br/>stan =**Zaakceptowane()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Wysyłanie wiadomości w transakcji

Cała praca transakcyjna jest wykonywana `transactional-state` przy stanie dostarczania transakcyjnego, który przenosi txn-id. W przypadku wysyłania wiadomości, stan transakcyjny jest przenoszony przez ramkę transferu wiadomości. 

| Klient (kontroler) | | Linia autobusowa (koordynator) |
| --- | --- | --- |
| (np.<br/>dostawa-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | dyspozycji( <br/> pierwszy =0, ostatni=0, <br/>stan=Zadeklarowany(<br/>txn-id={identyfikator transakcji}<br/>))|
| (np.<br/>uchwyt =1,<br/>dostawa-id=1, <br/>**stan<br/>= TransactionalState(<br/>txn-id=0)**) )<br/>{ ładunek }| ------> |  |
| | <------ | dyspozycji( <br/> pierwszy=1, ostatni=1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Usuwanie wiadomości w transakcji

Dyspozycja wiadomości obejmuje `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`operacje, takie jak . Aby wykonać te operacje w `transactional-state` ramach transakcji, przekazać z dyspozycji.

| Klient (kontroler) | | Linia autobusowa (koordynator) |
| --- | --- | --- |
| (np.<br/>dostawa-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | dyspozycji( <br/> pierwszy =0, ostatni=0, <br/>stan=Zadeklarowany(<br/>txn-id={identyfikator transakcji}<br/>))|
| | <------ |(np.<br/>uchwyt =2,<br/>dostawa-id=11, <br/>stan=null)<br/>{ ładunek }|  
| dyspozycji( <br/> pierwszy=11, ostatni=11, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Zaawansowane możliwości magistrali usług

W tej sekcji opisano zaawansowane możliwości usługi Azure Service Bus, które są oparte na wersjach roboczych rozszerzeń protokołu AMQP, obecnie opracowywanych w Oasis Technical Committee for AMQP. Usługa Service Bus implementuje najnowsze wersje tych wersji roboczych i przyjmuje zmiany wprowadzone w miarę osiągania stanu standardu w wersji tych wersji.

> [!NOTE]
> Zaawansowane operacje usługi Service Bus Messaging są obsługiwane za pośrednictwem wzorca żądania/odpowiedzi. Szczegóły tych operacji są opisane w artykule [AMQP 1.0 w usłudze Service Bus: operacje oparte na żądaniu odpowiedzi.](service-bus-amqp-request-response.md)
> 
> 

### <a name="amqp-management"></a>Zarządzanie AMQP

Specyfikacja zarządzania amqp jest pierwszym z projektów rozszerzeń omówionych w tym artykule. Ta specyfikacja definiuje zestaw protokołów warstwowych na górze protokołu AMQP, które umożliwiają interakcje zarządzania z infrastrukturą obsługi wiadomości za pośrednictwem protokołu AMQP. Specyfikacja definiuje operacje ogólne, takie jak *tworzenie,* *odczytywanie,* *aktualizowanie*i *usuwanie* dla zarządzania jednostkami wewnątrz infrastruktury obsługi wiadomości i zestaw operacji kwerend.

Wszystkie te gesty wymagają interakcji żądania/odpowiedzi między klientem a infrastrukturą obsługi wiadomości, dlatego specyfikacja określa sposób modelowania tego wzorca interakcji na podstawie protokołu AMQP: klient łączy się z infrastrukturą obsługi wiadomości, inicjuje sesję, a następnie tworzy parę łączy. Na jednym łączu klient działa jako nadawca, a z drugiej działa jako odbiorca, tworząc w ten sposób parę łączy, które mogą działać jako kanał dwukierunkowy.

| Operacja logiczna | Klient | Service Bus |
| --- | --- | --- |
| Utwórz ścieżkę odpowiedzi żądania |--> dołączyć(<br/>nazwa={*nazwa łącza*},<br/>uchwyt={*uchwyt numeryczny*},<br/>rola =**nadawca**,<br/>źródło =**null**,<br/>target="myentity/$management"<br/>) |Brak akcji |
| Utwórz ścieżkę odpowiedzi żądania |Brak akcji |\<-- dołącz(<br/>nazwa={*nazwa łącza*},<br/>uchwyt={*uchwyt numeryczny*},<br/>rola =**odbiornik**,<br/>źródło=null,<br/>target="myentity"<br/>) |
| Utwórz ścieżkę odpowiedzi żądania |--> dołączyć(<br/>nazwa={*nazwa łącza*},<br/>uchwyt={*uchwyt numeryczny*},<br/>rola =**odbiornik**,<br/>źródło="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Utwórz ścieżkę odpowiedzi żądania |Brak akcji |\<-- dołącz(<br/>nazwa={*nazwa łącza*},<br/>uchwyt={*uchwyt numeryczny*},<br/>rola =**nadawca**,<br/>source="myentity",<br/>target="myclient$id"<br/>) |

Mając tę parę łączy w miejscu, implementacja żądania/odpowiedzi jest prosta: żądanie jest komunikatem wysyłanym do jednostki wewnątrz infrastruktury obsługi wiadomości, która rozumie ten wzorzec. W tej wiadomości żądania pola *odpowiedzi do* sekcji właściwości jest ustawiona na identyfikator *docelowy* dla łącza, do którego można dostarczyć odpowiedzi. *target* Jednostka obsługi przetwarza żądanie, a następnie dostarcza odpowiedź za pomocą łącza, którego identyfikator *docelowy* jest zgodny z wskazanym *identyfikatorem odpowiedzi.*

Wzorzec oczywiście wymaga, aby kontener klienta i identyfikator wygenerowany przez klienta dla miejsca docelowego odpowiedzi były unikatowe dla wszystkich klientów i ze względów bezpieczeństwa również trudne do przewidzenia.

Wymiany komunikatów używane dla protokołu zarządzania i dla wszystkich innych protokołów, które używają tego samego wzorca, mają miejsce na poziomie aplikacji; nie definiują nowych gestów na poziomie protokołu AMQP. Jest to zamierzone, dzięki czemu aplikacje mogą natychmiast korzystać z tych rozszerzeń ze zgodnymi stosami AMQP 1.0.

Usługa Service Bus nie implementuje obecnie żadnej z podstawowych funkcji specyfikacji zarządzania, ale wzorzec żądania/odpowiedzi zdefiniowany przez specyfikację zarządzania ma zasadnicze podstawy dla funkcji zabezpieczeń opartych na oświadczeniach i dla prawie wszystkich zaawansowanych możliwości omówionych w następujących sekcjach:

### <a name="claims-based-authorization"></a>Autoryzacja oparta na oświadczeniach

Projekt specyfikacji oświadczeń opartych na oświadczeniach (CBS) zawiera kompilacje na wzorcu żądania/odpowiedzi specyfikacji zarządzania i opisuje uogólniony model używania tokenów zabezpieczających federacyjnego z usługą AMQP.

Domyślny model zabezpieczeń usługi AMQP omówione we wstępie opiera się na SASL i integruje się z uzgadniania połączenia AMQP. Korzystanie z SASL ma tę zaletę, że zapewnia rozszerzalny model, dla którego zdefiniowano zestaw mechanizmów, z których każdy protokół, który formalnie opiera się na SASL może korzystać. Wśród tych mechanizmów są "PLAIN" do transferu nazw użytkowników i haseł, "EXTERNAL" do powiązania z zabezpieczeniami na poziomie TLS, "ANONIMOWY", aby wyrazić brak jawnego uwierzytelniania/autoryzacji i szeroką gamę dodatkowych mechanizmów, które umożliwiają przekazywanie uwierzytelniania i/lub autoryzacji poświadczeń lub tokenów.

Integracja SASL AMQP ma dwie wady:

* Wszystkie poświadczenia i tokeny są ograniczone do połączenia. Infrastruktura obsługi wiadomości może chcieć zapewnić zróżnicowaną kontrolę dostępu dla dla jednostki; na przykład zezwalając nośnikowi tokenu na wysłanie do kolejki A, ale nie do kolejki B. Z kontekstu autoryzacji zakotwiczone w połączeniu, nie jest możliwe użycie jednego połączenia, a jednak użyć różnych tokenów dostępu dla kolejki A i kolejki B.
* Tokeny dostępu są zazwyczaj ważne tylko przez ograniczony czas. Ta ważność wymaga od użytkownika okresowego ponownego ponownego zezwalania tokenów i umożliwia wystawcy tokenu odmowę wydania nowego tokenu, jeśli uprawnienia dostępu użytkownika uległy zmianie. Połączenia AMQP mogą trwać przez dłuższy czas. Model SASL zapewnia tylko możliwość ustawienia tokenu w czasie połączenia, co oznacza, że infrastruktura obsługi wiadomości musi albo odłączyć klienta po wygaśnięciu tokenu, albo musi zaakceptować ryzyko umożliwienia dalszej komunikacji z klientem, który jest prawa dostępu mogły zostać cofnięte w międzyczasie.

Specyfikacja programu AMQP CBS, zaimplementowana przez usługę Service Bus, umożliwia eleganckie obejście obu tych problemów: umożliwia klientowi skojarzenie tokenów dostępu z każdym węzłem i zaktualizowanie tych tokenów przed ich wygaśnięciem, bez przerywania przepływu wiadomości.

PROGRAM CBS definiuje węzeł zarządzania wirtualnego o nazwie *$cbs*, który ma być dostarczany przez infrastrukturę obsługi wiadomości. Węzeł zarządzania akceptuje tokeny w imieniu innych węzłów w infrastrukturze obsługi wiadomości.

Gest protokołu jest wymianą żądań/odpowiedzi zgodnie ze specyfikacją zarządzania. Oznacza to, że klient ustanawia parę łączy z węzłem *$cbs,* a następnie przekazuje żądanie w łączu wychodzącym, a następnie czeka na odpowiedź w łączu przychodzącym.

Komunikat żądania ma następujące właściwości aplikacji:

| Klucz | Optional (Opcjonalność) | Typ wartości | Zawartość wartości |
| --- | --- | --- | --- |
| Operacji |Nie |ciąg |**token put-token** |
| type |Nie |ciąg |Typ tokenu, który jest umieszczany. |
| name |Nie |ciąg |"Odbiorca", do którego ma zastosowanie token. |
| Wygaśnięcia |Tak |sygnatura czasowa |Czas wygaśnięcia tokenu. |

Właściwość *name* identyfikuje jednostkę, z którą token jest skojarzony. W usłudze Service Bus jest to ścieżka do kolejki lub temat/subskrypcja. Właściwość *type* identyfikuje typ tokenu:

| Typ tokenu | Opis tokenu | Typ korpusu | Uwagi |
| --- | --- | --- | --- |
| amqp:jwt |Token JSON w sieci Web (JWT) |Wartość AMQP (ciąg) |Nie jest jeszcze dostępna. |
| amqp:swt |Prosty token internetowy (SWT) |Wartość AMQP (ciąg) |Obsługiwane tylko dla tokenów SWT wystawionych przez AAD/ACS |
| usługabus.windows.net:sastoken |Token SAS usługi magistrali usług |Wartość AMQP (ciąg) |- |

Tokeny przyznają prawa. Usługa Service Bus wie o trzech podstawowych prawach: "Wyślij" umożliwia wysyłanie, "Nasłuchiwanie" umożliwia odbieranie, a "Manage" umożliwia manipulowanie jednostkami. Tokeny SWT wystawione przez AAD/ACS wyraźnie zawierają te prawa jako roszczenia. Tokeny Sygnatury dostępu Współdzielonego usługi Service Bus odnoszą się do reguł skonfigurowanych w obszarze nazw lub encji, a te reguły są skonfigurowane z prawami. Podpisywanie tokenu za pomocą klucza skojarzonego z tą regułą sprawia, że token wyraża odpowiednie prawa. Token skojarzony z jednostką przy użyciu *put-token* pozwala połączonego klienta do interakcji z jednostką na prawa tokenu. Łącze, w którym klient przejmuje rolę *nadawcy,* wymaga prawa "Wyślij"; przyjęcie roli *odbiornika* wymaga prawa "Słuchaj".

Komunikat odpowiedzi ma następujące wartości *właściwości aplikacji*

| Klucz | Optional (Opcjonalność) | Typ wartości | Zawartość wartości |
| --- | --- | --- | --- |
| kod stanu |Nie |int |Kod odpowiedzi HTTP **[RFC2616]**. |
| opis stanu |Tak |ciąg |Opis stanu. |

Klient może wielokrotnie wywoływać *put-token* i dla dowolnej jednostki w infrastrukturze obsługi wiadomości. Tokeny są ograniczone do bieżącego klienta i zakotwiczone w bieżącym połączeniu, co oznacza, że serwer porzuca wszystkie zatrzymane tokeny po upuszczeniu połączenia.

Bieżąca implementacja usługi Service Bus zezwala tylko na program CBS w połączeniu z metodą SASL "ANONIMOWY". Połączenie SSL/TLS musi zawsze istnieć przed uzgadniania SASL.

Mechanizm ANONYMOUS musi być zatem obsługiwany przez wybranego klienta AMQP 1.0. Dostęp anonimowy oznacza, że uzgadnianie połączenia początkowego, w tym tworzenie sesji początkowej odbywa się bez usługi Service Bus wiedząc, kto tworzy połączenie.

Po ustanowieniu połączenia i sesji dołączanie łączy do węzła *$cbs* i wysyłanie żądania *put-token* są jedynymi dozwolonymi operacjami. Prawidłowy token musi zostać pomyślnie ustawiony przy użyciu żądania *put-token* dla niektórych węzłów jednostki w ciągu 20 sekund po nawiązaniu połączenia, w przeciwnym razie połączenie jest jednostronnie porzucane przez usługę Service Bus.

Klient jest następnie odpowiedzialny za śledzenie wygaśnięcia tokenu. Po wygaśnięciu tokenu usługa Service Bus niezwłocznie porzuca wszystkie łącza w połączeniu z odpowiednią encją. Aby zapobiec występowaniu problemów, klient może zastąpić token dla węzła nowym w dowolnym momencie za pośrednictwem wirtualnego węzła zarządzania *$cbs* tym samym gestem *put-token* i bez przeszkadzania w ruchu ładunku, który przepływa na różnych łączach.

### <a name="send-via-functionality"></a>Funkcja wysyłania przez

[Nadawca send-via / Transfer](service-bus-transactions.md#transfers-and-send-via) to funkcja, która umożliwia przekierowywać magistralę usługi do danej jednostki docelowej za pośrednictwem innej jednostki. Ta funkcja służy do wykonywania operacji między jednostkami w jednej transakcji.

Dzięki tej funkcji należy utworzyć nadawcę i ustanowić łącze do pliku `via-entity`. Podczas ustanawiania łącza przekazywane są dodatkowe informacje w celu ustalenia prawdziwego miejsca docelowego wiadomości/transferów w tym łączu. Po pomyślnym zakończeniu dołączania wszystkie wiadomości wysłane w tym łączu są automatycznie przekazywane do *jednostki docelowej* *za pośrednictwem encji*. 

> Uwaga: Uwierzytelnianie musi być wykonywane zarówno *dla jednostki za pośrednictwem,* jak i *encji docelowej* przed ustanowieniem tego łącza.

| Klient | | Service Bus |
| --- | --- | --- |
| dołączyć(<br/>nazwa={nazwa łącza},<br/>rola=nadawca,<br/>source={identyfikator łącza klienta},<br/>cel=**{via-entity}**,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | dołączyć(<br/>nazwa={nazwa łącza},<br/>rola = odbiornik,<br/>source={identyfikator łącza klienta},<br/>target={via-entity},<br/>właściwości=mapa [(<br/>com.microsoft:transfer-miejsce docelowe-adres=<br/>{jednostka docelowa} )] ) |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o amqp, odwiedź następujące linki:

* [Omówienie usługi AMQP usługi Service Bus]
* [Obsługa usługi AMQP 1.0 dla kolejek i tematów podzielonych na partycje usługi Service Bus]
* [Usługa AMQP w magistrali usług dla systemu Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Omówienie usługi AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Obsługa usługi AMQP 1.0 dla kolejek i tematów podzielonych na partycje usługi Service Bus]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[Usługa AMQP w magistrali usług dla systemu Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
