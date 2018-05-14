---
title: Architektura Blockchain Workbench Azure
description: Przegląd architektury Azure Blockchain Workbench i jej składniki.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: d37913caa94dc4cf79aef9c2c12a7aacce7c03ce
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-architecture"></a>Architektura Blockchain Workbench Azure

Azure Blockchain Workbench upraszcza projektowanie aplikacji blockchain zapewnia rozwiązanie przy użyciu kilku składników platformy Azure. Blockchain Workbench można wdrożyć przy użyciu szablonu rozwiązania w portalu Azure Marketplace. Szablon umożliwia użytkownikom do pobrania, moduły i składniki do wdrożenia z Blockchain Workbench takich jak blockchain stosu, typ aplikacji klienta i pomocy technicznej integracji IoT. Po wdrożeniu Blockchain Workbench zapewnia dostęp do aplikacji sieci web, aplikacji dla systemu iOS i Android — aplikacja.

![Architektura Blockchain Workbench](media/blockchain-workbench-architecture/architecture.png)

## <a name="identity-and-authentication"></a>Uwierzytelnianie i tożsamość

Przy użyciu narzędzia Blockchain Workbench, konsorcjum można było wykonać Federację tożsamości organizacji przy użyciu usługi Azure Active Directory (Azure AD). Workbench generuje nowych kont użytkowników dla tożsamości o łańcucha z tożsamości organizacji przechowywane w usłudze Azure AD. Mapowania tożsamości ułatwia uwierzytelnionego logowania do interfejsów API klienta i aplikacji oraz używa zasad uwierzytelniania w organizacji. Workbench umożliwia również skojarzyć tożsamościami w przedsiębiorstwie do określonych ról w ramach danego kontraktu inteligentne. Ponadto Workbench udostępnia mechanizm do identyfikacji akcji tych ról może zająć oraz porę.

Po wdrożeniu Blockchain Workbench użytkownicy korzystają z Blockchain Workbench za pośrednictwem aplikacji klienckich, interfejsu API klienta opartego na interfejsie REST lub wiadomości interfejsu API. We wszystkich przypadkach interakcje musi zostać uwierzytelniony, za pośrednictwem usługi Azure Active Directory (Azure AD) lub poświadczenia dotyczące urządzenia.

Użytkownicy Federację tożsamości do konsorcjum usługi Azure AD, wysyłając zaproszenia wiadomości e-mail do uczestników na swój adres e-mail. Podczas logowania się w tych użytkowników są uwierzytelniane przy użyciu nazwy, hasło i zasady. Na przykład uwierzytelniania dwuskładnikowego w organizacji.

Usługi Azure AD umożliwia zarządzanie wszystkich użytkowników, którzy mają dostęp do środowiska roboczego Blockchain. Każde urządzenie łączące się inteligentne kontrakt jest także powiązany z usługą Azure AD.

Usługi Azure AD jest również używane do przypisywania użytkowników do grupy specjalne administratora. Użytkownicy skojarzeni z grupy administratorów uzyskują administratora prawa/akcji środowiska roboczego Blockchain, takich jak wdrażanie kontrakty i przyznawanie uprawnień do użytkownikowi na dostęp kontrakt. Użytkownicy spoza tej grupy nie mają dostępu do czynności wykonywane przez administratorów.

## <a name="client-applications"></a>Aplikacje klienta

Workbench zapewnia automatycznie wygenerowanego klienta aplikacji sieci web i mobile (z systemem iOS, Android), która może służyć do sprawdzania poprawności, testowania i wyświetlania blockchain aplikacji. Interfejs aplikacji jest generowane dynamicznie na podstawie metadanych inteligentne kontraktu i może obsłużyć wszystkie przypadek użycia. Aplikacje klienckie dostarczania frontonu dla użytkownika do aplikacji pełną blockchain generowane przez Blockchain Workbench. Aplikacje klienckie uwierzytelniania użytkowników za pomocą usługi Azure Active Directory (Azure AD), a następnie prezentować środowisko użytkownika, dostosowane do kontekst biznesowy inteligentne kontraktu. Środowisko użytkownika umożliwia tworzenie nowych wystąpień inteligentne kontraktu przez uprawnionych osób i przedstawia możliwość wykonywania niektórych typów transakcji w odpowiednich miejscach w inteligentne kontraktu reprezentowany przez proces biznesowy.

W aplikacji sieci web autoryzowanych użytkowników można uzyskać dostępu do konsoli administratora. Konsola jest dostępna dla użytkowników w grupie administratorów w usłudze Azure AD i udostępnia następujące funkcje:

* Wdróż przewidzianych inteligentne kontrakty popularne scenariusze firmy Microsoft. Na przykład zasobów transfer scenariusza.
* Przekaż i wdróż inteligentne umowy.
* Przypisz dostęp użytkownika do inteligentnego kontraktu w kontekście konkretnej roli.

## <a name="gateway-service-api"></a>Interfejs API usługi bramy

Blockchain Workbench obejmuje interfejs API usługi bramy opartego na interfejsie REST. Podczas zapisywania blockchain, interfejsu API generuje i zapewnia komunikatów brokera zdarzeń. Gdy dane są żądane przez interfejs API, zapytania są wysyłane do bazy danych SQL poza łańcucha. Baza danych SQL zawiera replikę w łańcuchu danych i metadanych, który zawiera informacje o kontekście i konfiguracji obsługiwanych inteligentne umów. Kwerendy zwracają wymagane dane z repliki poza łańcucha w formacie informacji metadanych dla kontraktu.

Deweloperzy mogą uzyskiwać dostęp do interfejsu API usługi bramy tworzyć ani integrować rozwiązań blockchain bez polegania na Blockchain Workbench aplikacji klienta.

> [!NOTE]
> Aby włączyć uwierzytelniony dostęp do interfejsu API, dwie aplikacje klienckie są zarejestrowane w usłudze Azure Active Directory. Usługa Azure Active Directory wymaga rejestracji aplikacji różne każdego typu aplikacji (natywny i sieci web). 

## <a name="message-broker-for-incoming-messages"></a>Brokera komunikatów dla komunikatów przychodzących

Deweloperów, którzy chcą wysyłać bezpośrednio do Blockchain Workbench może wysyłać komunikaty bezpośrednio do usługi Service Bus. Na przykład wiadomości interfejsu API może służyć do integracji systemu do systemu lub urządzenia IoT.

## <a name="message-broker-for-downstream-consumers"></a>Brokera komunikatów dla konsumentów podrzędne

Podczas cyklu życia aplikacji występują zdarzenia. Zdarzenia mogą być wyzwalane przez interfejs API bramy lub na księgowe. Powiadomienia o zdarzeniach można inicjować podrzędne kodu opartego na zdarzenia.

Blockchain Workbench automatycznie wdraża dwa rodzaje odbiorców zdarzeń. Jeden jest wyzwalane przez zdarzenia blockchain do wypełnienia magazynu SQL poza łańcucha. Druga to Przechwytywanie metadanych dla zdarzeń generowanych przez interfejs API powiązane z przekazywania i przechowywanie dokumentów.

## <a name="message-consumers"></a>Wiadomość konsumentom

 Wiadomość konsumentom zająć wiadomości z usługi Service Bus. Odpowiedni model obsługi zdarzeń dla wiadomość konsumentom umożliwia rozszerzenia dodatkowych usług i systemów. Na przykład można dodać techniczną, aby wypełnić CosmosDB lub analizowania wiadomości przy użyciu usługi analiza strumienia Azure. W poniższych sekcjach opisano objęte Blockchain Workbench konsumentów wiadomości.

### <a name="distributed-ledger-consumer"></a>Rozproszone księgi konsumenta

Rozproszonej księgi technologii (DLT) wiadomości zawiera metadanych dla transakcji do zapisania blockchain. Klient pobiera komunikaty i wypychanie danych do konstruktora transakcji, osoby podpisującej i router.

### <a name="database-consumer"></a>Konsument bazy danych

Konsumenta bazy danych trwa komunikaty z usługi Service Bus i wypychanie danych do podłączonego bazy danych, takich jak bazy danych SQL.

### <a name="storage-consumer"></a>Konsument magazynu

Konsument magazynu pobiera komunikaty usługi Service Bus i wypycha dane do magazynu. Na przykład przechowywanie skrótu dokumentów w usłudze Azure Storage.

## <a name="transaction-builder-and-signer"></a>Konstruktor transakcji i osoby podpisującej

Jeśli wiadomość na brokera komunikatów przychodzących wymaga zapisania blockchain, będą przetwarzane przez odbiorców DLT. Użytkownik DLT jest usługa, która pobiera komunikat zawierający metadane dla żądanego transakcji do wykonania, a następnie wysyła informacje do *konstruktora transakcji i osoby podpisującej*. *Konstruktora transakcji i osoby podpisującej* składana transakcji blockchain na podstawie danych i blockchain żądane miejsce docelowe. Po łączone, transakcja jest podpisana. Klucze prywatne są przechowywane w magazynie kluczy Azure.

 Blockchain Workbench pobiera odpowiedni klucz prywatny z magazynu kluczy i podpisuje transakcji poza magazynu kluczy. Po podpisaniu transakcji są wysyłane do transakcji routery i księgi.

## <a name="transaction-routers-and-ledgers"></a>Routery transakcji i księgi

Routery transakcji księgi zająć podpisem transakcji i kierować je do odpowiednich blockchain. Obecnie Blockchain Workbench obsługuje Ethereum jako jego blockchain docelowej.

## <a name="dlt-watcher"></a>DLT obserwatora

Obserwatora technologii (DLT) rozproszonej księgi monitoruje zdarzenia występujące w bloku łańcuchów dołączony do Blockchain Workbench.
Zdarzenia odzwierciedlają informacje istotne dla użytkowników indywidualnych i systemów. Na przykład tworzenie nowego wystąpienia kontraktu, wykonanie transakcji, a zmiany stanu. Zdarzenia są przechwytywane i wysyłane do brokera komunikatów wychodzących, dlatego mogą być używane przez konsumentów podrzędne.

Na przykład konsumenta SQL monitoruje zdarzenia, wykorzystuje je i wypełnienie bazy danych SQL z wartościami uwzględnione. Kopia umożliwia odtworzenie repliki o łańcuchu danych w magazynie poza łańcucha.

## <a name="azure-sql-database"></a>Baza danych SQL Azure

Baza danych Azure SQL dołączonych do Blockchain Workbench przechowuje definicje kontraktu, metadanych konfiguracji i dostępnych SQL repliki danych przechowywanych w blockchain. Te dane można łatwo można zbadać wizualizowane i analizowane przez bezpośrednie uzyskiwanie dostępu do bazy danych. Deweloperzy i innych użytkowników można użyć bazy danych raportowania, analityka lub innych integracji skoncentrowane na danych. Użytkownicy mogą na przykład wizualizacji danych transakcji przy użyciu usługi Power BI.

Ta pamięć masowa poza łańcucha pozwala enterprise organizacjom danych zapytania SQL, a nie w księdze blockchain. Wprowadzając standardowe schemat, który jest niezależny stosów technologii blockchain magazynu poza łańcucha umożliwia także ponowne użycie raportów i innych artefaktów we wszystkich projektach, scenariusze i organizacji.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage jest używany do przechowywania kontrakty i metadane skojarzone z umowami.

Z zakupów i konosamenty, aby obrazy używane w wiadomości i medyczne zdjęcia, wideo pochodzącym z tym polecenie kamer treści i głównych obrazów ruchu, począwszy dokumenty mają znaczenie w wielu scenariuszach skoncentrowane na blockchain. Dokumenty nie są odpowiednie do umieszczenia bezpośrednio na blockchain.

Blockchain Workbench obsługuje możliwość dodawania, dokumentów lub innych zawartości multimedialnej z blockchain logiki biznesowej. Skrót zawartości dokumentu lub nośników znajduje się w blockchain i rzeczywistego dokumentu lub zawartości multimedialnej są przechowywane w usłudze Azure Storage. Informacje skojarzonej transakcji jest dostarczany do brokera komunikatów przychodzących, umieszczone w podpisane i kierowane do blockchain. Ten proces wyzwala zdarzenia, które są udostępniane za pośrednictwem brokera komunikatów wychodzących. Bazy danych SQL wykorzystuje te informacje i wysyła go do bazy danych na potrzeby zapytań o później. Systemy klienckie można również pobierać te zdarzenia do działania jako odpowiednie.

## <a name="monitoring"></a>Monitorowanie

Workbench umożliwia rejestrowanie aplikacji przy użyciu usługi Application Insights i Azure Monitor. Usługa Application Insights jest używany do przechowywania wszystkich zarejestrowanych informacji z Blockchain Workbench i zawiera błędy, ostrzeżenia i pomyślnych operacji. Usługi Application Insights mogą posłużyć deweloperom debugowanie problemów z Blockchain Workbench. 

Azure Monitor zawiera informacje dotyczące kondycji sieci blockchain. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Blockchain Workbench](blockchain-workbench-deploy.md)