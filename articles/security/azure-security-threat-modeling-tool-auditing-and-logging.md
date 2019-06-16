---
title: Inspekcja i rejestrowanie — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: środki zaradcze dotyczące zagrożeń, widoczne w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 4843828c89b04e36b0bcc73dcedf9c5735b73729
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610855"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Ramka zabezpieczeń: Inspekcja i rejestrowanie | Środki zaradcze 

| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identyfikowanie jednostki poufne w swoim rozwiązaniu i zaimplementować inspekcji zmian](#sensitive-entities)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Upewnij się, czy inspekcji i rejestrowania są wymuszane na aplikację](#auditing)</li><li>[Upewnij się, że rotacja dziennika i separacji w miejscu](#log-rotation)</li><li>[Upewnij się, czy aplikacja nie rejestruje danych poufnych użytkownika](#log-sensitive-data)</li><li>[Upewnij się, że inspekcji i pliki dziennika mają ograniczony dostęp](#log-restricted-access)</li><li>[Upewnij się, że są rejestrowane zdarzenia zarządzania użytkownika](#user-management)</li><li>[Upewnij się, że system ma wbudowane zabezpieczenia przed niewłaściwym użyciem](#inbuilt-defenses)</li><li>[Włączanie rejestrowania diagnostycznego dla aplikacji sieci web w usłudze Azure App Service](#diagnostics-logging)</li></ul> |
| **Baza danych** | <ul><li>[Upewnij się, że inspekcja logowania jest włączona w programie SQL Server](#identify-sensitive-entities)</li><li>[Włącz wykrywanie zagrożeń w usłudze Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Użyj usługi Azure Storage Analytics, aby inspekcja dostępu do usługi Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementowanie rejestrowania wystarczające](#sufficient-logging)</li><li>[Implementowanie obsługi wystarczające Niepowodzenie inspekcji](#audit-failure-handling)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że inspekcji i rejestrowania są wymuszane na interfejs API sieci Web](#logging-web-api)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Upewnij się, czy odpowiednie inspekcji i rejestrowania są wymuszane na terenie bramy](#logging-field-gateway)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Upewnij się, czy odpowiednie inspekcji i rejestrowania są wymuszane na bramy w chmurze](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identyfikowanie jednostki poufne w swoim rozwiązaniu i zaimplementować inspekcji zmian

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | Identyfikowanie jednostek w rozwiązaniu zawierające dane poufne i zaimplementować inspekcji zmian w tych jednostek i pól |

## <a id="auditing"></a>Upewnij się, czy inspekcji i rejestrowania są wymuszane na aplikację

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | Włączanie inspekcji i rejestrowania do wszystkich składników. Dzienniki inspekcji należy przechwytywać kontekstu użytkownika. Zidentyfikuj wszystkie ważne wydarzenia i rejestrowanie tych zdarzeń. Implementowanie centralnego rejestrowania |

## <a id="log-rotation"></a>Upewnij się, że rotacja dziennika i separacji w miejscu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Rotacji dziennika jest zautomatyzowany proces używany w przypadku Administracja systemu, w którym pliki dziennika Legitymacja są archiwizowane. Serwery, które często uruchamiania dużych aplikacji rejestrować wszystkie żądania: dzienniki przestrzennych, w przypadku rotacji dziennika jest sposób, aby ograniczyć łączny rozmiar dzienników wciąż zezwalając analizy ostatnie zdarzenia. </p><p>Zaloguj się rozdzielenie zasadniczo oznacza, że masz do przechowywania dziennika, pliki na partycji innej jako gdzie systemu operacyjnego/aplikacja jest uruchomiona celu zapobieżenia "odmowa usługi" lub zmiany na starszą wersję aplikacji jej wydajność</p>|

## <a id="log-sensitive-data"></a>Upewnij się, czy aplikacja nie rejestruje danych poufnych użytkownika

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Upewnij się, że nie Rejestruj wszystkie dane poufne, które użytkownik prześle do swojej witryny. Sprawdź, czy rejestrowanie zamierzone, a także efekty uboczne spowodowane przez problemy z projektu. Przykłady danych poufnych:</p><ul><li>Poświadczenia użytkownika</li><li>Numer ubezpieczenia społecznego i inne informacje identyfikacyjne</li><li>Numery kart kredytowych lub inne informacje finansowe</li><li>Informacje o kondycji</li><li>Klucze prywatne lub innych danych, który może służyć do odszyfrowania zaszyfrowanych informacji</li><li>Informacje systemu lub aplikacji, który może służyć do bardziej efektywnego ataku aplikacji</li></ul>|

## <a id="log-restricted-access"></a>Upewnij się, że inspekcji i pliki dziennika mają ograniczony dostęp

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Sprawdź prawa dostępu do plików dziennika są odpowiednio ustawiane. Konta aplikacji powinny mieć dostęp tylko do zapisu i operatory i personel pomocy technicznej powinien mieć dostęp tylko do odczytu, zgodnie z potrzebami.</p><p>Konta administratorów są to jedyne konta, które powinny mieć pełny dostęp. Sprawdź Windows ACL plików dziennika na upewnij się, że są one prawidłowo ograniczeniami:</p><ul><li>Kont aplikacji powinni mieć dostęp tylko do zapisu</li><li>Operatory i personel pomocy technicznej powinni mieć dostęp tylko do odczytu, zgodnie z potrzebami</li><li>Administratorzy są to jedyne konta, które powinny mieć pełny dostęp</li></ul>|

## <a id="user-management"></a>Upewnij się, że są rejestrowane zdarzenia zarządzania użytkownika

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Upewnij się, że aplikacja monitoruje zdarzeń zarządzania użytkownika, takie jak użytkownika udane i nieudane logowania, resetowania haseł, zmiany hasła, zablokowania konta, rejestracja użytkownika. Wykonywanie ułatwia wykrywanie i reagowanie na potencjalnie podejrzanego zachowania. Umożliwia również zbieranie danych dotyczących operacji; na przykład, aby śledzić kto uzyskuje dostęp do aplikacji</p>|

## <a id="inbuilt-defenses"></a>Upewnij się, że system ma wbudowane zabezpieczenia przed niewłaściwym użyciem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Kontrolki powinny zostać zastosowane który wyjątku zabezpieczeń w razie niewłaściwego użycia aplikacji. Np. Jeśli sprawdzenie poprawności danych wejściowych znajduje się w miejscu, a osoba atakująca próbuje wstrzyknąć złośliwy kod, który nie jest zgodny z wyrażeniem regularnym, wyjątek zabezpieczeń mogą być generowane co może być orientacyjną nadużycia systemu</p><p>Na przykład zalecane jest zapewnienie bezpieczeństwa wyjątków zarejestrowanych oraz działania podjęte w następujących kwestiach:</p><ul><li>Walidacja danych wejściowych</li><li>Naruszenia CSRF</li><li>Atak siłowy (górny limit liczby żądań dla poszczególnych użytkowników dla każdego zasobu)</li><li>Naruszeń przekazywania pliku</li><ul>|

## <a id="diagnostics-logging"></a>Włączanie rejestrowania diagnostycznego dla aplikacji sieci web w usłudze Azure App Service

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | ND  |
| **Kroki** | <p>Platforma Azure udostępnia wbudowaną funkcję diagnostyki, która pomaga w debugowaniu aplikacji internetowej usługi App Service. Ma również zastosowanie do aplikacji interfejsu API i aplikacji mobilnych. Aplikacje sieci web usługi App Service zapewnia funkcja diagnostyki dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web.</p><p>Te są logicznie oddzielone do diagnostyki serwera sieci web i diagnostyki aplikacji</p>|

## <a id="identify-sensitive-entities"></a>Upewnij się, że inspekcja logowania jest włączona w programie SQL Server

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Konfigurowanie inspekcji logowania](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Kroki** | <p>Inspekcja logowania serwera bazy danych musi być włączona do wykrywania potwierdzić zgadywanie ataki. Jest ważne przechwycić nieudanych prób logowania. Przechwytywanie prób zarówno udane i nieudane logowania zapewnia dodatkowe korzyści podczas badań śledczej</p>|

## <a id="threat-detection"></a>Włącz wykrywanie zagrożeń w usłudze Azure SQL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Usługi SQL Azure |
| **Atrybuty**              | Wersja SQL - V12 |
| **Odwołania**              | [Rozpoczynanie pracy z usługą wykrywania zagrożeń bazy danych SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Kroki** |<p>Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych. Zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań.</p><p>Użytkownicy mogą eksplorować podejrzane zdarzenia przy użyciu inspekcji usługi Azure SQL Database, aby określić, czy są one skutkiem próby uzyskania dostępu, naruszenia zabezpieczeń lub wykorzystania danych w bazie danych.</p><p>Wykrywanie zagrożeń upraszcza reagowanie na potencjalne zagrożenia bazy danych bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania bezpieczeństwa</p>|

## <a id="analytics"></a>Użyj usługi Azure Storage Analytics, aby inspekcja dostępu do usługi Azure Storage

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND |
| **Odwołania**              | [Przy użyciu usługi Storage Analytics do monitorowania typ autoryzacji](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Kroki** | <p>Dla każdego konta magazynu na jednym można włączyć usługi Azure Storage Analytics wykonać rejestrowania i przechowywanie danych metryk. Dzienniki analiz magazynu zawierają istotne informacje, takie jak metodę uwierzytelniania używaną przez osobę podczas uzyskiwania dostępu do magazynu.</p><p>Może to być bardzo przydatne, jeśli są ściśle ochrona dostępu do magazynu. Na przykład w usłudze Blob Storage można ustawić wszystkie kontenery na prywatną i zaimplementować korzystania z usługi sygnatury dostępu Współdzielonego w całej aplikacji. Następnie można sprawdzić dzienniki regularnie, aby zobaczyć, czy obiekty BLOB są dostępne przy użyciu kluczy konta magazynu, które mogą wskazywać naruszenia zabezpieczeń, czy obiekty BLOB były publiczne, ale nie powinny być.</p>|

## <a id="sufficient-logging"></a>Implementowanie rejestrowania wystarczające

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroki** | <p>Brak śladu inspekcji odpowiednie po zdarzenie naruszenia zabezpieczeń mogą utrudniać śledczej wysiłków. Windows Communication Foundation (WCF) oferuje możliwość rejestrowania prób uwierzytelnienia się pomyślnie i/lub nie powiodło się.</p><p>Rejestruje prób uwierzytelnienia zakończonych niepowodzeniem może zostać wyświetlone ostrzeżenie Administratorzy potencjalnych ataków siłowych. Podobnie rejestrowanie zdarzeń pomyślnego uwierzytelnienia może zapewnić dziennik inspekcji przydatne w przypadku złamania zabezpieczeń konta. Włączanie funkcji inspekcji zabezpieczeń usługi WCF firmy |

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładową konfigurację po włączeniu inspekcji
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Implementowanie obsługi wystarczające Niepowodzenie inspekcji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroki** | <p>Rozwinięte rozwiązanie konfiguracja nie wygeneruje wyjątek po błędzie do zapisania w dzienniku inspekcji. Skonfigurowanie usługi WCF nie można zgłosić wyjątek, gdy nie można zapisać w dzienniku inspekcji jest program nie będzie powiadamiany o awarii i inspekcji zdarzeń krytycznych mogą nie być wykrywane.</p>|

### <a name="example"></a>Przykład
`<behavior/>` Element pliku konfiguracji usługi WCF poniżej powoduje, że usługi WCF nie powiadomić aplikację, gdy WCF nie można zapisać w dzienniku inspekcji.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Konfigurowanie usługi WCF do powiadamiania programu zawsze wtedy, gdy go nie można zapisać w dzienniku inspekcji. Program powinien mieć schemat alternatywnych powiadomień w miejscu z alertem organizacji, która dzienników inspekcji nie są już uaktualniana. 

## <a id="logging-web-api"></a>Upewnij się, że inspekcji i rejestrowania są wymuszane na interfejs API sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Włączanie inspekcji i rejestrowania na interfejsy API sieci Web. Dzienniki inspekcji należy przechwytywać kontekstu użytkownika. Zidentyfikuj wszystkie ważne wydarzenia i rejestrowanie tych zdarzeń. Implementowanie centralnego rejestrowania |

## <a id="logging-field-gateway"></a>Upewnij się, czy odpowiednie inspekcji i rejestrowania są wymuszane na terenie bramy

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Bramy w terenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Gdy wiele urządzeń nawiązać połączenie bramy w terenie, upewnij się, czy próby nawiązania połączenia i stanu uwierzytelniania (powodzenie lub niepowodzenie) dla poszczególnych urządzeń są zarejestrowane i przechowywany w terenie bramy.</p><p>W przypadkach, w którym bramy w terenie jest przechowywanie poświadczeń usługi IoT Hub dla poszczególnych urządzeń, upewnij się również, że inspekcja jest wykonywane, gdy te poświadczenia są pobierane. Opracowanie procesu do okresowego przekazywania dzienników do usługi Azure IoT Hub i magazynowania na długi czas.</p> |

## <a id="logging-cloud-gateway"></a>Upewnij się, czy odpowiednie inspekcji i rejestrowania są wymuszane na bramy w chmurze

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | IoT Cloud Gateway | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wprowadzenie do usługi IoT Hub obsługi, monitorowaniem](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Kroki** | <p>Projektowanie pod kątem zbieranie i przechowywanie danych inspekcji zebranych przez monitorowanie operacji usługi IoT Hub. Włącz następujące kategorie monitorowania:</p><ul><li>Operacje dotyczące tożsamości urządzenia</li><li>Komunikacja z urządzenia do chmury</li><li>Komunikacja z chmury do urządzenia</li><li>Połączenia</li><li>Operacje przekazywania plików</li></ul>|