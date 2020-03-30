---
title: Architektura OPC Vault — Platforma Azure | Dokumenty firmy Microsoft
description: Architektura usługi zarządzania certyfikatami OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "71200147"
---
# <a name="opc-vault-architecture"></a>Architektura OPC Vault

W tym artykule przedstawiono omówienie mikrousługi OPC Vault i modułu OPC Vault IoT Edge.

Aplikacje OPC UA używają certyfikatów wystąpień aplikacji w celu zapewnienia zabezpieczeń na poziomie aplikacji. Bezpieczne połączenie jest ustanawiane przy użyciu kryptografii asymetrycznej, dla której certyfikaty aplikacji zapewniają parę kluczy publicznych i prywatnych. Certyfikaty mogą być podpisywane samodzielnie lub podpisywane przez urząd certyfikacji(CA).

Aplikacja OPC UA ma listę zaufanych certyfikatów, która reprezentuje aplikacje, którym ufa. Certyfikaty te mogą być podpisane samodzielnie lub podpisane przez urząd certyfikacji lub mogą być samemu urzędem certyfikacji lub urzędem certyfikacji. Jeśli zaufany certyfikat jest częścią większego łańcucha certyfikatów, aplikacja ufa wszystkim certyfikatom, które tworzą łańcuch do certyfikatu na liście zaufania. Jest to prawdą, tak długo, jak cały łańcuch certyfikatów można sprawdzić poprawności.

Główną różnicą między ufaniem certyfikatom z podpisem własnym a ufaniem certyfikatowi urzędu certyfikacji jest wysiłek instalacji wymagany do wdrożenia i utrzymania zaufania. Istnieje również dodatkowy wysiłek, aby hostować urząd certyfikacji specyficzne dla firmy. 

Aby rozpowszechniać zaufanie dla certyfikatów z podpisem własnym dla wielu serwerów z pojedynczą aplikacją kliencką, należy zainstalować wszystkie certyfikaty aplikacji serwera na liście zaufania aplikacji klienckiej. Ponadto należy zainstalować certyfikat aplikacji klienckiej na wszystkich listach zaufania aplikacji serwera. Ten wysiłek administracyjny jest dość obciążeniem, a nawet zwiększa się, gdy trzeba wziąć pod uwagę okresy istnienia certyfikatu i odnowić certyfikaty.

Korzystanie z urzędu certyfikacji specyficznego dla firmy może znacznie uprościć zarządzanie zaufaniem z wieloma serwerami i klientami. W takim przypadku administrator generuje certyfikat wystąpienia aplikacji podpisanej jako urząd certyfikacji raz dla każdego używanego klienta i serwera. Ponadto certyfikat urzędu certyfikacji jest instalowany na każdej liście zaufania aplikacji, na wszystkich serwerach i klientach. Dzięki takiemu podejściu tylko wygasłe certyfikaty muszą być odnawiane i zastępowane dla aplikacji, których dotyczy problem.

Usługa zarządzania certyfikatami Azure Industrial IoT OPC UA ułatwia zarządzanie urzędem certyfikacji specyficznym dla firmy dla aplikacji OPC UA. Ta usługa jest oparta na mikrousługi OPC Vault. OPC Vault udostępnia mikrousługi do obsługi urzędu certyfikacji specyficzne dla firmy w bezpiecznej chmurze. To rozwiązanie jest wspierane przez usługi zabezpieczone przez usługę Azure Active Directory (Azure AD), usługę Azure Key Vault z sprzętowymi modułami zabezpieczeń (HSM), usługę Azure Cosmos DB i opcjonalnie centrum IoT hub jako magazyn aplikacji.

Mikrousługa usługi OPC Vault jest przeznaczona do obsługi przepływu pracy opartego na rolach, w którym administratorzy zabezpieczeń i osoby zatwierdzające z prawami do podpisywania w usłudze Azure Key Vault zatwierdzają lub odrzucają żądania.

Aby uzyskać zgodność z istniejącymi rozwiązaniami OPC UA, usługi obejmują obsługę modułu brzegowego opartego na mikrousługach OPC Vault. Implementuje to interfejs **OPC UA Global Discovery Server i Certificate Management,** aby rozpowszechniać certyfikaty i listy zaufania zgodnie z częścią 12 specyfikacji. 


## <a name="architecture"></a>Architektura

Architektura jest oparta na mikrousługach OPC Vault, z modułem OPC Vault IoT Edge dla sieci fabrycznej i przykładowym uxem internetowym do kontrolowania przepływu pracy:

![Diagram architektury OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikrousługa OPC Vault

Mikrousługa usługi OPC Vault składa się z następujących interfejsów do implementacji przepływu pracy w celu dystrybucji i zarządzania urzędem certyfikacji specyficznym dla firmy dla aplikacji OPC UA.

### <a name="application"></a>Aplikacja 
- Aplikacja OPC UA może być serwerem lub klientem lub obu. OPC Vault służy w tym przypadku jako organ rejestracji aplikacji. 
- Oprócz podstawowych operacji rejestrowania, aktualizowania i wyrejestrowywać aplikacje, istnieją również interfejsy do znajdowania i wyszukiwania dla aplikacji z wyrażeniami wyszukiwania. 
- Żądania certyfikatów muszą odwoływać się do prawidłowej aplikacji, aby przetworzyć żądanie i wydać podpisany certyfikat ze wszystkimi rozszerzeniami specyficznymi dla OPC UA. 
- Usługa aplikacji jest obsługiwana przez bazę danych w usłudze Azure Cosmos DB.

### <a name="certificate-group"></a>Grupa certyfikatów
- Grupa certyfikatów to jednostka przechowująca główny urząd certyfikacji lub podrzędny certyfikat, w tym klucz prywatny do podpisywania certyfikatów. 
- Długość klucza RSA, długość skrótu SHA-2 i okresy istnienia są konfigurowane zarówno dla certyfikatów urzędu certyfikacji wystawcy, jak i podpisanych aplikacji. 
- Certyfikaty urzędu certyfikacji są przechowywane w usłudze Azure Key Vault, z którego jest wspierany przez moduł HSM poziomu 2 FIPS 140–2. Klucz prywatny nigdy nie opuszcza bezpiecznego magazynu, ponieważ podpisywanie odbywa się za pomocą operacji usługi Azure AD zabezpieczonej przez usługę Azure AD. 
- Certyfikaty urzędu certyfikacji można odnawiać wraz z czasem i przechowywać w bezpiecznym magazynie ze względu na historię usługi Key Vault. 
- Lista odwołań dla każdego certyfikatu urzędu certyfikacji jest również przechowywana w magazynie kluczy jako klucz tajny. Gdy aplikacja jest wyrejestrowana, certyfikat aplikacji jest również odwoływany na liście odwołania certyfikatów (CRL) przez administratora.
- Można odwołać pojedyncze certyfikaty, a także certyfikaty wsadowe.

### <a name="certificate-request"></a>Żądanie certyfikatu
Żądanie certyfikatu implementuje przepływ pracy w celu wygenerowania nowej pary kluczy lub podpisanego certyfikatu przy użyciu żądania podpisywania certyfikatów (CSR) dla aplikacji UA OPC. 
- Żądanie jest przechowywane w bazie danych z towarzyszącymi informacjami, takimi jak temat lub CSR, oraz odwołaniem do aplikacji OPC UA. 
- Logika biznesowa w usłudze sprawdza poprawność żądania względem informacji przechowywanych w bazie danych aplikacji. Na przykład uri aplikacji w bazie danych musi odpowiadać uri aplikacji w csr.
- Administrator zabezpieczeń z prawami do podpisywania (czyli rola osoba zatwierdzająca) zatwierdza lub odrzuca żądanie. Jeśli żądanie zostanie zatwierdzone, zostanie wygenerowana nowa para kluczy lub podpisany certyfikat (lub oba). Nowy klucz prywatny jest bezpiecznie przechowywany w magazynie kluczy, a nowy podpisany certyfikat publiczny jest przechowywany w bazie danych żądań certyfikatów.
- Żądający może sondować stan żądania, dopóki nie zostanie zatwierdzone lub odwołane. Jeśli żądanie zostało zatwierdzone, klucz prywatny i certyfikat można pobrać i zainstalować w magazynie certyfikatów aplikacji OPC UA.
- Żądacz może teraz zaakceptować żądanie usunięcia niepotrzebnych informacji z bazy danych żądań. 

Przez cały okres istnienia podpisanego certyfikatu aplikacja może zostać usunięta lub klucz może zostać naruszony. W takim przypadku menedżer urzędu certyfikacji może:
- Usuń aplikację, która usuwa również wszystkie oczekujące i zatwierdzone żądania certyfikatów aplikacji. 
- Usuń tylko jedno żądanie certyfikatu, jeśli tylko klucz jest odnawiany lub zagrożony.

Teraz naruszone zatwierdzone i zaakceptowane żądania certyfikatów są oznaczone jako usunięte.

Menedżer może regularnie odnawiać umowy CRL urzędu wystawcy. W czasie odnawiania wszystkie usunięte żądania certyfikatów są odwoływane, a numery seryjne certyfikatów są dodawane do listy odwołania listy crl. Odwołane żądania certyfikatów są oznaczone jako odwołane. W nagłych wachtach można również odwołać pojedyncze żądania certyfikatu.

Na koniec zaktualizowane listy CRL są dostępne do dystrybucji do uczestniczących klientów I serwerów OPC UA.

## <a name="opc-vault-iot-edge-module"></a>Moduł OPC Vault IoT Edge
Aby obsługiwać sieć fabryczną Global Discovery Server, można wdrożyć moduł OPC Vault na krawędzi. Uruchom go jako lokalną aplikację .NET Core lub uruchom ją w kontenerze platformy Docker. Należy zauważyć, że z powodu braku obsługi uwierzytelniania Auth2 w bieżącym stosie OPC UA .NET Standard funkcjonalność modułu krawędziowego OPC Vault jest ograniczona do roli czytnika. Nie można personifikować użytkownika z modułu brzegowego do mikrousługi przy użyciu standardowego interfejsu GDS OPC UA.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o architekturze OPC Vault, możesz:

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie programu OPC Vault](howto-opc-vault-deploy.md)
