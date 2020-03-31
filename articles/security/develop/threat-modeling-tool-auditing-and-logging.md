---
title: Inspekcja i rejestrowanie — narzędzie Microsoft Threat Modeling Tool — Azure | Dokumenty firmy Microsoft
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728410"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Ramka zabezpieczeń: Inspekcja i rejestrowanie | Czynniki 

| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identyfikowanie poufnych jednostek w rozwiązaniu i wdrażanie inspekcji zmian](#sensitive-entities)</li></ul> |
| **Aplikacja internetowa** | <ul><li>[Upewnij się, że inspekcja i rejestrowanie jest wymuszane w aplikacji](#auditing)</li><li>[Upewnij się, że rotacja i separacja dziennika są na miejscu](#log-rotation)</li><li>[Upewnij się, że aplikacja nie rejestruje poufnych danych użytkownika](#log-sensitive-data)</li><li>[Upewnij się, że pliki inspekcji i dzienników mają ograniczony dostęp](#log-restricted-access)</li><li>[Upewnij się, że zdarzenia zarządzania użytkownikami są rejestrowane](#user-management)</li><li>[Upewnij się, że system ma wbudowane mechanizmy obronne przed niewłaściwym użyciem](#inbuilt-defenses)</li><li>[Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](#diagnostics-logging)</li></ul> |
| **baza danych** | <ul><li>[Upewnij się, że inspekcja logowania jest włączona w programie SQL Server](#identify-sensitive-entities)</li><li>[Włącz wykrywanie zagrożeń w usłudze Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Za pomocą usługi Azure Storage Analytics inspekcja dostępu do usługi Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Zaimplementuj wystarczające rejestrowanie](#sufficient-logging)</li><li>[Wdrożenie wystarczającej obsługi błędów inspekcji](#audit-failure-handling)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że inspekcja i rejestrowanie jest wymuszane w interfejsie API sieci Web](#logging-web-api)</li></ul> |
| **Brama pola IoT** | <ul><li>[Upewnij się, że odpowiednie inspekcje i rejestrowanie są wymuszane w bramie polej](#logging-field-gateway)</li></ul> |
| **Brama w chmurze IoT** | <ul><li>[Upewnij się, że w usłudze Cloud Gateway jest wymuszana odpowiednia inspekcja i rejestrowanie](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identyfikowanie poufnych jednostek w rozwiązaniu i wdrażanie inspekcji zmian

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | Identyfikowanie jednostek w rozwiązaniu zawierających poufne dane i wdrażanie inspekcji zmian w tych jednostkach i polach |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Upewnij się, że inspekcja i rejestrowanie jest wymuszane w aplikacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | Włącz inspekcję i rejestrowanie wszystkich składników. Dzienniki inspekcji powinny przechwytywać kontekst użytkownika. Zidentyfikuj wszystkie ważne zdarzenia i zarejestruj te zdarzenia. Wdrażanie scentralizowanego rejestrowania |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Upewnij się, że rotacja i separacja dziennika są na miejscu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Rotacja dzienników jest zautomatyzowanym procesem używanym w administrowaniu systemem, w którym zarchiwizowane są przestarzałe pliki dziennika. Serwery, które uruchamiają duże aplikacje często rejestrują każde żądanie: w obliczu nieporęcznych dzienników rotacja dzienników jest sposobem na ograniczenie całkowitego rozmiaru dzienników, a jednocześnie umożliwia analizę ostatnich zdarzeń. </p><p>Separacja dzienników w zasadzie oznacza, że musisz przechowywać pliki dziennika na innej partycji, ponieważ w przypadku, gdy system operacyjny / aplikacja jest uruchomiona, aby zapobiec atakowi typu "odmowa usługi" lub obniżeniu jej wydajności przez aplikację</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Upewnij się, że aplikacja nie rejestruje poufnych danych użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Sprawdź, czy nie rejestrujesz żadnych poufnych danych przesyłanych przez użytkownika do witryny. Sprawdź, czy zamierzone rejestrowanie, a także skutki uboczne spowodowane problemami z projektem. Przykłady poufnych danych obejmują:</p><ul><li>Poświadczenia użytkownika</li><li>Numer ubezpieczenia społecznego lub inne informacje identyfikujące</li><li>Numery kart kredytowych lub inne informacje finansowe</li><li>Informacje o stanie zdrowia</li><li>Klucze prywatne lub inne dane, które mogą być używane do odszyfrowywania zaszyfrowanych informacji</li><li>Informacje o systemie lub aplikacji, które mogą być wykorzystane do skuteczniejszego ataku na aplikację</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Upewnij się, że pliki inspekcji i dzienników mają ograniczony dostęp

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Sprawdź, czy prawa dostępu do plików dziennika są odpowiednio ustawione. Konta aplikacji powinny mieć dostęp tylko do zapisu, a operatorzy i personel pomocy technicznej powinni mieć dostęp tylko do odczytu w razie potrzeby.</p><p>Konta administratorów są jedynymi kontami, do których dostęp powinien mieć pełny dostęp. Sprawdź acl systemu Windows na plikach dziennika, aby upewnić się, że są one prawidłowo ograniczone:</p><ul><li>Konta aplikacji powinny mieć dostęp tylko do zapisu</li><li>Operatorzy i personel pomocniczy powinni mieć dostęp tylko do odczytu, jeśli zajmą się potrzebami</li><li>Administratorzy są jedynymi kontami, które powinny mieć pełny dostęp</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Upewnij się, że zdarzenia zarządzania użytkownikami są rejestrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Upewnij się, że aplikacja monitoruje zdarzenia zarządzania użytkownikami, takie jak pomyślne i nieudane logowania użytkowników, resetowanie hasła, zmiany hasła, blokada konta, rejestracja użytkownika. Pomaga to wykryć i reagować na potencjalnie podejrzane zachowanie. Umożliwia również zbieranie danych operacyjnych; na przykład, aby śledzić, kto uzyskuje dostęp do aplikacji</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Upewnij się, że system ma wbudowane mechanizmy obronne przed niewłaściwym użyciem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Formanty powinny być w miejscu, które zgłaszają wyjątek zabezpieczeń w przypadku niewłaściwego użycia aplikacji. Np. Jeśli sprawdzanie poprawności danych wejściowych jest w miejscu i osoba atakująca próbuje wstrzyknąć złośliwy kod, który nie pasuje do wyrażenia regularnego, można zrzucić wyjątek zabezpieczeń, który może wskazywać na niewłaściwe użycie systemu</p><p>Na przykład zaleca się rejestrowanie wyjątków zabezpieczeń i podejmowanych działań w następujących kwestiach:</p><ul><li>Walidacja danych wejściowych</li><li>Naruszenia CSRF</li><li>Brutalna siła (górny limit liczby żądań na użytkownika na zasób)</li><li>Naruszenia przekazywania plików</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ środowiska — platforma Azure |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Platforma Azure udostępnia wbudowaną diagnostykę ułatwiając debugowanie aplikacji sieci web usługi App Service. Dotyczy to również aplikacji interfejsu API i aplikacji mobilnych. Aplikacje sieci Web usługi App Service zapewniają funkcje diagnostyczne do rejestrowania informacji z serwera sieci web i aplikacji sieci web.</p><p>Są one logicznie podzielone na diagnostykę serwera www i diagnostykę aplikacji</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Upewnij się, że inspekcja logowania jest włączona w programie SQL Server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Konfigurowanie inspekcji logowania](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Kroki** | <p>Inspekcja logowania serwera bazy danych musi być włączona w celu wykrywania/potwierdzania ataków zgadujących hasła. Ważne jest, aby przechwycić nieudane próby logowania. Przechwytywanie zarówno udanych, jak i nieudanych prób logowania zapewnia dodatkowe korzyści podczas dochodzeń kryminalistycznych</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Włącz wykrywanie zagrożeń w usłudze Azure SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Usługi SQL Azure |
| **Atrybuty**              | Wersja SQL - V12 |
| **Odwołania**              | [Wprowadzenie do wykrywania zagrożeń bazy danych SQL](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Kroki** |<p>Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa dla bazy danych. Zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie, ponieważ występują one, zapewniając alerty zabezpieczeń dotyczące nietypowych działań.</p><p>Użytkownicy mogą eksplorować podejrzane zdarzenia przy użyciu usługi Azure SQL Database Auditing, aby ustalić, czy są one wynikiem próby uzyskania dostępu, naruszenia lub wykorzystania danych w bazie danych.</p><p>Wykrywanie zagrożeń ułatwia reagowanie na potencjalne zagrożenia dla bazy danych bez konieczności bycia ekspertem w dziedzinie bezpieczeństwa lub zarządzania zaawansowanymi systemami monitorowania bezpieczeństwa</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Za pomocą usługi Azure Storage Analytics inspekcja dostępu do usługi Azure Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy |
| **Odwołania**              | [Używanie analizy magazynu do monitorowania typu autoryzacji](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Kroki** | <p>Dla każdego konta magazynu można włączyć usługę Azure Storage Analytics do wykonywania rejestrowania i przechowywania danych metryk. Dzienniki analizy magazynu zawierają ważne informacje, takie jak metoda uwierzytelniania używana przez kogoś podczas uzyskiwania dostępu do magazynu.</p><p>Może to być bardzo pomocne, jeśli ściśle chronisz dostęp do pamięci masowej. Na przykład w magazynie obiektów Blob można ustawić wszystkie kontenery na prywatne i zaimplementować korzystanie z usługi sygnatury dostępu Współdzielonego w aplikacjach. Następnie można regularnie sprawdzać dzienniki, aby sprawdzić, czy obiekty BLOB są dostępne przy użyciu kluczy konta magazynu, co może wskazywać na naruszenie zabezpieczeń lub jeśli obiekty blob są publiczne, ale nie powinny być.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Zaimplementuj wystarczające rejestrowanie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroki** | <p>Brak odpowiedniej ścieżki audytu po incydencie bezpieczeństwa może utrudniać działania kryminalistyczne. Windows Communication Foundation (WCF) oferuje możliwość rejestrowania udanych i/lub nieudanych prób uwierzytelniania.</p><p>Rejestrowanie nieudanych prób uwierzytelniania może ostrzec administratorów o potencjalnych atakach siłowych. Podobnie rejestrowanie zdarzeń pomyślnego uwierzytelniania może zapewnić użyteczną ścieżkę inspekcji, gdy konto jest zagrożone. Włącz funkcję inspekcji zabezpieczeń usługi WCF |

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładową konfigurację z włączoną inspekcją
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

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Wdrożenie wystarczającej obsługi błędów inspekcji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroki** | <p>Opracowane rozwiązanie jest skonfigurowane do nie generowania wyjątku, gdy nie można zapisać w dzienniku inspekcji. Jeśli WCF jest skonfigurowany, aby nie zgłaszać wyjątek, gdy nie można zapisać w dzienniku inspekcji, program nie zostanie powiadomiony o awarii i inspekcji krytycznych zdarzeń zabezpieczeń może nie wystąpić.</p>|

### <a name="example"></a>Przykład
Element `<behavior/>` pliku konfiguracyjnego WCF poniżej nakazuje WCF nie powiadamiać aplikacji, gdy WCF nie może zapisać do dziennika inspekcji.
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
Skonfiguruj WCF, aby powiadamiał program, gdy nie można zapisać w dzienniku inspekcji. Program powinien mieć alternatywny schemat powiadomień w celu ostrzeżenia organizacji, że ścieżki inspekcji nie są obsługiwane. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Upewnij się, że inspekcja i rejestrowanie jest wymuszane w interfejsie API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Włącz inspekcję i rejestrowanie w interfejsach API sieci Web. Dzienniki inspekcji powinny przechwytywać kontekst użytkownika. Zidentyfikuj wszystkie ważne zdarzenia i zarejestruj te zdarzenia. Wdrażanie scentralizowanego rejestrowania |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Upewnij się, że odpowiednie inspekcje i rejestrowanie są wymuszane w bramie polej

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Gdy wiele urządzeń łączy się z bramą pól, upewnij się, że próby połączenia i stan uwierzytelniania (powodzenie lub niepowodzenie) dla poszczególnych urządzeń są rejestrowane i obsługiwane w bramie pola.</p><p>Ponadto w przypadkach, gdy brama pola jest obsługa poświadczeń Usługi IoT Hub dla poszczególnych urządzeń, upewnij się, że inspekcja jest wykonywana podczas pobierania tych poświadczeń. Opracowanie procesu okresowego przekazywania dzienników do usługi Azure IoT Hub/storage w celu długoterminowego przechowywania.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Upewnij się, że w usłudze Cloud Gateway jest wymuszana odpowiednia inspekcja i rejestrowanie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama w chmurze IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wprowadzenie do monitorowania operacji usługi IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Kroki** | <p>Projektowanie do zbierania i przechowywania danych inspekcji zebranych za pośrednictwem monitorowania operacji usługi IoT Hub. Włącz następujące kategorie monitorowania:</p><ul><li>Operacje tożsamości urządzenia</li><li>Komunikacja między urządzeniami a chmurami</li><li>Komunikacja między chmurami a urządzeniami</li><li>Połączenia</li><li>Operacje przekazywania plików</li></ul>|