---
title: Architektura magazynu OPC — platforma Azure | Microsoft Docs
description: Architektura usługi zarządzania certyfikatami magazynu OPC
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 151eb535e70bd485c978ac21539c784bbea0a532
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973233"
---
# <a name="opc-vault-architecture"></a>Architektura magazynu OPC

Ten artykuł zawiera omówienie **mikrousługi magazynu OPC** oraz **magazynu OPC IoT Edge**.

## <a name="overview"></a>Przegląd

Aplikacje OPC UA używają certyfikatów wystąpień aplikacji, aby zapewnić zabezpieczenia na poziomie aplikacji. Bezpieczne połączenie jest nawiązywane za pomocą kryptografii asymetrycznej, dla którego certyfikaty aplikacji zapewniają parę kluczy publicznych i prywatnych. Certyfikaty mogą być podpisane z podpisem własnym lub mogą być podpisane przez urząd certyfikacji.

Aplikacja OPC UA ma listę zaufanych certyfikatów reprezentujących aplikacje, które ufa. Te certyfikaty mogą być podpisane z podpisem własnym, podpisane przez urząd certyfikacji lub urzędem certyfikacji lub urzędem certyfikacji podrzędnej. Jeśli zaufany certyfikat jest częścią większego łańcucha certyfikatów, aplikacja ufa wszystkim certyfikatom, które są powiązane z certyfikatem na liście zaufania, o ile cały łańcuch certyfikatów może być zweryfikowany.

Główną różnicą między zaufanymi certyfikatami z podpisem własnym i zaufaniem do certyfikatu urzędu certyfikacji jest nakład pracy związany z instalacją i utrzymywaniem zaufania oraz dodatkowym wysiłkiem do hostowania urzędu certyfikacji specyficznego dla danej firmy. 

W celu dystrybucji zaufania dla certyfikatów z podpisem własnym dla n serwerów z jedną aplikacją kliencką wszystkie certyfikaty aplikacji na serwerze muszą być zainstalowane na liście zaufania aplikacji klienta, a certyfikat aplikacji klienta musi być zainstalowany na wszystkich Listy zaufania aplikacji serwera. Ta praca administracyjna jest w pełni uciążliwa i jeszcze bardziej zwiększa się, gdy okresy istnienia certyfikatów są brane pod uwagę, a certyfikaty są odnawiane.

Korzystanie z urzędu certyfikacji specyficznego dla firmy może znacznie uprościć zarządzanie zaufaniem z wieloma serwerami i klientami. W takim przypadku administrator generuje certyfikat wystąpienia aplikacji podpisanej przez urząd certyfikacji jeden raz dla każdego używanego klienta i serwera. Ponadto certyfikat urzędu certyfikacji jest instalowany na każdej liście zaufania aplikacji na wszystkich serwerach i klientach. W tym podejściu tylko wygasłe certyfikaty muszą zostać odnowione i zastąpione dla aplikacji, których to dotyczy.

Usługa zarządzania certyfikatami usługi Azure Industrial IoT OPC UA to nasze rozwiązanie służące do zarządzania urzędem certyfikacji specyficznym dla danej firmy dla aplikacji OPC UA opartych na mikrousługach magazynu OPC.

Magazyn OPC udostępnia mikrousługi obsługujące urząd certyfikacji specyficzny dla danej firmy w bezpiecznej chmurze, objęty usługami zabezpieczonymi przez usługę Azure AD z Azure Key Vault ze sprzętowymi modułami zabezpieczeń, Cosmos DB i opcjonalnie IoT Hub jako magazyn aplikacji.

Mikrousługa magazynu OPC została zaprojektowana z myślą o obsłudze przepływu pracy opartego na rolach, gdy z żądaniami osobistymi podpisane certyfikaty aplikacji oraz administratorzy zabezpieczeń i osoby zatwierdzające z prawami do podpisywania w Azure Key Vault zatwierdzać lub odrzucać te żądania.

Aby zapewnić zgodność z istniejącymi rozwiązaniami OPC UA GDS, usługi obejmują obsługę modułu brzegowego kopii zapasowej mikrousług magazynu OPC, który implementuje *serwer wykrywania globalnego OPC UA oraz interfejs zarządzania certyfikatami* do dystrybucji Certyfikaty i listy zaufania zgodnie z częścią 12 specyfikacji. Jednak zgodnie z naszą wiedzą, ten interfejs serwera GDS nie jest jeszcze szeroko używany i ma jeszcze ograniczoną funkcjonalność (rola czytnika). [Na żądanie poprawimy środowisko na żądanie klienta (*)](#yet-unsupported-features).

## <a name="architecture"></a>Architektura

Architektura jest oparta na mikrousługach magazynu OPC z magazynem OPC IoT Edge module dla sieci fabryki i przykładowym środowisku internetowym do sterowania przepływem pracy:

![Architektura OPCVault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikrousługa magazynu OPC

Mikrousługa magazynu OPC składa się z następujących interfejsów, które umożliwiają wdrożenie przepływu pracy w celu dystrybucji i zarządzania urzędem certyfikacji specyficznym dla firmy dla aplikacji OPC UA:

### <a name="application"></a>Aplikacja 
- "Aplikacja OPC UA" może być serwerem lub klientem lub jednocześnie. Magazyn OPC służy w tym przypadku jako urząd rejestrowania aplikacji. 
- Poza podstawowymi operacjami rejestrowania, aktualizowania i wyrejestrowywania aplikacji istnieją również interfejsy umożliwiające wyszukiwanie aplikacji i wyszukiwanie ich w wyrażeniach. 
- Żądania certyfikatu muszą odwoływać się do prawidłowej aplikacji, aby przetworzyć żądanie i wydać podpisany certyfikat ze wszystkimi rozszerzeniami OPC UA. 
- Usługa aplikacji jest objęta usługą CosmosDB Database lub [rejestrem urządzeń OpcTwin (*)](#yet-unsupported-features), w zależności od konfiguracji klienta.

### <a name="certificate-group"></a>Grupa certyfikatów
- Grupa certyfikatów to jednostka, w której jest przechowywany główny urząd certyfikacji lub certyfikat podrzędnego urzędu certyfikacji, w tym klucz prywatny służący do podpisywania certyfikatów. 
- Długość wartości skrótu SHA-2 i okresy istnienia są konfigurowane zarówno dla urzędu certyfikacji wystawcy, jak i podpisanych certyfikatów aplikacji. 
- Wiele grup można obsługiwać w jednej usłudze, aby umożliwić korzystanie z przyszłych rozszerzeń z grupami certyfikatów algorytmu https, użytkownika lub ECC [(*)](#yet-unsupported-features). 
- Certyfikaty urzędu certyfikacji są przechowywane w Azure Key Vaultach z użyciem sprzętowego modułu zabezpieczeń FIPS 140-2 Level 2 (HSM). Klucz prywatny nigdy nie opuszcza bezpiecznego magazynu, ponieważ podpisywanie jest wykonywane przez AzureAD zabezpieczonej operacji Key Vault. 
- Certyfikaty urzędu certyfikacji mogą zostać odnowione z upływem czasu i nadal w bezpiecznym magazynie ze względu na historię Key Vault. 
- Lista odwołania dla każdego certyfikatu urzędu certyfikacji jest również przechowywana w Key Vault jako wpis tajny. Po wyrejestrowaniu aplikacji certyfikat aplikacji jest również odwołany na liście CRL przez administratora.
- Obsługa wsadowych i pojedynczych certyfikatów jest obsługiwana.

### <a name="certificate-request"></a>Żądanie certyfikatu
Żądanie certyfikatu implementuje przepływ pracy w celu wygenerowania nowej pary kluczy lub podpisanego certyfikatu przy użyciu "żądania podpisania certyfikatu" (CSR) dla aplikacji OPC UA. 
- Żądanie jest przechowywane w bazie danych z towarzyszącymi informacjami, takimi jak podmiot lub CSR, oraz odwołaniem do aplikacji OPC UA. 
- Logika biznesowa w usłudze sprawdza poprawność żądania względem informacji przechowywanych w bazie danych aplikacji. Na przykład identyfikator URI aplikacji w bazie danych musi być zgodny z identyfikatorem URI aplikacji w CSR.
- Administrator zabezpieczeń z prawami do podpisywania (rola osoby zatwierdzającej) zatwierdza lub odrzuca żądanie. Jeśli żądanie zostanie zatwierdzone, zostanie wygenerowana Nowa para kluczy i/lub podpisany certyfikat. Nowy klucz prywatny jest bezpiecznie przechowywany w magazynie kluczy, podczas gdy nowy podpisany certyfikat publiczny jest przechowywany w bazie danych żądania certyfikatu.
- Obiekt żądający może sondować stan żądania, dopóki nie zostanie zatwierdzony lub odwołany. Jeśli żądanie zostało zatwierdzone, klucz prywatny i certyfikat można pobrać i zainstalować w magazynie certyfikatów aplikacji OPC UA.
- Obiekt żądający może teraz zaakceptować żądanie usunięcia niepotrzebnych informacji z bazy danych żądań. 

Po upływie okresu istnienia podpisanego certyfikatu aplikacja może zostać usunięta lub klucz może ulec naruszeniu. W takim przypadku Menedżer urzędu certyfikacji może:
- Usuń aplikację, która jednocześnie usuwa wszystkie oczekujące i zatwierdzone żądania certyfikatów aplikacji. 
- Inna opcja polega na usunięciu pojedynczego żądania certyfikatu w przypadku odnowienia lub złamania zabezpieczeń klucza.
- Obecnie naruszone zabezpieczenia zatwierdzone i akceptowane żądania certyfikatów są oznaczane jako usunięte.
- Menedżer może regularnie wykonywać odnowienie listy CRL urzędu certyfikacji. W momencie odnowienia wszystkie usunięte żądania certyfikatu zostaną odwołane, a numery seryjne certyfikatów są dodawane do listy odwołania listy CRL. Odwołane żądania certyfikatów są oznaczane jako odwołane.
- W przypadku pilnych zdarzeń można również odwołać pojedyncze żądania certyfikatu.
- Na koniec zaktualizowane listy CRL są dostępne do dystrybucji do uczestniczących klientów i serwerów OPC UA.

Aby uzyskać więcej informacji na temat interfejsu API usługi OPC magazynu, zobacz dokumentację struktury Swagger mikrousługi.

## <a name="opc-vault-iot-edge-module"></a>Moduł IoT Edge magazynu OPC
Aby zapewnić obsługę globalnego serwera odnajdywania sieci, moduł magazynu OPC można wdrożyć na krawędzi, uruchomić jako lokalną aplikację .Net Core lub uruchomić w kontenerze platformy Docker. Ze względu na brak obsługi uwierzytelniania elementu Auth2 w bieżącym stosie programu .NET Standard OPC UA, funkcje modułu brzegowego magazynu OPC są ograniczone do roli czytelnik, ponieważ nie można spersonifikować użytkownika z modułu brzegowego do mikrousługi przy użyciu programu OPC UA GDS Standard  interfejsu. W tym punkcie[(*)](#yet-unsupported-features)są dozwolone tylko operacje, które nie wymagają roli składnika zapisywania, administratora lub osoby zatwierdzającej. 

## <a name="yet-unsupported-features"></a>Jeszcze Nieobsługiwane funkcje

**(*)** jeszcze nieobsługiwane.
