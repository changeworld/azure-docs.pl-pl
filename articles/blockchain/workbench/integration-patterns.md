---
title: Blokada Smart kontraktu wzorców integracji w aplikacji Azure Blockchain Workbench
description: Omówienie inteligentne kontraktu wzorców integracji w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e1bc16d32167d62d5f66f64bb383fcceeb79eb5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267151"
---
# <a name="smart-contract-integration-patterns"></a>Wzorce integracji inteligentne kontraktu

Kontrakty inteligentne często będzie reprezentować biznesowego przepływu pracy wymagającej integrację z zewnętrznymi systemami i urządzeń.

Wymagania te przepływy pracy obejmują konieczność inicjowania transakcji na rejestru rozproszonego, które zawierają dane z systemu zewnętrznego, usługi lub urządzenia. Muszą również mieć systemy zewnętrzne, reagowanie na zdarzenia pochodzące z kontraktów inteligentnych na rejestru rozproszonego.

Interfejs API REST i integracja obsługi komunikatów zapewniają możliwość zarówno wysyłać z systemów zewnętrznych kontraktów inteligentnych zawartych w aplikacji Azure Blockchain Workbench transakcji, jak i Wysyłaj powiadomienia o zdarzeniach z systemami zewnętrznymi, w oparciu o zmiany, które przyjmują Umieść w aplikacji.

W przypadku scenariuszy integracji danych aplikacji Azure Blockchain Workbench obejmuje zestaw widoków bazy danych, które łączą się z kombinacją dane transakcyjne z łańcucha bloków i metadane dotyczące aplikacji i narzędzi smart Contract.

Ponadto niektóre scenariusze, takie jak te związane z łańcucha lub nośników, może również wymagać integracji dokumentów. Gdy w aplikacji Azure Blockchain Workbench nie zapewnia, że wywołania interfejsu API do obsługi dokumentów bezpośrednio, dokumenty można zintegrować aplikacji łańcucha bloków na platformie Azure. Ta sekcja zawiera również tego wzorca.

Ta sekcja zawiera wzorce określone dla wdrażania każdego z tych typów integracji w rozwiązaniach typu end to end.

## <a name="rest-api-based-integration"></a>Integracja z interfejsu API REST

Możliwości w ramach aplikacji Azure Blockchain Workbench generowane aplikację sieci web są udostępniane za pośrednictwem interfejsu API REST. Funkcje obejmują przekazywanie aplikacji Azure Blockchain Workbench, konfiguracji i administracji aplikacjami, wysyłanie transakcji do rejestru rozproszonego i wysyłania zapytań do metadanych i rejestr danych aplikacji.

Interfejs API REST jest używany głównie dla klientów interaktywnych, takich jak sieci web, mobilnych i bot aplikacji.

W tej sekcji omówiono wzorce koncentruje się na aspektach interfejsu API REST, które wysyłają transakcji do rejestru rozproszonego oraz te, które zapytania o dane dotyczące transakcji z platformy Azure Blockchain Workbench *wyłączone łańcucha* bazy danych SQL.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Wysyłanie transakcje rozproszone rejestr z systemu zewnętrznego

Interfejsu API REST usługi Azure Blockchain Workbench umożliwia wysyłanie żądań uwierzytelnionych do wykonania transakcji na rejestru rozproszonego.

![Wysyłanie transakcji do rejestru rozproszonego](./media/integration-patterns/send-transactions-ledger.png)

Ten problem wystąpi, za pomocą proces przedstawiony powyżej, gdzie:

-   Zewnętrzna aplikacja uwierzytelnia się z usługą Azure Active Directory aprowizowany w ramach wdrażania aplikacji Azure Blockchain Workbench.
-   Autoryzowani użytkownicy otrzymują tokenu elementu nośnego, który można wysłać żądań do interfejsu API.
-   Aplikacjami zewnętrznymi wywołań interfejsu API REST przy użyciu tokenu elementu nośnego.
-   Interfejs API REST żądanie, jak jeden komunikat i wysyła je do usługi Service Bus. W tym miejscu zostanie ono pobierane, podpisane i wysyłane do odpowiedniego rejestru rozproszonego.
-   Interfejs API REST kieruje żądanie do Blockchain Workbench bazy danych SQL Azure do zapisywania żądania i ustanowić bieżący stan inicjowania obsługi administracyjnej.
-   Bazy danych SQL zwraca stan inicjowania obsługi administracyjnej i wywołania interfejsu API zwraca identyfikator aplikacji zewnętrznej, która nazwała go.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Wysyłanie zapytań dotyczących metadanych aplikacji Blockchain Workbench i transakcje rozproszone finansowe

Interfejs API REST Azure Blockchain Workbench umożliwia wysyłać żądań uwierzytelnionych Szczegóły zapytania związane z wykonaniem inteligentne kontraktu na rejestru rozproszonego.

![Tworzenie zapytań metadanych](./media/integration-patterns/querying-metadata.png)

Ten problem wystąpi, za pomocą proces przedstawiony powyżej, gdzie:

1. Zewnętrzna aplikacja uwierzytelnia się z usługą Azure Active Directory aprowizowany w ramach wdrażania aplikacji Azure Blockchain Workbench.
2. Autoryzowani użytkownicy otrzymują tokenu elementu nośnego, który można wysłać żądań do interfejsu API.
3. Aplikacjami zewnętrznymi wywołań interfejsu API REST przy użyciu tokenu elementu nośnego.
4. Interfejs API REST wykonuje zapytania o dane na żądanie z bazy danych SQL i zwraca go do klienta.

## <a name="messaging-integration"></a>Integracja obsługi komunikatów

Komunikatów integracji umożliwia interakcję z systemów, usług i urządzeń, w których logowanie interakcyjne nie jest niemożliwe lub niepożądane. Integracja obsługi komunikatów koncentruje się na dwa typy komunikatów, których żądać, że transakcji można wykonać względem rejestru rozproszonego oraz zdarzenia, które są dostępne w tym rejestr, gdy transakcje miały miejsce.

Integracja obsługi komunikatów, który koncentruje się na wykonywanie i monitorowanie transakcji związanych z tworzenia użytkownika, kontrakt tworzenia i wykonywanie transakcji w kontraktach i jest używany głównie *bezobsługowego* systemów zaplecza.

W tej sekcji omówiono wzorce koncentruje się na aspektach API oparta na komunikatach wysyłających transakcji do rejestru rozproszonego oraz te, które reprezentują udostępnianych przez bazowego rejestru rozproszonego komunikaty o zdarzeniach.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Dostarczanie zdarzeń jednokierunkowe z inteligentnych umowy do odbiorcy zdarzeń 

W tym scenariuszu występuje zdarzenie w ramach inteligentnych umowy, na przykład zmiany stanu lub wykonania określonego typu transakcji. To zdarzenie jest emitowany za pośrednictwem usługi Event Grid do klientów transmisji i tych konsumentów, a następnie podjąć odpowiednie działania.

Przykładem tego scenariusza jest, że w przypadku transakcji odbiorcy będą otrzymywać alerty i może podejmować działań, takich jak rejestrowanie informacji w bazie danych SQL lub usługi Common Data Service. Jest to ten sam wzorzec, występującego w aplikacji Workbench do wypełnienia jego *wyłączone łańcucha* bazy danych SQL.

Będzie inny, jeśli kontrakt inteligentne przechodzi do określonego stanu, na przykład gdy kontrakt przechodzi w *OutOfCompliance*. W przypadku tej zmiany stanu wyzwalać alert zostanie wysłane na telefon komórkowy przez administratora.

![Dostarczanie zdarzeń jednokierunkowe](./media/integration-patterns/one-way-event-delivery.png)

Ten problem wystąpi, za pomocą proces przedstawiony powyżej, gdzie:

-   Inteligentne kontraktu przechodzi do stanu nowych i wysyła zdarzenie do rejestru.
-   Księgi odbiera i dostarcza zdarzenia do aplikacji Azure Blockchain Workbench.
-   Azure Blockchain Workbench subskrybuje zdarzenia z księgi i odbiera zdarzenia.
-   Azure Blockchain Workbench publikuje zdarzenie subskrybentom w usługi Event Grid.
-   Systemy zewnętrzne mają subskrypcję usługi Event Grid, używanie wiadomość i wykonaj odpowiednie akcje.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Dostarczanie zdarzeń jednokierunkowe wiadomości z zewnętrznego systemu do inteligentnego kontraktu

Istnieje również scenariusz, który przepływa w odwrotnym kierunku. W tym przypadku zdarzenie jest generowane przez czujnik lub zewnętrznego systemu i dane z tego zdarzenia, które mają być wysyłane do inteligentnego kontraktu.

Typowym przykładem jest dostarczanie danych z rynków finansowych, z na przykład ceny towarów, akcji i zobowiązania, inteligentne kontraktu.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie aplikacji Azure Blockchain Workbench w oczekiwanym formacie

Niektóre aplikacje są kompilowane do integracji z aplikacji Azure Blockchain Workbench bezpośrednio generuje i wysyłanie komunikatów w oczekiwanym formacie.

![Bezpośrednie dostarczanie](./media/integration-patterns/direct-delivery.png)

Ten problem wystąpi, za pomocą proces przedstawiony powyżej, gdzie:

-   Zdarzenie występuje w systemie zewnętrznym, wyzwalającego tworzenia wiadomości dla aplikacji Azure Blockchain Workbench.
-   System zewnętrzny ma kodu napisanego w celu tworzenia tego komunikatu w znanym formacie i wysyła to bezpośrednio z usługą Service Bus.
-   Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera komunikat.
-   Azure Blockchain Workbench inicjuje wywołanie w księdze wysyłanie danych z systemu zewnętrznego do określonej umowy.
-   Po otrzymaniu komunikatu kontrakt przechodzi w stan nowego.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie komunikatów w formacie nieznany do aplikacji Azure Blockchain Workbench

Niektóre systemy nie można zmodyfikować w celu dostarczenia komunikatu w standardowych formatów używanych przez aplikacji Azure Blockchain Workbench. W tych przypadkach istniejących mechanizmów i komunikat można często używane w formacie tych systemów. W szczególności typy natywne wiadomości te systemy mogą zostać przekształcone przy użyciu Logic Apps, Azure Functions lub inny kod niestandardowy do mapowania do jednej ze standardowych komunikatów oczekiwane formaty.

![Format nieznany komunikat](./media/integration-patterns/unknown-message-format.png)

Ten problem wystąpi, za pomocą proces przedstawiony powyżej, gdzie:

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

W zależności od możliwości dostosowywania systemu zewnętrznego może lub nie można dostarczać komunikatów w jednym z formatów standardowych, których oczekuje aplikacji Azure Blockchain Workbench. Możliwości systemów zewnętrznych do generowania jeden z następujących komunikatów będzie jest określana na podstawie której następujące dwie ścieżki zwrotu zostaną wykonane.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie aplikacji Azure Blockchain Workbench w oczekiwanym formacie

![](./media/integration-patterns/direct-delivery.png)

W tym modelu kontraktu i zmianę stanu kolejnych odbywa się zgodnie z powyższymi przetwarzania where -

-   Gdy wykorzystasz zakończenia lub punktu kontrolnego określonej podczas wykonywania kodu zewnętrznego, zdarzenia są wysyłane do usługi Service Bus, nawiązanie połączenia aplikacji Azure Blockchain Workbench.

-   Dla systemów, które nie mogą być bezpośrednio dostosowane do zapisu komunikatu, który odpowiada oczekiwaniom interfejsu API zostanie on przekształcony.

-   Zawartość komunikatu jest spakowany i wysłany na konkretną funkcję w kontrakcie inteligentne. Można to zrobić w imieniu użytkownika skojarzonego z systemu zewnętrznego.

-   Funkcja wykonuje i zazwyczaj spowoduje zmiany stanu. Zmiana stanu przenosi do przodu biznesowego przepływu pracy zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, aby teraz wykonywane zgodnie z potrzebami.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie komunikatów w formacie nieznany do aplikacji Azure Blockchain Workbench

![Format nieznany komunikat](./media/integration-patterns/unknown-message-format.png)

W tym modelu, gdzie w standardowym formacie nie można wysłać wiadomości bezpośrednio, komunikację z usługą kontrakt i nastąpi zmiana stanu kolejnych zgodnie z powyższymi przetwarzania where:

1.  Gdy wykorzystasz zakończenia lub punktu kontrolnego określonej podczas wykonywania kodu zewnętrznego, zdarzenia są wysyłane do usługi Service Bus, nawiązanie połączenia aplikacji Azure Blockchain Workbench.
2.  Aplikacji logiki lub kod niestandardowy jest używany do odbierania tego komunikatu i przekształcić je do standardowych aplikacji Azure Blockchain Workbench sformatowany komunikat.
3.  Aplikacja logiki wysyła przekształcony komunikat bezpośrednio z usługą Service Bus.
4.  Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera komunikat.
5.  Azure Blockchain Workbench inicjuje wywołanie w księdze wysyłanie danych z systemu zewnętrznego do określonej umowy.
6. Zawartość komunikatu jest spakowany i wysłany na konkretną funkcję w kontrakcie inteligentne. Można to zrobić w imieniu użytkownika skojarzonego z systemu zewnętrznego.
7.  Funkcja wykonuje i zazwyczaj spowoduje zmiany stanu. Zmiana stanu przenosi do przodu biznesowego przepływu pracy zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, aby teraz wykonywane zgodnie z potrzebami.

## <a name="iot-integration"></a>Integracja z IoT

Typowy scenariusz integracji jest uwzględnienie pobranych z czujników w inteligentne kontraktu danych telemetrii. Na podstawie danych dostarczanych przez czujniki, kontraktów inteligentnych może podjąć świadomej działania i zmienić stan zamówienia.

Na przykład jeśli ciężarówki dostarczanie medycyna temperatury wzrastać do 110 stopni, go może mieć wpływ na efektywność lekarstwa i może spowodować, że problem bezpieczeństwa publicznego w przeciwnym razie Wykryto i usunięto z łańcucha dostaw. Jeśli sterownik accelerated jego samochodu do 100 mil na godzinę, wynikowy informacji z czujnika może wywołać anulowania ubezpieczenia przez jego dostawcę ubezpieczenia. Jeśli samochodu samochodu wypożyczeń, GPS danych może wskazywać, kiedy sterownika wystąpił poza jego umową wypożyczeń lokalizację geograficzną i karę jest opłata w wysokości.

Żądania jest czujniki te można dostarczania danych na stałym poziomie i nie jest właściwe wysłać wszystkie te dane do inteligentnego kontraktu. Typowym podejściem jest, aby ograniczyć liczbę komunikatów wysłanych do łańcucha bloków, dostarczając wszystkie komunikaty do magazynu pomocniczego. Na przykład dostarczanie komunikatów odebranych przy tylko stałych interwałów, na przykład raz na godzinę, a gdy zawartej wartość mieści się poza uzgodnionych od zakresu dla inteligentnych kontraktu. Sprawdzanie wartości wykraczające poza tolerancji, gwarantuje, że do danych odpowiednich dla kontraktów logiki biznesowej jest odbierane i wykonywane. Sprawdzenie wartości z interwałem potwierdza nadal przeprowadza raportowanie czujnika. Wszystkie dane są wysyłane do magazynu pomocniczego raportowania umożliwiające szersze raportowania, analizy i uczenia maszynowego. Na przykład wprowadzenie odczyty czujników z GPS może nie być wymagane co minutę dla inteligentnych kontraktu, można dostarczyć interesujące dane, które ma być używany w raportach lub mapowania tras.

Na platformie Azure integracji z urządzeniami odbywa się zwykle z usługą IoT Hub. Usługi IoT Hub umożliwia routing komunikatów na podstawie zawartości i włącza typ tworzonego funkcji opisanych powyżej.

![Komunikatów Internetu rzeczy](./media/integration-patterns/iot.png)

Powyższy proces zebrano wzorca, aby w ten sposób jest implementowany:

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

Wiele scenariuszy może wymagać trzeba zastosować attestable plików. Kilka przyczyn będzie ona nieodpowiednie blokować pliki z łańcucha bloków. Zamiast tego typowym podejściem jest, aby wykonać skrót kryptograficzny (na przykład SHA-256) dla pliku i udostępnić ten skrót rejestru rozproszonego. Wykonywanie skrót ponownie w dowolnym momencie w przyszłości powinna zwracać ten sam wynik. Jeśli plik jest modyfikowany, nawet jeśli modyfikowany jest tylko jeden piksel obrazu, skrót zwróci inną wartość.

![Integracja magazynu](./media/integration-patterns/storage-integration.png)

Wzorzec można zaimplementować gdzie:

-   System zewnętrzny będzie nadal występować pliku w mechanizm magazynu, takich jak Azure Storage.
-   Skrót jest generowany przy użyciu pliku lub pliku oraz skojarzone metadane takie jak identyfikator właściciela, adres URL, w którym znajduje się plik, itp.
-   Skrót, a wszystkie metadane są wysyłane do funkcji w kontrakcie inteligentne, takie jak *FileAdded*
-   W przyszłości plików i metadanych może być mieszany ponownie i porównana z wartościami przechowywanymi w księdze.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Wymagania wstępne dotyczące wdrażania wzorce integracji przy użyciu protokołu REST i interfejsów API wiadomości

W celu ułatwienia możliwość interakcji z kontraktem inteligentnych przy użyciu REST lub komunikat interfejsu API dla systemu zewnętrznego lub urządzenia, że musi wystąpić-

1. W usłudze Azure Active Directory przez konsorcjum zostanie utworzone konto reprezentujący zewnętrzne systemowej lub urządzenia.
2. Odpowiednie kontraktami inteligentnych aplikacji Azure Blockchain Workbench ma funkcje zdefiniowane w celu akceptowania zdarzeń z systemu zewnętrznego lub urządzenia.
3. Plik konfiguracji aplikacji dla Twojej umowy inteligentnych zawiera roli, która będzie systemowej lub urządzenia przypisane.
4. Plik konfiguracji aplikacji dla Twojej umowy inteligentne identyfikuje w która stwierdza, że ta funkcja może być wywoływany przez zdefiniowanej roli.
5. Plik konfiguracji aplikacji i jej kontraktów inteligentnych są przekazywane do aplikacji Azure Blockchain Workbench.

Po przekazaniu aplikacji, konto usługi Azure Active Directory do systemu zewnętrznego jest przypisany do Umowy i skojarzonych ról.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testowanie przepływów integracji systemu zewnętrznego przed pisania kodu integracji 

Daje możliwość integracji z zewnętrznymi systemami kluczowe znaczenie ma wiele scenariuszy. Jest to pożądane, aby można było zweryfikować inteligentne kontraktu projekt wcześniejsze lub równolegle w celu opracowywania kodu integracji z zewnętrznymi systemami.

Korzystanie z usługi Azure Active Directory (Azure AD) można znacznie przyspieszyć deweloperskiej i godziny na wartość. W szczególności integracji kodu za pomocą zewnętrznego systemu może potrwać nietrywialnymi ilość czasu. Za pomocą usługi Azure AD i Autogenerowanie środowiska użytkownika w aplikacji Azure Blockchain Workbench, to umożliwić programistom Zaloguj się do aplikacji Workbench, jako że systemu zewnętrznego i wypełnianie wartości oczekiwanych z tego systemu zewnętrznego za pośrednictwem najlepsze środowisko użytkownika. Dzięki temu do szybkiego tworzenia i weryfikacji koncepcji w środowiska weryfikacji koncepcji albo przed uaktualnieniem programu lub w sposób równoległy kodem integracji zapisywana systemów zewnętrznych.
