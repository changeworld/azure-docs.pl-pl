---
title: Inteligentne wzorce integracji kontraktu w Azure Blockchain Workbench
description: Przegląd wzorce integracji inteligentne kontraktu w Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: a6a44e30fe58617b43c5491a72fc882015bc9591
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="smart-contract-integration-patterns"></a>Wzorce integracji inteligentne kontraktu

Kontrakty inteligentne często będą stanowiły biznesowego przepływu pracy niezbędne do współpracy z systemami zewnętrznymi i urządzeniami.

Wymagania te przepływy pracy zawierają należy zainicjować transakcji w rozproszonej księgi, które zawierają dane z systemu zewnętrznego, usługi lub urządzeń. Muszą mieć systemami zewnętrznymi reagowania na zdarzenia pochodzące z usługi inteligentnego kontraktów na rozproszonych księgi.

Interfejs API REST i obsługi integracji oferują możliwość zarówno Wyślij transakcje z systemów zewnętrznych do inteligentnego umów zawartych w aplikacji Azure Blockchain Workbench, a także wysłać powiadomienia o zdarzeniach z systemami zewnętrznymi, na podstawie zmian, które przyjmują Umieść w aplikacji.

W przypadku scenariuszy integracji danych Azure Blockchain Workbench obejmuje zestaw widoków bazy danych, które łączą się z kombinacją danych transakcyjnych z blockchain i metadane o aplikacjach i kontrakty inteligentne.

Ponadto niektóre scenariusze, takie jak powiązane podać łańcucha lub nośnik, może również wymagać integracji dokumentów. Podczas Azure Blockchain Workbench nie zapewnia, że wywołania interfejsu API do obsługi dokumentów bezpośrednio, dokumentów można włączyć do aplikacji Blockchain Azure. Ta sekcja zawiera również tego wzorca.

Ta sekcja zawiera wzorce dla każdego z tych typów integracji Implementowanie w rozwiązaniach do pełnego.

## <a name="rest-api-based-integration"></a>Integracja z interfejsu API REST

Możliwości w aplikacji sieci web Azure Blockchain Workbench generowane są udostępniane za pośrednictwem interfejsu API REST. W tym przekazywanie Azure Blockchain Workbench, konfiguracji i administrowanie aplikacjami wysyłania transakcji rozproszonej księgi i wysyłania zapytań do metadanych i księgi danych aplikacji.

Interfejs API REST jest używany głównie interakcyjne klientów, takich jak sieci web, mobilnych, i bot aplikacji.

W tej sekcji analizuje wzorce koncentruje się na aspekty interfejsu API REST, które przesyłają transakcji rozproszonej księgi oraz te, które zapytania na danych dotyczących transakcji z platformy Azure Blockchain Workbench *wyłączone łańcucha* bazy danych SQL.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Wysyłanie transakcje rozproszone księgi z zewnętrznego systemu

Interfejsu API REST Azure Blockchain Workbench umożliwia wysyłanie żądań uwierzytelnionych do wykonania transakcji na rozproszonych księgi.

![Wysyłanie transakcji rozproszonej księdze](media/blockchain-workbench-integration-patterns/send-transactions-ledger.png)

Dzieje się tak przy użyciu procesu przedstawione powyżej, gdzie:

-   Uwierzytelnia zewnętrznej aplikacji do usługi Azure Active Directory udostępnione jako część wdrożenia Azure Blockchain Workbench.
-   Autoryzowani użytkownicy otrzymują tokenu elementu nośnego, który można wysłać żądań do interfejsu API.
-   Aplikacji zewnętrznych wywołań interfejsu API REST przy użyciu tokenu elementu nośnego.
-   Interfejsu API REST żądanie jako wiadomości i wysyła go do magistrali usługi. W tym miejscu zostanie ono pobierane, podpisane i wysyłane do odpowiedniej księgi rozproszonych.
-   Interfejs API REST zgłasza żądanie do usługi Azure Blockchain Workbench bazą danych SQL do rejestrowania żądania i ustanowienia bieżący stan inicjowania obsługi administracyjnej.
-   Bazy danych SQL zwraca stan inicjowania obsługi administracyjnej i wywołania interfejsu API zwraca identyfikator aplikacji zewnętrznych, które ją.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Wykonywanie zapytania metadanych Blockchain Workbench i transakcje rozproszone finansowe

Interfejsu API REST Azure Blockchain Workbench zapewnia możliwość wysyłania żądań uwierzytelniony do szczegółów zapytania związane z wykonywaniem inteligentne kontraktu w księdze rozproszonej.

![Zapytanie dotyczące metadanych](media/blockchain-workbench-integration-patterns/querying-metadata.png)

Dzieje się tak przy użyciu procesu przedstawione powyżej, gdzie:

1. Uwierzytelnia zewnętrznej aplikacji do usługi Azure Active Directory udostępnione jako część wdrożenia Azure Blockchain Workbench.
2. Autoryzowani użytkownicy otrzymują tokenu elementu nośnego, który można wysłać żądań do interfejsu API.
3. Aplikacji zewnętrznych wywołań interfejsu API REST przy użyciu tokenu elementu nośnego.
4. Interfejs API REST odpytuje dane żądania z bazą danych SQL i zwraca go do klienta.

## <a name="messaging-integration"></a>Integracja obsługi wiadomości

Wiadomości integracji umożliwia interakcję z systemów, usług i urządzeń, których interakcyjnego logowania nie jest niemożliwe lub niepożądane. Integracja komunikatów koncentruje się na dwóch typów komunikatów, które żądania, że transakcji można wykonać na rozproszonych finansów i zdarzenia, które są dostępne w tym księgi, gdy transakcje miały miejsce.

Integracja komunikatów koncentruje się na wykonanie i monitorowanie transakcje związane z Tworzenie użytkownika, tworzenie kontraktu i wykonywanie transakcji w kontraktach i jest głównie używana przez *bezobsługowe* systemów zaplecza.

W tej sekcji analizuje wzorce koncentruje się na aspekty interfejsu API programu wiadomości wysyłających transakcji rozproszonej księgi i tych, które reprezentują komunikaty o zdarzeniach udostępnianych przez podstawowej księgi rozproszonych.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Dostarczania zdarzeń jednokierunkowe z inteligentne kontraktu odbiorcy zdarzeń 

W tym scenariuszu występuje zdarzenie w ramach inteligentnych kontraktu, na przykład zmiana stanu lub wykonania określonego typu transakcji. To zdarzenie jest emitowany za pośrednictwem siatki zdarzenia podrzędne konsumentów i tych konsumentów, a następnie podjąć odpowiednie działania.

Przykład tego scenariusza to, że w przypadku transakcji klienta będzie otrzymywać alerty i może wykonać działania, takie jak rejestrowanie informacji bazy danych SQL lub wspólnej usługi danych. To tego samego wzorca, stosowanej do wypełnienia Workbench jego *wyłączone łańcucha* bazy danych SQL.

Inny się, jeśli kontrakt inteligentne przechodzi w określonym stanie, na przykład gdy kontrakt przechodzi w stan *OutOfCompliance*. W przypadku tej zmiany stanu go może wywołać alert na wysłanie na telefon komórkowy przez administratora.

![Dostarczania zdarzeń jednokierunkowej](media/blockchain-workbench-integration-patterns/one-way-event-delivery.png)

Dzieje się tak przy użyciu procesu przedstawione powyżej, gdzie:

-   Inteligentne kontrakt przechodzi w stan nowego i wysyła odpowiednie zdarzenie w księdze.
-   Księgi odbiera i dostarcza zdarzenia Azure Blockchain Workbench.
-   Azure Blockchain Workbench subskrybuje zdarzenia z księgi i odbiera zdarzenia.
-   Azure Blockchain Workbench publikuje zdarzenia do subskrybentów w siatce zdarzeń.
-   Systemy zewnętrzne subskrybują siatki zdarzeń, korzystać z wiadomości i podjąć odpowiednie działania.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Dostarczania zdarzeń jednokierunkowe wiadomości z zewnętrznego systemu do kontraktu inteligentne

Istnieje również scenariusz, w którym wypływających z przeciwnym kierunku. W takim przypadku zdarzenie zostanie wygenerowane przez czujnik lub zewnętrznego systemu i dane z tego zdarzenia, które mają być wysyłane do inteligentnego kontraktu.

Typowym przykładem jest dostarczania danych z rynkach finansowych, z na przykład ceny towarów, zasobu lub obligacji, inteligentne kontraktu.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczania Blockchain Workbench Azure w oczekiwanym formacie

Niektóre aplikacje są wbudowane w celu integracji z Azure Blockchain Workbench i bezpośrednio generuje i wysyłanie komunikatów w oczekiwanym formacie.

![Bezpośrednie dostarczania](media/blockchain-workbench-integration-patterns/direct-delivery.png)

Dzieje się tak przy użyciu procesu przedstawione powyżej, gdzie:

-   Występuje zdarzenie w systemie zewnętrznym, które wyzwala utworzenia komunikatu na Azure Blockchain Workbench.
-   System zewnętrzny ma kodu napisanego w celu utworzenia tego komunikatu w formacie znanych i wysyła to bezpośrednio do usługi Service Bus.
-   Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera wiadomość.
-   Azure Blockchain Workbench inicjuje połączenie w księdze wysyłanie danych z systemu zewnętrznego z określonym kontraktem.
-   Po otrzymaniu komunikatu kontrakt przechodzi w nowym stanie.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczania wiadomości w formacie nieznany Azure Blockchain Workbench

Niektóre systemy nie można zmodyfikować w celu dostarczania komunikatów standardowych formatów używanych przez Azure Blockchain Workbench. W tych przypadkach istniejących mechanizmów i komunikat można często używane w formacie tych systemów. W szczególności typy macierzystych wiadomości tych systemów można je przekształcać przy użyciu aplikacji logiki, usługi Azure Functions lub innych niestandardowych kodów mapować do jednej ze standardowych formatów oczekiwano wiadomości.

![Format komunikatu nieznany](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

Dzieje się tak przy użyciu procesu przedstawione powyżej, gdzie:

-   Występuje zdarzenie w systemie zewnętrznym wyzwala Tworzenie komunikatu.
-   Aplikacji logiki lub kodu niestandardowego służy do odbierania wiadomości i przekształcać je do standardowych sformatowany komunikat Azure Blockchain Workbench.
-   Aplikację logiki wysyła wiadomość po przekształceniu bezpośrednio do usługi Service Bus.
-   Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera wiadomość.
-   Azure Blockchain Workbench inicjuje połączenie w księdze wysyłanie danych z systemu zewnętrznego do określonych funkcji kontraktu.
-   Funkcja wykonuje i zwykle modyfikuje stan. Zmiana stanu przenosi do przodu zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, można teraz wykonać odpowiednie biznesowego przepływu pracy.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Przechodzenie kontroli do zewnętrznego przetwarzania i poczekać na zakończenie

Istnieją scenariusze, w którym inteligentne kontraktu należy zatrzymać wykonywanie wewnętrzne i dostarcz procesu zewnętrznego. Czy Proces zewnętrzny następnie zostać ukończone, Wyślij wiadomość do inteligentnego kontraktu i wykonywanie następnie będzie nadal w ramach inteligentnych kontraktu.

#### <a name="transition-to-the-external-process"></a>Przejście do procesu zewnętrznego

Ten wzorzec jest zwykle implementowany w następujący sposób:

-   Kontrakt inteligentne przejść do określonego stanu. W tym stanie, albo nie lub mogą być wykonywane ograniczonej liczby funkcji, dopóki system zewnętrzny podejmuje odpowiednie działania.
-   Zmiana stanu jest udostępniane jako zdarzenia podrzędne konsumenta.
-   Konsument podrzędne odbiera zdarzenia i wyzwala wykonanie kodu zewnętrznego.

![Formant przejścia do procesu zewnętrznego](media/blockchain-workbench-integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Powrót formantu z inteligentne kontraktu

W zależności od możliwość dostosowywania systemu zewnętrznego może lub nie może istnieć możliwość dostarczania komunikatów w jednym z formatów standardowe, które oczekuje Azure Blockchain Workbench. Oparta na możliwości systemów zewnętrznych do generowania jeden z tych komunikatów określi, które z następujących dwóch ścieżek powrotu zostaną wykonane.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczania Blockchain Workbench Azure w oczekiwanym formacie

![](media/blockchain-workbench-integration-patterns/direct-delivery.png)

W tym modelu komunikatu kontraktu i zmianę stanu kolejnych występuje po powyższych przetworzyć where -

-   Po osiągnięciu zakończenia lub określonego punktu kontrolnego na wykonanie kodu zewnętrznego, zdarzenia są wysyłane do usługi Service Bus, podłączone do środowiska roboczego Blockchain Azure.

-   Dla systemów, które nie mogą być dostosowywane bezpośrednio do zapisu komunikat, który spełnia oczekiwania dotyczące interfejsu API zostanie on przekształcony.

-   Zawartość komunikatu jest umieszczone w i wysyłane do określonych funkcji inteligentnego kontraktu. Jest to realizowane w imieniu użytkownika skojarzonego z systemu zewnętrznego.

-   Funkcja wykonuje i zwykle spowoduje zmiany stanu. Zmiana stanu przenosi do przodu zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, można teraz wykonać odpowiednie biznesowego przepływu pracy.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczania wiadomości w formacie nieznany Azure Blockchain Workbench

![Format komunikatu nieznany](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

W tym modelu, w którym w standardowym formacie nie można wysłać wiadomości, komunikacji do kontraktu i nastąpi zmiana stanu kolejnych następujących powyższych przetworzyć gdzie:

1.  Po osiągnięciu zakończenia lub określonego punktu kontrolnego na wykonanie kodu zewnętrznego, zdarzenia są wysyłane do usługi Service Bus, podłączone do środowiska roboczego Blockchain Azure.
2.  Aplikacji logiki lub kodu niestandardowego służy do odbierania wiadomości i przekształcać je do standardowych sformatowany komunikat Azure Blockchain Workbench.
3.  Aplikację logiki wysyła wiadomość po przekształceniu bezpośrednio do usługi Service Bus.
4.  Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera wiadomość.
5.  Azure Blockchain Workbench inicjuje połączenie w księdze wysyłanie danych z systemu zewnętrznego z określonym kontraktem.
6. Zawartość komunikatu jest umieszczone w i wysyłane do określonych funkcji inteligentnego kontraktu. Jest to realizowane w imieniu użytkownika skojarzonego z systemu zewnętrznego.
7.  Funkcja wykonuje i zwykle spowoduje zmiany stanu. Zmiana stanu przenosi do przodu zostaną uwzględnione w umowie inteligentne, umożliwiające innych funkcji, można teraz wykonać odpowiednie biznesowego przepływu pracy.

## <a name="iot-integration"></a>Integracja IoT

Typowy scenariusz integracji jest uwzględnienie pobrane z czujników w inteligentne kontraktu danych telemetrycznych. Na podstawie danych dostarczonych przez czujniki, inteligentne kontrakty można podjąć świadomej działania i Zmień stan kontraktu.

Na przykład jeśli ciężarówka, dostarczając medycznych były temperatury wzrastać do 110 stopni, go może mieć wpływ na skuteczność medycznych i mogą być przyczyną problemu bezpieczeństwa publicznego, jeśli nie wykryto i usunąć z łańcucha dostaw. Jeśli sterownik przyspieszony jego samochodu do 100 mil na godzinę, wynikowy informacji z czujnika może wyzwolić anulowania ubezpieczenia przez jego dostawcę ubezpieczenia. Jeśli samochód samochodu wynajem, GPS danych można sygnalizującego, kiedy poszło sterownika poza geograficznych, jego umową wynajem i obciążania kary.

Żądanie jest takich czujników można dostarczania danych na podstawie stałej oraz nie jest odpowiednie do wysłania wszystkich tych danych do inteligentnego kontraktu. Typowy podejście jest ograniczyć liczbę wiadomości wysyłanych do blockchain dostarczając wszystkie komunikaty do magazynu pomocniczego. Na przykład dostarczanie komunikatów odebranych interwałem tylko usunięte, na przykład raz na godzinę, a gdy zawartych w niej wartość znajduje się poza ustaloną na zakres dla kontraktu inteligentne. Sprawdzanie wartości, które dzielą się poza tolerancji, gwarantuje, że dane dotyczące logiki biznesowej kontraktów jest odebranych i wykonywane. Sprawdzanie wartości z interwałem potwierdza nadal zgłasza czujnika. Wszystkie dane są wysyłane do dodatkowej magazynu raportowania umożliwiające szerszych raportowania, analizy i uczenia maszynowego. Na przykład podczas pobierania odczyty czujnik GPS może nie być wymagane co minutę dla kontraktu inteligentne, mogły udostępnić interesujące dane do użycia w raportach lub mapowania tras.

Integracja z urządzeń na platformie Azure zwykle odbywa się z Centrum IoT. Centrum IoT umożliwia rozsyłanie wiadomości na podstawie zawartości i umożliwia typ funkcji opisanych powyżej.

![Komunikaty IoT](media/blockchain-workbench-integration-patterns/iot.png)

Proces powyżej przedstawiono wzorca dla w ten sposób jest implementowany:

-   Urządzenie komunikuje się bezpośrednio lub za pośrednictwem bramy pola do Centrum IoT.
-   Centrum IoT odbiera komunikaty i ocenia wiadomości przed trasy ustanowić zawartości komunikatu, który Sprawdź na przykład. *Czujnik bada temperatury większa niż 50 stopni?*
-   Centrum IoT wysyła wiadomości, które spełniają kryteria zdefiniowane usługi Service Bus dla trasy.
-   Aplikacja logiki lub inny kod nasłuchuje usługi Service Bus, która została ustanowiona Centrum IoT dla trasy.
-   Aplikację logiki lub inny kod pobiera i przekształcenie komunikat do znanego formatu.
-   Po przekształceniu komunikat teraz w standardowym formacie, są wysyłane do usługi Service Bus dla platformy Azure Blockchain Workbench.
-   Azure Blockchain Workbench subskrybuje zdarzenia z usługi Service Bus i pobiera wiadomość.
-   Azure Blockchain Workbench inicjuje połączenie w księdze wysyłanie danych z systemu zewnętrznego z określonym kontraktem.
-   Po otrzymaniu komunikatu, kontrakt ocenia danych i może zmienić stan, w zależności od wyniku tej oceny, na przykład wysokiej temperatury, zmiany stanu do *zgodności z*.

## <a name="data-integration"></a>Integracja danych

Oprócz i interfejs API REST Azure Blockchain Workbench zapewnia dostęp do bazy danych SQL wypełniane przy użyciu aplikacji i kontraktu metadane, a także danych transakcyjnych z księgi rozproszonych.

![Integracja danych](media/blockchain-workbench-integration-patterns/data-integration.png)

Integracja danych jest dobrze znany:

-   Azure Blockchain Workbench są przechowywane metadane dotyczące aplikacji, przepływy pracy, kontrakty i transakcje jako część normalne działanie działania.
-   Systemy zewnętrzne lub narzędzi Podaj co najmniej jeden oknach dialogowych, aby ułatwić zbierania informacji o bazie danych, takie jak nazwa serwera bazy danych, nazwy bazy danych, typ uwierzytelniania, poświadczenia logowania i bazę danych, która wyświetla mogą korzystać z.
-   Zapytania są zapisywane dla widoków bazy danych SQL do podrzędne, udostępniamy przez systemy zewnętrzne, usług raportowania, narzędzia dla deweloperów i narzędzi użytkowych przedsiębiorstwa.

## <a name="storage-integration"></a>Integracja magazynu

Wiele scenariuszy może wymagać trzeba zastosować attestable plików. Kilka przyczyn będzie ona nieodpowiednie umieścić pliki na blockchain. Zamiast tego typowym podejściem jest wykonać skrót kryptograficzny (na przykład SHA-256) dla pliku i udostępnić ten skrót księgi rozproszonych. Wykonywanie skrót ponownie w dowolnym momencie przyszłych powinien zwrócić takiego samego wyniku. Jeśli plik jest modyfikowany, nawet jeśli modyfikowany jest tylko jeden piksel obrazu, skrót zwróci inną wartość.

![Integracja magazynu](media/blockchain-workbench-integration-patterns/storage-integration.png)

Można zaimplementować wzorzec gdzie:

-   System zewnętrzny będzie nadal występował pliku w mechanizmu magazynowania, takie jak magazyn Azure.
-   Skrót jest generowana za pomocą pliku lub pliku oraz skojarzone metadane takich jak identyfikator właściciela, adres URL, w którym znajduje się plik, itp.
-   Skrót i wszystkie metadane są wysyłane do funkcji inteligentnego umowy, takich jak *FileAdded*
-   W przyszłości plików i metadanych może być ponownie mieszany i porównane z wartościami przechowywanymi w księdze.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Wymagania wstępne dotyczące stosowania wzorców integracji przy użyciu REST i komunikat interfejsów API

W celu ułatwienia możliwość interakcji z kontraktem inteligentnych przy użyciu REST lub komunikat interfejsu API dla systemu zewnętrznego lub urządzeń, poniżej musi wystąpić-

1. W usłudze Azure Active Directory dla konsorcjum konto jest tworzone reprezentujący systemu zewnętrznego lub urządzenia.
2. Odpowiednie zapisane inteligentne aplikacji Azure Blockchain Workbench ma funkcje zdefiniowane do akceptowania zdarzenia z systemu zewnętrznego lub urządzenia.
3. Plik konfiguracji aplikacji dla programu smart kontraktu zawiera roli, która będzie systemu lub urządzenia przypisane.
4. Pliku konfiguracji aplikacji dla programu smart kontraktu identyfikuje w które stwierdza, że ta funkcja może być wywoływany przez zdefiniowanej roli.
5. Pliku konfiguracji aplikacji i jej inteligentne kontrakty są przekazywane do Azure Blockchain Workbench.

Po przekazaniu aplikacji konta usługi Azure Active Directory do systemu zewnętrznego jest przypisany do kontraktu i skojarzone role.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testowanie przepływów integracji systemu zewnętrznego przed pisanie kodu integracji 

Udostępnia możliwość integracji z systemami zewnętrznymi kluczowe znaczenie ma wiele scenariuszy. Jest to pożądane, aby można było zweryfikować projektowania inteligentne kontraktu wcześniejsze lub równolegle z programowaniem kodu do integracji z systemami zewnętrznymi.

Korzystanie z usługi Azure Active Directory (Azure AD) można znacznie przyspieszyć wydajność deweloperów i czasu na wartość. W szczególności integracja kodu z zewnętrznego systemu może potrwać nieuproszczony ilość czasu. Za pomocą usługi Azure AD i generowania automatycznego UX przez Azure Blockchain Workbench, to umożliwiają deweloperom zalogować się do środowiska roboczego jako tego systemu zewnętrznego i wypełnić wartości oczekiwanych z tego systemu zewnętrznego za pośrednictwem UX. Dzięki temu do szybkiego tworzenia i weryfikacji koncepcji dowód koncepcji środowiska albo przed lub równolegle do kodu integracji zapisywana w systemach zewnętrznych.
