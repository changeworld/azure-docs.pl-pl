---
title: Azure autoryzacji — narzędzie Microsoft Threat modelowania — | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: b9ad3ceeb77a4adc2c47b262aa40a48c14423198
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019521"
---
# <a name="security-frame-authorization--mitigations"></a>Ramka zabezpieczeń: Autoryzacji | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Granic zaufania maszyny** | <ul><li>[Upewnij się, że odpowiednie listy ACL są skonfigurowane do ograniczania nieautoryzowanego dostępu do danych na urządzeniu](#acl-restricted-access)</li><li>[Upewnij się, że zawartość poufnej aplikacji specyficzne dla użytkownika jest przechowywana w katalogu profilu użytkownika](#sensitive-directory)</li><li>[Upewnij się, że wdrożone aplikacje są uruchamiane z najmniejszymi uprawnieniami](#deployed-privileges)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Wymusić kolejności kolejny krok podczas przetwarzania przepływów logika biznesowa](#sequential-logic)</li><li>[Implementowanie tempa ograniczania mechanizm zapobiegania — wyliczenie](#rate-enumeration)</li><li>[Sprawdź, czy odpowiednie autoryzacji jest już na miejscu i następuje reguły najniższych uprawnień](#principle-least-privilege)</li><li>[Logika i zasobów dostępu autoryzacji decyzje biznesowe nie powinny być oparte na przychodzące parametry żądania](#logic-request-parameters)</li><li>[Upewnij się, że zawartość i zasoby nie są wyliczalny ani dostępne za pośrednictwem wymuszone przeglądania](#enumerable-browsing)</li></ul> |
| **Baza danych** | <ul><li>[Upewnij się, że najmniej uprzywilejowane konta są używane do połączenia z serwerem bazy danych](#privileged-server)</li><li>[Implementowanie wiersza poziom zabezpieczeń zabezpieczenia na poziomie wiersza aby uniemożliwić dostęp do siebie nawzajem danych dzierżawcy](#rls-tenants)</li><li>[Rola administratora systemu powinien mieć tylko niezbędne użytkowników](#sysadmin-users)</li></ul> |
| **Brama chmury IoT** | <ul><li>[Połączyć się z bramą chmury przy użyciu najmniej uprzywilejowane tokenów](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Użyj uprawnień tylko do wysyłania klucza sygnatury dostępu Współdzielonego do generowania tokenów urządzenia](#sendonly-sas)</li><li>[Nie używaj tokenów dostępu, które udostępniają bezpośredni dostęp do Centrum zdarzeń](#access-tokens-hub)</li><li>[Połącz do Centrum zdarzeń za pomocą kluczy SAS, które ma minimalne uprawnienia wymagane](#sas-minimum-permissions)</li></ul> |
| **Dokumentów w usłudze Azure DB** | <ul><li>[Używaj tokenów zasobów do nawiązania połączenia DB rozwiązania Cosmos Azure, jeśli to możliwe](#resource-docdb)</li></ul> |
| **Granic zaufania Azure** | <ul><li>[Włącz szczegółowe zarządzanie dostępem do subskrypcji platformy Azure przy użyciu funkcji RBAC](#grained-rbac)</li></ul> |
| **Granic zaufania sieci szkieletowej usług** | <ul><li>[Ogranicz dostęp klienta do operacji klastra przy użyciu funkcji RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Wykonaj modelowania zabezpieczeń i używać zabezpieczeń na poziomie pola w przypadku, gdy wymagane](#modeling-field)</li></ul> |
| **Portal programu Dynamics CRM** | <ul><li>[Wykonaj modelowania zabezpieczeń w portalu kont z uwzględnieniem, że model zabezpieczeń dla portalu różni się od reszty CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Udziel uprawnień szczegółowych zakresu jednostek w magazynie tabel platformy Azure](#permission-entities)</li><li>[Włącz opartej na rolach kontroli dostępu (RBAC) na konto magazynu platformy Azure przy użyciu usługi Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Klientów urządzeń przenośnych** | <ul><li>[Implementowanie niejawne zdjęcia lub umieszczanie w katalogu głównym wykrywania](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Odwołanie słabe klasy w programie WCF](#weak-class-wcf)</li><li>[Formant autoryzacji wdrożenie usługi WCF](#wcf-authz)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Implementuje mechanizm właściwą autoryzację w interfejsie API sieci Web ASP.NET](#authz-aspnet)</li></ul> |
| **Urządzenia IoT** | <ul><li>[Sprawdzanie autoryzacji w urządzeniu Jeśli obsługuje różne akcje, które wymagają różne poziomy uprawnień](#device-permission)</li></ul> |
| **Pole IoT bramy** | <ul><li>[Wykonaj sprawdzanie autoryzacji w bramy pola, jeśli obsługuje różne akcje, które wymagają różne poziomy uprawnień](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Upewnij się, że odpowiednie listy ACL są skonfigurowane do ograniczania nieautoryzowanego dostępu do danych na urządzeniu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że odpowiednie listy ACL są skonfigurowane do ograniczania nieautoryzowanego dostępu do danych na urządzeniu|

## <a id="sensitive-directory"></a>Upewnij się, że zawartość poufnej aplikacji specyficzne dla użytkownika jest przechowywana w katalogu profilu użytkownika

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że zawartość poufnej aplikacji specyficzne dla użytkownika jest przechowywana w katalogu profilu użytkownika. To, aby uniemożliwić dostęp do siebie nawzajem danych przez wielu użytkowników maszyny.|

## <a id="deployed-privileges"></a>Upewnij się, że wdrożone aplikacje są uruchamiane z najmniejszymi uprawnieniami

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że z najmniejszymi uprawnieniami uruchomieniu wdrożonej aplikacji. |

## <a id="sequential-logic"></a>Wymusić kolejności kolejny krok podczas przetwarzania przepływów logika biznesowa

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Aby sprawdzić, czy ten etap został uruchomiony za pośrednictwem przez oryginalnego użytkownika, aby wymusić aplikacji tylko przetwarzać przepływy logiki biznesowej w kolejności kolejny krok z wszystkich kroków przetwarzanych w czasie człowieka realistyczne i przetwarza poza kolejnością, pominięte kroki, kroki przetworzonych od innego użytkownika lub zbyt szybko przesłanych transakcji.|

## <a id="rate-enumeration"></a>Implementowanie tempa ograniczania mechanizm zapobiegania — wyliczenie

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, czy losowe identyfikatory poufnych. Wdrażanie kontroli CAPTCHA na stronach anonimowy. Upewnij się, że błędów i wyjątków nie powinny ujawniać określonych danych|

## <a id="principle-least-privilege"></a>Sprawdź, czy odpowiednie autoryzacji jest już na miejscu i następuje reguły najniższych uprawnień

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Zasada oznacza nadanie tych uprawnień, które są niezbędne do użytkownicy pracować konta użytkownika. Na przykład użytkownik kopii zapasowej nie trzeba instalować oprogramowanie: w związku z tym kopii zapasowej użytkownik ma uprawnienia tylko do uruchamiania aplikacji kopii zapasowej i związanych z kopii zapasowej. Inne uprawnienia, takie jak instalowanie nowego oprogramowania są zablokowane. Zasada ma zastosowanie również do użytkownika komputerów osobistych, który zwykle działa w normalne konto użytkownika i otwiera konta uprzywilejowanego, chroniony hasłem (superuser) tylko, gdy sytuacji wymaga go całkowicie. </p><p>Tej zasady można również będą stosowane do aplikacji sieci web. Zamiast wyłącznie w zależności od metody uwierzytelniania opartej na rolach przy użyciu sesji, a nie chcemy uprawnienia są przypisane do użytkowników za pomocą uwierzytelniania na podstawie bazy danych systemu. Używamy nadal sesji, aby ustalić, czy użytkownik był zalogowany poprawnie, tylko teraz zamiast przypisywać tego użytkownika z określoną rolę, które firma Microsoft przypisać mu z uprawnieniami, aby sprawdzić, jakie akcje, jest on uprzywilejowane do wykonania w systemie. Duży pro tej metody jest również, zawsze, gdy użytkownik musi zostać przypisany mniej uprawnienia, zmiany zostaną zastosowane na bieżąco, ponieważ przypisanie nie zależy od sesji, które w przeciwnym wypadku musieli najpierw wygaśnie.</p>|

## <a id="logic-request-parameters"></a>Logika i zasobów dostępu autoryzacji decyzje biznesowe nie powinny być oparte na przychodzące parametry żądania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Zawsze, gdy są sprawdzanie, czy użytkownik jest ograniczony do sprawdzenia niektórych danych, ograniczenia dostępu powinny być przetworzone po stronie serwera. Nazwa użytkownika powinny być przechowywane w zmiennej sesji podczas logowania i powinien być używany do pobierania danych użytkownika z bazy danych |

### <a name="example"></a>Przykład
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Teraz atakujący możliwe nie można modyfikować i zmienić operacji aplikacji, ponieważ identyfikator dla pobierania danych jest obsługiwana po stronie serwera.

## <a id="enumerable-browsing"></a>Upewnij się, że zawartość i zasoby nie są wyliczalny ani dostępne za pośrednictwem wymuszone przeglądania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Poufne pliki statyczne i konfiguracji nie powinny być przechowywane w katalogu sieci web. Dla zawartości, nie muszą być publiczne, powinny być stosowane albo odpowiednie właściwości kontroli dostępu lub usuwanie zawartości.</p><p>Ponadto przeglądanie wymuszone zwykle w połączeniu z siłowych technik w celu zebrania informacji przez próbujących uzyskać dostęp dowolną liczbę adresów URL, jak to możliwe wyliczanie katalogów i plików na serwerze. Osoby atakujące mogą sprawdzić wszystkie odmiany często istniejących plików. Na przykład wyszukiwania plików hasła może obejmować pliki w tym psswd.txt, password.htm password.dat i innych zmian.</p><p>Aby temu zaradzić, możliwości wykrywania atakami siłowymi powinny być włączone.</p>|

## <a id="privileged-server"></a>Upewnij się, że najmniej uprzywilejowane konta są używane do połączenia z serwerem bazy danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Baza danych SQL uprawnienia hierarchii](https://msdn.microsoft.com/library/ms191465), [zabezpieczanych obiektów bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Najmniej uprzywilejowane konta powinien służyć do łączenia z bazą danych. Logowania aplikacji powinny być ograniczone w bazie danych i mają być wykonywane tylko wybrane procedury składowane. Logowania aplikacji powinien mieć nie bezpośredniego dostępu do tabel. |

## <a id="rls-tenants"></a>Implementowanie wiersza poziom zabezpieczeń zabezpieczenia na poziomie wiersza aby uniemożliwić dostęp do siebie nawzajem danych dzierżawcy

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Sql Azure, OnPrem |
| **Atrybuty**              | MsSQL2016 wersji — w wersji 12, wersja programu SQL — SQL |
| **Odwołania**              | [Zabezpieczenia na poziomie wiersza serwera SQL (kontrola dostępu)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroki** | <p>Zabezpieczenia na poziomie wiersza umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (np. członkostwa w grupie lub kontekstu wykonania).</p><p>Wiersz poziom zabezpieczeń kontrola dostępu ułatwia projektu i kodowania zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Możliwe jest na przykład zapewnienie, że pracownicy mają dostęp tylko do tych wierszy danych, które są odpowiednie do ich działu, lub ograniczenie dostępu do danych klienta tylko do danych odpowiednich dla firmy.</p><p>Logika ograniczenie dostępu jest znajduje się w warstwie bazy danych zamiast od danych w innej warstwie aplikacji. System bazy danych ma zastosowanie ograniczenia dostępu za każdym razem, gdy nastąpiła by dostęp do danych z dowolnej wersji. To sprawia, że system zabezpieczeń bardziej niezawodne i niezawodne zmniejszając powierzchni systemu zabezpieczeń.</p><p>|

Należy pamiętać, że zabezpieczenia na poziomie wiersza jako funkcja bazy danych poza pole ma zastosowanie tylko do od 2016 programu SQL Server i bazy danych Azure SQL. Jeśli funkcja zabezpieczenia na poziomie wiersza poza pole nie jest zaimplementowana, należy zapewnić, że dostęp do danych jest ograniczony przy użyciu widoków i procedur

## <a id="sysadmin-users"></a>Rola administratora systemu powinien mieć tylko niezbędne użytkowników

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Baza danych SQL uprawnienia hierarchii](https://msdn.microsoft.com/library/ms191465), [zabezpieczanych obiektów bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Członkowie stałej roli serwera SysAdmin powinny być bardzo ograniczoną i nigdy nie zawiera konta używane przez aplikacje.  Przejrzyj listę użytkowników w roli i usunąć wszystkie zbędne konta|

## <a id="cloud-least-privileged"></a>Połączyć się z bramą chmury przy użyciu najmniej uprzywilejowane tokenów

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama chmury IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy - Centrum IoT Azure |
| **Odwołania**              | [Kontrolę dostępu Centrum iot](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroki** | Podaj co najmniej poziomu uprawnień, aby różne składniki, które połączyć się z bramą chmury (Centrum IoT). Typowym przykładem jest — składnika zainicjowania/zarządzania urządzeniami używa registryread/zapisu, zdarzenie procesora (ASA) korzysta z połączenia usługi. Poszczególne urządzenia łączyć się przy użyciu poświadczeń urządzenia|

## <a id="sendonly-sas"></a>Użyj uprawnień tylko do wysyłania klucza sygnatury dostępu Współdzielonego do generowania tokenów urządzenia

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Event Hub | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Klucz sygnatury dostępu Współdzielonego jest używane do generowania tokenów poszczególnych urządzeń. Użyj klucza sygnatury dostępu Współdzielonego uprawnienia tylko do wysyłania podczas generowania tokenu urządzenia dla danego wydawcy|

## <a id="access-tokens-hub"></a>Nie używaj tokenów dostępu, które udostępniają bezpośredni dostęp do Centrum zdarzeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Event Hub | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Token, który daje bezpośredni dostęp do Centrum zdarzeń nie powinny być podawane na urządzeniu. Za pomocą tokenu najniższych uprawnieniach dla urządzenia, które umożliwia dostęp tylko do wydawcy może pomóc w identyfikacji i wyeliminować go, jeśli okaże się nieautoryzowanego lub naruszony urządzenia.|

## <a id="sas-minimum-permissions"></a>Połącz do Centrum zdarzeń za pomocą kluczy SAS, które ma minimalne uprawnienia wymagane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Event Hub | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Podaj co najmniej poziomu uprawnień do różnych aplikacji zaplecza, które nawiązać połączenia z Centrum zdarzeń. Generowanie oddzielnych klucza sygnatury dostępu Współdzielonego dla każdej aplikacji zaplecza i zapewniają tylko wymagane uprawnienia — Wyślij i Odbierz lub Zarządzaj do nich.|

## <a id="resource-docdb"></a>Używaj tokenów zasobów do nawiązania połączenia rozwiązania Cosmos bazy danych, jeśli to możliwe

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dokumentów w usłudze Azure DB | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Token zasobu jest skojarzony z zasobem uprawnienia bazy danych Azure rozwiązania Cosmos i przechwytywanie relacji między użytkownikiem bazy danych i uprawnień, które użytkownik ma dla określonego zasobu aplikacji bazy danych rozwiązania Cosmos Azure (np. kolekcji i dokumentów). Aby uzyskać dostęp do bazy danych rozwiązania Cosmos platformy Azure, jeśli klient nie może być zaufany za pomocą postępowania z kluczami głównego lub w trybie tylko do odczytu — podobnie jak aplikację użytkownika klienta przenośnego lub stacjonarnego zawsze używać token zasobu. Użyj klucza głównego lub tylko do odczytu klucze z wewnętrznej bazy danych aplikacji, które można bezpiecznie przechowywać klucze.|

## <a id="grained-rbac"></a>Włącz szczegółowe zarządzanie dostępem do subskrypcji platformy Azure przy użyciu funkcji RBAC

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania Azure | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroki** | Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań.|

## <a id="cluster-rbac"></a>Ogranicz dostęp klienta do operacji klastra przy użyciu funkcji RBAC

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania sieci szkieletowej usług | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Odwołania**              | [Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroki** | <p>Sieć szkieletowa usług Azure obsługuje dwa typy kontroli różny dostęp dla klientów, które są podłączone do klastra usługi sieć szkieletowa: administratora i użytkownika. Kontrola dostępu umożliwia administratora klastrów, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników, co klaster bardziej bezpieczne.</p><p>Administratorzy mają pełny dostęp do funkcji zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko do odczytu możliwości zarządzania (na przykład możliwość wykonywania kwerend) i możliwość usuwania aplikacji i usług.</p><p>Role dwóch klienta (administratora i klient) określa się podczas tworzenia klastra, podając oddzielne certyfikaty dla każdego.</p>|

## <a id="modeling-field"></a>Wykonaj modelowania zabezpieczeń i używać zabezpieczeń na poziomie pola w przypadku, gdy wymagane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj modelowania zabezpieczeń i używać zabezpieczeń na poziomie pola w przypadku, gdy wymagane|

## <a id="portal-security"></a>Wykonaj modelowania zabezpieczeń w portalu kont z uwzględnieniem, że model zabezpieczeń dla portalu różni się od reszty CRM

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM Portal | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj modelowania zabezpieczeń w portalu kont z uwzględnieniem, że model zabezpieczeń dla portalu różni się od reszty CRM|

## <a id="permission-entities"></a>Udziel uprawnień szczegółowych zakresu jednostek w magazynie tabel platformy Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | StorageType - tabeli |
| **Odwołania**              | [Jak delegować dostęp do obiektów na koncie magazynu platformy Azure przy użyciu sygnatury dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroki** | Magazyn tabel Azure niektórych scenariuszy biznesowych, może być konieczne do przechowywania poufnych danych przeznaczoną do różnych stron. Np. poufnych danych dotyczących różnych krajów. W takich przypadkach sygnatury SAS można konstruować określając zakresami kluczy partycji i wiersza, tak, aby użytkownik mógł korzystać z danych specyficznych dla danego kraju.| 

## <a id="rbac-azure-manager"></a>Włącz opartej na rolach kontroli dostępu (RBAC) na konto magazynu platformy Azure przy użyciu usługi Azure Resource Manager

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Jak zabezpieczyć konto magazynu z kontroli dostępu opartej na rolach (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroki** | <p>Podczas tworzenia nowego konta magazynu jest wybierz model wdrożenia klasycznego lub usługi Azure Resource Manager. Klasycznego modelu tworzenie zasobów na platformie Azure umożliwia tylko all-or-nothing dostępu do subskrypcji, a więc konta magazynu.</p><p>Model usługi Azure Resource Manager należy umieścić konta magazynu w zasobów grupy i kontroli dostępu do tego konta określonego magazynu przy użyciu usługi Azure Active Directory płaszczyzny zarządzania. Na przykład można udzielić określonym użytkownikom możliwość dostępu klucze konta magazynu, podczas gdy inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie ma dostępu do kluczy konta magazynu.</p>|

## <a id="rooting-detection"></a>Implementowanie niejawne zdjęcia lub umieszczanie w katalogu głównym wykrywania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klientów urządzeń przenośnych | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Aplikacja powinna chronić własnych danych konfiguracji i użytkownika w przypadku, jeśli telefon jest ścieżką do katalogu głównego lub ma zdjęte zabezpieczenia systemu. Umieszczanie katalogu głównym/zdjęto zabezpieczeń fundamentalne oznacza nieautoryzowanym dostępem, normalnych użytkowników, którzy nie są na ich własnych telefonów. W związku z tym aplikacji powinien mieć logikę wykrywania niejawne podczas uruchamiania aplikacji, aby wykryć, czy telefonu został odblokowany dostęp.</p><p>Logika wykrywania można po prostu uzyskiwać dostęp do plików, które zwykle tylko główny użytkownik może uzyskać dostęp, na przykład:</p><ul><li>/system/app/Superuser.apk</li><li>/ sbin/su</li><li>/System/bin/su</li><li>/System/xbin/su</li><li>/Data/Local/xbin/su</li><li>/Data/local/bin/su</li><li>/System/SD/xbin/su</li><li>/System/bin/FailSafe/su</li><li>/Data/Local/su</li></ul><p>Aplikacja może uzyskać dostępu do żadnego z tych plików, oznacza, że aplikacja jest uruchomiona jako użytkownik root.</p>|

## <a id="weak-class-wcf"></a>Odwołanie słabe klasy w programie WCF

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroki** | <p>System używa Odwołanie słabe klasy, które może umożliwić atakującemu wykonanie nieautoryzowanego kodu. Program odwołuje się do klasy zdefiniowanej przez użytkownika, która nie jest jednoznacznie zidentyfikować. Podczas ładowania tej klasy lekko zidentyfikowanych .NET modułu ładującego typu CLR wyszukuje klasy w następujących lokalizacjach w podanej kolejności:</p><ol><li>Jeśli zestawu typu jest znany, moduł ładujący wyszukuje plik konfiguracji przekierowania lokalizacjach, GAC, bieżącego zestawu przy użyciu informacji o konfiguracji i podstawowego katalogu aplikacji</li><li>Jeśli zestaw jest nieznany, moduł ładujący wyszukuje bieżącego zestawu, mscorlib i lokalizację zwrócony przez program obsługi zdarzeń TypeResolve</li><li>Ta kolejność wyszukiwania CLR może być modyfikowany z punkty zaczepienia, takich jak mechanizm przekazywania typu i zdarzenia AppDomain.TypeResolve</li></ol><p>Jeśli osoba atakująca wykorzystuje kolejność wyszukiwania CLR, tworząc klasę alternatywnych o takiej samej nazwie i umieszczenie go w lokalizacji alternatywnej czy CLR załaduje najpierw CLR zostanie przypadkowo wykonywania kodu dostarczone przez osobę atakującą</p>|

### <a name="example"></a>Przykład
`<behaviorExtensions/>` WCF, aby dodać klasę niestandardowe zachowanie określonego rozszerzenia WCF powoduje, że element Poniższy plik konfiguracji usługi WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Przy użyciu w pełni kwalifikowane nazwy (silne) unikatowo identyfikuje typ i jeszcze bardziej podkreśla zabezpieczenia systemu. Podczas rejestrowania typów w pliku machine.config i app.config, należy użyć zestawu w pełni kwalifikowanej nazwy.

### <a name="example"></a>Przykład
`<behaviorExtensions/>` WCF, aby dodać klasę silnie odwołanie do niestandardowych zachowanie określonego rozszerzenia WCF powoduje, że element Poniższy plik konfiguracji usługi WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>Formant autoryzacji wdrożenie usługi WCF

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroki** | <p>Ta usługa korzysta z kontrolki autoryzacji. Kiedy klient wywołuje określonej usługi WCF, WCF zawiera różnych systemów autoryzacji, które pozwalają sprawdzić, czy element wywołujący ma uprawnienia do wykonania metody usługi na serwerze. Jeśli formanty autoryzacji nie są włączone dla usługi WCF, uwierzytelniony użytkownik może osiągnąć podwyższenie poziomu uprawnień.</p>|

### <a name="example"></a>Przykład
Następująca konfiguracja powoduje, że usługi WCF do sprawdza poziom autoryzacji klienta podczas wykonywania usługi:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Aby sprawdzić, czy element wywołujący metody usługi jest autoryzowany, aby to zrobić, należy użyć schemat autoryzacji usługi. Usługi WCF dostępne są dwa tryby i pozwala na określenie schematu niestandardowej autoryzacji. Tryb UseWindowsGroups korzysta role systemu Windows, a użytkownicy i tryb UseAspNetRoles dostawcy ról ASP.NET, takich jak SQL Server do uwierzytelniania.

### <a name="example"></a>Przykład
Następująca konfiguracja powoduje, że usługi WCF, aby upewnić się, czy klient jest częścią grupy Administratorzy przed wykonaniem Dodaj usługi:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Usługa następnie jest zadeklarowany jako następujących czynności:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementuje mechanizm właściwą autoryzację w interfejsie API sieci Web ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Generic, MVC5 |
| **Atrybuty**              | Brak dostawcy tożsamości dostawca — usługi AD FS, tożsamość — usługi Azure AD |
| **Odwołania**              | [Uwierzytelnianie i autoryzacja w składniku ASP.NET Web API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroki** | <p>Informacje o rolach dla użytkowników aplikacji mogą być uzyskane z usługi Azure AD lub oświadczenia usług AD FS, jeśli aplikacja korzysta z ich jako dostawca tożsamości lub sama aplikacja może pod warunkiem, że. W żadnym z tych przypadkach implementacji niestandardowych autoryzacji należy zweryfikować informacje o rolach użytkownika.</p><p>Informacje o rolach dla użytkowników aplikacji mogą być uzyskane z usługi Azure AD lub oświadczenia usług AD FS, jeśli aplikacja korzysta z ich jako dostawca tożsamości lub sama aplikacja może pod warunkiem, że. W żadnym z tych przypadkach implementacji niestandardowych autoryzacji należy zweryfikować informacje o rolach użytkownika.</p>

### <a name="example"></a>Przykład
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Kontrolerów i metod akcji, które trzeba chronione powinny być dekorowane za powyżej atrybutu.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Sprawdzanie autoryzacji w urządzeniu Jeśli obsługuje różne akcje, które wymagają różne poziomy uprawnień

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Urządzenia należy zezwolić na obiekt wywołujący, aby sprawdzić, czy wywołującego ma wymaganych uprawnień do wykonania żądanej akcji. Dla pozwala na przykład załóżmy, że urządzenie jest blokady inteligentnej drzwi, które można monitorować z chmury, a także udostępnia funkcje, takie jak zdalne Blokowanie drzwi.</p><p>Blokady inteligentnej drzwi udostępnia funkcje odblokowywaniem tylko wtedy, gdy ktoś fizycznie wyposażony w pobliżu drzwi karty. W takim przypadku wykonania polecenia zdalnego i formantu ma się odbywać w taki sposób, że nie ma żadnej funkcji, aby odblokować drzwi jako brama chmury nie ma uprawnień do wysyłania polecenia, aby odblokować drzwi.</p>|

## <a id="field-permission"></a>Wykonaj sprawdzanie autoryzacji w bramy pola, jeśli obsługuje różne akcje, które wymagają różne poziomy uprawnień

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Pole IoT bramy | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Pola bramy należy zezwolić obiekt wywołujący, aby sprawdzić, czy wywołującego ma wymaganych uprawnień do wykonania żądanej akcji. Np. powinna być różne uprawnienia dla użytkownika interfejsu/interfejsem API administratora używane do konfigurowania urządzeń v/s bramy pola, które się z nim połączyć.|
