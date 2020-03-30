---
title: Autoryzacja — narzędzie microsoft do modelowania zagrożeń — Azure | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728383"
---
# <a name="security-frame-authorization--mitigations"></a>Rama zabezpieczeń: Autoryzacja | Czynniki 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że prawidłowe listy ACL są skonfigurowane w celu ograniczenia nieautoryzowanego dostępu do danych na urządzeniu](#acl-restricted-access)</li><li>[Upewnij się, że zawartość aplikacji dla użytkownika jest przechowywana w katalogu profilu użytkownika](#sensitive-directory)</li><li>[Upewnij się, że wdrożone aplikacje są uruchamiane z najmniejszymi uprawnieniami](#deployed-privileges)</li></ul> |
| **Aplikacja internetowa** | <ul><li>[Wymuszanie kolejności kroków sekwencyjnych podczas przetwarzania przepływów logiki biznesowej](#sequential-logic)</li><li>[Wdrożenie mechanizmu ograniczania szybkości, aby zapobiec wyliczaniu](#rate-enumeration)</li><li>[Zapewnienie prawidłowego upoważnienia i stosowania zasady najmniejszych uprawnień](#principle-least-privilege)</li><li>[Logika biznesowa i decyzje dotyczące autoryzacji dostępu do zasobów nie powinny opierać się na parametrach żądania przychodzącego](#logic-request-parameters)</li><li>[Upewnij się, że zawartość i zasoby nie są wyliczenia lub dostępne za pomocą zdecydowanego przeglądania](#enumerable-browsing)</li></ul> |
| **baza danych** | <ul><li>[Upewnij się, że konta o najniższych uprawnieniach są używane do łączenia się z serwerem bazy danych](#privileged-server)</li><li>[Implementowanie protokołu RLS zabezpieczeń na poziomie wiersza, aby uniemożliwić dzierżawcom dostęp do wzajemnych danych](#rls-tenants)</li><li>[Rola Sysadmin powinna mieć tylko prawidłowych niezbędnych użytkowników](#sysadmin-users)</li></ul> |
| **Brama w chmurze IoT** | <ul><li>[Łączenie się z bramą w chmurze przy użyciu tokenów o najniższych uprawnieniach](#cloud-least-privileged)</li></ul> |
| **Centrum zdarzeń Azure** | <ul><li>[Używanie klucza sas uprawnień tylko do wysyłania do generowania tokenów urządzeń](#sendonly-sas)</li><li>[Nie używaj tokenów dostępu, które zapewniają bezpośredni dostęp do Centrum zdarzeń](#access-tokens-hub)</li><li>[Łączenie się z Centrum zdarzeń przy użyciu kluczy Sygnatury dostępu Współdzielonego, które mają wymagane minimalne uprawnienia](#sas-minimum-permissions)</li></ul> |
| **Baza danych dokumentów platformy Azure** | <ul><li>[W miarę możliwości łączenie się z usługą Azure Cosmos DB umożliwia łączenie się z usługą Azure Cosmos DB](#resource-docdb)</li></ul> |
| **Granica zaufania platformy Azure** | <ul><li>[Włącz zarządzanie dostępem szczegółowym do subskrypcji platformy Azure przy użyciu funkcji RBAC](#grained-rbac)</li></ul> |
| **Granica zaufania sieci usług** | <ul><li>[Ograniczanie dostępu klienta do operacji klastra przy użyciu funkcji RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[W razie potrzeby przeprowadzaj modelowanie zabezpieczeń i korzystaj z zabezpieczeń na poziomie pola](#modeling-field)</li></ul> |
| **Dynamiczny portal CRM** | <ul><li>[Wykonaj modelowanie zabezpieczeń kont portalu, mając na uwadze, że model zabezpieczeń portalu różni się od reszty crm](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Udzielanie uprawnień szczegółowej dla zakresu jednostek w usłudze Azure Table Storage](#permission-entities)</li><li>[Włącz kontrolę dostępu opartą na rolach (RBAC) na koncie magazynu platformy Azure przy użyciu usługi Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Klient mobilny** | <ul><li>[Implementowanie niejawnego wykrywania jailbreak lub rootingu](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Słabe odniesienie do klasy w WCF](#weak-class-wcf)</li><li>[Kontrola autoryzacji implementacji WCF](#wcf-authz)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Implementowanie odpowiedniego mechanizmu autoryzacji w ASP.NET interfejsie API sieci Web](#authz-aspnet)</li></ul> |
| **Urządzenie IoT** | <ul><li>[Przeprowadzaj kontrole autoryzacji w urządzeniu, jeśli obsługuje różne akcje, które wymagają różnych poziomów uprawnień](#device-permission)</li></ul> |
| **Brama pola IoT** | <ul><li>[Przeprowadzaj kontrole autoryzacji w bramie polowej, jeśli obsługuje ona różne akcje wymagające różnych poziomów uprawnień](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Upewnij się, że prawidłowe listy ACL są skonfigurowane w celu ograniczenia nieautoryzowanego dostępu do danych na urządzeniu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że prawidłowe listy ACL są skonfigurowane w celu ograniczenia nieautoryzowanego dostępu do danych na urządzeniu|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Upewnij się, że zawartość aplikacji dla użytkownika jest przechowywana w katalogu profilu użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że zawartość aplikacji specyficzne dla użytkownika są przechowywane w katalogu profilu użytkownika. Ma to na celu uniemożliwienie wielu użytkownikom komputera dostępu do danych innych.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Upewnij się, że wdrożone aplikacje są uruchamiane z najmniejszymi uprawnieniami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że wdrożona aplikacja jest uruchamiana z najmniejszymi uprawnieniami. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Wymuszanie kolejności kroków sekwencyjnych podczas przetwarzania przepływów logiki biznesowej

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Aby sprawdzić, czy ten etap został przeprowadzony przez oryginalnego użytkownika, który chce wymusić aplikację, aby przetwarzać tylko przepływy logiki biznesowej w kolejności sekwencyjnej kroków, a wszystkie kroki są przetwarzane w realistycznym czasie ludzkim, a nie proces poza kolejnością, pominięte kroki , przetworzone kroki od innego użytkownika lub zbyt szybko przesłane transakcje.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Wdrożenie mechanizmu ograniczania szybkości, aby zapobiec wyliczaniu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że poufne identyfikatory są losowe. Implementowanie kontroli CAPTCHA na stronach anonimowych. Upewnij się, że błąd i wyjątek nie powinny ujawniać konkretnych danych|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Zapewnienie prawidłowego upoważnienia i stosowania zasady najmniejszych uprawnień

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Zasada oznacza nadanie kontu użytkownika tylko tych uprawnień, które są niezbędne dla pracy użytkowników. Na przykład użytkownik kopii zapasowej nie musi instalować oprogramowania: w związku z tym użytkownik kopii zapasowej ma prawa tylko do uruchamiania kopii zapasowych i aplikacji związanych z tworzeniem kopii zapasowych. Wszelkie inne uprawnienia, takie jak instalowanie nowego oprogramowania, są blokowane. Zasada ta ma zastosowanie również do użytkownika komputera osobistego, który zwykle pracuje na zwykłym koncie użytkownika i otwiera uprzywilejowane konto chronione hasłem (czyli superużytnika) tylko wtedy, gdy sytuacja tego absolutnie wymaga. </p><p>Zasada ta może być również stosowana do aplikacji internetowych. Zamiast wyłącznie w zależności od metod uwierzytelniania opartych na rolach przy użyciu sesji, chcemy raczej przypisać uprawnienia użytkownikom za pomocą systemu uwierzytelniania opartego na bazie danych. Nadal używamy sesji w celu określenia, czy użytkownik był zalogowany poprawnie, dopiero teraz zamiast przypisywać tego użytkownika z określoną rolą, przypisujemy mu uprawnienia do sprawdzania, które akcje ma uprzywilejowane do wykonania w systemie. Również duży pro tej metody jest, gdy użytkownik musi być przypisany mniej uprawnień zmiany zostaną zastosowane w locie, ponieważ przypisanie nie zależy od sesji, która w przeciwnym razie musiał wygasnąć pierwszy.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Logika biznesowa i decyzje dotyczące autoryzacji dostępu do zasobów nie powinny opierać się na parametrach żądania przychodzącego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Za każdym razem, gdy sprawdzasz, czy użytkownik jest ograniczony do przeglądania niektórych danych, ograniczenia dostępu powinny być przetwarzane po stronie serwera. Identyfikator użytkownika powinien być przechowywany wewnątrz zmiennej sesji przy logowaniu i powinien być używany do pobierania danych użytkownika z bazy danych |

### <a name="example"></a>Przykład
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Teraz możliwe osoby atakujące nie mogą manipulować i zmieniać operacji aplikacji, ponieważ identyfikator pobierania danych jest obsługiwany po stronie serwera.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Upewnij się, że zawartość i zasoby nie są wyliczenia lub dostępne za pomocą zdecydowanego przeglądania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Poufne pliki statyczne i konfiguracyjne nie powinny być przechowywane w katalogu głównym sieci Web. W przypadku treści, które nie muszą być publiczne, należy zastosować odpowiednie kontrole dostępu lub usunąć samą zawartość.</p><p>Ponadto silne przeglądanie jest zwykle łączone z technikami Brute Force w celu zbierania informacji, próbując uzyskać dostęp do jak największej liczby adresów URL, aby wyliczyć katalogi i pliki na serwerze. Osoby atakujące mogą sprawdzić, czy nie ma wszystkich odmian często istniejących plików. Na przykład wyszukiwanie plików haseł obejmowałoby pliki, w tym psswd.txt, password.htm, password.dat i inne odmiany.</p><p>Aby to złagodzić, należy uwzględnić możliwości wykrywania prób siły brutalnej.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Upewnij się, że konta o najniższych uprawnieniach są używane do łączenia się z serwerem bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Hierarchia uprawnień bazy danych SQL](https://msdn.microsoft.com/library/ms191465), [zabezpiecza bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Konta o najniższych uprzywilejowaniach powinny być używane do łączenia się z bazą danych. Logowanie do aplikacji powinno być ograniczone w bazie danych i powinno wykonywać tylko wybrane procedury przechowywane. Login aplikacji nie powinien mieć bezpośredniego dostępu do tabeli. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementowanie protokołu RLS zabezpieczeń na poziomie wiersza, aby uniemożliwić dzierżawcom dostęp do wzajemnych danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Sql Azure, OnPrem |
| **Atrybuty**              | Wersja SQL - Wersja V12, WERSJA SQL - MsSQL2016 |
| **Odwołania**              | [Zabezpieczenia na poziomie wiersza programu SQL Server (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroki** | <p>Zabezpieczenia na poziomie wiersza umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (np. członkostwa w grupie lub kontekstu wykonania).</p><p>Zabezpieczenia na poziomie wiersza (RLS) upraszcza projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Możliwe jest na przykład zapewnienie, że pracownicy mają dostęp tylko do tych wierszy danych, które są odpowiednie do ich działu, lub ograniczenie dostępu do danych klienta tylko do danych odpowiednich dla firmy.</p><p>Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie z dala od danych w innej warstwie aplikacji. System bazy danych stosuje ograniczenia dostępu za każdym razem, gdy próbowano uzyskać dostęp do danych z dowolnej warstwy. Dzięki temu system zabezpieczeń jest bardziej niezawodny i niezawodny dzięki zmniejszeniu powierzchni systemu zabezpieczeń.</p><p>|

Należy pamiętać, że RLS jako funkcja bazy danych out-of-the-box ma zastosowanie tylko do programu SQL Server począwszy od 2016 r. i bazy danych SQL platformy Azure. Jeśli nie zaimplementowano funkcji RLS po wyjęciu z pudełka, należy zapewnić, że dostęp do danych jest ograniczony Za pomocą widoków i procedur

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Rola Sysadmin powinna mieć tylko prawidłowych niezbędnych użytkowników

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Hierarchia uprawnień bazy danych SQL](https://msdn.microsoft.com/library/ms191465), [zabezpiecza bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Członkowie roli serwera stałego SysAdmin powinni być bardzo ograniczeni i nigdy nie zawierać kont używanych przez aplikacje.  Przejrzyj listę użytkowników w roli i usuń niepotrzebne konta|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Łączenie się z bramą w chmurze przy użyciu tokenów o najniższych uprawnieniach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama w chmurze IoT | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługa Azure IoT Hub |
| **Odwołania**              | [Kontrola dostępu usługi Iot Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroki** | Podaj uprawnienia najmniejszych uprawnień do różnych składników, które łączą się z bramą w chmurze (Centrum IoT Hub). Typowym przykładem jest — składnik zarządzania/inicjowania obsługi administracyjnej urządzeń używa rejestru odczytu/zapisu, procesor zdarzeń (ASA) używa service connect. Poszczególne urządzenia łączą się przy użyciu poświadczeń urządzenia|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Używanie klucza sas uprawnień tylko do wysyłania do generowania tokenów urządzeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie uwierzytelniania i modelu zabezpieczeń centrum zdarzeń](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Klucz sygnatury dostępu Współdzielonego służy do generowania tokenów poszczególnych urządzeń. Używanie klucza tylko do wysyłania tylko sygnatury dostępu Współdzielonego podczas generowania tokenu urządzenia dla danego wydawcy|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Nie używaj tokenów dostępu, które zapewniają bezpośredni dostęp do Centrum zdarzeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie uwierzytelniania i modelu zabezpieczeń centrum zdarzeń](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Token, który udziela bezpośredniego dostępu do centrum zdarzeń nie powinny być podane do urządzenia. Korzystanie z tokenu najmniej uprzywilejowanego dla urządzenia, które daje dostęp tylko do wydawcy pomoże zidentyfikować i czarnej listy, jeśli okaże się, że nieautoryzowane lub zagrożone urządzenie.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Łączenie się z Centrum zdarzeń przy użyciu kluczy Sygnatury dostępu Współdzielonego, które mają wymagane minimalne uprawnienia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie uwierzytelniania i modelu zabezpieczeń centrum zdarzeń](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Podaj uprawnienia najmniejszych uprawnień do różnych aplikacji zaplecza, które łączą się z Centrum zdarzeń. Generuj oddzielne klucze Sygnatury dostępu Współdzielonego dla każdej aplikacji zaplecza i podaj im tylko wymagane uprawnienia — wysyłanie, odbieranie lub zarządzanie nimi.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>W miarę możliwości łączenie się z bazą danych usługi Cosmos DB umożliwia łączenie się z bazą danych usługi Cosmos

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Token zasobu jest skojarzony z zasobem uprawnień usługi Azure Cosmos DB i przechwytuje relację między użytkownikiem bazy danych a uprawnieniem, które użytkownik ma dla określonego zasobu aplikacji usługi Azure Cosmos DB (np. kolekcja, dokument). Zawsze używaj tokenu zasobu, aby uzyskać dostęp do usługi Azure Cosmos DB, jeśli klient nie może ufać za pomocą kluczy głównego lub tylko do odczytu — takich jak aplikacja użytkownika końcowego, taka jak klient mobilny lub stacjonarny. Użyj klucza głównego lub kluczy tylko do odczytu z aplikacji wewnętrznej bazy danych, które mogą bezpiecznie przechowywać te klucze.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Włącz zarządzanie dostępem szczegółowym do subskrypcji platformy Azure przy użyciu funkcji RBAC

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroki** | Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Ograniczanie dostępu klienta do operacji klastra przy użyciu funkcji RBAC

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania sieci usług | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Platforma Azure |
| **Odwołania**              | [Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroki** | <p>Usługa Azure Service Fabric obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są połączeni z klastrem sieci szkieletowej usług: administrator i użytkownik. Kontrola dostępu umożliwia administratorowi klastra ograniczenie dostępu do niektórych operacji klastra dla różnych grup użytkowników, dzięki czemu klaster jest bezpieczniejszy.</p><p>Administratorzy mają pełny dostęp do funkcji zarządzania (w tym funkcji odczytu/zapisu). Użytkownicy domyślnie mają tylko dostęp do odczytu do funkcji zarządzania (na przykład możliwości zapytań) i możliwość rozpoznawania aplikacji i usług.</p><p>Określ dwie role klienta (administratora i klienta) w momencie tworzenia klastra, udostępniając oddzielne certyfikaty dla każdego z nich.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>W razie potrzeby przeprowadzaj modelowanie zabezpieczeń i korzystaj z zabezpieczeń na poziomie pola

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | W razie potrzeby przeprowadzaj modelowanie zabezpieczeń i korzystaj z zabezpieczeń na poziomie pola|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Wykonaj modelowanie zabezpieczeń kont portalu, mając na uwadze, że model zabezpieczeń portalu różni się od reszty crm

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamiczny portal CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Wykonaj modelowanie zabezpieczeń kont portalu, mając na uwadze, że model zabezpieczeń portalu różni się od reszty crm|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Udzielanie uprawnień szczegółowej dla zakresu jednostek w usłudze Azure Table Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Rodzaj magazynu — tabela |
| **Odwołania**              | [Jak delegować dostęp do obiektów na koncie magazynu platformy Azure przy użyciu sygnatury dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroki** | W niektórych scenariuszach biznesowych usługa Azure Table Storage może być wymagana do przechowywania poufnych danych przeznaczonych dla różnych stron. Np. dane wrażliwe dotyczące różnych krajów/regionów. W takich przypadkach sygnatury sygnatury sygnatury sygnatury Sygnatury dostępu Współdzielonego można skonstruować, określając zakresy kluczy partycji i wierszy, dzięki czemu użytkownik może uzyskać dostęp do danych specyficznych dla określonego kraju/regionu.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Włącz kontrolę dostępu opartą na rolach (RBAC) na koncie magazynu platformy Azure przy użyciu usługi Azure Resource Manager

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Jak zabezpieczyć konto magazynu za pomocą kontroli dostępu opartej na rolach (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroki** | <p>Podczas tworzenia nowego konta magazynu, należy wybrać model wdrażania klasycznego lub usługi Azure Resource Manager. Klasyczny model tworzenia zasobów na platformie Azure umożliwia tylko dostęp do subskrypcji lub nic, a co za tym idzie, do konta magazynu.</p><p>W modelu usługi Azure Resource Manager można umieścić konto magazynu w grupie zasobów i kontrolować dostęp do płaszczyzny zarządzania tego konta magazynu przy użyciu usługi Azure Active Directory. Na przykład można dać określonym użytkownikom możliwość dostępu do kluczy konta magazynu, podczas gdy inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie można uzyskać dostępu do kluczy konta magazynu.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementowanie niejawnego wykrywania jailbreak lub rootingu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Aplikacja powinna chronić własną konfigurację i dane użytkownika w przypadku, gdy telefon jest zakorzeniony lub więzienie złamane. Zakorzenienie / łamanie więzienia oznacza nieautoryzowany dostęp, którego normalni użytkownicy nie zrobią na własnych telefonach. W związku z tym aplikacja powinna mieć niejawną logikę wykrywania podczas uruchamiania aplikacji, aby wykryć, czy telefon został zakorzeniony.</p><p>Logika wykrywania może po prostu uzyskać dostęp do plików, do których zwykle dostęp może uzyskać tylko użytkownik root, na przykład:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Jeśli aplikacja może uzyskać dostęp do dowolnego z tych plików, oznacza to, że aplikacja jest uruchomiona jako użytkownik root.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Słabe odniesienie do klasy w WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroki** | <p>System używa odwołania do klasy słabe, które mogą umożliwić osobie atakującej wykonanie nieautoryzowanego kodu. Program odwołuje się do klasy zdefiniowanej przez użytkownika, która nie jest jednoznacznie zidentyfikowana. Gdy .NET ładuje tę słabo zidentyfikowaną klasę, moduł ładujący typu CLR wyszukuje klasę w następujących lokalizacjach w określonej kolejności:</p><ol><li>Jeśli zespół typu jest znany, moduł ładujący przeszukuje lokalizacje przekierowań pliku konfiguracyjnego, gac, bieżący zestaw przy użyciu informacji o konfiguracji i katalog podstawowy aplikacji</li><li>Jeśli zestaw jest nieznany, moduł ładujący przeszukuje bieżący zestaw, mscorlib i lokalizację zwróconą przez program obsługi zdarzeń TypeResolve</li><li>Ta kolejność wyszukiwania CLR może być modyfikowana za pomocą haków, takich jak mechanizm przekazywania typów i zdarzenie AppDomain.TypeResolve</li></ol><p>Jeśli osoba atakująca wykorzysta kolejność wyszukiwania CLR, tworząc klasę alternatywną o tej samej nazwie i umieszczając ją w innej lokalizacji, którą najpierw załaduje clr, program CLR nieumyślnie wykona dostarczony przez osobę atakującą kod</p>|

### <a name="example"></a>Przykład
Element `<behaviorExtensions/>` pliku konfiguracyjnego WCF poniżej nakazuje WCF dodać klasę zachowania niestandardowego do określonego rozszerzenia WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Używanie w pełni kwalifikowanych (silnych) nazw jednoznacznie identyfikuje typ i dodatkowo zwiększa bezpieczeństwo systemu. Podczas rejestrowania typów w plikach machine.config i app.config należy używać w pełni kwalifikowanych nazw zestawów.

### <a name="example"></a>Przykład
Element `<behaviorExtensions/>` pliku konfiguracyjnego WCF poniżej nakazuje WCF dodać silnie odwołuje się do klasy zachowania niestandardowego do określonego rozszerzenia WCF.
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

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>Kontrola autoryzacji implementacji WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroki** | <p>Ta usługa nie używa formantu autoryzacji. Gdy klient wywołuje określoną usługę WCF, WCF udostępnia różne schematy autoryzacji, które weryfikują, że wywołujący ma uprawnienia do wykonywania metody usługi na serwerze. Jeśli formanty autoryzacji nie są włączone dla usług WCF, uwierzytelniony użytkownik może osiągnąć eskalację uprawnień.</p>|

### <a name="example"></a>Przykład
Następująca konfiguracja nakazuje WCF, aby nie sprawdzała poziomu autoryzacji klienta podczas wykonywania usługi:
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
Użyj schematu autoryzacji usługi, aby sprawdzić, czy wywołujący metodę usługi jest do tego upoważniony. WCF udostępnia dwa tryby i umożliwia definicję schematu autoryzacji niestandardowej. W trybie UseWindowsGroups używane są role systemu Windows i użytkowników, a tryb UseAspNetRoles używa do uwierzytelniania dostawcy roli ASP.NET, takiego jak SQL Server.

### <a name="example"></a>Przykład
Następująca konfiguracja nakazuje WCF, aby upewnić się, że klient jest częścią grupy Administratorzy przed wykonaniem dodaj usługi:
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
Usługa jest następnie zadeklarowana jako następująca:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementowanie odpowiedniego mechanizmu autoryzacji w ASP.NET interfejsie API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, MVC5 |
| **Atrybuty**              | Nie dotyczy, dostawca tożsamości — usługa ADFS, dostawca tożsamości — usługa Azure AD |
| **Odwołania**              | [Uwierzytelnianie i autoryzacja w ASP.NET interfejsie API sieci Web](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroki** | <p>Informacje o roli dla użytkowników aplikacji mogą pochodzić z oświadczeń usługi Azure AD lub ADFS, jeśli aplikacja opiera się na nich jako dostawcy tożsamości lub sama aplikacja może ją pod warunkiem. W każdym z tych przypadków implementacji autoryzacji niestandardowej należy sprawdzić poprawność informacji o roli użytkownika.</p><p>Informacje o roli dla użytkowników aplikacji mogą pochodzić z oświadczeń usługi Azure AD lub ADFS, jeśli aplikacja opiera się na nich jako dostawcy tożsamości lub sama aplikacja może ją pod warunkiem. W każdym z tych przypadków implementacji autoryzacji niestandardowej należy sprawdzić poprawność informacji o roli użytkownika.</p>

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
Wszystkie kontrolery i metody działania, które muszą być chronione powinny być ozdobione powyższym atrybutem.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Przeprowadzaj kontrole autoryzacji w urządzeniu, jeśli obsługuje różne akcje, które wymagają różnych poziomów uprawnień

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Urządzenie powinno autoryzować wywołującego, aby sprawdzić, czy osoba dzwoniąca ma wymagane uprawnienia do wykonywania żądanej akcji. Na przykład, powiedzmy, że urządzenie jest Inteligentny zamek drzwi, które mogą być monitorowane z chmury, a także zapewnia funkcje, takie jak zdalne blokowanie drzwi.</p><p>Inteligentny zamek drzwi zapewnia funkcję odblokowywania tylko wtedy, gdy ktoś fizycznie zbliża się do drzwi z kartą. W takim przypadku implementacja zdalnego polecenia i sterowania powinna odbywać się w taki sposób, aby nie zapewniała żadnych funkcji, aby odblokować drzwi, ponieważ brama w chmurze nie jest upoważniona do wysyłania polecenia w celu odblokowania drzwi.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Przeprowadzaj kontrole autoryzacji w bramie polowej, jeśli obsługuje ona różne akcje wymagające różnych poziomów uprawnień

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Brama pola powinna autoryzować wywołującego, aby sprawdzić, czy osoba dzwoniąca ma wymagane uprawnienia do wykonywania żądanej akcji. Na przykład powinny istnieć różne uprawnienia dla interfejsu użytkownika administratora/interfejsu API używanego do konfigurowania urządzeń bramy pola v/s, które się z nim łączą.|
