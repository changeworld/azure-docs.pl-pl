---
title: Protokołu AMQP 1.0 w usłudze Azure Service Bus i Event Hubs przewodnik dotyczący protokołu | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący protokołu wyrażeń i opis protokołu AMQP 1.0 w usłudze Azure Service Bus i Event Hubs
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
ms.openlocfilehash: c99f4491af8fe3e5f0f0ed7a264995ae3ec5911f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749448"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Protokołu AMQP 1.0 w przewodnik dotyczący protokołu usługi Azure Service Bus i Event Hubs

1.0 protokołu zaawansowane kolejkowanie komunikatów jest standardowy protokół ramek i transferu dla asynchronicznie, bezpieczną i niezawodną transferu wiadomości między dwiema stronami. Jest to podstawowy protokół komunikatów usługi Azure Service Bus i usługi Azure Event Hubs. Obie te usługi obsługują także protokół HTTPS. Własności protokołu SBMP, który jest również obsługiwany jest wycofywane na rzecz protokołu AMQP.

Protokołu AMQP 1.0 jest wynikiem współpracy szerokiego branży, który o nazwie vhacks dostawców oprogramowania pośredniczącego, takich jak Microsoft i Red Hat obejmującej wielu użytkowników oprogramowania pośredniczącego obsługi komunikatów, takie jak JP Morgan Chase reprezentujący branży usług finansowych. Forum techniczne normalizacji specyfikacje rozszerzenie i protokół AMQP jest OASIS i osiągnięcia formalne zatwierdzania międzynarodowego standardu jako 19494 ISO/IEC.

## <a name="goals"></a>Cele

W tym artykule krótko podsumowano podstawowe pojęcia dotyczące protokołu AMQP 1.0 komunikatów specyfikacji oraz niewielki zestaw oceniać robocze specyfikacje rozszerzenia, które są aktualnie kończy się działanie Komitet Techniczny AMQP języka OASIS oraz wyjaśniono, jak usługa Azure Service Bus implementuje i opiera się na tych specyfikacji.

Celem jest dla każdego dewelopera, aby można było korzystać z usługi Azure Service Bus za pośrednictwem protokołu AMQP 1.0 przy użyciu dowolnego istniejącego stosu klienta protokołu AMQP 1.0 na dowolnej platformie.

Typowe ogólnego przeznaczenia stosów protokołu AMQP 1.0, takich jak Apache protonów lub AMQP.NET Lite zawierają już implementację wszystkich protokołów core protokołu AMQP 1.0. Te podstawowe gestów czasami są ujęte w nawiasy API wyższego poziomu; Protonów Apache nawet oferuje dwa imperatywne API Messenger i reaktywne API reaktora.

W poniższym dyskusji przyjęto założenie, zarządzania połączenia AMQP, sesje i łączy i obsługa transferów ramki i sterowanie przepływem są obsługiwane przez odpowiednie stosu (np. Apache protonów-C) i nie wymagają dużo, jeśli istnieje szczególną uwagę od deweloperów aplikacji. Przyjęto założenie, abstrakcyjnie istnienie kilka interfejsów API podstawowych takich jak możliwość nawiązywania połączenia, a także tworzenie pewnego rodzaju *nadawcy* i *odbiorcy* abstrakcji obiektów, który następnie mają pewne kształt `send()` i `receive()` operacji, odpowiednio.

Podczas omawiania zaawansowane możliwości usługi Azure Service Bus, takie jak przeglądanie komunikatów lub zarządzania sesjami, te funkcje zostały wyjaśnione w warunkach protokołu AMQP, ale także jako warstwowej pseudo-wdrożenia na podstawie tego zakładanego abstrakcji interfejsu API.

## <a name="what-is-amqp"></a>Co to jest AMQP?

Protokół AMQP jest protokołem ramek i transferu. Ramek oznacza, że zapewnia strukturę strumieniom danych binarnych, które będą działać w dowolnym kierunku połączenia sieciowego. Struktura zapewnia nakreślenia odrębnych bloków danych, nazywane *ramek*, aby być wymieniane między stronami w połączonych. Możliwości przeniesienia, upewnij się, obie strony komunikujące się ustanowić udostępnione opis po ramki są przenoszone i podczas transferu uznaje się pełne informacje.

W przeciwieństwie do wersji wcześniej wygasłe projekt produkowane przez grupę roboczą protokołu AMQP, które są nadal używane przez kilku brokerami protokołu AMQP 1.0 ostateczne i standardowych grupy roboczej nie określają obecność brokera komunikatów lub dowolnego określonego topologii jednostki wewnątrz brokera komunikatów.

Protokół może służyć do symetryczne komunikacji peer-to-peer, interakcji z brokerami, które obsługują kolejek i publikowania/subskrybowania jednostek, jak usługi Azure Service Bus. Może również służyć interakcji z infrastrukturą obsługi wiadomości gdzie wzorce interakcji różnią się od regularnych kolejki, tak jak w przypadku za pomocą usługi Azure Event Hubs. Centrum zdarzeń działa podobnie jak kolejka, gdy zdarzenia są wysyłane do niego, ale działa więcej takich jak usługi serial magazynu, gdy zdarzenia są odczytywane z jest on nieco podobny stacji taśm. Klient wybiera przesunięcie w strumieniu danych o dostępności i jest następnie udostępniany wszystkie zdarzenia z tego przesunięcie na najnowszy dostępny.

Protokół AMQP 1.0 jest zaprojektowane jako extensible umożliwiające dalsze specyfikacji w celu zwiększenia jego możliwości. Specyfikacje trzy rozszerzenia omówionych w tym dokumencie przedstawiono to. Do komunikacji w ramach istniejącej infrastruktury protokołu HTTPS/Websocket Konfigurowanie natywnej portów TCP protokołu AMQP może być trudne. Specyfikacja powiązania definiuje sposób warstwy protokołu AMQP przez Websocket. Do interakcji z infrastrukturą obsługi wiadomości w czasie żądania/odpowiedzi do celów zarządzania lub umożliwiają korzystanie z zaawansowanych funkcji, specyfikacji protokołu AMQP zarządzania definiuje prymitywów wymagane interakcji podstawowe. Dla integracji modelu federacyjnej autoryzacji Specyfikacja oświadczeń — zabezpieczenia na poziomie protokołu AMQP definiuje, jak skojarzyć i odnowić tokenach autoryzacji skojarzone z łącza.

## <a name="basic-amqp-scenarios"></a>Podstawowe scenariusze protokołu AMQP

W tej sekcji opisano podstawowe użycie protokołu AMQP 1.0 przy użyciu usługi Azure Service Bus, w tym tworzenie połączeń, sesje i łącza i transferu wiadomości do i z jednostek usługi Service Bus, takich jak kolejki, tematy i subskrypcje.

Najbardziej autorytatywne źródło, aby dowiedzieć się więcej o tym, jak działa protokół AMQP jest specyfikacji protokołu AMQP 1.0, ale specyfikację został napisany dokładnie Przewodnik wdrożenia, a nie uczyć protokołu. Ta sekcja koncentruje się na wprowadzenie tyle terminologii, zgodnie z potrzebami do opisywania, jak Usługa Service Bus używa protokołu AMQP 1.0. Aby uzyskać bardziej szczegółowe wprowadzenie do protokołu AMQP, jak również szersze omówienie protokołu AMQP 1.0, możesz przejrzeć [ten kurs wideo][this video course].

### <a name="connections-and-sessions"></a>Połączenia i sesji

AMQP wywołuje programy komunikujące się *kontenery*; te zawierają *węzłów*, które są jednostkami komunikujące się wewnątrz tych kontenerów. Kolejka może być takim węzłem. Protokół AMQP umożliwia Multipleksowanie, dzięki czemu jedno połączenie może służyć do wielu ścieżek komunikacji między węzłami. na przykład klienta aplikacji jednocześnie może odbierać z jednej kolejki i wysyłać do kolejnej kolejki za pośrednictwem tego samego połączenia sieciowego.

![][1]

Połączenie sieciowe jest tym samym zakotwiczone w kontenerze. Jest on inicjowane przez kontener w roli klienta, dzięki czemu wychodzące połączenie gniazda TCP do kontenera w roli odbiornik nasłuchuje i akceptuje połączenia przychodzące TCP. Uzgadnianie połączenia zawiera negocjowania wersję protokołu, zgłaszając lub negocjowania protokołu Transport Level Security (TLS/SSL) a uzgadnianie uwierzytelnianie i autoryzacja w zakresie połączenia, który jest oparty na SASL.

Usługa Azure Service Bus wymaga użycia protokołu TLS przez cały czas. Obsługuje połączenia za pośrednictwem portu TCP 5671, według którego połączenia TCP jest najpierw nałożony z protokołem TLS przed wejściem uzgadnianie protokołu AMQP i obsługuje połączenia za pośrednictwem portu TCP 5672 zgodnie z którą serwer natychmiast oferuje wymagane uaktualnienie połączenia protokołu TLS używa modelu ustalonym protokołu AMQP. Powiązanie funkcji WebSockets AMQP tworzy tunel za pośrednictwem portu TCP 443, który następnie jest odpowiednikiem połączeń AMQP 5671.

Po skonfigurowaniu połączenia i TLS, Service Bus oferuje dwie opcje mechanizmu SASL:

* ZWYKŁY SASL jest powszechnie używany do przekazywania poświadczeń użytkownika i hasło do serwera. Usługa Service Bus nie ma konta, ale o nazwie [zasady zabezpieczenia dostępu do udostępnionych](service-bus-sas.md), która przyznaje prawa i skojarzone z kluczem. Nazwa reguły jest używana jako nazwa użytkownika i klucza (zgodnie z algorytmem base64 tekstu) jest używany jako hasło. Praw skojarzonych z wybranym reguły określające dozwolone połączenia operacje.
* SASL ANONIMOWEGO jest używana do pomijanie SASL autoryzacji, gdy klient chce używać model (CBS) oświadczenia — zabezpieczenia na poziomie, który jest opisany w dalszej części. Po wybraniu tej opcji, mogą anonimowo ustanowić połączenia klienta przez krótki czas, w którym klient może komunikować się tylko z punktem końcowym CBS, wykonać uzgadnianie CBS.

Po nawiązaniu połączenia transportowego kontenery każdego zadeklarować maksymalny rozmiar ramki są one gotowy obsłużyć, a po upłynięciu limitu czasu bezczynności one będzie jednostronnie Rozłącz, jeśli nie ma działania w ramach połączenia.

Które deklarują, jak wiele równoczesnych kanały są obsługiwane. Kanał jest ścieżką jednokierunkowe, ruchu wychodzącego, wirtualnej transferu na podstawie połączenia. Sesja trwa kanału z każdego z połączonych kontenerów w celu utworzenia ścieżki komunikacji dwukierunkowej.

Sesje mają model sterowania przepływem na podstawie okna; Po utworzeniu sesji każdej ze stron oświadcza, ile klatek jest gotowy do akceptowania do jej okna odbierania. Jak ramki exchange strony, wypełnienie przeniesionych ramek, że okno i transfery zatrzymany po zapełnieniu okna i dopóki okno uzyska resetowania lub używając rozwinięte *przepływ performative* (*performative* jest Protokół AMQP okres gestów na poziomie protokołu wymieniane między dwiema stronami).

Ten model oparty na oknach jest z grubsza podobny do koncepcji TCP sterowaniu przepływem na podstawie okna, ale na poziomie sesji w gniazda. Koncepcja protokołu zezwalanie na wiele jednoczesnych sesji istnieje tak, aby ruch o wysokim priorytecie, który może presją ostatnie ograniczone normalnym ruchu, takich jak na drogach lane express.

Usługa Azure Service Bus używa obecnie dokładnie jednej sesji dla każdego połączenia. Usługa Service Bus maksymalny ramki — rozmiar wynosi 262 144 bajty (w bajtach 256 KB) dla usługi Service Bus wersji Standard i centrów zdarzeń. Jest 1 048 576 (1 MB) dla usługi Service Bus w warstwie Premium. Usługa Service Bus nie nakłada żadnych określonego poziomu sesji ograniczania systemu windows, ale regularnie resetuje okna jako część sterowanie przepływem na poziomie łącza (zobacz [następnej sekcji](#links)).

Połączenia, kanałów i sesje są tymczasowych. Jeśli połączenie podstawowe zwija połączeń, musi wznawiana tunelu TLS, kontekst autoryzacji SASL i sesji.

### <a name="links"></a>Linki

Protokół AMQP przesyłanie komunikatów za pośrednictwem łącza. Ścieżka komunikacji, utworzony za pośrednictwem sesji, która umożliwia przesyłania komunikatów w jednym kierunku; jest link Negocjowanie stan transferu to łącze i dwukierunkowej między stronami w połączonych.

![][2]

Łącza mogą być tworzone przy użyciu dowolnego kontenera, w dowolnym momencie i za pośrednictwem istniejącej sesji, co sprawia, że protokół AMQP różni się od wielu innych protokołów, w tym HTTP i MQTT, gdzie inicjowania transferu i ścieżki transferu jest wyłączne uprawnień innych firm, tworzenia Połączenie gniazda.

Inicjowanie łącza kontenera zadaje przeciwny kontener, aby zaakceptować łącze i go wybierze roli nadawcy i adresata. W związku z tym kontener może zainicjować tworzenie jednokierunkowe albo zaufanych ścieżek komunikacji dwukierunkowej, z drugim modelowane jako pary łączy.

Łącza są o nazwie i skojarzone z węzłów. Jak wspomniano na początku, węzły są komunikujące się jednostki w kontenerze.

W usłudze Service Bus węzeł odpowiada bezpośrednio kolejki, tematu, subskrypcji lub kolejki utraconych wiadomości podrzędnej kolejki lub subskrypcji. Nazwa węzła używana na protokół AMQP w związku z tym jest względnej nazwy jednostki w przestrzeni nazw usługi Service Bus. Jeśli kolejka jest o nazwie `myqueue`, również jest jej nazwa węzła protokołu AMQP. Subskrypcji tematu następuje po Konwencji interfejsu API protokołu HTTP, posortowana w kolekcji zasobów "subskrypcje", a tym samym subskrypcji **sub** na temat **mytopic** ma nazwę węzła AMQP  **sub-mytopic/subskrypcje**.

Klienta nawiązującego połączenie jest również wymagane do używania nazwy lokalnego węzła usługi do tworzenia łączy; Usługa Service Bus nie jest normatywnych dotyczących tych nazw węzła i nie interpretuje je. Stosy klienta protokołu AMQP 1.0 zazwyczaj korzystają ze schematu do zapewnienia specyficzne te nazwy węzłów tymczasowych w zakresie klienta.

### <a name="transfers"></a>Transfery

Po ustanowieniu łącze wiadomości można przesyłać za pośrednictwem tego łącza. W AMQP, transferu jest wykonywane za pomocą gestu jawne protokołu ( *transferu* performative) który przenosi wiadomości od nadawcy do odbiorcy za pośrednictwem łącza. Przeniesienie została ukończona, gdy jej "rozliczenia", co oznacza, że obie strony ustalonymi udostępnionego znajomości wynik ten transfer.

![][3]

W najprostszym przypadku nadawcy możliwość wysyłania komunikatów, "wstępnie rozliczona", co oznacza, że klient nie jest zainteresowany wynikiem i odbiornik nie zapewnia żadnych opinię na temat wyniki operacji. Ten tryb jest obsługiwane przez usługę Service Bus na poziomie protokołu AMQP, ale nie są widoczne w żadnym z interfejsów API klienta.

Regularne przypadek jest komunikaty są wysyłane nierozliczonych i odbiornika następnie wskazuje zatwierdzenia lub odrzucenia przy użyciu *dyspozycji* performative. Odrzucenia występuje, gdy odbiornik nie może akceptować wiadomości z dowolnego powodu wiadomość o odrzuceniu zawiera informacje dotyczące przyczyny, która jest strukturą błąd definicją protokołu AMQP. Jeśli wiadomości zostały odrzucone z powodu błędów wewnętrznych wewnątrz usługi Service Bus, usługa zwraca dodatkowe informacje w tej struktury, który może służyć zapewniające wskazówki diagnostyki do personelu działu pomocy technicznej, jeśli zgłaszasz żądania pomocy technicznej. Później dowiesz się więcej na temat błędów.

Jest specjalną postać odrzucenia *wydane* stanu, co oznacza, że odbiorca ma zastrzeżeń Technical Preview do transferu, ale również nie jest zainteresowany rozliczanie transferu. Tego przypadku istnieje, na przykład, gdy komunikat jest dostarczany do klienta usługi Service Bus, a klient zdecyduje się na "Porzuć" wiadomość, ponieważ nie może wykonywać pracę wynikających z przetwarzania komunikatu; dostarczanie komunikatów, sama nie jest na pozycji błędu. Zmiana tego stanu jest *zmodyfikowane* stanu, co pozwala na komunikat zmiany, ponieważ jest on zwalniany. Ten stan nie jest używana przez usługę Service Bus w chwili obecnej.

Specyfikacja protokołu AMQP 1.0 definiuje dodatkowe dyspozycji stan o nazwie *odebrane*, to w szczególności pozwala obsługiwać łącze odzyskiwania. Link odzyskiwania umożliwia przywracanie stanu łącza i wszelkie oczekujące dostaw nowe połączenie i sesji, gdy poprzednie połączenie i sesji zostały utracone.

Usługa Service Bus nie obsługuje odzyskiwania łącze; Jeśli klient utraci połączenie do usługi Service Bus z komunikatem nierozliczonych przeniesienie oczekujące, transfer tej wiadomości zostaną utracone i klient musi ponownie, ponownie ustanowić połączenie i spróbuj ponownie transferu.

W efekcie usługi Service Bus i Event Hubs "co najmniej raz" obsługuje transfer, gdzie można czuwać komunikatu przechowywane i zaakceptowane nadawcy, ale czynności nie pomocy technicznej "dokładnie jednokrotne" transferu na poziomie protokołu AMQP, gdy system będzie podejmowana próba odzyskania łącze i Przejdź do negocjowania stanu dostarczania, aby uniknąć jego duplikowania na potrzeby transferu komunikatów.

Aby zrekompensować możliwy duplikat wysyła, Usługa Service Bus obsługuje wykrywania duplikatów jako opcjonalna funkcja w kolejek i tematów. Wykrywanie duplikatów rejestruje identyfikatorów komunikatów komunikaty przychodzące w przedziale czasu zdefiniowanych przez użytkownika, a następnie wszystkich wiadomości wysłanych o takich samych identyfikatorów komunikatów podczas tego samego okna w trybie dyskretnym spada.

### <a name="flow-control"></a>Sterowanie przepływem

Oprócz modelu kontroli przepływu z poziomu sesji omówionych wcześniej każde łącze posiada własny model kontroli przepływu. Sterowanie przepływem na poziomie sesji chroni kontenera z konieczności obsługi zbyt wiele ramek na raz, sterowanie przepływem na poziomie łącza powoduje umieszczenie aplikacji odpowiada za liczbę komunikatów, które chce obsługiwać ze łącze i kiedy.

![][4]

Łącze, transfery tylko zdarzyć, gdy nadawca ma wystarczającą ilość *łączenie środków*. Kredyt łącze jest zbioru liczników, odbiornika zdarzenia za pomocą *przepływ* performative, która obejmuje łącze. Gdy nadawca jest przypisane łącze środków, próbuje użycie tego środki przez dostarczanie wiadomości. Każdego zmniejsza dostarczania wiadomości łącze pozostałe środki o 1. Po wyczerpaniu środków łącze Zatrzymaj dostaw.

Kiedy roli odbiornik usługi Service Bus, natychmiast zapewnia nadawcy środkami łącze często tak, aby od razu można wysyłać wiadomości. W przypadku użycia środków link usługi Service Bus od czasu do czasu wysyła *przepływu* performative przez nadawcę do zaktualizowania salda środków łącza.

W roli nadawcy usługi Service Bus wysyła wiadomości do użycie kredyt oczekujących łącze.

Wywołanie "otrzymywać" na poziomie interfejsu API przekłada się na *przepływ* performative są wysyłane do usługi Service Bus przez klienta i usługi Service Bus używa tego kredytu wykonując pierwszy dostępne, odblokowane komunikat z kolejki, blokowanie, i przesłanie ich. Jeśli żaden komunikat nie jest łatwo dostępne do dostarczania, wszelkie oczekujące środki przez dowolny link nawiązać z czy określonego obiektu pozostaje zarejestrowane w kolejności przybycia i zablokowane i przesyłane, gdy tylko staną się dostępne, aby wszystkie oczekujące środki na korzystanie z komunikatów.

Blokadę komunikatu jest zwalniany, gdy transfer jest rozliczany jako jeden z terminala stany *zaakceptowane*, *odrzucone*, lub *wydane*. Komunikat zostanie usunięty z usługi Service Bus, gdy stan końcowy jest *zaakceptowane*. Pozostaje w usłudze Service Bus i są dostarczane do następnego odbiorcy, gdy transfer osiągnie innych Państw. Usługa Service Bus automatycznie przenosi wiadomość do kolejki utraconych wiadomości jednostki po osiągnięciu maksymalna liczba prób dostarczenia dozwoloną dla jednostki z powodu odrzucenia powtarzanych lub wersji.

Mimo że interfejsów API usługi Service Bus nie ujawniaj bezpośrednio takiej opcji już dziś, klienta protokołu AMQP niższego poziomu, można użyć danego modelu środki łącze włączyć interakcji "pull-style" wystawiających jedną jednostkę kredytu na każde żądanie odbioru do modelu "push-style" wystawianie dużej liczby połączyć środki na korzystanie z, a następnie komunikaty, gdy tylko staną się dostępne bez dalszej interakcji. Wypychania jest świadczona za pośrednictwem [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) lub [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) ustawienia właściwości. Gdy są one różna od zera, klienta protokołu AMQP używa go jako środków łącza.

W tym kontekście jest ważne dowiedzieć się, że zegar wygasania blokadę komunikatu w jednostki rozpoczyna się, gdy komunikat jest pobierana z obiektu, nie po umieszczeniu komunikatu w sieci. Zawsze wtedy, gdy klient wskazuje gotowości do odbierania komunikatów przez wysłanie linku środków, dlatego należy się spodziewać się aktywnie ściąganie wiadomości w sieci i gotowe do obsługi tych. W przeciwnym razie blokadę komunikatu mogło wygasnąć przed nawet świadczy wiadomości. Użyj linku środki kontroli przepływu bezpośrednio powinny odzwierciedlać natychmiastowego gotowości do czynienia z dostępnych komunikatów wysłanych do odbiorcy.

Podsumowując poniższe sekcje zawierają schematyczny omówienie przepływu performative podczas interakcje interfejsu API. Każda sekcja zawiera opis różnych operacji logicznej. Niektóre z tych kontaktów może być "z opóźnieniem," co oznacza, że ich może być wykonana tylko gdy jest to wymagane. Tworzenie nadawcy wiadomości nie może powodować interakcji sieci, do momentu pierwszego wiadomość jest wysyłana lub wymagane.

Strzałki w poniższej tabeli oznaczają kierunek przepływu performative.

#### <a name="create-message-receiver"></a>Utwórz odbiornik komunikatów

| Klient | Service Bus |
| --- | --- |
| --> Dołącz ()<br/>Nazwa = {Nazwa linku}<br/>Obsługa = {liczbowych uchwyt}<br/>Rola =**odbiorcy**,<br/>Źródło = {nazwa jednostki}<br/>docelowy = {identyfikator klienta link}<br/>) |Klient dołącza do jednostki jako odbiorcy |
| Usługa Service Bus odpowiada dołączanie jego końcem łącza |< — dołączanie ()<br/>Nazwa = {Nazwa linku}<br/>Obsługa = {liczbowych uchwyt}<br/>Rola =**nadawcy**,<br/>Źródło = {nazwa jednostki}<br/>docelowy = {identyfikator klienta link}<br/>) |

#### <a name="create-message-sender"></a>Tworzenie nadawcy wiadomości

| Klient | Service Bus |
| --- | --- |
| --> Dołącz ()<br/>Nazwa = {Nazwa linku}<br/>Obsługa = {liczbowych uchwyt}<br/>Rola =**nadawcy**,<br/>Źródło = {klienta łącza ID}<br/>docelowy = {nazwa jednostki}<br/>) |Brak akcji |
| Brak akcji |< — dołączanie ()<br/>Nazwa = {Nazwa linku}<br/>Obsługa = {liczbowych uchwyt}<br/>Rola =**odbiorcy**,<br/>Źródło = {klienta łącza ID}<br/>docelowy = {nazwa jednostki}<br/>) |

#### <a name="create-message-sender-error"></a>Tworzenie nadawcy wiadomości (błąd)

| Klient | Service Bus |
| --- | --- |
| --> Dołącz ()<br/>Nazwa = {Nazwa linku}<br/>Obsługa = {liczbowych uchwyt}<br/>Rola =**nadawcy**,<br/>Źródło = {klienta łącza ID}<br/>docelowy = {nazwa jednostki}<br/>) |Brak akcji |
| Brak akcji |< — dołączanie ()<br/>Nazwa = {Nazwa linku}<br/>Obsługa = {liczbowych uchwyt}<br/>Rola =**odbiorcy**,<br/>Źródło = null,<br/>target=null<br/>)<br/><br/><--odłączyć ()<br/>Obsługa = {liczbowych uchwyt}<br/>zamknięte =**true**,<br/>błąd = {informacje o błędzie}<br/>) |

#### <a name="close-message-receiversender"></a>Zamknij komunikat odbiornik/nadawcy

| Klient | Service Bus |
| --- | --- |
| --> odłączyć ()<br/>Obsługa = {liczbowych uchwyt}<br/>closed=**true**<br/>) |Brak akcji |
| Brak akcji |<--odłączyć ()<br/>Obsługa = {liczbowych uchwyt}<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Wyślij (Powodzenie)

| Klient | Service Bus |
| --- | --- |
| --> () transferu<br/>dostarczanie id = {liczbowych uchwyt}<br/>dostarczanie tag = {binarne uchwyt}<br/>rozliczane =**false**,, bardziej =**false**,<br/>Stan =**null**,<br/>Wznów =**false**<br/>) |Brak akcji |
| Brak akcji |<--(dyspozycji<br/>Rola odbiornik,<br/>najpierw = {identyfikator dostawy}<br/>ostatnie = {identyfikator dostawy}<br/>settled=**true**,<br/>Stan =**zaakceptowane**<br/>) |

#### <a name="send-error"></a>Wyślij (błąd)

| Klient | Service Bus |
| --- | --- |
| --> () transferu<br/>dostarczanie id = {liczbowych uchwyt}<br/>dostarczanie tag = {binarne uchwyt}<br/>rozliczane =**false**,, bardziej =**false**,<br/>Stan =**null**,<br/>Wznów =**false**<br/>) |Brak akcji |
| Brak akcji |<--(dyspozycji<br/>Rola odbiornik,<br/>najpierw = {identyfikator dostawy}<br/>ostatnie = {identyfikator dostawy}<br/>settled=**true**,<br/>Stan =**odrzucone**()<br/>błąd = {informacje o błędzie}<br/>)<br/>) |

#### <a name="receive"></a>Odbieranie

| Klient | Service Bus |
| --- | --- |
| --> () przepływu<br/>link-credit=1<br/>) |Brak akcji |
| Brak akcji |< transferu ()<br/>dostarczanie id = {liczbowych uchwyt}<br/>dostarczanie tag = {binarne uchwyt}<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>Stan =**null**,<br/>Wznów =**false**<br/>) |
| --> () dyspozycji<br/>Rola =**odbiorcy**,<br/>najpierw = {identyfikator dostawy}<br/>ostatnie = {identyfikator dostawy}<br/>settled=**true**,<br/>Stan =**zaakceptowane**<br/>) |Brak akcji |

#### <a name="multi-message-receive"></a>Odbierać wiele wiadomości

| Klient | Service Bus |
| --- | --- |
| --> () przepływu<br/>link-credit=3<br/>) |Brak akcji |
| Brak akcji |< transferu ()<br/>dostarczanie id = {liczbowych uchwyt}<br/>dostarczanie tag = {binarne uchwyt}<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>Stan =**null**,<br/>Wznów =**false**<br/>) |
| Brak akcji |< transferu ()<br/>dostarczanie id = {liczbowych uchwyt + 1},<br/>dostarczanie tag = {binarne uchwyt}<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>Stan =**null**,<br/>Wznów =**false**<br/>) |
| Brak akcji |< transferu ()<br/>dostarczanie id = {liczbowych uchwyt + 2},<br/>dostarczanie tag = {binarne uchwyt}<br/>rozliczane =**false**,<br/>więcej =**false**,<br/>Stan =**null**,<br/>Wznów =**false**<br/>) |
| --> () dyspozycji<br/>Rola odbiornik,<br/>najpierw = {identyfikator dostawy}<br/>ostatnie = {identyfikator dostawy + 2},<br/>settled=**true**,<br/>Stan =**zaakceptowane**<br/>) |Brak akcji |

### <a name="messages"></a>Komunikaty

W poniższych sekcjach opisano z sekcji standardowy komunikat protokołu AMQP właściwości, które są używane przez usługę Service Bus oraz sposobu mapowania ich na zestaw API usługi Service Bus.

Wszystkie właściwości, która aplikacja ma definiuje powinno zostać zamapowane na protokół AMQP firmy `application-properties` mapy.

#### <a name="header"></a>nagłówek

| Nazwa pola | Sposób użycia | Nazwa interfejsu API |
| --- | --- | --- |
| trwałe |- |- |
| priority |- |- |
| czas wygaśnięcia |Czas wygaśnięcia dla tego komunikatu |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| pierwszy przejmująca |- |- |
| Liczba prób dostarczenia |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Nazwa pola | Sposób użycia | Nazwa interfejsu API |
| --- | --- | --- |
| message-id |Zdefiniowane przez aplikację, dowolny identyfikator dla tego komunikatu. Używane do wykrywania duplikatów. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identyfikator użytkownika zdefiniowanych przez aplikację nie są interpretowane przez usługę Service Bus. |Nie jest dostępny za pośrednictwem interfejsu API usługi Service Bus. |
| na |Identyfikator docelowego zdefiniowanych przez aplikację nie są interpretowane przez usługę Service Bus. |[Do](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| temat |Identyfikator celu wiadomości zdefiniowanych przez aplikację, nie są interpretowane przez usługę Service Bus. |[Etykieta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Odpowiedz do |Wskaźnik ścieżki odpowiedzi zdefiniowany przez aplikację nie są interpretowane przez usługę Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Identyfikator korelacji zdefiniowanych przez aplikację nie są interpretowane przez usługę Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Typ zawartości |Zdefiniowane przez aplikację wskaźnik typu zawartości dla treści nie interpretowane przez usługę Service Bus. |[Typ zawartości](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Zdefiniowane przez aplikację kodowanie zawartości wskaźnik dla treści nie interpretowane przez usługę Service Bus. |Nie jest dostępny za pośrednictwem interfejsu API usługi Service Bus. |
| czas w przypadku wygaśnięcia bezwzględne |Deklaruje, w których bezwzględną błyskawicznych komunikat wygasa. Ignorowane na dane wejściowe (nagłówek stwierdzamy TTL), autorytatywny w danych wyjściowych. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Godzina utworzenia |Deklaruje co wiadomość została utworzona. Nie są używane przez usługę Service Bus |Nie jest dostępny za pośrednictwem interfejsu API usługi Service Bus. |
| Identyfikator grupy |Zdefiniowane przez aplikację identyfikator powiązany zestaw komunikatów. Używane dla sesji magistrali usług. |[Identyfikator sesji](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| group-sequence |Licznik identyfikowanie numer sekwencji względne wiadomości wewnątrz sesji. Ignorowane przez usługę Service Bus. |Nie jest dostępny za pośrednictwem interfejsu API usługi Service Bus. |
| Odpowiedz na grupy identyfikator |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Adnotacje wiadomości

Istnieje kilka innych usługi Service bus komunikat właściwości, które nie są częścią właściwości komunikatu protokołu AMQP i są przekazywane jako `MessageAnnotations` wiadomości.

| Klucz mapy adnotacji | Sposób użycia | Nazwa interfejsu API |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Deklaruje co komunikat powinien pojawić się w jednostce |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | Klucz zdefiniowanych przez aplikację, które określają, które partycji komunikat powinny znaleźć się w. | [właściwości partitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | Zdefiniowane przez aplikację klucza partycji wartość, gdy transakcja jest używany do wysyłania komunikatów za pośrednictwem kolejki transferu. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | Zdefiniowane przez usługę czasu UTC reprezentujący rzeczywisty czas enqueuing wiadomości. Dane wejściowe są ignorowane na. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-number | Zdefiniowane przez usługę unikatowy numer przypisany do wiadomości. | [sequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Numer sekwencji zdefiniowane przez usługę umieszczonych w kolejce wiadomości. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-locked-until | Zdefiniowane przez usługę. Data i godzina, do której zostanie zablokowane wiadomości w kolejce/subskrypcji. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | Zdefiniowane przez usługę. Odebranie komunikatu z kolejki utraconych wiadomości, źródło oryginalnej wiadomości. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Transakcja funkcji

Transakcja grupuje dwóch lub więcej operacji w zakresie wykonywania. Ze względu na charakter takich transakcji musi zapewnić, że wszystkie operacje należących do danej grupy operacji powodzenie lub niepowodzenie wspólnie.
Operacje są pogrupowane według identyfikatora `txn-id`.

Do interakcji z transakcji, klient działa jako `transaction controller` , która kontroluje operacje, które powinny być zgrupowane razem. Usługa Service Bus pełni rolę `transactional resource` i wykonuje pracę, zgodnie z żądaniem `transaction controller`.

Klient i usługa komunikują się za pośrednictwem `control link` , która została ustanowiona przez klienta. `declare` i `discharge` komunikaty są wysyłane przez kontroler za pośrednictwem łącza kontroli można przydzielić i odpowiednio realizowania transakcji (nie przedstawiają one granic transakcji pracy). Ten Link nie jest wykonywana rzeczywistych operacji wysyłania i odbierania. Każdej żądanej operacji transakcji jest jawnie oznaczone symbolem żądaną `txn-id` i w związku z tym może wystąpić na dowolny link w połączeniu. Jeśli łącze kontroli jest zamknięty, gdy istnieją transakcje zakończona, utworzone przez siebie, następnie wszystkie takie natychmiast wycofywania transakcji, a próby wykonania dalszych transakcji pracy na ich doprowadzi do błędu. Wiadomości na formant łącza nie może być wstępnie uregulowane.

Każde połączenie musi zainicjować link własny kontroli w taki sposób, aby mieć możliwość rozpoczęcia i zakończenia transakcji. Usługa definiuje specjalny docelowy, który działa jako `coordinator`. Klient/kontroler ustanawia łącze kontroli do tego obiektu docelowego. Formant łącza jest poza granicami jednostki, oznacza to, że tego samego łącza kontroli może służyć do inicjowania i zwalnia transakcje dla wielu jednostek.

#### <a name="starting-a-transaction"></a>Uruchamianie transakcji

Aby rozpocząć transakcji pracy. Kontroler musi uzyskać `txn-id` z koordynatora. Jest to realizowane przez wysłanie `declare` wpisz wiadomość. Jeśli deklaracja się powiedzie, koordynator odpowiada za pomocą sytuacji dyspozycji niesie ze sobą przypisane `txn-id`.

| Klient (kontroler) | | Usługa Service Bus (koordynator) |
| --- | --- | --- |
| Dołącz)<br/>Nazwa = {Nazwa linku}<br/>... ,<br/>Rola =**nadawcy**,<br/>target=**Coordinator**<br/>) | ------> |  |
|  | <------ | Dołącz)<br/>Nazwa = {Nazwa linku}<br/>... ,<br/>target=Coordinator()<br/>) |
| Transfer)<br/>dostarczanie id = 0,...)<br/>{AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | (dyspozycji <br/> najpierw = 0, ostatnia = 0, <br/>Stan =**Declared**()<br/>**Identyfikator transakcji**= {identyfikator transakcji}<br/>))|

#### <a name="discharging-a-transaction"></a>Wykonującej transakcji

Kontroler stwierdza transakcji pracy, wysyłając `discharge` wiadomości z koordynatorem. Kontroler wskazuje, że chce przekazać ani wycofać transakcji pracy przez ustawienie `fail` flagi treści zrzutu. W przypadku nie można wykonać zrzutu koordynator wiadomości jest odrzucane ten wynik wykonywania `transaction-error`.

> Uwaga: niepowodzenie = true odwołuje się do wycofania transakcji i kończyć się niepowodzeniem = false odwołuje się do zatwierdzenia.

| Klient (kontroler) | | Usługa Service Bus (koordynator) |
| --- | --- | --- |
| Transfer)<br/>dostarczanie id = 0,...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | (dyspozycji <br/> najpierw = 0, ostatnia = 0, <br/>Stan = (zadeklarowanych<br/>transakcji id = {Identyfikator transakcji}<br/>))|
| | . . . <br/>Transakcji pracy<br/>na inne linki<br/> . . . |
| Transfer)<br/>dostarczanie id = 57,...)<br/>{AmqpValue)<br/>**Zwalnia (identyfikator transakcji = 0,<br/>się nie powieść = false)**)}| ------> |  |
| | <------ | (dyspozycji <br/> najpierw = 57, ostatnia = 57, <br/>state=**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Wysyłanie komunikatu w ramach transakcji

Wszystkie transakcyjnych zadań odbywa się ze stanem transakcyjnych dostarczania `transactional-state` , niesie ze sobą identyfikator transakcji. W przypadku wysyłania komunikatów, transakcyjną stanu odbywa się przez ramkę transferu wiadomości. 

| Klient (kontroler) | | Usługa Service Bus (koordynator) |
| --- | --- | --- |
| Transfer)<br/>dostarczanie id = 0,...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | (dyspozycji <br/> najpierw = 0, ostatnia = 0, <br/>Stan = (zadeklarowanych<br/>transakcji id = {Identyfikator transakcji}<br/>))|
| Transfer)<br/>Obsługa = 1,<br/>dostarczanie id = 1, <br/>**state=<br/>TransactionalState(<br/>txn-id=0)**)<br/>{ładunek}| ------> |  |
| | <------ | (dyspozycji <br/> najpierw = 1, ostatnia = 1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Usuwanie wiadomości w ramach transakcji

Komunikat dyspozycji obejmuje operacje, takie jak `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Aby wykonać te operacje w ramach transakcji, należy przekazać `transactional-state` z dyspozycji.

| Klient (kontroler) | | Usługa Service Bus (koordynator) |
| --- | --- | --- |
| Transfer)<br/>dostarczanie id = 0,...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | (dyspozycji <br/> najpierw = 0, ostatnia = 0, <br/>Stan = (zadeklarowanych<br/>transakcji id = {Identyfikator transakcji}<br/>))|
| | <------ |Transfer)<br/>Obsługa = 2,<br/>dostarczanie id = 11 <br/>Stan = null)<br/>{ładunek}|  
| (dyspozycji <br/> najpierw = 11, ostatnia = 11 <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Zaawansowane funkcje usługi Service Bus

W tej sekcji omówiono zaawansowane funkcje usługi Azure Service Bus, które są oparte na rozszerzeniach projekt na protokół AMQP, obecnie opracowywane Komitet Techniczny OASIS dla protokołu AMQP. Usługa Service Bus implementuje najnowsze wersje tych projektów i przyjmuje zmiany wprowadzone tylko te projekty osiągnąć stan standard.

> [!NOTE]
> Komunikaty usługi Service Bus zaawansowane operacje są obsługiwane przez wzorzec żądań/odpowiedzi. Szczegółowe informacje o tych operacji są opisane w artykule [protokołu AMQP 1.0 w usłudze Service Bus: operacje na podstawie odpowiedzi żądań](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Zarządzanie protokołu AMQP

Specyfikacji protokołu AMQP zarządzania jest pierwszym rozszerzenia projektu omówionych w tym artykule. Ta specyfikacja definiuje zestaw protokołów warstwowo protokołu AMQP, które umożliwiają interakcje z infrastrukturą obsługi wiadomości za pośrednictwem protokołu AMQP. Specyfikacja definiuje ogólny operacje, takie jak *tworzenie*, *odczytu*, *aktualizacji*, i *Usuń* do zarządzania jednostkami wewnątrz Infrastruktura obsługi komunikatów i zestaw operacji zapytania.

Wszystkie te gesty wymagają interakcji żądanie/odpowiedź, między klientem a infrastruktura obsługi komunikatów, a w związku z tym specyfikacja definiuje sposób modelu tego wzorca interakcji na podstawie protokołu AMQP: klient łączy się infrastruktury obsługi wiadomości inicjuje sesję, a następnie tworzy parę łącza. Na jedno łącze klient działa jako nadawcy, a na drugim działa jako odbiornik, co powoduje utworzenie pary łączy, które mogą pełnić rolę kanału dwukierunkowego.

| Operacja logiczna | Klient | Service Bus |
| --- | --- | --- |
| Tworzenie ścieżki odpowiedzi żądania |--> Dołącz ()<br/>Nazwa = {*nazwę łącza*},<br/>Obsługa = {*liczbowych uchwyt*},<br/>Rola =**nadawcy**,<br/>źródło =**null**,<br/>target = "Zarządzanie myentity / $"<br/>) |Brak akcji |
| Tworzenie ścieżki odpowiedzi żądania |Brak akcji |\<--dołączyć ()<br/>Nazwa = {*nazwę łącza*},<br/>Obsługa = {*liczbowych uchwyt*},<br/>Rola =**odbiorcy**,<br/>Źródło = null,<br/>target = "myentity"<br/>) |
| Tworzenie ścieżki odpowiedzi żądania |--> Dołącz ()<br/>Nazwa = {*nazwę łącza*},<br/>Obsługa = {*liczbowych uchwyt*},<br/>Rola =**odbiorcy**,<br/>Źródło = "myentity / $" Zarządzanie,<br/>target = "identyfikator myclient$"<br/>) | |
| Tworzenie ścieżki odpowiedzi żądania |Brak akcji |\<--dołączyć ()<br/>Nazwa = {*nazwę łącza*},<br/>Obsługa = {*liczbowych uchwyt*},<br/>Rola =**nadawcy**,<br/>Źródło = "myentity"<br/>target = "identyfikator myclient$"<br/>) |

Posiadanie tej pary łączy w miejscu, wykonania żądania/odpowiedzi jest proste: żądanie jest wiadomością wysłaną do jednostki wewnątrz infrastruktura obsługi komunikatów, która obsługuje ten wzorzec. W tym komunikacie żądania *odpowiedzi* pole *właściwości* sekcji jest ustawiona na *docelowej* identyfikator dla tego połączenia, na którym dostarczyć odpowiedź. Jednostka obsługi przetwarza żądanie, a następnie dostarcza odpowiedzi przez łącze którego *docelowej* identyfikator jest zgodny wskazany *Odpowiedz do* identyfikator.

Wzorzec wymaga oczywiście, że kontener klienta i identyfikator generowany przez klienta jako miejsce docelowe odpowiedzi są unikatowe dla wszystkich klientów i, ze względów bezpieczeństwa również trudne do przewidzenia.

Wymianę komunikatów, używane przez protokół zarządzania i innych protokołów, które używają tego samego wzorca odbywa się na poziomie aplikacji; nie mogą określać nowych gestów na poziomie protokołu AMQP. Jest zamierzone, tak aby aplikacje mogą korzystać natychmiastowego tych rozszerzeń, za pomocą zgodne stosów protokołu AMQP 1.0.

Usługa Service Bus nie obecnie zaimplementowaniem dowolne z podstawowych funkcji specyfikacji zarządzania, ale wzorzec żądań/odpowiedzi ze specyfikacją zarządzania jest podstawowe dla funkcji oświadczeń — zabezpieczenia na poziomie i niemal wszystkich zaawansowanych funkcje omówione w poniższych sekcjach:

### <a name="claims-based-authorization"></a>Autoryzacja oparta na oświadczeniach

Projekt specyfikacji protokołu AMQP oświadczenia na podstawie-autoryzacji (CBS) jest oparta na wzorcu zarządzania specyfikacji żądania/odpowiedzi i opisano uogólnionego modelu używania tokenów zabezpieczeń z obsługą protokołu AMQP.

Model zabezpieczeń domyślnego protokołu AMQP opisano we wprowadzeniu opiera się na SASL i integruje się z uzgadniania połączenia protokołu AMQP. Używa mechanizmu SASL ma tę zaletę, że zapewnia extensible modelu, dla którego zdefiniowano zestaw mechanizmów z mogą korzystać dowolny protokół, który formalnie leans na SASL. Wśród tych mechanizmów są "Zwykły" przesyłania nazwy użytkownika i hasła, "Zewnętrzna", aby powiązać z zabezpieczeniami na poziomie protokołu TLS "ANONYMOUS", aby wyrazić braku jawnego uwierzytelniania/autoryzacji i szerokiej gamy dodatkowe mechanizmy, które umożliwiają przekazywanie poświadczenia uwierzytelniania lub autoryzacji lub tokenów.

Integracja SASL firmy AMQP ma dwie wady:

* Wszystkie poświadczenia i tokeny są ograniczone do połączenia. To infrastruktura obsługi komunikatów może być w celu zapewnienia kontroli dostępu zróżnicowane na podstawie jednostek; na przykład dzięki czemu elementu nośnego tokenu wysyłać do kolejki A, ale nie do kolejki B. W kontekście autoryzacji zakotwiczone w ramach połączenia nie jest możliwość używania jednego połączenia i jeszcze używanie tokenów dostępu innej kolejki A i B. kolejki
* Tokeny dostępu są zazwyczaj ważne tylko przez ograniczony czas. Ważność ta wymaga od użytkownika okresowo ponownie pobrać tokenów i stanowi przy tym okazję do wystawcy tokenów odmowy wystawianie świeże tokenie, jeśli zostały zmienione uprawnienia dostępu użytkownika. Połączenia AMQP może trwać przez dłuższy czas. SASL model zapewnia tylko szansę, aby ustawić token w czasie połączenia, co oznacza, że infrastruktura obsługi komunikatów, albo ma, aby odłączyć klienta, gdy token jest ważny, lub musi zaakceptować ryzyko umożliwienia dalszego komunikacji z klientem kto firmy prawa dostępu został odwołany w międzyczasie.

Specyfikację protokołu AMQP CBS implementowany przez usługi Service Bus umożliwia obejście elegancki dla obu tych problemów: Umożliwia ona klienta, aby skojarzyć tokenów dostępu z każdego węzła i zaktualizować tych tokenów, zanim wygasną, bez przerywania przepływu wiadomości.

CBS definiuje węzłem zarządzanie wirtualnym o nazwie *$cbs*, muszą być dostarczone przez infrastruktura obsługi komunikatów. Węzeł zarządzania akceptuje tokeny w imieniu innych węzłów w infrastrukturze obsługi wiadomości.

Gest protokół jest żądanie/nietypizowana odpowiedź programu exchange zgodnie z definicją w specyfikacji zarządzania. Czy oznacza, że klient nawiąże pary łączy z *$cbs* węzeł i następnie przekazuje żądania wychodzące łącze, a następnie czeka na odpowiedź na łącza przychodzącego.

Komunikat żądania ma następujące właściwości aplikacji:

| Klucz | Optional (Opcjonalność) | Typ wartości | Wartość zawartości |
| --- | --- | --- | --- |
| operacja |Nie |string |**Umieść token** |
| type |Nie |string |Typ tokenu dotyczy żądanie put. |
| name |Nie |string |Do której stosują się token "audience". |
| wygaśnięcie |Yes |timestamp |Czas wygaśnięcia tokenu. |

*Nazwa* właściwość identyfikuje jednostki, z którym token jest włączona. W usłudze Service Bus jest ścieżka do kolejki lub tematu/subskrypcji. *Typu* właściwość identyfikuje typ tokenu:

| Typ tokenu | Opis tokenu | Typ treści | Uwagi |
| --- | --- | --- | --- |
| amqp:jwt |Tokenu Web JSON (JWT) |Protokół AMQP wartość (ciąg) |Nie jest jeszcze dostępna. |
| amqp:swt |Prosty Token sieci Web (SWT) |Protokół AMQP wartość (ciąg) |Obsługiwane tylko w przypadku SWT tokeny wystawione przez usługi AAD/usługi ACS |
| servicebus.windows.net:sastoken |Token sygnatury dostępu Współdzielonego usługi Service Bus |Protokół AMQP wartość (ciąg) |- |

Tokeny przyznaje prawa. Usługa Service Bus obsługującemu trzy podstawowe prawa: "Wyślij" umożliwia wysyłanie "Nasłuchiwania" umożliwia odbieranie i "Manage" umożliwia manipulowanie jednostek. SWT tokeny wystawione przez usługi AAD/ACS jawnie uwzględnić te prawa jako oświadczenia. Tokeny sygnatur dostępu Współdzielonego usługi Service Bus można znaleźć reguły skonfigurowane na przestrzeń nazw lub jednostki, a te zasady są skonfigurowane przy użyciu uprawnień. Podpisywania tokenu przy użyciu klucza skojarzone z tą regułą ten sposób sprawia, że token express odpowiednich praw. Token skojarzone z jednostki przy użyciu *put token* pozwala połączonego komputera klienckiego do interakcji z jednostką na token praw. Link, w którym klient ma na *nadawcy* rola wymaga "Send" bezpośrednio; podjęcia *odbiorcy* rola wymaga "Nasłuchiwania" po prawej.

Komunikat odpowiedzi zawiera następujące *właściwości aplikacji* wartości

| Klucz | Optional (Opcjonalność) | Typ wartości | Wartość zawartości |
| --- | --- | --- | --- |
| Kod stanu: |Nie |int |Kod odpowiedzi HTTP **[specyfikacją RFC2616]**. |
| status-description |Yes |string |Opis stanu. |

Klient może wywołać *put token* wielokrotnie i dla dowolnej jednostki w infrastrukturze obsługi wiadomości. Tokeny są ograniczone do bieżącego klienta i zakotwiczone w bieżącym połączeniu, co oznacza, że serwer porzuca wszystkie tokeny zachowane po spadku połączenia.

Bieżąca implementacja usługi Service Bus zezwala tylko CBS w połączeniu z metodą SASL "ANONYMOUS". Połączenia SSL/TLS, zawsze musi istnieć przed uzgadnianie SASL.

Mechanizm anonimowe, w związku z tym musi być obsługiwana przez wybranego klienta protokołu AMQP 1.0. Dostęp anonimowy oznacza, że uzgadniania połączenia początkowego, takich jak tworzenie początkowej sesji możliwe bez usługi Service Bus, wiedząc, który tworzy połączenie.

Po nawiązaniu połączenia i sesji, dołączając łącza do *$cbs* węzła i wysyłanie *put token* żądania tylko dozwolone są operacje. Należy ustawić prawidłowy token pomyślnie za pomocą *put token* żądania do jednego z węzłów jednostki w ciągu 20 sekund po nawiązaniu połączenia, w przeciwnym razie połączenie zostanie jednostronnie porzucone przez usługę Service Bus.

Klient jest następnie odpowiedzialna za rejestrowanie informacji o wygaśnięciu tokenu. Po wygaśnięciu tokenu usługi Service Bus niezwłocznie porzuca wszystkie łącza połączenia na odpowiednie jednostki. Aby zapobiec występuje problem, klient token można zastąpić dla węzła z nowym w dowolnym momencie za pośrednictwem wirtualnej *$cbs* węzła zarządzania o tej samej *put token* gestu i bez pobierania sposób ładunku ruch tego przepływów dla innego łącza.

### <a name="send-via-functionality"></a>Wysyłanie przez funkcje

[Send via / Transfer nadawcy](service-bus-transactions.md#transfers-and-send-via) jest funkcją, umożliwiająca usługi Service bus do przodu danej komunikatów do jednostki docelowej przy użyciu innej jednostki. Ta funkcja jest używana do wykonywania operacji między jednostkami w ramach jednej transakcji.

Dzięki tej funkcji, Utwórz nadawcy i ustanowić łącze do `via-entity`. Podczas ustanawiania łącza, dodatkowe informacje są przekazywane do ustanowienia wartość true, miejsce docelowe komunikatów/transfer ten Link. Po dołączanie zakończy się powodzeniem, wszystkie komunikaty wysyłane na ten link, są automatycznie przekazywane do *jednostki docelowej* za pośrednictwem *za pośrednictwem jednostki*. 

> Uwaga: Authentication zapewnia dostęp do wykonania dla obu *za pośrednictwem jednostki* i *jednostki docelowej* przed nawiązaniem tego łącza.

| Klient | | Service Bus |
| --- | --- | --- |
| Dołącz)<br/>Nazwa = {Nazwa linku}<br/>Rola = nadawcy<br/>Źródło = {klienta łącza ID}<br/>docelowy =**{za pośrednictwem entity}**,<br/>**Właściwości mapy = [(<br/>com.microsoft:transfer adresu =<br/>{jednostki docelowej})]** ) | ------> | |
| | <------ | Dołącz)<br/>Nazwa = {Nazwa linku}<br/>Rola odbiornik,<br/>Źródło = {klienta łącza ID}<br/>target={via-entity},<br/>Właściwości mapy [() =<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołu AMQP, skorzystaj z następujących linków:

* [Omówienie AMQP usługi Service Bus]
* [Obsługa protokołu AMQP 1.0 tematów i kolejek usługi Service Bus na partycje]
* [Protokół AMQP w usłudze Service Bus dla systemu Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Omówienie AMQP usługi Service Bus]: service-bus-amqp-overview.md
[Obsługa protokołu AMQP 1.0 tematów i kolejek usługi Service Bus na partycje]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[Protokół AMQP w usłudze Service Bus dla systemu Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
