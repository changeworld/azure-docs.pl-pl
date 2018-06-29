---
title: Inspekcji i rejestrowania Azure - Microsoft Threat narzędzia modelowania — | Dokumentacja firmy Microsoft
description: środki zaradcze w przypadku zagrożeń widoczne w narzędziu modelowania zagrożeń
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 8837dfaf156e5a4d07598f2c58694663a9ff5580
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029985"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Ramka zabezpieczeń: Inspekcji i rejestrowania | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identyfikowania poufnych jednostek w rozwiązaniu i implementować inspekcji zmian](#sensitive-entities)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Upewnij się, czy przeprowadzania inspekcji i rejestrowania są wymuszane na aplikację](#auditing)</li><li>[Upewnij się, że rotacji dziennika i oddzielenie zostały spełnione](#log-rotation)</li><li>[Upewnij się, że aplikacja nie rejestruje danych poufnych użytkownika](#log-sensitive-data)</li><li>[Upewnij się, że inspekcja i pliki dziennika mają dostęp ograniczony](#log-restricted-access)</li><li>[Upewnij się, że użytkownik administracyjny zdarzenia są rejestrowane](#user-management)</li><li>[Upewnij się, że system ma wbudowane zabezpieczenia przed niewłaściwym użyciem](#inbuilt-defenses)</li><li>[Włączanie rejestrowania diagnostyki dla aplikacji sieci web w usłudze aplikacji Azure](#diagnostics-logging)</li></ul> |
| **Baza danych** | <ul><li>[Upewnij się, że inspekcja logowania jest włączona na serwerze SQL](#identify-sensitive-entities)</li><li>[Włączyć wykrywanie zagrożeń SQL Azure](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Przeprowadź inspekcję dostępu do usługi Azure Storage za pomocą analityka magazynu Azure](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementowanie wystarczające rejestrowania](#sufficient-logging)</li><li>[Implementowanie obsługi wystarczające Niepowodzenie inspekcji](#audit-failure-handling)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że inspekcji i rejestrowania są wymuszane na interfejs API sieci Web](#logging-web-api)</li></ul> |
| **Pole IoT bramy** | <ul><li>[Upewnij się, czy odpowiedniej inspekcji i rejestrowania są wymuszane na pole bramy](#logging-field-gateway)</li></ul> |
| **Brama chmury IoT** | <ul><li>[Upewnij się, czy odpowiednie inspekcji i rejestrowania są wymuszane na bramy chmury](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Identyfikowania poufnych jednostek w rozwiązaniu i implementować inspekcji zmian

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | Identyfikowanie jednostek w rozwiązaniu zawierające dane poufne i implementować inspekcji zmian w tych jednostek i pól |

## <a id="auditing"></a>Upewnij się, czy przeprowadzania inspekcji i rejestrowania są wymuszane na aplikację

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | Włączanie inspekcji i rejestrowania dla wszystkich składników. Dzienniki inspekcji należy przechwytywać kontekstu użytkownika. Zidentyfikuj wszystkie zdarzenia ważne i dziennika zdarzeń. Implementowanie funkcji centralnego rejestrowania |

## <a id="log-rotation"></a>Upewnij się, że rotacji dziennika i oddzielenie zostały spełnione

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Rotacji dziennika jest używany w administracji systemu, w którym są archiwizowane pliki dziennika z zautomatyzowany proces. Serwery, które często uruchamiać aplikacje dużych rejestrować wszystkie żądania: w wypadku przestrzennych dzienniki rotacji dziennika to sposób ograniczyć całkowity rozmiar dzienników, umożliwiając analizy ostatnie zdarzenia. </p><p>Zaloguj się oddzielenie zasadniczo oznacza, że masz do przechowywania dziennika, pliki na partycji innej jako gdzie OS/aplikacja jest uruchomiona celu zapobieżenia obniżenie aplikacji lub typu "odmowa usługi" jego wydajność,</p>|

## <a id="log-sensitive-data"></a>Upewnij się, że aplikacja nie rejestruje danych poufnych użytkownika

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Sprawdź, czy dane poufne, które użytkownik prześle nie logowania do witryny. Sprawdź, czy zamierzone rejestrowania, a także efekty uboczne spowodowane przez problemy z projektu. Przykłady danych poufnych:</p><ul><li>Poświadczenia użytkownika</li><li>Numer ubezpieczenia społecznego i inne informacje identyfikacyjne</li><li>Numer karty kredytowej lub inne informacje finansowe</li><li>Informacje o kondycji</li><li>Klucze prywatne lub innych danych, która może posłużyć do odszyfrowania zaszyfrowanych informacji</li><li>Informacje systemu lub aplikacji, które umożliwia bardziej efektywne ataki aplikacji</li></ul>|

## <a id="log-restricted-access"></a>Upewnij się, że inspekcja i pliki dziennika mają dostęp ograniczony

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Sprawdź prawa dostępu do plików dziennika jest skonfigurowana. Konta aplikacji powinny mieć dostęp tylko do zapisu i operatory i pomocy technicznej powinien mieć dostęp tylko do odczytu, zgodnie z potrzebami.</p><p>Konta administratorów są to jedyne konta, które ma pełny dostęp. Sprawdź ACL systemu Windows pliki dziennika na upewnij się, że są one poprawnie ograniczeniami:</p><ul><li>Konta aplikacji powinny mieć dostęp tylko do zapisu</li><li>Operatory i personel pomocy technicznej powinien mieć dostęp tylko do odczytu, w razie potrzeby</li><li>Administratorzy są to jedyne konta, które ma pełny dostęp</li></ul>|

## <a id="user-management"></a>Upewnij się, że użytkownik administracyjny zdarzenia są rejestrowane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Upewnij się, że aplikacja monitoruje zdarzeń zarządzania użytkownika, takich jak nazwy logowania użytkownika udane i nieudane, resetowania haseł, zmiany hasła, zablokowania konta, rejestracja użytkownika. Wykonywanie ułatwia wykrywanie i reagowanie na podejrzanego zachowania. Umożliwia również zbieranie danych operacji; na przykład, aby śledzić kto uzyskuje dostęp do aplikacji</p>|

## <a id="inbuilt-defenses"></a>Upewnij się, że system ma wbudowane zabezpieczenia przed niewłaściwym użyciem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki**                   | <p>Formanty powinny zostać zastosowane którego zgłosić wyjątek zabezpieczeń w razie niewłaściwego użycia aplikacji. Np. jeśli atakujący podejmie próbę wprowadzenia złośliwego kodu, który nie pasuje do wyrażenia regularnego sprawdzania poprawności danych wejściowych jest już na miejscu, wyjątek zabezpieczeń może zostać wygenerowany może to przykładowy nadużycia systemu</p><p>Na przykład zalecane jest zostały zarejestrowane wyjątki zabezpieczeń oraz działania podjęte w następujących kwestiach:</p><ul><li>Walidacja danych wejściowych</li><li>Naruszenia CSRF</li><li>Atak siłowy (górny limit liczby żądań na użytkownika na zasobów)</li><li>Naruszeń przekazywania pliku</li><ul>|

## <a id="diagnostics-logging"></a>Włączanie rejestrowania diagnostyki dla aplikacji sieci web w usłudze aplikacji Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | ND  |
| **Kroki** | <p>Platforma Azure udostępnia wbudowaną funkcję diagnostyki, która pomaga w debugowaniu aplikacji internetowej usługi App Service. Ma również zastosowanie do aplikacji interfejsu API i aplikacji mobilnych. Aplikacje sieci web usługi aplikacji udostępniają funkcje diagnostyczne dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web.</p><p>Te logicznie podzielone na diagnostyki serwera sieci web i diagnostyki aplikacji</p>|

## <a id="identify-sensitive-entities"></a>Upewnij się, że inspekcja logowania jest włączona na serwerze SQL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Konfigurowanie inspekcji logowania](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Kroki** | <p>Inspekcja logowania serwera bazy danych musi być włączony do wykrywania potwierdzić na odgadnięcie hasła ataków. Jest ważne przechwycić nieudanych prób logowania. Przechwytywanie prób zarówno udane i nieudane logowania zapewnia dodatkowych korzyści podczas badania śledczej</p>|

## <a id="threat-detection"></a>Włączyć wykrywanie zagrożeń SQL Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Usługi SQL Azure |
| **Atrybuty**              | Wersja programu SQL — wersja 12 |
| **Odwołania**              | [Rozpoczynanie pracy z wykrywanie zagrożeń bazy danych SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Kroki** |<p>Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych. Zapewnia nową warstwę zabezpieczeń, co umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w nietypowych działań.</p><p>Użytkownicy mogą eksplorować podejrzane zdarzenia przy użyciu usługi Azure SQL Database Auditing w celu określenia, czy są one wynikiem próba uzyskania dostępu, naruszenia lub wykorzystać w bazie danych.</p><p>Wykrywanie zagrożeń upraszcza potencjalne zagrożenia do bazy danych bez konieczności ekspertów zabezpieczeń lub zarządzać zabezpieczeniami zaawansowanymi systemy monitorowania</p>|

## <a id="analytics"></a>Przeprowadź inspekcję dostępu do usługi Azure Storage za pomocą analityka magazynu Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND |
| **Odwołania**              | [Przy użyciu magazynu Analytics można monitorować typu autoryzacji](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Kroki** | <p>Dla każdego konta magazynu co można włączyć analityka magazynu Azure do przechowywania danych metryki i wykonywać rejestrowanie. Dzienniki analityka magazynu zawierają istotne informacje, takie jak metodę uwierzytelniania używaną przez osobę przy uzyskiwaniu dostępu do magazynu.</p><p>Może to być naprawdę pomocne, jeśli są ściśle ochrona dostępu do magazynu. Na przykład w magazynie obiektów Blob można ustawić opcję prywatne dla wszystkich kontenerów i implementować korzystania z usługi SAS w całej aplikacji. Następnie można sprawdzić dzienniki regularnie, aby sprawdzić, czy obiektów blob są dostępne przy użyciu kluczy konta magazynu, które mogą wskazywać naruszenia zabezpieczeń, lub jeśli obiekty BLOB są publiczne, ale nie powinny być one.</p>|

## <a id="sufficient-logging"></a>Implementowanie wystarczające rejestrowania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | .NET Framework |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroki** | <p>Brak dziennik inspekcji właściwe po zdarzenia zabezpieczeń mogą utrudniać śledczej działań. Windows Communication Foundation (WCF) oferuje możliwość rejestrowania prób uwierzytelnienia się pomyślnie i/lub nie powiodło się.</p><p>Rejestrowanie nieudanych prób uwierzytelnienia może zostać wyświetlone ostrzeżenie administratorów o potencjalnych ataków siłowych. Podobnie rejestrowanie zdarzeń pomyślnego uwierzytelnienia zapewniają dziennik inspekcji przydatne w przypadku złamania zabezpieczeń konta uprawnionego. Włączanie funkcji inspekcji zabezpieczeń usługi WCF w |

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
| **Zastosowanie technologii** | .NET Framework |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroki** | <p>Rozwinięte rozwiązanie z konfiguracją nie można wygenerować wyjątek po błędzie do zapisania w dzienniku inspekcji. Jeśli WCF z konfiguracją nie można zgłosić wyjątek, gdy nie można zapisać do dziennika inspekcji, program nie zostanie poinformowany o awarii i inspekcję zdarzeń zabezpieczenia krytyczne mogą nie być wykrywane.</p>|

### <a name="example"></a>Przykład
`<behavior/>` Element pliku konfiguracji usługi WCF poniżej nakazuje WCF nie powiadomiono aplikacji podczas WCF nie może zapisać do dziennika inspekcji.
````
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
````
Konfigurowanie usługi WCF do powiadamiania programu, jeśli to nie można zapisać do dziennika inspekcji. Program ma schemat alternatywnych powiadomień w celu alert organizację, która audytu nie są obsługiwane. 

## <a id="logging-web-api"></a>Upewnij się, że inspekcji i rejestrowania są wymuszane na interfejs API sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Włączanie inspekcji i rejestrowania na interfejsów API sieci Web. Dzienniki inspekcji należy przechwytywać kontekstu użytkownika. Zidentyfikuj wszystkie zdarzenia ważne i dziennika zdarzeń. Implementowanie funkcji centralnego rejestrowania |

## <a id="logging-field-gateway"></a>Upewnij się, czy odpowiedniej inspekcji i rejestrowania są wymuszane na pole bramy

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Pole IoT bramy | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Wiele urządzeń nawiązania połączenia bramy pola, upewnij się, że próby nawiązania połączenia i stanu uwierzytelniania (powodzenie lub niepowodzenie) dla poszczególnych urządzeń są rejestrowane i przechowywany w polu Brama.</p><p>Ponadto w przypadkach, gdy pole bramy jest obsługę Centrum IoT poświadczenia dla poszczególnych urządzeń, upewnij się, że inspekcja jest wykonywane, gdy te poświadczenia są pobierane. Tworzenie procesu okresowo przekazywania dzienników do platformy Azure IoT Hub i magazynowania dla długi okres przechowywania.</p> |

## <a id="logging-cloud-gateway"></a>Upewnij się, czy odpowiednie inspekcji i rejestrowania są wymuszane na bramy chmury

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama chmury IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wprowadzenie do monitorowania operacji centrum IoT](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Kroki** | <p>Projekt do gromadzenia i przechowywania danych inspekcji zebranych przez monitorowanie operacji centrum IoT. Włącz monitorowanie następujących kategorii:</p><ul><li>Operacje tożsamości urządzenia</li><li>Komunikacja urządzenia do chmury</li><li>Komunikacja z chmury do urządzenia</li><li>Połączenia</li><li>Przekazywania plików</li></ul>|