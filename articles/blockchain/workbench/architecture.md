---
title: Architektura usługi Azure Blockchain Workbench
description: Przegląd architektury aplikacji Azure Blockchain Workbench i jego składników.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8dc8304e97ff66356a8874a146231f0042e5f509
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510139"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architektura usługi Azure Blockchain Workbench

Azure Blockchain Workbench upraszcza tworzenie aplikacji łańcucha bloków, zapewniając rozwiązanie z użyciem kilka składników platformy Azure. Blockchain Workbench można wdrożyć przy użyciu szablonu rozwiązania w witrynie Azure Marketplace. Szablon pozwala wybrać, moduły i składniki do wdrożenia, łącznie z łańcucha bloków stosu, typ aplikacji klienckiej i obsługę integracji IoT. Po wdrożeniu aplikacji Blockchain Workbench zapewnia dostęp do aplikacji sieci web, aplikacji dla systemu iOS i aplikacji dla systemu Android.

![Architektura aplikacji Blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Uwierzytelnianie i tożsamość

Korzystając z aplikacji Blockchain Workbench, konsorcjum może tworzyć federacje ich tożsamościami w przedsiębiorstwie za pomocą usługi Azure Active Directory (Azure AD). Środowisko robocze generuje nowych kont użytkowników o łańcucha tożsamości za pomocą tożsamości organizacji przechowywanych w usłudze Azure AD. Mapowania tożsamości ułatwia uwierzytelnionego Zaloguj się do interfejsów API klienta i aplikacji oraz używa zasad uwierzytelniania w organizacji. Workbench umożliwia również skojarzyć tożsamościami w przedsiębiorstwie do określonych ról w ramach danego kontraktu inteligentne. Ponadto Workbench również zapewnia mechanizm do identyfikowania akcje te role można wykonać i w jakich czasie.

Po wdrożeniu aplikacji Blockchain Workbench użytkownikom korzystać z aplikacji Blockchain Workbench za pośrednictwem aplikacji klienckich, interfejsu API klienta opartego na protokole REST lub interfejs API komunikatów. We wszystkich przypadkach interakcje musi zostać uwierzytelniony, za pośrednictwem usługi Azure Active Directory (Azure AD) lub poświadczeń określonych urządzeń.

Użytkownicy Federację tożsamości do konsorcjum usługi Azure AD, wysyłając wiadomość e-mail z zaproszeniem do uczestników ich adres e-mail. Podczas logowania, użytkownicy ci są uwierzytelniane przy użyciu nazwy, hasła i zasady. Na przykład uwierzytelniania dwuskładnikowego w organizacji.

Usługa Azure AD jest używany do zarządzania wszystkimi użytkownikami, którzy mają dostęp do aplikacji Blockchain Workbench. Każde urządzenie łączące się inteligentne kontraktu jest również skojarzony z usługą Azure AD.

Usługa Azure AD służy także do przypisywania użytkowników do grupy administratorów specjalne. Użytkownicy skojarzeni z grupy Administratorzy przyznano im dostęp do uprawnień i akcji w ramach aplikacji Blockchain Workbench w tym wdrażanie umów i przyznawanie uprawnień do użytkownika, kontrakt dostęp do. Użytkownicy spoza tej grupy mają dostęp do akcji administratora.

## <a name="client-applications"></a>Aplikacje klienckie

Środowisko robocze udostępnia aplikacje automatycznie wygenerowanego klienta dla sieci web i mobilnych (iOS lub Android), która może służyć do sprawdzania poprawności, testowania i wyświetlania aplikacji łańcucha bloków. Interfejs aplikacji jest generowana dynamicznie na podstawie metadanych inteligentne kontraktu i może obsłużyć wszystkie przypadek użycia. Aplikacje klienckie oferują frontonu przeznaczonych dla użytkowników aplikacje pełny łańcuch bloków, generowane przez Blockchain Workbench. Aplikacje klienckie uwierzytelniania użytkowników za pośrednictwem usługi Azure Active Directory (Azure AD), a następnie prezentować interfejs użytkownika dostosowane do kontekst biznesowy umowy inteligentne. Środowisko użytkownika umożliwia tworzenie nowych wystąpień inteligentne umowy przez autoryzowanych użytkowników indywidualnych, a następnie wyświetla możliwość wykonywania niektórych typów transakcji w odpowiednich miejscach w procesie biznesowym, inteligentne kontraktu reprezentowany przez.

W aplikacji sieci web autoryzowani użytkownicy mogą uzyskać dostęp do konsoli administratora. Konsola jest dostępna dla użytkowników w grupie administratorów w usłudze Azure AD i zapewnia dostęp do następujących funkcji:

* Wdrożenie firmy Microsoft, pod warunkiem kontrakty inteligentne w popularnych scenariuszach. Na przykład zasób transferu scenariusz.
* Przekaż i wdróż swoje własne kontraktów inteligentnych.
* Przypisywanie dostępu użytkownika do inteligentnego umowy w kontekście określonej roli.

Aby uzyskać więcej informacji, zobacz [aplikacje klienckie przykładowej aplikacji Azure Blockchain Workbench w witrynie GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-development-kit/connect/mobile/blockchain-workbench/workbench-client).

## <a name="gateway-service-api"></a>Interfejs API usługi bramy

Blockchain Workbench obejmuje interfejs API usługi bramy oparte na protokole REST. Podczas zapisywania w łańcuch bloków, interfejs API generuje i dostarcza komunikaty do brokera zdarzeń. Gdy dane są żądane przez interfejs API, zapytania są wysyłane do bazy danych SQL poza łańcuchem. Baza danych SQL zawiera repliki w łańcuchu danych i metadanych, który zawiera informacje o kontekście i konfiguracji dla obsługiwanych kontraktów inteligentnych. Zapytania zwracają wymaganych danych z repliki poza łańcuchem w formacie wspieranemu przez metadane w ramach umowy.

Deweloperzy mają dostęp do interfejsu API usługi bramy można tworzyć ani integrować rozwiązań łańcucha bloków bez polegania na aplikacje klienckie Blockchain Workbench.

> [!NOTE]
> Aby włączyć dostępu uwierzytelnionego do interfejsu API, dwie aplikacje klienckie są zarejestrowane w usłudze Azure Active Directory. Usługa Azure Active Directory wymaga rejestracje aplikacji distinct każdego typu aplikacji (natywne i sieci web). 

## <a name="message-broker-for-incoming-messages"></a>Brokera komunikatów dla komunikatów przychodzących

Deweloperzy, którzy mają zostać wysłane wiadomości bezpośrednio z aplikacji Blockchain Workbench wiadomości można wysyłać bezpośrednio do usługi Service Bus. Na przykład wiadomości interfejsu API może służyć do integracji systemu do systemu lub urządzeń IoT.

## <a name="message-broker-for-downstream-consumers"></a>Brokera komunikatów dla klientów niższego rzędu

Podczas cyklu życia aplikacji występują zdarzenia. Zdarzenia mogą być wywoływane przez interfejs API bramy lub w księdze. Powiadomienia o zdarzeniach można zainicjować podrzędnego kodu na podstawie zdarzeń.

Blockchain Workbench automatycznie wdraża dwa rodzaje odbiorcy zdarzeń. Jednego użytkownika jest wyzwalany przez zdarzenia łańcucha bloków do wypełniania magazynu SQL poza łańcuchem. Innych klientów jest Przechwytywanie metadanych dla zdarzeń generowanych przez interfejs API związane z przekazywanie i przechowywanie dokumentów.

## <a name="message-consumers"></a>Odbiorcami komunikatów

 Odbiorcami komunikatów umieść komunikaty z usługi Service Bus. Odpowiedni model obsługi zdarzeń dla odbiorcami komunikatów umożliwia rozszerzenia dodatkowych usług i systemów. Na przykład można dodać obsługę wypełniania bazy danych cosmos DB lub oceń komunikatów za pomocą usługi Azure Stream Analytics. W poniższych sekcjach opisano odbiorcami komunikatów uwzględnione w aplikacji Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Konsument rejestru rozproszonego

Wiadomości technologii (DLT) rejestru rozproszonego zawierać metadane dla transakcji, które ma zostać zapisana łańcucha bloków. Konsument pobiera komunikaty i wypycha dane do konstruktora transakcji, osoby podpisującej i router.

### <a name="database-consumer"></a>Konsument bazy danych

Użytkownik bazy danych przyjmuje komunikaty z usługi Service Bus i wypycha dane do dołączonej bazie danych, takich jak SQL database.

### <a name="storage-consumer"></a>Konsument magazynu

Konsument magazynu przyjmuje komunikaty z usługi Service Bus i wypychanie danych do dołączonego magazynu. Na przykład przechowywanie skrótu dokumenty w usłudze Azure Storage.

## <a name="transaction-builder-and-signer"></a>Konstruktor transakcji i osoby podpisującej

Jeśli komunikat na brokera komunikatów przychodzących wymaga zapisania łańcucha bloków, będą przetwarzane przez odbiorców DLT. Konsument DLT to usługa, która pobiera komunikat zawierający metadane dla żądanego transakcji do wykonania, a następnie wysyła informacje do *konstruktora transakcji i osoby podpisującej*. *Konstruktora transakcji i osoby podpisującej* składa transakcji łańcucha bloków na podstawie danych i docelowy żądanego łańcucha bloków. Po złożeniu transakcji jest podpisany. Klucze prywatne są przechowywane w usłudze Azure Key Vault.

 Blockchain Workbench umożliwia pobranie odpowiedniego klucza prywatnego z usługi Key Vault i podpisuje transakcji poza usługą Key Vault. Po podpisaniu transakcji są wysyłane do routerów transakcji i rejestrów.

## <a name="transaction-routers-and-ledgers"></a>Routery transakcji i rejestrów

Routery transakcji rejestrów zająć podpisem transakcji i przekazywać je do odpowiednich łańcucha bloków. Obecnie Blockchain Workbench obsługuje Ethereum jako jego łańcucha bloków docelowych.

## <a name="dlt-watcher"></a>DLT obserwatora

Obserwator technologii (DLT) rejestru rozproszonego monitoruje zdarzenia występujące w bloku łańcuchów dołączony do aplikacji Blockchain Workbench.
Zdarzenia odzwierciedlają informacje istotne dla użytkowników indywidualnych i systemów. Na przykład tworzenie nowych wystąpień kontraktu, wykonywanie transakcji, a zmiany stanu. Zdarzenia są przechwytywane i wysyłane do brokera komunikatów wychodzących, dzięki czemu mogą być używane przez konsumentów podrzędnego.

Na przykład konsumenta SQL monitoruje zdarzenia, wykorzystuje je i wypełnia bazy danych SQL przy użyciu uwzględnionych wartościach. Kopia umożliwia odtwarzania repliki dane w magazynie poza łańcuchem.

## <a name="azure-sql-database"></a>Baza danych Azure SQL Database

Azure SQL database, dołączony do aplikacji Blockchain Workbench przechowuje definicje kontraktu, metadanych konfiguracji i dostępny SQL repliki danych przechowywanych w łańcucha bloków. Te dane można łatwo być badane, wizualizowane lub analizowanych przez bezpośredni dostęp do bazy danych. Deweloperzy i inni użytkownicy mogą na użytek bazy danych raportowania i analizy lub innych opartych na danych integracji. Na przykład użytkownicy można wizualizować dane transakcji przy użyciu usługi Power BI.

Ten magazyn poza łańcuchem umożliwia przedsiębiorstwami przesyłać zapytania dotyczące danych w języku SQL, a nie w księdze łańcucha bloków. Standaryzując standardowego schematu, który jest niezależny od stosy technologii łańcucha bloków magazynu poza łańcuchem umożliwia także ponowne użycie raportów i innych artefaktów we wszystkich projektach, scenariusze i organizacji.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage jest używane do przechowywania kontrakty i metadane skojarzone z umowy.

Zamówienia zakupu i listy przewozowe, aby obrazy używane w wiadomości i aplikacje medyczne, aby wideo pochodzących z kamer jednostki policji i głównych obrazów ruchu, począwszy dokumentów pełnić rolę w wielu scenariuszach skoncentrowane na łańcucha bloków. Dokumenty nie są odpowiednie do umieszczenia bezpośrednio na łańcucha bloków.

Blockchain Workbench obsługuje możliwość dodawania dokumentów lub innych zawartości multimedialnej z logiką biznesową łańcucha bloków. Skrót zawartości dokumentu lub nośników znajduje się w łańcucha bloków i rzeczywiste dokumentu lub zawartości multimedialnej jest przechowywany w usłudze Azure Storage. Informacje skojarzone transakcji jest dostarczane do brokera komunikatów przychodzących, spakowane w, podpisane i kierowane do łańcucha bloków. Ten proces wyzwala zdarzenia, które są udostępniane za pośrednictwem brokera komunikatów wychodzących. Bazy danych SQL wykorzystuje te informacje i wysyła je do bazy danych później podczas wykonywania zapytań. Systemy klienckie mogą również korzystać z tych zdarzeń, która będzie działać w odpowiednim.

## <a name="monitoring"></a>Monitorowanie

Workbench umożliwia rejestrowanie aplikacji za pomocą usługi Application Insights i Azure Monitor. Application Insights jest używany do przechowywania wszystkich zarejestrowanych informacji z aplikacji Blockchain Workbench i zawiera błędy, ostrzeżenia i operacje zakończone powodzeniem. Usługa Application Insights może służyć przez deweloperów do debugowania problemów z aplikacji Blockchain Workbench. 

Usługa Azure Monitor zawiera informacje dotyczące kondycji sieć łańcucha bloków. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)