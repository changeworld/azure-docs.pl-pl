---
title: Blokada Smart kontraktu wzorców integracji w aplikacji Azure Blockchain Workbench
description: Omówienie inteligentne kontraktu wzorców integracji w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 11e0e1436e3f640c30fec5e8d6fd9ca10adbd707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60867691"
---
# <a name="smart-contract-integration-patterns"></a>Wzorce integracji inteligentne kontraktu

Kontrakty inteligentne często reprezentują biznesowego przepływu pracy wymagającej integrację z zewnętrznymi systemami i urządzeń.

Wymagania te przepływy pracy obejmują konieczność inicjowania transakcji na rejestru rozproszonego, które zawierają dane z systemu zewnętrznego, usługi lub urządzenia. Muszą również mieć systemy zewnętrzne, reagowanie na zdarzenia pochodzące z kontraktów inteligentnych na rejestru rozproszonego.

Interfejs API REST i integracja obsługi komunikatów wysyła transakcji z systemami zewnętrznymi, do kontraktów inteligentnych zawartych w aplikacji Azure Blockchain Workbench. Wysyła również powiadomienia o zdarzeniach z systemami zewnętrznymi, w oparciu o zmiany, które odbywają się w obrębie aplikacji.

W przypadku scenariuszy integracji danych aplikacji Azure Blockchain Workbench obejmuje zestaw widoków bazy danych, które łączą się z kombinacją dane transakcyjne z łańcucha bloków i metadane dotyczące aplikacji i narzędzi smart Contract.

Ponadto niektóre scenariusze, takie jak te związane z łańcucha lub nośników, może również wymagać integracji dokumentów. Gdy w aplikacji Azure Blockchain Workbench nie zapewnia, że wywołania interfejsu API do obsługi dokumentów bezpośrednio, dokumenty można zintegrować aplikacji łańcucha bloków. Ta sekcja zawiera również tego wzorca.

Ta sekcja zawiera wzorce określone dla wdrażania każdego z tych typów integracji w rozwiązaniach typu end to end.

## <a name="rest-api-based-integration"></a>Integracja z interfejsu API REST

Możliwości w ramach aplikacji Azure Blockchain Workbench generowane aplikację sieci web są udostępniane za pośrednictwem interfejsu API REST. Funkcje obejmują przekazywanie aplikacji Azure Blockchain Workbench, konfiguracji i administracji aplikacjami, wysyłanie transakcji do rejestru rozproszonego i wysyłania zapytań do metadanych i rejestr danych aplikacji.

Interfejs API REST jest używany głównie dla klientów interaktywnych, takich jak sieci web, mobilnych i bot aplikacji.

W tej sekcji omówiono wzorce koncentruje się na aspektach interfejsu API REST, wysyłających transakcji do rejestru rozproszonego i wzorców tego zapytania o dane dotyczące transakcji z platformy Azure Blockchain Workbench *wyłączone łańcucha* bazy danych SQL.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Wysyłanie transakcje rozproszone rejestr z systemu zewnętrznego

Interfejs API REST Azure Blockchain Workbench wysyła uwierzytelnionego żądania do wykonania transakcji na rejestru rozproszonego.

![Wysyłanie transakcji do rejestru rozproszonego](./media/integration-patterns/send-transactions-ledger.png)

Wykonywanie transakcji występuje, przy użyciu procesu przedstawiony wcześniej, gdzie:

-   Zewnętrzna aplikacja uwierzytelnia się z usługą Azure Active Directory aprowizowany w ramach wdrażania aplikacji Azure Blockchain Workbench.
-   Autoryzowani użytkownicy otrzymują tokenu elementu nośnego, który można wysłać żądań do interfejsu API.
-   Aplikacjami zewnętrznymi wywołań interfejsu API REST przy użyciu tokenu elementu nośnego.
-   Interfejs API REST żądanie, jak jeden komunikat i wysyła je do usługi Service Bus. W tym miejscu jest pobierana, podpisane i wysyłane do odpowiedniego rejestru rozproszonego.
-   Interfejs API REST kieruje żądanie do Blockchain Workbench bazy danych SQL Azure do zapisywania żądania i ustanowić bieżący stan inicjowania obsługi administracyjnej.
-   Bazy danych SQL zwraca stan inicjowania obsługi administracyjnej i wywołania interfejsu API zwraca identyfikator aplikacji zewnętrznej, która nazwała go.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Wysyłanie zapytań dotyczących metadanych aplikacji Blockchain Workbench i transakcje rozproszone finansowe

Interfejs API REST Azure Blockchain Workbench wysyła uwierzytelnionego żądania do Szczegóły zapytania związane z wykonaniem inteligentne kontraktu na rejestru rozproszonego.

![Tworzenie zapytań metadanych](./media/integration-patterns/querying-metadata.png)

Wykonywanie zapytań występuje, przy użyciu procesu przedstawiony wcześniej, gdzie:

1. Zewnętrzna aplikacja uwierzytelnia się z usługą Azure Active Directory aprowizowany w ramach wdrażania aplikacji Azure Blockchain Workbench.
2. Autoryzowani użytkownicy otrzymują tokenu elementu nośnego, który można wysłać żądań do interfejsu API.
3. Aplikacjami zewnętrznymi wywołań interfejsu API REST przy użyciu tokenu elementu nośnego.
4. Interfejs API REST wykonuje zapytania o dane na żądanie z bazy danych SQL i zwraca go do klienta.

## <a name="messaging-integration"></a>Integracja obsługi komunikatów

Wiadomości integracji umożliwia interakcję z systemów, usług i urządzeń, w którym interakcyjnego logowania nie jest niemożliwe lub niepożądane. Integracja obsługi komunikatów, który koncentruje się na dwa typy komunikatów: komunikaty żądania transakcji można wykonać względem rejestru rozproszonego i zdarzenia udostępniane przez ten rejestr, gdy transakcji miały miejsce.

Integracja obsługi komunikatów, który koncentruje się na wykonywanie i monitorowanie transakcji związanych z tworzenia użytkownika, kontrakt tworzenia i wykonywanie transakcji w kontraktach i jest używany głównie *bezobsługowego* systemów zaplecza.

W tej sekcji omówiono wzorce koncentruje się na aspektach oparta na komunikatach interfejsu API, który Wyślij transakcje rozproszone księdze i wzorców, które reprezentują udostępnianych przez bazowego rejestru rozproszonego komunikaty o zdarzeniach.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Dostarczanie zdarzeń jednokierunkowe z inteligentnych umowy do odbiorcy zdarzeń 

W tym scenariuszu występuje zdarzenie w ramach inteligentnych umowy, na przykład zmiany stanu lub wykonania określonego typu transakcji. To zdarzenie jest emitowany za pośrednictwem usługi Event Grid do klientów transmisji i tych konsumentów, a następnie podjąć odpowiednie działania.

Przykładem tego scenariusza jest, że w przypadku transakcji odbiorcy będą otrzymywać alerty i może podejmować działań, takich jak rejestrowanie informacji w bazie danych SQL lub usługi Common Data Service. Ten scenariusz jest tym samym wzorcem, występującego w aplikacji Workbench do wypełnienia jego *wyłączone łańcucha* bazy danych SQL.

Będzie inny, jeśli kontrakt inteligentne przechodzi do określonego stanu, na przykład gdy kontrakt przechodzi w *OutOfCompliance*. W przypadku tej zmiany stanu wyzwalać alert zostanie wysłane na telefon komórkowy przez administratora.

![Dostarczanie zdarzeń jednokierunkowe](./media/integration-patterns/one-way-event-delivery.png)

W tym scenariuszu występuje przy użyciu procesu przedstawiony wcześniej, gdzie:

-   Inteligentne kontraktu przechodzi do stanu nowych i wysyła zdarzenie do rejestru.
-   Księgi odbiera i dostarcza zdarzenia do aplikacji Azure Blockchain Workbench.
-   Azure Blockchain Workbench subskrybuje zdarzenia z księgi i odbiera zdarzenia.
-   Azure Blockchain Workbench publikuje zdarzenie subskrybentom w usługi Event Grid.
-   Systemy zewnętrzne mają subskrypcję usługi Event Grid, używanie wiadomości i podjąć odpowiednie działania.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Dostarczanie zdarzeń jednokierunkowe wiadomości z zewnętrznego systemu do inteligentnego kontraktu

Istnieje również scenariusz, który przepływa w odwrotnym kierunku. W tym przypadku zdarzenie jest generowane przez czujnik lub zewnętrznego systemu i dane z tego zdarzenia, które mają być wysyłane do inteligentnego kontraktu.

Typowym przykładem jest dostarczanie danych z rynków finansowych, z na przykład ceny towarów, akcji i zobowiązania, inteligentne kontraktu.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie aplikacji Azure Blockchain Workbench w oczekiwanym formacie

Niektóre aplikacje są kompilowane do integracji z aplikacji Azure Blockchain Workbench bezpośrednio generuje i wysyłanie komunikatów w oczekiwanym formacie.

![Bezpośrednie dostarczanie](./media/integration-patterns/direct-delivery.png)

Ta dostarczania wiadomości, że przy użyciu procesu przedstawiony wcześniej, gdzie:

-   Zdarzenie występuje w systemie zewnętrznym, wyzwalającego tworzenia wiadomości dla aplikacji Azure Blockchain Workbench.
-   System zewnętrzny ma kodu napisanego w celu tworzenia tego komunikatu w znanym formacie i wysyła je bezpośrednio z usługą Service Bus.
-   Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera komunikat.
-   Azure Blockchain Workbench inicjuje wywołanie w księdze wysyłanie danych z systemu zewnętrznego do określonej umowy.
-   Po otrzymaniu komunikatu kontrakt przechodzi w stan nowego.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie komunikatów w formacie nieznany do aplikacji Azure Blockchain Workbench

Niektóre systemy nie można zmodyfikować w celu dostarczenia komunikatu w standardowych formatów używanych przez aplikacji Azure Blockchain Workbench. W tych przypadkach istniejących mechanizmów i komunikat można często używane w formacie tych systemów. W szczególności typy natywne wiadomości te systemy mogą zostać przekształcone przy użyciu Logic Apps, Azure Functions lub inny kod niestandardowy do mapowania do jednej ze standardowych komunikatów oczekiwane formaty.

![Format nieznany komunikat](./media/integration-patterns/unknown-message-format.png)

Dzieje się tak, że przy użyciu procesu przedstawiony wcześniej, gdzie:

-   W systemie zewnętrznym, wyzwalającego tworzenia komunikat o wystąpieniu zdarzenia.
-   Aplikacji logiki lub kod niestandardowy jest używany do odbierania tego komunikatu i przekształcić je do standardowych aplikacji Azure Blockchain Workbench sformatowany komunikat.
-   Aplikacja logiki wysyła przekształcony komunikat bezpośrednio z usługą Service Bus.
-   Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera komunikat.
-   Azure Blockchain Workbench inicjuje wywołanie w księdze wysyłanie danych z systemu zewnętrznego do konkretną funkcję w umowie.
-   Funkcja wykonuje i zazwyczaj zmienia stan. Zmiana stanu przenosi do przodu biznesowego przepływu pracy zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, aby teraz wykonywane zgodnie z potrzebami.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Przeniesienie kontrolki zewnętrznej przetwarzania i poczekać na ukończenie

Istnieją scenariusze, w którym inteligentne kontraktu należy zatrzymać wykonywanie wewnętrzne i dostarcz procesu zewnętrznego. Czy Proces zewnętrzny, następnie należy wykonać, Wyślij wiadomość do inteligentnego kontraktu i wykonywanie następnie będzie nadal w ramach inteligentnych kontraktu.

#### <a name="transition-to-the-external-process"></a>Przejście w procesie zewnętrznym

Ten wzorzec jest zwykle implementowany przy użyciu następujących podejść:

-   Inteligentne kontraktu przejść do określonego stanu. W tym stanie, albo nie lub ograniczoną liczbę funkcji mogą być wykonywane, dopóki systemu zewnętrznego ma odpowiednią akcję.
-   Zmiany stanu jest udostępniane jako zdarzenie z klientem podrzędnymi.
-   Podrzędny odbiorcy odbiera zdarzenia i wyzwala wykonanie kodu zewnętrznego.

![Kontrolki przejście procesu zewnętrznego](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Powrót sterowania z inteligentnych kontraktu

W zależności od możliwości dostosowywania systemu zewnętrznego może lub nie można dostarczać komunikatów w jednym z formatów standardowych, których oczekuje aplikacji Azure Blockchain Workbench. Oparta na możliwości systemów zewnętrznych do generowania jednego z tych komunikatów ustalić, które z następujących dwóch zwracają ścieżki jest zajęta.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie aplikacji Azure Blockchain Workbench w oczekiwanym formacie

![](./media/integration-patterns/direct-delivery.png)

W tym modelu komunikatu kontraktu i zmianę stanu kolejnych występuje po poprzedniego procesu gdzie -

-   Gdy wykorzystasz zakończenia lub punktu kontrolnego określonej podczas wykonywania kodu zewnętrznego, zdarzenia są wysyłane do usługi Service Bus, nawiązanie połączenia aplikacji Azure Blockchain Workbench.

-   Dla systemów, które nie mogą być bezpośrednio dostosowane do zapisu komunikatu, który odpowiada oczekiwaniom interfejsu API jest on przekształcany.

-   Zawartość komunikatu jest spakowany i wysłany na konkretną funkcję w kontrakcie inteligentne. To dostarczanie odbywa się w imieniu użytkownika skojarzonego z systemu zewnętrznego.

-   Funkcja wykonuje i zazwyczaj zmienia stan. Zmiana stanu przenosi do przodu biznesowego przepływu pracy zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, aby teraz wykonywane zgodnie z potrzebami.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie komunikatów w formacie nieznany do aplikacji Azure Blockchain Workbench

![Format nieznany komunikat](./media/integration-patterns/unknown-message-format.png)

W tym modelu, gdzie w standardowym formacie nie można wysłać wiadomości bezpośrednio, komunikację z usługą kontrakt i nastąpi zmiana stanu kolejnych po poprzednim przetwarzania where:

1.  Gdy wykorzystasz zakończenia lub punktu kontrolnego określonej podczas wykonywania kodu zewnętrznego, zdarzenia są wysyłane do usługi Service Bus, nawiązanie połączenia aplikacji Azure Blockchain Workbench.
2.  Aplikacji logiki lub kod niestandardowy jest używany do odbierania tego komunikatu i przekształcić je do standardowych aplikacji Azure Blockchain Workbench sformatowany komunikat.
3.  Aplikacja logiki wysyła przekształcony komunikat bezpośrednio z usługą Service Bus.
4.  Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera komunikat.
5.  Azure Blockchain Workbench inicjuje wywołanie w księdze wysyłanie danych z systemu zewnętrznego do określonej umowy.
6. Zawartość komunikatu jest spakowany i wysłany na konkretną funkcję w kontrakcie inteligentne. To dostarczanie odbywa się w imieniu użytkownika skojarzonego z systemu zewnętrznego.
7.  Funkcja wykonuje i zazwyczaj zmienia stan. Zmiana stanu przenosi do przodu biznesowego przepływu pracy zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, aby teraz wykonywane zgodnie z potrzebami.

## <a name="iot-integration"></a>Integracja z IoT

Typowy scenariusz integracji jest uwzględnienie pobranych z czujników w inteligentne kontraktu danych telemetrii. Na podstawie danych dostarczanych przez czujniki, kontraktów inteligentnych może podjąć świadomej działania i zmienić stan zamówienia.

Na przykład jeśli ciężarówki dostarczanie medycyna temperatury wzrastać do 110 stopni, go może mieć wpływ na efektywność lekarstwa i może spowodować, że problem bezpieczeństwa publicznego w przeciwnym razie Wykryto i usunięto z łańcucha dostaw. Jeśli sterownik accelerated jego samochodu do 100 mil na godzinę, wynikowy informacji z czujnika może wywołać anulowania ubezpieczenia przez jego dostawcę ubezpieczenia. Jeśli samochodu samochodu wypożyczeń, GPS danych może wskazywać, kiedy poszło sterownika poza lokalizację geograficzną objętych umową ich dzierżawa i karę jest opłata w wysokości.

Żądania jest czujniki te można dostarczania danych na stałym poziomie i nie jest właściwe wysłać wszystkie te dane do inteligentnego kontraktu. Typowym podejściem jest, aby ograniczyć liczbę komunikatów wysłanych do łańcucha bloków, dostarczając wszystkie komunikaty do magazynu pomocniczego. Na przykład dostarczanie komunikatów odebranych przy tylko stałych interwałów, na przykład raz na godzinę, a gdy zawartej wartość mieści się poza uzgodnionych od zakresu dla inteligentnych kontraktu. Sprawdzanie wartości wykraczające poza tolerancji, gwarantuje, że do danych odpowiednich dla kontraktów logiki biznesowej jest odbierane i wykonywane. Sprawdzenie wartości z interwałem potwierdza nadal przeprowadza raportowanie czujnika. Wszystkie dane są wysyłane do magazynu pomocniczego raportowania umożliwiające szersze raportowania, analizy i uczenia maszynowego. Na przykład wprowadzenie odczyty czujników z GPS może nie być wymagane co minutę dla inteligentnych kontraktu, można dostarczyć interesujące dane, które ma być używany w raportach lub mapowania tras.

Na platformie Azure integracji z urządzeniami odbywa się zwykle z usługą IoT Hub. Usługi IoT Hub umożliwia routing komunikatów na podstawie zawartości i włącza typ tworzonego funkcji opisanych powyżej.

![Komunikatów Internetu rzeczy](./media/integration-patterns/iot.png)

Proces przedstawiono wzorzec:

-   Urządzenia komunikuje się bezpośrednio lub za pośrednictwem bramy w terenie do usługi IoT Hub.
-   Usługa IoT Hub odbiera komunikaty, a wartość komunikatów dla trasy ustanowione, sprawdź zawartość wiadomości, na przykład. *Czujnik bada temperatury większą niż 50 stopni?*
-   Usługa IoT Hub wysyła komunikaty, które spełniają kryteria zdefiniowane usługi Service Bus dla danej trasy.
-   Aplikację logiki lub inny kod nasłuchuje do usługi Service Bus, która ustanowiona usługi IoT Hub dla danej trasy.
-   Aplikacja logiki lub inny kod pobiera i przekształcenia komunikatu do znanego formatu.
-   Przekształcony komunikat teraz w standardowym formacie, są wysyłane do usługi Service Bus dla aplikacji Azure Blockchain Workbench.
-   Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera komunikat.
-   Azure Blockchain Workbench inicjuje wywołanie w księdze wysyłanie danych z systemu zewnętrznego do określonej umowy.
-   Po otrzymaniu komunikatu, kontrakt oblicza dane i może zmiany stanu, w oparciu o wyniki tej oceny, na przykład dla temperatura, zmienić stan na *zgodności z*.

## <a name="data-integration"></a>Integracja danych

Oprócz REST i interfejs API oparty na komunikat aplikacji Azure Blockchain Workbench umożliwia również dostęp do bazy danych SQL wypełniane przy użyciu aplikacji i kontrakt metadane, a także dane transakcyjne z rejestrów rozproszonych.

![Integracja danych](./media/integration-patterns/data-integration.png)

Integracja danych jest dobrze znany:

-   Azure Blockchain Workbench są przechowywane metadane dotyczące aplikacji, przepływów pracy, kontrakty i transakcji jako część normalnego zachowania operacyjne.
-   Systemy zewnętrzne lub narzędzi należy podać jeden lub więcej okien dialogowych ułatwiające zbieranie informacji o bazie danych, takie jak nazwa serwera bazy danych, nazwę bazy danych, typ uwierzytelniania, poświadczenia logowania i bazę danych, która wyświetla korzystanie z.
-   Zapytania są zapisywane względem widoki bazy danych SQL, ułatwiające podrzędnego użycie przez systemy zewnętrzne, usługi, raportowanie, narzędzi deweloperskich i narzędzi zwiększających produktywność w przedsiębiorstwie.

## <a name="storage-integration"></a>Integracja magazynu

Wiele scenariuszy może wymagać trzeba zastosować attestable plików. Kilka przyczyn nie jest umieścić pliki w łańcuch bloków. Zamiast tego typowym podejściem jest, aby wykonać skrót kryptograficzny (na przykład SHA-256) dla pliku i udostępnić ten skrót rejestru rozproszonego. Wykonywanie skrót ponownie w dowolnym momencie w przyszłości powinna zwracać ten sam wynik. Jeśli plik zostanie zmodyfikowany, nawet jeśli modyfikowany jest tylko jeden piksel obrazu skrót zwraca inną wartość.

![Integracja magazynu](./media/integration-patterns/storage-integration.png)

Wzorzec można zaimplementować gdzie:

-   System zewnętrzny będzie nadal występować pliku w mechanizm magazynu, takich jak Azure Storage.
-   Skrót jest generowany przy użyciu pliku lub pliku oraz skojarzone metadane takie jak identyfikator właściciela, adres URL, w którym znajduje się plik, itp.
-   Skrót, a wszystkie metadane są wysyłane do funkcji w kontrakcie inteligentne, takie jak *FileAdded*
-   W przyszłości plików i metadanych może być mieszany ponownie i porównana z wartościami przechowywanymi w księdze.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Wymagania wstępne dotyczące wdrażania wzorce integracji przy użyciu protokołu REST i interfejsów API wiadomości

W celu ułatwienia możliwość interakcji z kontraktem inteligentnych przy użyciu REST lub komunikat interfejsu API dla systemu zewnętrznego lub urządzenia, że musi wystąpić-

1. W usłudze Azure Active Directory przez konsorcjum zostanie utworzone konto reprezentujący zewnętrzne systemowej lub urządzenia.
2. Co najmniej jeden odpowiednich kontraktów inteligentnych aplikacji Azure Blockchain Workbench ma funkcje zdefiniowane w celu akceptowania zdarzeń z systemu zewnętrznego lub urządzenia.
3. Plik konfiguracji aplikacji dla Twojej umowy inteligentnych zawiera roli, co system lub urządzenie przypisane.
4. Plik konfiguracji aplikacji dla Twojej umowy inteligentne identyfikuje w której z informacją, że ta funkcja jest wywoływana przez zdefiniowanej roli.
5. Plik konfiguracji aplikacji i jej kontraktów inteligentnych są przekazywane do aplikacji Azure Blockchain Workbench.

Po przekazaniu aplikacji, konto usługi Azure Active Directory do systemu zewnętrznego jest przypisany do Umowy i skojarzonych ról.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testowanie przepływów integracji systemu zewnętrznego przed pisania kodu integracji 

Integracja z systemami zewnętrznymi to decydujące znaczenie dla wielu scenariuszy. Jest to pożądane, aby można było zweryfikować inteligentne kontraktu projekt wcześniejsze lub równolegle w celu opracowywania kodu integracji z zewnętrznymi systemami.

Korzystanie z usługi Azure Active Directory (Azure AD) można znacznie przyspieszyć deweloperskiej i godziny na wartość. W szczególności integracji kodu za pomocą zewnętrznego systemu może potrwać nietrywialnymi ilość czasu. Za pomocą usługi Azure AD i Autogenerowanie środowiska użytkownika w aplikacji Azure Blockchain Workbench, można umożliwić programistom Zaloguj się do aplikacji Blockchain Workbench jako systemu zewnętrznego i wypełnić wartości z systemu zewnętrznego za pośrednictwem najlepsze środowisko użytkownika. Można szybko twórz i weryfikowania pomysłów środowiska weryfikacji koncepcji, przed kodem integracji jest przeznaczony dla systemów zewnętrznych.
