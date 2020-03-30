---
title: Architektura usługi Azure Blockchain Workbench
description: Omówienie architektury usługi Azure Blockchain Workbench Preview i jej składników.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: aa972e8ae486d181f0c48df72ec89c925c940451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324895"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architektura usługi Azure Blockchain Workbench

Usługa Azure Blockchain Workbench Preview upraszcza tworzenie aplikacji łańcucha bloków, udostępniając rozwiązanie przy użyciu kilku składników platformy Azure. Blockchain Workbench można wdrożyć przy użyciu szablonu rozwiązania w portalu Azure Marketplace. Szablon umożliwia wybranie modułów i składników do wdrożenia, w tym stosu łańcucha bloków, typu aplikacji klienckiej i obsługi integracji IoT. Po wdrożeniu blockchain Workbench zapewnia dostęp do aplikacji sieci web, aplikacji na iOS i aplikacji na Androida.

![Architektura blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Tożsamość i uwierzytelnianie

Korzystając z programu Blockchain Workbench, konsorcjum może sfedować swoje tożsamości przedsiębiorstwa przy użyciu usługi Azure Active Directory (Azure AD). Workbench generuje nowe konta użytkowników dla tożsamości w łańcuchu z tożsamościami przedsiębiorstwa przechowywanymi w usłudze Azure AD. Mapowanie tożsamości ułatwia uwierzytelnione logowanie do interfejsów API i aplikacji klienta i używa zasad uwierzytelniania organizacji. Workbench zapewnia również możliwość skojarzenia tożsamości przedsiębiorstwa z określonymi rolami w ramach danego kontraktu inteligentnego. Ponadto Workbench zapewnia również mechanizm do identyfikowania akcji, które te role mogą podjąć i w jakim czasie.

Po wdrożeniu programu Blockchain Workbench użytkownicy wchodzą w interakcję z blockchain workbench za pośrednictwem aplikacji klienckich, interfejsu API klienta opartego na rest lub interfejsu API obsługi wiadomości. We wszystkich przypadkach interakcje muszą być uwierzytelnione za pośrednictwem usługi Azure Active Directory (Azure AD) lub poświadczeń specyficznych dla urządzenia.

Użytkownicy powiększyć swoje tożsamości do konsorcjum usługi Azure AD, wysyłając zaproszenie e-mail do uczestników na ich adres e-mail. Podczas logowania ci użytkownicy są uwierzytelniani przy użyciu nazwy, hasła i zasad. Na przykład uwierzytelnianie dwuskładnikowe ich organizacji.

Usługa Azure AD służy do zarządzania wszystkimi użytkownikami, którzy mają dostęp do blockchain workbench. Każde urządzenie łączące się z umową inteligentną jest również skojarzone z usługą Azure AD.

Usługa Azure AD służy również do przypisywania użytkowników do specjalnej grupy administratorów. Użytkownicy powiązani z grupą administratorów mają dostęp do praw i działań w ramach Blockchain Workbench, w tym wdrażania umów i udzielania użytkownikowi uprawnień dostępu do umowy. Użytkownicy spoza tej grupy nie mają dostępu do akcji administratora.

## <a name="client-applications"></a>Aplikacje klienckie

Workbench udostępnia automatycznie generowane aplikacje klienckie dla sieci web i urządzeń mobilnych (iOS, Android), które mogą być używane do sprawdzania poprawności, testowania i wyświetlania aplikacji blockchain. Interfejs aplikacji jest generowany dynamicznie na podstawie metadanych inteligentnego kontraktu i może pomieścić każdy przypadek użycia. Aplikacje klienckie dostarczają frontonu skierowanego do pełnej aplikacji blockchain generowanych przez Blockchain Workbench. Aplikacje klienckie uwierzytelniają użytkowników za pośrednictwem usługi Azure Active Directory (Azure AD), a następnie prezentują środowisko użytkownika dostosowane do kontekstu biznesowego umowy inteligentnej. Środowisko użytkownika umożliwia tworzenie nowych wystąpień inteligentnego kontraktu przez autoryzowane osoby, a następnie przedstawia możliwość wykonywania niektórych typów transakcji w odpowiednich punktach procesu biznesowego, który reprezentuje umowa inteligentna.

W aplikacji sieci web autoryzowani użytkownicy mogą uzyskać dostęp do Konsoli administratora. Konsola jest dostępna dla użytkowników w grupie Administrator w usłudze Azure AD i zapewnia dostęp do następujących funkcji:

* Wdrażanie firmy Microsoft pod warunkiem inteligentnych umów dla popularnych scenariuszy. Na przykład scenariusz przeniesienia środków trwałych.
* Przekazywanie i wdrażanie własnych inteligentnych kontraktów.
* Przypisz użytkownikowi dostęp do inteligentnego kontraktu w kontekście określonej roli.

Aby uzyskać więcej informacji, zobacz [przykładowe aplikacje klienckie Azure Blockchain Workbench w usłudze GitHub.](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)

## <a name="gateway-service-api"></a>Interfejs API usługi bramy

Blockchain Workbench zawiera interfejs API usługi bramy opartej na rest. Podczas pisania do łańcucha bloków interfejs API generuje i dostarcza wiadomości do brokera zdarzeń. Gdy dane są wymagane przez interfejs API, kwerendy są wysyłane do poza łańcuchowej bazy danych SQL. Baza danych SQL zawiera replikę danych w łańcuchu i metadanych, która zawiera informacje kontekstowe i konfiguracyjne dla obsługiwanych kontraktów inteligentnych. Kwerendy zwracają wymagane dane z repliki poza łańcuchem w formacie poinformowanym przez metadane dla umowy.

Deweloperzy mogą uzyskać dostęp do interfejsu API usługi bramy, aby tworzyć lub integrować rozwiązania blockchain bez polegania na aplikacjach klienckich Blockchain Workbench.

> [!NOTE]
> Aby włączyć uwierzytelniony dostęp do interfejsu API, dwie aplikacje klienckie są zarejestrowane w usłudze Azure Active Directory. Usługa Azure Active Directory wymaga różnych rejestracji aplikacji każdego typu aplikacji (natywnego i sieci Web). 

## <a name="message-broker-for-incoming-messages"></a>Broker wiadomości dla wiadomości przychodzących

Deweloperzy, którzy chcą wysyłać wiadomości bezpośrednio do Blockchain Workbench, mogą wysyłać wiadomości bezpośrednio do usługi Service Bus. Na przykład wiadomości interfejsu API może służyć do integracji między systemem lub urządzeń IoT.

## <a name="message-broker-for-downstream-consumers"></a>Broker wiadomości dla dalszych konsumentów

Podczas cyklu życia aplikacji występują zdarzenia. Zdarzenia mogą być wyzwalane przez interfejs API bramy lub w księdze. Powiadomienia o zdarzeniach mogą inicjować kod podrzędny na podstawie zdarzenia.

Blockchain Workbench automatycznie wdraża dwa typy konsumentów zdarzeń. Jeden konsument jest wyzwalany przez zdarzenia łańcucha bloków, aby wypełnić sklep SQL off-chain. Innym konsumentem jest przechwytywanie metadanych dla zdarzeń generowanych przez interfejs API związanych z przekazywania i przechowywania dokumentów.

## <a name="message-consumers"></a>Wiadomości dla konsumentów

 Konsumenci wiadomości przyjmują wiadomości z usługi Service Bus. Podstawowy model zdarzeń dla konsumentów wiadomości umożliwia rozszerzenia dodatkowych usług i systemów. Na przykład można dodać obsługę do wypełniania usługi CosmosDB lub oceny wiadomości przy użyciu usługi Azure Streaming Analytics. W poniższych sekcjach opisano komunikat konsumentów zawartych w Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Konsument księgi rozproszonej

Komunikaty technologii rozproszonej księgi (DLT) zawierają metadane dla transakcji, które mają być zapisywane w łańcuchu bloków. Konsument pobiera wiadomości i wypycha dane do konstruktora transakcji, sygnatariusza i routera.

### <a name="database-consumer"></a>Konsument bazy danych

Konsument bazy danych pobiera wiadomości z usługi Service Bus i wypycha dane do dołączonej bazy danych, takiej jak baza danych SQL.

### <a name="storage-consumer"></a>Konsument pamięci masowej

Konsument magazynu pobiera komunikaty z usługi Service Bus i wypycha dane do dołączonego magazynu. Na przykład przechowywanie dokumentów skrótowych w usłudze Azure Storage.

## <a name="transaction-builder-and-signer"></a>Konstruktor transakcji i sygnatariusz

Jeśli wiadomość na brokera wiadomości przychodzących musi być zapisana w łańcuchu bloków, zostanie przetworzona przez konsumenta DLT. Konsument DLT jest usługą, która pobiera komunikat zawierający metadane dla żądanej transakcji do wykonania, a następnie wysyła informacje do *konstruktora transakcji i sygnatariusza.* *Konstruktor transakcji i sygnatariusz* montuje transakcję blockchain na podstawie danych i żądanego miejsca docelowego łańcucha bloków. Po zmontowaniu transakcja jest podpisywane. Klucze prywatne są przechowywane w usłudze Azure Key Vault.

 Blockchain Workbench pobiera odpowiedni klucz prywatny z usługi Key Vault i podpisuje transakcję poza magazynem kluczy. Po podpisaniu transakcja jest wysyłana do routerów i książek transakcji.

## <a name="transaction-routers-and-ledgers"></a>Routery i księgi transakcji

Routery i księgi transakcji przyjmują podpisane transakcje i kierują je do odpowiedniego łańcucha bloków. Obecnie Blockchain Workbench wspiera Ethereum jako docelowy łańcuch bloków.

## <a name="dlt-watcher"></a>Obserwator DLT

Obserwator technologii rozproszonej księgi (DLT) monitoruje zdarzenia występujące na łańcuchach bloków dołączonych do blockchain workbench.
Zdarzenia odzwierciedlają informacje istotne dla osób fizycznych i systemów. Na przykład tworzenie nowych wystąpień umowy, wykonywanie transakcji i zmiany stanu. Zdarzenia są przechwytywane i wysyłane do brokera wiadomości wychodzących, dzięki czemu mogą być używane przez dalszych konsumentów.

Na przykład konsument SQL monitoruje zdarzenia, zużywa je i wypełnia bazę danych SQL za pomocą dołączonych wartości. Kopia umożliwia odtworzenie repliki danych w łańcuchu w magazynie poza siecią.

## <a name="azure-sql-database"></a>Baza danych Azure SQL Database

Baza danych SQL platformy Azure dołączona do programu Blockchain Workbench przechowuje definicje umów, metadane konfiguracji i replikę danych dostępną do SQL przechowywaną w łańcuchu bloków. Te dane można łatwo wyszukiwać, wizualizować lub analizować bezpośrednio uzyskując dostęp do bazy danych. Deweloperzy i inni użytkownicy mogą używać bazy danych do raportowania, analizy lub innych integracji zorientowanych na dane. Na przykład użytkownicy mogą wizualizować dane transakcji przy użyciu usługi Power BI.

Ta poza łańcuchowa pamięć masowa umożliwia organizacjom przedsiębiorstwa wykonywanie zapytań o dane w języku SQL, a nie w księdze łańcucha bloków. Ponadto poprzez standaryzację standardowego schematu, który jest niezależny od stosów technologii blockchain, magazyn poza łańcuchem umożliwia ponowne wykorzystanie raportów i innych artefaktów w projektach, scenariuszach i organizacjach.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage służy do przechowywania kontraktów i metadanych skojarzonych z kontraktami.

Od zamówień zakupu i list przewozowych, przez obrazy używane w wiadomościach i obrazach medycznych, po wideo pochodzące z kontinuum, w tym kamery policyjne i główne filmy, dokumenty odgrywają rolę w wielu scenariuszach zorientowanych na blockchain. Dokumenty nie są odpowiednie do umieszczania bezpośrednio w łańcuchu bloków.

Blockchain Workbench obsługuje możliwość dodawania dokumentów lub innych treści multimedialnych z logiką biznesową blockchain. Skrót dokumentu lub zawartości multimedialnej jest przechowywany w łańcuchu bloków, a rzeczywista zawartość dokumentu lub nośnika jest przechowywana w usłudze Azure Storage. Skojarzone informacje o transakcji są dostarczane do brokera wiadomości przychodzących, pakowane, podpisane i kierowane do łańcucha bloków. Ten proces wyzwala zdarzenia, które są udostępniane za pośrednictwem brokera wiadomości wychodzących. Baza danych SQL zużywa te informacje i wysyła je do bazy danych w celu późniejszego wykonywania zapytań. Systemy niższego szczebla mogą również zużywać te zdarzenia do działania w stosownych przypadkach.

## <a name="monitoring"></a>Monitorowanie

Workbench zapewnia rejestrowanie aplikacji przy użyciu usługi Application Insights i Azure Monitor. Usługa Application Insights służy do przechowywania wszystkich zarejestrowanych informacji z blockchain workbench i zawiera błędy, ostrzeżenia i pomyślne operacje. Usługa Application Insights może być używana przez deweloperów do debugowania problemów z blockchain workbench. 

Usługa Azure Monitor udostępnia informacje o kondycji sieci blockchain. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)