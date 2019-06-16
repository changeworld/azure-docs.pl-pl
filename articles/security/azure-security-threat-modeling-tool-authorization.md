---
title: Autoryzacja — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: c922556417ac92cc3667927fc8f846ace960a14a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935880"
---
# <a name="security-frame-authorization--mitigations"></a>Ramka zabezpieczeń: Autoryzacja | Środki zaradcze 
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Maszyny granicy zaufania** | <ul><li>[Upewnij się, że odpowiednie listy ACL są skonfigurowane do ograniczania nieautoryzowanego dostępu do danych na urządzeniu](#acl-restricted-access)</li><li>[Upewnij się, że zawartość poufnych aplikacji specyficzne dla użytkownika jest przechowywany w katalogu profilu użytkownika](#sensitive-directory)</li><li>[Upewnij się, że wdrożone aplikacje są uruchamiane z najmniejszymi uprawnieniami](#deployed-privileges)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Wymuszanie kolejny krok kolejności podczas przetwarzania przepływów logiki biznesowej](#sequential-logic)</li><li>[Implementuje mechanizm zapobiegania wyliczenie ograniczania szybkości](#rate-enumeration)</li><li>[Upewnij się, że odpowiednie zezwolenia są używane, i po niej reguły najniższych uprawnień](#principle-least-privilege)</li><li>[Business Logic Apps i zasobów dostępu decyzji dotyczących autoryzacji, nie powinna być oparta na parametry żądania przychodzące](#logic-request-parameters)</li><li>[Upewnij się, że zawartość i zasoby nie są wyliczalny ani dostępne za pośrednictwem wymuszone przeglądania](#enumerable-browsing)</li></ul> |
| **Baza danych** | <ul><li>[Upewnij się, że najmniej uprzywilejowane konta są używane do łączenia się z serwerem bazy danych](#privileged-server)</li><li>[Implementowanie wiersz zabezpieczeń na poziomie aby uniemożliwić dostęp do siebie nawzajem danych dzierżaw](#rls-tenants)</li><li>[Rola administratora systemu powinien mieć tylko prawidłowych użytkowników wymagane](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Połączenia z bramą chmury przy użyciu najmniej uprzywilejowane tokenów](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Użyj uprawnień tylko do wysyłania klucza sygnatury dostępu Współdzielonego do generowania tokenów urządzeń](#sendonly-sas)</li><li>[Nie używaj tokenów dostępu, które udostępniają bezpośredni dostęp do Centrum zdarzeń](#access-tokens-hub)</li><li>[Łączenie z Centrum zdarzeń za pomocą kluczy sygnatury dostępu Współdzielonego, które ma minimalne uprawnienia wymagane](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Nawiązywanie połączenia z usługi Azure Cosmos DB, jeśli to możliwe za pomocą tokenów zasobów](#resource-docdb)</li></ul> |
| **Granicy zaufania platformy Azure** | <ul><li>[Włącz szczegółowe zarządzanie dostępem do subskrypcji platformy Azure przy użyciu funkcji RBAC](#grained-rbac)</li></ul> |
| **Granicy zaufania usługi Service Fabric** | <ul><li>[Ogranicz dostęp klienta do operacji klastra przy użyciu funkcji RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Wykonaj bezpieczeństwa modelowania i użyć zabezpieczeń na poziomie pola w przypadku, gdy wymagane](#modeling-field)</li></ul> |
| **Dynamics CRM Portal** | <ul><li>[Wykonaj modelowania zabezpieczeń w portalu konta, pamiętając o tym, który model zabezpieczeń dla portalu różni się od reszty systemu CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Udziel uprawnień szczegółowych dotyczących zakresu jednostek w usłudze Azure Table Storage](#permission-entities)</li><li>[Włącz opartej na rolach kontrola dostępu (RBAC) do konta usługi Azure storage za pomocą usługi Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobile Client** | <ul><li>[Implementowanie niejawne zdjęcia zabezpieczeń systemu lub zakorzenienia wykrywania](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Odwołania do klasy słabe w programie WCF](#weak-class-wcf)</li><li>[Kontrolki programu WCF — Implementowanie autoryzacji](#wcf-authz)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Implementuje mechanizm autoryzacji odpowiednie ASP.NET Web API](#authz-aspnet)</li></ul> |
| **Urządzenia IoT** | <ul><li>[Kontrole autoryzacji na urządzeniu, gdy obsługuje różne akcje, które wymagają różnych poziomów uprawnień](#device-permission)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Wykonaj sprawdzanie autoryzacji w bramy w terenie, jeśli obsługuje ona różne akcje, które wymagają różnych poziomów uprawnień](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Upewnij się, że odpowiednie listy ACL są skonfigurowane do ograniczania nieautoryzowanego dostępu do danych na urządzeniu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że odpowiednie listy ACL są skonfigurowane do ograniczania nieautoryzowanego dostępu do danych na urządzeniu|

## <a id="sensitive-directory"></a>Upewnij się, że zawartość poufnych aplikacji specyficzne dla użytkownika jest przechowywany w katalogu profilu użytkownika

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że zawartość poufnych aplikacji specyficzne dla użytkownika jest przechowywany w katalogu profilu użytkownika. To, aby uniemożliwić dostęp do siebie nawzajem danych przez wielu użytkowników.|

## <a id="deployed-privileges"></a>Upewnij się, że wdrożone aplikacje są uruchamiane z najmniejszymi uprawnieniami

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że uruchomieniu wdrożonej aplikacji, z najmniejszymi uprawnieniami. |

## <a id="sequential-logic"></a>Wymuszanie kolejny krok kolejności podczas przetwarzania przepływów logiki biznesowej

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Aby sprawdzić, czy ten etap został uruchomiony za pośrednictwem przez użytkownika oryginalnego chcesz wymusić aplikacji tylko procesów biznesowych przepływów operacji logicznych w kolejności kolejny krok, wszystkie czynności trwa przetwarzanie w czasie przez ludzi realistyczne i przetwarza poza kolejnością, pominięte kroki , przetwarzane kroki od innego użytkownika lub zbyt szybko przesłane transakcji.|

## <a id="rate-enumeration"></a>Implementuje mechanizm zapobiegania wyliczenie ograniczania szybkości

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że poufne identyfikatory losowych. Implementowanie formantu CAPTCHA na stronach anonimowe. Upewnij się, że błędów i wyjątków nie powinno ujawnić określonych danych|

## <a id="principle-least-privilege"></a>Upewnij się, że odpowiednie zezwolenia są używane, i po niej reguły najniższych uprawnień

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Zasada oznacza, podając konto użytkownika z tych uprawnień, które są istotne dla użytkowników pracować. Na przykład użytkownika kopii zapasowej nie trzeba zainstalować oprogramowanie: z tego powodu kopii zapasowej użytkownik ma uprawnienia tylko do uruchamiania aplikacji kopii zapasowej i związane z kopii zapasowej. Inne uprawnienia, takie jak instalowanie nowego oprogramowania, są blokowane. Zasada ma zastosowanie także do użytkownika komputer osobisty, który zwykle działa w ramach konta zwykłego użytkownika i otwiera konta uprzywilejowanego, chronionych hasłem (administratora) tylko kiedy sytuacji absolutnie to uzasadnione. </p><p>Ta zasada można również będą stosowane do aplikacji sieci web. Zamiast wyłącznie zależnie od metody uwierzytelniania opartej na rolach za pomocą sesji, zamiast chcemy przypisywanie uprawnień do użytkowników za pomocą systemu uwierzytelniania opartego na bazie danych. Używamy nadal sesji, aby ustalić, czy użytkownik był zalogowany poprawnie, tylko teraz zamiast przypisywać tego użytkownika z określoną rolę, które firma Microsoft przypisać mu z uprawnieniami, aby sprawdzić, akcje, które użytkownik jest uprzywilejowany do wykonania w systemie. Big pro tej metody jest również, w każdym przypadku, gdy użytkownik musi zostać przypisany mniejszej liczby uprawnień, zmiany zostaną zastosowane na bieżąco, ponieważ przypisanie nie zależy od sesji, które w przeciwnym wypadku musiały wygasają najpierw.</p>|

## <a id="logic-request-parameters"></a>Business Logic Apps i zasobów dostępu decyzji dotyczących autoryzacji, nie powinna być oparta na parametry żądania przychodzące

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Zawsze, gdy podczas sprawdzania, czy użytkownik jest ograniczony do sprawdzenia niektórych danych, ograniczenia dostępu powinny być przetworzone po stronie serwera. Identyfikator userID ma być przechowywany w zmiennej sesji podczas logowania i powinien być używany do pobierania danych użytkownika z bazy danych |

### <a name="example"></a>Przykład
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Teraz atakujący możliwe można manipulować i nie zmieniać operacji aplikacji, ponieważ identyfikator pobierania danych jest obsługiwane po stronie serwera.

## <a id="enumerable-browsing"></a>Upewnij się, że zawartość i zasoby nie są wyliczalny ani dostępne za pośrednictwem wymuszone przeglądania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Poufne statycznej i plików konfiguracji nie powinny być przechowywane w folderze głównym w sieci web. Zawartość nie muszą być publiczne, powinny być stosowane albo odpowiednie właściwości kontroli dostępu lub usunięcie samej zawartości.</p><p>Ponadto wymuszone przeglądania jest zwykle w połączeniu z atak Siłowy techniki, aby zebrać informacje za pomocą próby uzyskania dostępu dowolną liczbę adresów URL możliwie wyliczanie katalogów i plików na serwerze. Osoby atakujące mogą wyszukać wszystkie odmiany często istniejących plików. Na przykład wyszukiwanie plików hasło będzie obejmować pliki w tym psswd.txt, password.htm, password.dat i inne różnice.</p><p>Aby rozwiązać ten problem, należy uwzględnić możliwości wykrywania ataków siłowych.</p>|

## <a id="privileged-server"></a>Upewnij się, że najmniej uprzywilejowane konta są używane do łączenia się z serwerem bazy danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Hierarchia uprawnienia bazy danych SQL](https://msdn.microsoft.com/library/ms191465), [zabezpieczanych obiektów bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Najmniej uprzywilejowane konta powinny być używane do łączenia z bazą danych. Logowania aplikacji powinny być ograniczone w bazie danych i należy wykonywać tylko wybrane procedur składowanych. Logowanie do aplikacji nie powinny mieć tabeli bezpośredniego dostępu. |

## <a id="rls-tenants"></a>Implementowanie wiersz zabezpieczeń na poziomie aby uniemożliwić dostęp do siebie nawzajem danych dzierżaw

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Sql Azure, OnPrem |
| **Atrybuty**              | MsSQL2016 wersja — wersja V12, wersja SQL — SQL |
| **Odwołania**              | [SQL Server Row-Level Security (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroki** | <p>Zabezpieczenia na poziomie wiersza umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (np. członkostwa w grupie lub kontekstu wykonania).</p><p>Row-Level Security (RLS) upraszcza projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Możliwe jest na przykład zapewnienie, że pracownicy mają dostęp tylko do tych wierszy danych, które są odpowiednie do ich działu, lub ograniczenie dostępu do danych klienta tylko do danych odpowiednich dla firmy.</p><p>Logika ograniczeń dostępu jest znajduje się w warstwie bazy danych, a nie od danych w innej warstwie aplikacji. Za każdym razem, gdy podejmowana jest próba tego dostępu do danych z dowolnej warstwy system bazy danych mają zastosowanie ograniczenia dostępu. Dzięki temu system zabezpieczeń bardziej niezawodne i niezawodne, zmniejszając prawdopodobieństwo system zabezpieczeń.</p><p>|

Należy pamiętać, że zabezpieczenia na poziomie wiersza jako funkcja bazy danych poza pole dotyczy tylko programu SQL Server od 2016 i Azure SQL database. Jeśli funkcja zabezpieczeń na poziomie wiersza poza pole nie jest zaimplementowana, należy zapewnić, że dostęp do danych jest ograniczony przy użyciu widoków i procedur

## <a id="sysadmin-users"></a>Rola administratora systemu powinien mieć tylko prawidłowych użytkowników wymagane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Hierarchia uprawnienia bazy danych SQL](https://msdn.microsoft.com/library/ms191465), [zabezpieczanych obiektów bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Członkowie stałej roli serwera SysAdmin powinny być bardzo ograniczona i nigdy nie zawiera konta używane przez aplikacje.  Przejrzyj listę użytkowników w roli i usunąć wszelkie niepotrzebne konta|

## <a id="cloud-least-privileged"></a>Połączenia z bramą chmury przy użyciu najmniej uprzywilejowane tokenów

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | IoT Cloud Gateway | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługi Azure IoT Hub |
| **Odwołania**              | [Kontrola dostępu w usłudze iot Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroki** | Podaj co najmniej uprawnienia uprawnień do różnych składników, łączących się z bramy w chmurze (usługi IoT Hub). Typowym przykładem jest — składnik zarządzania/Inicjowanie obsługi administracyjnej urządzeń używa registryread/zapisu, zdarzenie procesora (ASA) używa, połączenie z usługą. Poszczególne urządzenia są połączone przy użyciu poświadczeń urządzenia|

## <a id="sendonly-sas"></a>Użyj uprawnień tylko do wysyłania klucza sygnatury dostępu Współdzielonego do generowania tokenów urządzeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Klucz sygnatury dostępu Współdzielonego jest używany do generowania tokenów poszczególnych urządzeń. Użyj klucza sygnatury dostępu Współdzielonego uprawnienia tylko do wysyłania podczas generowania tokenu urządzenia dla danego wydawcy|

## <a id="access-tokens-hub"></a>Nie używaj tokenów dostępu, które udostępniają bezpośredni dostęp do Centrum zdarzeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Token udzielający bezpośredni dostęp do Centrum zdarzeń nie powinien być podawany na urządzeniu. Za pomocą najniższych uprawnieniach tokenu dla urządzenia, który zapewnia dostęp tylko do wydawcy może pomóc w zidentyfikowaniu i utworzyć listę niedozwolonych Jeśli okaże się nieautoryzowanego lub naruszony urządzenia.|

## <a id="sas-minimum-permissions"></a>Łączenie z Centrum zdarzeń za pomocą kluczy sygnatury dostępu Współdzielonego, które ma minimalne uprawnienia wymagane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Podaj co najmniej uprawnienia uprawnień do różnych aplikacji zaplecza, łączących się z Centrum zdarzeń. Generowanie oddzielne klucze sygnatur dostępu Współdzielonego dla każdej aplikacji zaplecza i zapewniają tylko wymagane uprawnienia — Wyślij i Odbierz lub Zarządzaj, aby je.|

## <a id="resource-docdb"></a>Nawiązywanie połączenia z usługi Cosmos DB, jeśli to możliwe za pomocą tokenów zasobów

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Document DB | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Token zasobu jest skojarzony z zasobem uprawnienia usługi Azure Cosmos DB i przechwytuje relację między użytkownikiem bazy danych i uprawnienia, które użytkownik ma dla określonego zasobu aplikacji usługi Azure Cosmos DB (np. kolekcji i dokumentów). Zawsze używaj token zasobu, można uzyskać dostęp do usługi Azure Cosmos DB, jeśli klient nie może być zaufany za pomocą postępowania z kluczami główny lub tylko do odczytu — takich jak takich jak klientów mobilnych i klasycznych aplikacji użytkownika końcowego. Użyj klucza głównego lub klucze tylko do odczytu z poziomu aplikacji zaplecza, które można bezpiecznie przechowywać klucze.|

## <a id="grained-rbac"></a>Włącz szczegółowe zarządzanie dostępem do subskrypcji platformy Azure przy użyciu funkcji RBAC

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroki** | Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań.|

## <a id="cluster-rbac"></a>Ogranicz dostęp klienta do operacji klastra przy użyciu funkcji RBAC

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania usługi Service Fabric | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Środowisko — platformy Azure |
| **Odwołania**              | [Kontrola dostępu oparta na rolach dla klientów usługi Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroki** | <p>Usługa Azure Service Fabric obsługuje dwa typy kontroli dostępu do różnych klientów, które są podłączone do klastra usługi Service Fabric: administratora i użytkownika. Kontrola dostępu pozwala administrator klastra ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników, dzięki czemu klaster jest bardziej bezpieczne.</p><p>Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu/zapisu). Użytkownicy, domyślnie mają tylko dostęp do odczytu do możliwości zarządzania (na przykład, zapytanie możliwości) i możliwość usuwania aplikacji i usług.</p><p>Należy określić role dwóch klientów (administratora i klientów) w czasie tworzenia klastra, podając oddzielnych certyfikatów dla każdego.</p>|

## <a id="modeling-field"></a>Wykonaj bezpieczeństwa modelowania i użyć zabezpieczeń na poziomie pola w przypadku, gdy wymagane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj bezpieczeństwa modelowania i użyć zabezpieczeń na poziomie pola w przypadku, gdy wymagane|

## <a id="portal-security"></a>Wykonaj modelowania zabezpieczeń w portalu konta, pamiętając o tym, który model zabezpieczeń dla portalu różni się od reszty systemu CRM

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM Portal | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj modelowania zabezpieczeń w portalu konta, pamiętając o tym, który model zabezpieczeń dla portalu różni się od reszty systemu CRM|

## <a id="permission-entities"></a>Udziel uprawnień szczegółowych dotyczących zakresu jednostek w usłudze Azure Table Storage

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | StorageType — tabela |
| **Odwołania**              | [Jak delegować dostęp do obiektów na koncie magazynu platformy Azure przy użyciu sygnatury dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroki** | W niektórych scenariuszach biznesowych usługa Azure Table Storage może być konieczne przechowywanie danych poufnych, który jest przeznaczony dla innej strony. Np. poufne dane dotyczące różnych krajów/regionów. W takich przypadkach można konstruować sygnatury SAS, określając zakresów kluczy partycji i wiersza tak, aby użytkownik mógł korzystać z danych specyficznych dla określonego kraju/regionu.| 

## <a id="rbac-azure-manager"></a>Włącz opartej na rolach kontrola dostępu (RBAC) do konta usługi Azure storage za pomocą usługi Azure Resource Manager

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Jak zabezpieczyć swoje konto magazynu przy użyciu kontroli dostępu opartej na rolach (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroki** | <p>Podczas tworzenia nowego konta magazynu, możesz wybrać modelu wdrożenia klasycznego lub usługi Azure Resource Manager. Klasyczny model tworzenia zasobów na platformie Azure umożliwia tylko sztywnego dostępu do subskrypcji, a w pozycji konta magazynu.</p><p>Przy użyciu modelu usługi Azure Resource Manager należy umieścić na koncie magazynu w zasobów grupy i kontroli dostępu do płaszczyzny zarządzania tym konkretnym kontem magazynu za pomocą usługi Azure Active Directory. Na przykład można udzielić określonym użytkownikom możliwość dostępu do kluczy konta magazynu, podczas gdy inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie dostęp do kluczy konta magazynu.</p>|

## <a id="rooting-detection"></a>Implementowanie niejawne zdjęcia zabezpieczeń systemu lub zakorzenienia wykrywania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Mobile Client | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Aplikacja powinna zabezpieczenie własnych danych konfiguracji i użytkowników w przypadku, gdy telefon jest ścieżką lub złamanych. Zakorzenienia/umożliwiający istotne oznacza przed nieautoryzowanym dostępem, normalnych użytkowników, którzy nie na ich własnych telefonach. W związku z tym aplikacja powinna mieć logikę wykrywania niejawne podczas uruchamiania aplikacji, aby wykryć, jeśli numer telefonu został odblokowany dostęp.</p><p>Logika wykrywania można po prostu uzyskiwać dostęp do plików, które zwykle tylko główny użytkownik może uzyskać dostęp, na przykład:</p><ul><li>/system/app/Superuser.apk</li><li>/ sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/Data/Local/xbin/su</li><li>/Data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/Data/Local/su</li></ul><p>Aplikacja można uzyskać dostęp do dowolnego z tych plików, oznacza, że aplikacja działa jako użytkownik root.</p>|

## <a id="weak-class-wcf"></a>Odwołania do klasy słabe w programie WCF

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroki** | <p>System używa Odwołanie słabe klasy, które mogą umożliwić osobie atakującej do wykonania nieautoryzowanego kodu. Program odwołuje się do klasy zdefiniowane przez użytkownika, który nie jest jednoznacznie identyfikowany. Podczas ładowania tej klasy słabo zidentyfikowanych .NET modułu ładującego typu CLR wyszukuje klasy w następujących lokalizacjach w podanej kolejności:</p><ol><li>Jeśli zestaw tego typu jest znany, moduł ładujący przeszukuje plik konfiguracji przekierowania lokalizacje, pamięci podręcznej GAC, bieżący zestaw przy użyciu informacji o konfiguracji i katalog podstawowy aplikacji</li><li>Jeśli zestaw jest nieznany, moduł ładujący przeszukuje bieżącego zestawu mscorlib i zwróconej przez program obsługi zdarzeń TypeResolve lokalizacji</li><li>CLR kolejności wyszukiwania mogą być modyfikowane przy użyciu punktów zaczepienia, takie jak mechanizm przekazywania dalej typów i zdarzeń AppDomain.TypeResolve</li></ol><p>Jeśli osoba atakująca wykorzystuje kolejność wyszukiwania CLR, tworząc klasę alternatywnych o takiej samej nazwie i umieszczenie go w lokalizacji alternatywnej, środowisko CLR załaduje najpierw CLR zostanie przypadkowo wykonywania kodu dostarczane przez osobę atakującą</p>|

### <a name="example"></a>Przykład
`<behaviorExtensions/>` WCF, aby dodać klasę niestandardowe zachowanie określonego rozszerzenia WCF powoduje, że element pliku konfiguracji usługi WCF poniżej.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Przy użyciu w pełni kwalifikowane nazwy (silną) unikatowo identyfikuje typ i jeszcze bardziej podkreśla zabezpieczenia systemu. Podczas rejestrowania typy w pliku machine.config i pliku app.config, należy użyć w pełni kwalifikowane nazwy zestawów.

### <a name="example"></a>Przykład
`<behaviorExtensions/>` WCF można dodać odwołanie do silnie niestandardowe zachowanie klasy do określonego rozszerzenia WCF powoduje, że element pliku konfiguracji usługi WCF poniżej.
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

## <a id="wcf-authz"></a>Kontrolki programu WCF — Implementowanie autoryzacji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroki** | <p>Ta usługa nie używa kontrolkę autoryzacji. Gdy klient wywołuje określonej usługi WCF, WCF zapewnia różnych systemów autoryzacji, które pozwalają sprawdzić, czy obiekt wywołujący ma uprawnienia do wykonania metody usługi na serwerze. Jeśli kontrolek autoryzacji nie są włączone dla usługi WCF, uwierzytelniony użytkownik może osiągnąć podwyższenie poziomu uprawnień.</p>|

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
Aby sprawdzić, czy obiekt wywołujący metody usługi jest autoryzowany, aby to zrobić, należy użyć schemat autoryzacji usługi. Usługi WCF udostępnia dwa tryby i pozwala na określenie schematu autoryzacja niestandardowa. Tryb UseWindowsGroups używa Windows ról i użytkowników i tryb UseAspNetRoles używa dostawcy ról ASP.NET, takich jak SQL Server do uwierzytelniania.

### <a name="example"></a>Przykład
Następująca konfiguracja powoduje, że usługi WCF, aby upewnić się, czy klient jest częścią grupy administratorów, przed wykonaniem usługi Dodaj:
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
Usługa następnie jest zadeklarowana następująco:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementuje mechanizm autoryzacji odpowiednie ASP.NET Web API

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, MVC5 |
| **Atrybuty**              | N/d, dostawca tożsamości dostawcę — usługi AD FS, tożsamości — usłudze Azure AD |
| **Odwołania**              | [Uwierzytelnianie i autoryzacja w składniku ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroki** | <p>Informacje o rolach dla użytkowników aplikacji mogą być uzyskane z usługi Azure AD lub oświadczenia usług AD FS, jeśli aplikacja zależy od ich jako dostawcy tożsamości lub może sama aplikacja, pod warunkiem, że. We wszystkich tych przypadkach implementacji autoryzacja niestandardowa, należy zweryfikować informacje o roli użytkownika.</p><p>Informacje o rolach dla użytkowników aplikacji mogą być uzyskane z usługi Azure AD lub oświadczenia usług AD FS, jeśli aplikacja zależy od ich jako dostawcy tożsamości lub może sama aplikacja, pod warunkiem, że. We wszystkich tych przypadkach implementacji autoryzacja niestandardowa, należy zweryfikować informacje o roli użytkownika.</p>

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
Kontrolerów i metod akcji, które muszą chronione, powinny być dekorowane za pomocą powyżej atrybutu.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Kontrole autoryzacji na urządzeniu, gdy obsługuje różne akcje, które wymagają różnych poziomów uprawnień

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Urządzenie powinno zezwolić obiekt wywołujący, aby sprawdzić, czy obiekt wywołujący ma wymagane uprawnienia do wykonania żądanej akcji. Pozwala na przykład załóżmy, że urządzenie jest Smartlock drzwi biblioteki, które można monitorować z poziomu chmury, a także zawiera funkcje, takie jak zdalne Blokowanie drzwi biblioteki.</p><p>Smart Lock drzwi udostępnia funkcje odblokowywanie tylko wtedy, gdy ktoś fizycznie jest dostarczany w pobliżu drzwi biblioteki za pomocą karty. W tym przypadku implementacji zdalne sterowanie i kontrola powinno się odbywać w taki sposób, że nie zawiera żadnych funkcji, aby odblokować drzwi biblioteki jako bramy w chmurze nie ma uprawnień do wysyłania poleceń, aby odblokować drzwi biblioteki.</p>|

## <a id="field-permission"></a>Wykonaj sprawdzanie autoryzacji w bramy w terenie, jeśli obsługuje ona różne akcje, które wymagają różnych poziomów uprawnień

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Bramy w terenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Bramy w terenie, należy autoryzować obiekt wywołujący, aby sprawdzić, czy obiekt wywołujący ma wymagane uprawnienia do wykonania żądanej akcji. Np. powinny być różne uprawnienia dla użytkownika interfejsu/interfejsem API administratora używane do konfigurowania urządzenia bramy v/s pola, które nawiązać z nim.|
