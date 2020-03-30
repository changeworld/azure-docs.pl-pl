---
title: Wzorce inteligentnej integracji kontraktów — Azure Blockchain Workbench
description: Omówienie wzorców integracji inteligentnych umów w usłudze Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: f9626edd5bd655e3de5d0f9648041faf832e3b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325968"
---
# <a name="smart-contract-integration-patterns"></a>Wzorce integracji kontraktów inteligentnych

Inteligentne kontrakty często reprezentują przepływ pracy biznesowej, który musi być zintegrowany z zewnętrznymi systemami i urządzeniami.

Wymagania tych przepływów pracy obejmują potrzebę inicjowania transakcji w rozproszonej księdze, które zawierają dane z zewnętrznego systemu, usługi lub urządzenia. Muszą również mieć systemy zewnętrzne reagować na zdarzenia pochodzące z inteligentnych umów w rozproszonej księdze.

Interfejs API REST i integracja wiadomości wysyła transakcje z systemów zewnętrznych do inteligentnych umów zawartych w aplikacji Azure Blockchain Workbench. Wysyła również powiadomienia o zdarzeniach do systemów zewnętrznych na podstawie zmian, które mają miejsce w aplikacji.

W scenariuszach integracji danych azure blockchain workbench zawiera zestaw widoków bazy danych, które łączą kombinację danych transakcyjnych z łańcucha bloków i meta-danych o aplikacjach i inteligentnych kontraktach.

Ponadto niektóre scenariusze, takie jak scenariusze związane z łańcuchem dostaw lub mediami, mogą również wymagać integracji dokumentów. Chociaż azure blockchain workbench nie zapewnia wywołania interfejsu API do obsługi dokumentów bezpośrednio, dokumenty mogą być włączone do aplikacji blockchain. Ta sekcja zawiera również ten wzorzec.

Ta sekcja zawiera wzorce zidentyfikowane do implementowania każdego z tych typów integracji w rozwiązaniach end-to-end.

## <a name="rest-api-based-integration"></a>Integracja oparta na interfejsie API REST

Możliwości w aplikacji sieci web generowanej przez azure blockchain Workbench są udostępniane za pośrednictwem interfejsu API REST. Możliwości obejmują przekazywanie, konfigurowanie i administrowanie aplikacjami w usłudze Azure Blockchain, wysyłanie transakcji do rozproszonej księgi oraz wykonywanie zapytań dotyczących metadanych aplikacji i danych księgi.

Interfejs API REST jest używany głównie dla klientów interaktywnych, takich jak aplikacje sieci web, mobile i bot.

W tej sekcji omówiono wzorce skoncentrowane na aspektach interfejsu API REST, które wysyłają transakcje do rozproszonej księgi i wzorce, które wysyłają zapytania o dane dotyczące transakcji z *niesąsiącej bazy* danych SQL platformy Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Wysyłanie transakcji do księgi rozproszonej z systemu zewnętrznego

Interfejs API REST systemu Azure Blockchain Workbench wysyła uwierzytelnione żądania do wykonywania transakcji w rozproszonej księdze.

![Wysyłanie transakcji do księgi rozproszonej](./media/integration-patterns/send-transactions-ledger.png)

Wykonywanie transakcji odbywa się przy użyciu procesu przedstawionego wcześniej, gdzie:

-   Aplikacja zewnętrzna uwierzytelnia się w usłudze Azure Active Directory aprowizacji w ramach wdrażania usługi Azure Blockchain Workbench.
-   Autoryzowani użytkownicy otrzymują token na okaziciela, który można wysyłać z żądaniami do interfejsu API.
-   Aplikacje zewnętrzne nawiązują połączenia do interfejsu API REST przy użyciu tokenu nośnika.
-   Interfejs API REST pakiety żądania jako komunikat i wysyła go do usługi Service Bus. W tym miejscu jest pobierany, podpisywane i wysyłane do odpowiedniej księgi rozproszonej.
-   Interfejs API REST sprawia, że żądanie do usługi Azure Blockchain Workbench SQL DB do rejestrowania żądania i ustanowienia bieżącego stanu inicjowania obsługi administracyjnej.
-   Baza danych SQL zwraca stan inicjowania obsługi administracyjnej, a wywołanie interfejsu API zwraca identyfikator do zewnętrznej aplikacji, która go wywoływała.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Wykonywanie zapytań o metadane programu Blockchain Workbench i transakcje rozproszonej księgi

Interfejs API REST systemu Azure Blockchain Workbench wysyła uwierzytelnione żądania do szczegółów zapytania związanych z wykonywaniem inteligentnego kontraktu w rozproszonej księdze.

![Wykonywanie zapytań o metadane](./media/integration-patterns/querying-metadata.png)

Wykonywanie zapytań odbywa się przy użyciu procesu przedstawionego wcześniej, gdzie:

1. Aplikacja zewnętrzna uwierzytelnia się w usłudze Azure Active Directory aprowizacji w ramach wdrażania usługi Azure Blockchain Workbench.
2. Autoryzowani użytkownicy otrzymują token na okaziciela, który można wysyłać z żądaniami do interfejsu API.
3. Aplikacje zewnętrzne nawiązują połączenia do interfejsu API REST przy użyciu tokenu nośnika.
4. Interfejs API REST wysyła kwerendy do danych dla żądania z bazy danych SQL i zwraca go do klienta.

## <a name="messaging-integration"></a>Integracja z wiadomościami

Integracja wiadomości ułatwia interakcję z systemami, usługami i urządzeniami, w których interaktywne logowanie nie jest możliwe lub pożądane. Integracja wiadomości koncentruje się na dwóch typach wiadomości: wiadomości żądających transakcji są wykonywane w rozproszonej księdze i zdarzenia udostępniane przez tę księgę, gdy transakcje miały miejsce.

Integracja wiadomości koncentruje się na wykonywaniu i monitorowaniu transakcji związanych z tworzeniem przez użytkownika, tworzeniem kontraktów i wykonywaniem transakcji na kontraktach i jest używana głównie przez *bezgłowy* system zaplecza.

W tej sekcji omówiono wzorce skoncentrowane na aspektach interfejsu API opartego na wiadomościach, które wysyłają transakcje do rozproszonej księgi i wzorców, które reprezentują komunikaty zdarzeń udostępniane przez podstawową rozproszoną księgę.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Jednokierunkowa dostawa zdarzeń z inteligentnej umowy do konsumenta zdarzeń 

W tym scenariuszu zdarzenie występuje w ramach inteligentnego kontraktu, na przykład zmiany stanu lub wykonanie określonego typu transakcji. To zdarzenie jest transmitowane za pośrednictwem usługi Event Grid do dalszych konsumentów, a ci konsumenci następnie podjąć odpowiednie działania.

Przykładem tego scenariusza jest to, że w przypadku transakcji konsument będzie alerty i może podjąć działania, takie jak rejestrowanie informacji w bazy danych SQL lub common data service. W tym scenariuszu jest ten sam wzorzec, który następuje workbench wypełnić jego *poza łańcuchem* BAZY DANYCH SQL.

Innym byłoby, jeśli inteligentny kontrakt przechodzi do określonego stanu, na przykład, gdy umowa przechodzi do *OutOfCompliance*. W przypadku wystąpienia tej zmiany stanu może to spowodować wyzwolenie alertu do wysłania do telefonu komórkowego administratora.

![Dostarczanie zdarzeń w jedną stronę](./media/integration-patterns/one-way-event-delivery.png)

W tym scenariuszu występuje przy użyciu procesu przedstawionego wcześniej, gdzie:

-   Inteligentny kontrakt przechodzi do nowego stanu i wysyła zdarzenie do księgi.
-   Księga odbiera i dostarcza zdarzenie do azure blockchain workbench.
-   Azure Blockchain Workbench jest subskrybowany do zdarzeń z księgi i odbiera zdarzenie.
-   Azure Blockchain Workbench publikuje zdarzenie do subskrybentów w usłudze Event Grid.
-   Systemy zewnętrzne są subskrybowane do siatki zdarzeń, zużywają komunikat i podejmują odpowiednie akcje.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Jednokierunkowe dostarczanie wiadomości z systemu zewnętrznego do inteligentnego kontraktu

Istnieje również scenariusz, który płynie z przeciwnego kierunku. W takim przypadku zdarzenie jest generowane przez czujnik lub system zewnętrzny, a dane z tego zdarzenia powinny być wysyłane do inteligentnego kontraktu.

Typowym przykładem jest dostarczanie danych z rynków finansowych, na przykład cen towarów, akcji lub obligacji, do inteligentnego kontraktu.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie workbenchu łańcucha bloków platformy Azure w oczekiwanym formacie

Niektóre aplikacje są tworzone do integracji z azure blockchain workbench i bezpośrednio generuje i wysyła wiadomości w oczekiwanych formatach.

![Dostawa bezpośrednia](./media/integration-patterns/direct-delivery.png)

Ta dostawa odbywa się przy użyciu procesu przedstawionego wcześniej, gdzie:

-   Zdarzenie występuje w systemie zewnętrznym, który wyzwala utworzenie wiadomości dla azure blockchain workbench.
-   System zewnętrzny ma kod napisany w celu utworzenia tej wiadomości w znanym formacie i wysyła go bezpośrednio do usługi Service Bus.
-   Azure Blockchain Workbench jest subskrybowany do zdarzeń z usługi Service Bus i pobiera komunikat.
-   Azure Blockchain Workbench inicjuje wywołanie księgi, wysyłając dane z systemu zewnętrznego do określonego kontraktu.
-   Po otrzymaniu wiadomości kontrakt przechodzi do nowego stanu.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie wiadomości w formacie nieznanym platformie Azure Blockchain Workbench

Niektóre systemy nie mogą być modyfikowane w celu dostarczania wiadomości w standardowych formatach używanych przez azure blockchain workbench. W takich przypadkach można często używać istniejących mechanizmów i formatów komunikatów z tych systemów. W szczególności typy wiadomości natywnych tych systemów można przekształcić przy użyciu aplikacji logiki, usługi Azure functions lub innego kodu niestandardowego do mapowania do jednego ze standardowych formatów wiadomości oczekiwanych.

![Nieznany format wiadomości](./media/integration-patterns/unknown-message-format.png)

Dzieje się tak przy użyciu procesu przedstawionego wcześniej, gdzie:

-   Zdarzenie występuje w systemie zewnętrznym, który wyzwala tworzenie wiadomości.
-   Aplikacja logiki lub kod niestandardowy służy do odbierania tej wiadomości i przekształcania jej w standardową wiadomość sformatowaną w usłudze Azure Blockchain Workbench.
-   Aplikacja logiki wysyła przekształcony komunikat bezpośrednio do usługi Service Bus.
-   Azure Blockchain Workbench jest subskrybowany do zdarzeń z usługi Service Bus i pobiera komunikat.
-   Azure Blockchain Workbench inicjuje wywołanie księgi, wysyłając dane z systemu zewnętrznego do określonej funkcji w umowie.
-   Funkcja wykonuje i zazwyczaj modyfikuje stan. Zmiana stanu przesuwa się do przodu przepływ pracy biznesowej odzwierciedlony w umowie inteligentnej, umożliwiając wykonanie innych funkcji w razie potrzeby.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Przejście kontroli do procesu zewnętrznego i oczekiwanie na zakończenie

Istnieją scenariusze, w których umowa inteligentna musi zatrzymać wykonanie wewnętrzne i przekazać do procesu zewnętrznego. Ten proces zewnętrzny zostanie następnie zakończony, wyśle wiadomość do inteligentnego kontraktu, a wykonanie będzie kontynuowane w ramach inteligentnego kontraktu.

#### <a name="transition-to-the-external-process"></a>Przejście do procesu zewnętrznego

Ten wzorzec jest zazwyczaj implementowane przy użyciu następującego podejścia:

-   Inteligentny kontrakt przechodzi do określonego stanu. W tym stanie nie lub ograniczona liczba funkcji mogą być wykonywane, dopóki system zewnętrzny podejmie żądaną akcję.
-   Zmiana stanu jest rzut y na jaw jako zdarzenie do konsumenta niższego rzędu.
-   Konsument podrzędny odbiera zdarzenie i wyzwala wykonanie kodu zewnętrznego.

![Kontrola przejścia do procesu zewnętrznego](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Powrót kontroli z inteligentnego kontraktu

W zależności od możliwości dostosowania systemu zewnętrznego może lub nie może być w stanie dostarczyć wiadomości w jednym ze standardowych formatów, że Azure Blockchain Workbench oczekuje. Na podstawie możliwości systemów zewnętrznych do generowania jednego z tych komunikatów określić, które z następujących dwóch ścieżek zwracanych jest podejmowana.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie workbenchu łańcucha bloków platformy Azure w oczekiwanym formacie

![](./media/integration-patterns/direct-delivery.png)

W tym modelu komunikacja z umową i późniejsza zmiana stanu następuje po poprzednim procesie, w którym

-   Po osiągnięciu zakończenia lub określonego punktu kontrolnego w wykonaniu kodu zewnętrznego zdarzenie jest wysyłane do usługi Service Bus połączone z azure blockchain workbench.

-   Dla systemów, które nie mogą być bezpośrednio dostosowane do pisania wiadomości, która jest zgodna z oczekiwaniami interfejsu API, jest przekształcany.

-   Zawartość wiadomości jest pakowany i wysyłany do określonej funkcji na smart contract. Ta dostawa odbywa się w imieniu użytkownika skojarzonego z systemem zewnętrznym.

-   Funkcja wykonuje i zazwyczaj modyfikuje stan. Zmiana stanu przesuwa się do przodu przepływ pracy biznesowej odzwierciedlony w umowie inteligentnej, umożliwiając wykonanie innych funkcji w razie potrzeby.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie wiadomości w formacie nieznanym platformie Azure Blockchain Workbench

![Nieznany format wiadomości](./media/integration-patterns/unknown-message-format.png)

W tym modelu, w którym wiadomość w standardowym formacie nie może być wysyłana bezpośrednio, komunikacja do umowy i późniejsza zmiana stanu następuje po poprzednim procesie, w którym:

1.  Po osiągnięciu zakończenia lub określonego punktu kontrolnego w wykonaniu kodu zewnętrznego zdarzenie jest wysyłane do usługi Service Bus połączone z azure blockchain workbench.
2.  Aplikacja logiki lub kod niestandardowy służy do odbierania tej wiadomości i przekształcania jej w standardową wiadomość sformatowaną w usłudze Azure Blockchain Workbench.
3.  Aplikacja logiki wysyła przekształcony komunikat bezpośrednio do usługi Service Bus.
4.  Azure Blockchain Workbench jest subskrybowany do zdarzeń z usługi Service Bus i pobiera komunikat.
5.  Azure Blockchain Workbench inicjuje wywołanie księgi, wysyłając dane z systemu zewnętrznego do określonego kontraktu.
6. Zawartość wiadomości jest pakowany i wysyłany do określonej funkcji na smart contract. Ta dostawa odbywa się w imieniu użytkownika skojarzonego z systemem zewnętrznym.
7.  Funkcja wykonuje i zazwyczaj modyfikuje stan. Zmiana stanu przesuwa się do przodu przepływ pracy biznesowej odzwierciedlony w umowie inteligentnej, umożliwiając wykonanie innych funkcji w razie potrzeby.

## <a name="iot-integration"></a>Integracja z IoT

Typowy scenariusz integracji jest włączenie danych telemetrycznych pobranych z czujników w umowie inteligentnej. Na podstawie danych dostarczanych przez czujniki inteligentne kontrakty mogą podejmować świadome działania i zmieniać stan umowy.

Na przykład, jeśli temperatura samochodu ciężarowego dostarczającego lek wzrosła do 110 stopni, może to wpłynąć na skuteczność leku i może spowodować problem z bezpieczeństwem publicznym, jeśli nie zostanie wykryte i usunięte z łańcucha dostaw. Jeśli kierowca przyspieszyłby swój samochód do 100 mil na godzinę, wynikające z tego informacje z czujnika mogą spowodować anulowanie ubezpieczenia przez swojego ubezpieczyciela. Jeśli samochód był wynajętym samochodem, dane GPS mogą wskazywać, kiedy kierowca wyszedł poza geografię objętą umową najmu i naliczyć karę.

Wyzwaniem jest to, że czujniki te mogą dostarczać dane w sposób ciągły i nie jest właściwe wysyłanie wszystkich tych danych do inteligentnego kontraktu. Typowym podejściem jest ograniczenie liczby wiadomości wysyłanych do łańcucha bloków podczas dostarczania wszystkich wiadomości do sklepu pomocniczego. Na przykład dostarczać wiadomości odebrane tylko w ustalonych odstępach czasu, na przykład raz na godzinę i gdy wartość zawarta mieści się poza uzgodnionym zakresem dla inteligentnego kontraktu. Sprawdzanie wartości, które nie mieszczą się w tolerancjach, zapewnia, że dane istotne dla logiki biznesowej kontraktów są odbierane i wykonywane. Sprawdzanie wartości w interwale potwierdza, że czujnik nadal zgłasza. Wszystkie dane są wysyłane do dodatkowego magazynu raportowania, aby umożliwić szersze raportowanie, analizę i uczenie maszynowe. Na przykład, podczas gdy uzyskanie odczytów czujników dla GPS może nie być wymagane co minutę dla inteligentnego kontraktu, mogą one dostarczyć interesujących danych do wykorzystania w raportach lub trasach mapowania.

Na platformie Azure integracja z urządzeniami jest zazwyczaj wykonywana za pomocą usługi IoT Hub. Usługa IoT Hub udostępnia routing wiadomości na podstawie zawartości i włącza typ funkcji opisanych wcześniej.

![Komunikaty IoT](./media/integration-patterns/iot.png)

Proces przedstawia wzór:

-   Urządzenie komunikuje się bezpośrednio lub za pośrednictwem bramy polowej do usługi IoT Hub.
-   Usługa IoT Hub odbiera wiadomości i ocenia wiadomości względem utworzonych tras, które sprawdzają zawartość wiadomości, na przykład. *Czy czujnik zgłasza temperaturę większą niż 50 stopni?*
-   Centrum IoT hub wysyła komunikaty, które spełniają kryteria do zdefiniowanej usługi Service Bus dla trasy.
-   Aplikacja logiki lub inny kod nasłuchuje usługi Service Bus, który usługa IoT Hub ustanowił dla trasy.
-   Aplikacja logiki lub inny kod pobiera i przekształca wiadomość do znanego formatu.
-   Przekształcona wiadomość, teraz w standardowym formacie, jest wysyłana do usługi Service Bus dla azure blockchain workbench.
-   Azure Blockchain Workbench jest subskrybowany do zdarzeń z usługi Service Bus i pobiera komunikat.
-   Azure Blockchain Workbench inicjuje wywołanie księgi, wysyłając dane z systemu zewnętrznego do określonego kontraktu.
-   Po otrzymaniu wiadomości, umowa ocenia dane i może zmienić stan na podstawie wyniku tej oceny, na przykład dla wysokiej temperatury, zmienić stan na *Out of Compliance*.

## <a name="data-integration"></a>Integracja danych

Oprócz interfejsu API opartego na rest i wiadomości, Azure Blockchain Workbench zapewnia również dostęp do bazy danych SQL wypełnionych metaoprawnikami aplikacji i umowy, a także danymi transakcyjnymi z rozproszonych książek.

![Integracja danych](./media/integration-patterns/data-integration.png)

Integracja danych jest dobrze znana:

-   Usługa Azure Blockchain Workbench przechowuje metadane dotyczące aplikacji, przepływów pracy, umów i transakcji w ramach normalnego zachowania operacyjnego.
-   Systemy zewnętrzne lub narzędzia zapewniają co najmniej jedno okno dialogowe ułatwiające zbieranie informacji o bazie danych, takich jak nazwa serwera bazy danych, nazwa bazy danych, typ uwierzytelniania, poświadczenia logowania i widoki bazy danych do wykorzystania.
-   Zapytania są zapisywane w widokach bazy danych SQL, aby ułatwić dalsze zużycie przez systemy zewnętrzne, usługi, raportowanie, narzędzia programistyczne i narzędzia zwiększające produktywność w przedsiębiorstwie.

## <a name="storage-integration"></a>Integracja pamięci masowej

Wiele scenariuszy może wymagać konieczności włączenia plików poświadcają. Z wielu powodów niewłaściwe jest umieszczanie plików w łańcuchu bloków. Zamiast tego typowym podejściem jest wykonanie skrótu kryptograficznego (na przykład SHA-256) względem pliku i udziału, który miesza się w rozproszonej księdze. Wykonywanie skrótu ponownie w dowolnym momencie w przyszłości należy zwrócić ten sam wynik. Jeśli plik zostanie zmodyfikowany, nawet jeśli tylko jeden piksel jest modyfikowany w obrazie, skrót zwraca inną wartość.

![Integracja pamięci masowej](./media/integration-patterns/storage-integration.png)

Wzór można zaimplementować tam, gdzie:

-   System zewnętrzny utrwala plik w mechanizmie magazynu, takim jak usługa Azure Storage.
-   Skrót jest generowany z plikiem lub plikiem i skojarzonymi metadanymi, takimi jak identyfikator właściciela, adres URL, pod którym znajduje się plik itp.
-   Skrót i wszelkie metadane są wysyłane do funkcji na inteligentnym kontrakcie, takich jak *FileAdded*
-   W przyszłości plik i meta-dane mogą być mieszane ponownie i porównywane z wartościami przechowywanymi w księdze.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Wymagania wstępne dotyczące implementowania wzorców integracji przy użyciu interfejsów API REST i message

Aby ułatwić zewnętrznemu systemowi lub urządzeniu interakcję z inteligentnym kontraktem za pomocą interfejsu API REST lub message, muszą wystąpić następujące

1. W usłudze Azure Active Directory dla konsorcjum tworzone jest konto reprezentujące zewnętrzny system lub urządzenie.
2. Jeden lub więcej odpowiednich umów inteligentnych dla aplikacji Azure Blockchain Workbench mają funkcje zdefiniowane do akceptowania zdarzeń z zewnętrznego systemu lub urządzenia.
3. Plik konfiguracji aplikacji dla inteligentnego kontraktu zawiera rolę, którą jest przypisana system lub urządzenie.
4. Plik konfiguracji aplikacji dla inteligentnego kontraktu identyfikuje, w których stanach ta funkcja jest wywoływana przez zdefiniowaną rolę.
5. Plik konfiguracji aplikacji i jego inteligentne kontrakty są przekazywane do usługi Azure Blockchain Workbench.

Po przekazaniu aplikacji konto usługi Azure Active Directory dla systemu zewnętrznego jest przypisane do umowy i skojarzonej roli.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testowanie przepływów integracji systemu zewnętrznego przed zapisaniem kodu integracji 

Integracja z systemami zewnętrznymi jest kluczowym wymogiem wielu scenariuszy. Pożądane jest, aby móc zweryfikować inteligentny projekt kontraktu przed lub równolegle do rozwoju kodu do integracji z systemami zewnętrznymi.

Korzystanie z usługi Azure Active Directory (Azure AD) może znacznie przyspieszyć produktywność deweloperów i czas do wartości. W szczególności integracji kodu z systemem zewnętrznym może zająć nietrywialne ilość czasu. Korzystając z usługi Azure AD i automatycznej generacji środowiska użytkownika przez usługę Azure Blockchain Workbench, można zezwolić deweloperom na logowanie się do blockchain workbench jako systemu zewnętrznego i wypełnianie wartości z systemu zewnętrznego za pośrednictwem środowiska użytkownika. Można szybko rozwijać i weryfikować pomysły w środowisku weryfikacji koncepcji przed zapisaniem kodu integracji dla systemów zewnętrznych.
