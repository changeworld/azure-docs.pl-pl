---
title: Historia wersji programu Azure AD Connect Health
description: W tym dokumencie opisano Nowości dla usługi Azure AD Connect Health i co zostało uwzględnione w tych wersjach.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58de8de9a9fab67d743d4560ccda037d8f41301b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299003"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Historia wersji
Zespół usługi Azure Active Directory regularnie aktualizuje program Azure AD Connect Health z nowych funkcji i funkcji. W tym artykule wymieniono wersje i funkcje, które zostały zwolnione.  

> [!NOTE]
> Programu Connect Health agentów są automatycznie aktualizowane po wydaniu nowej wersji. Upewnij się, włączono ustawienia automatycznej aktualizacji z witryny Azure portal. 
>

Azure AD Connect Health do celów synchronizacji jest zintegrowany z instalacji programu Azure AD Connect. Przeczytaj więcej na temat [Historia wersji programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) opinii dotyczącej funkcji głosu na [kanału Connect Health User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)


## <a name="may-2019"></a>Maja 2019 r
**Aktualizacja agenta:** 
* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.1.51.0) 
   1. Naprawienie usterki w celu rozróżnienia między wieloma logowania, współdzielić ten sam client-request-id.
   2. Naprawienie usterki do analizowania błędów nieprawidłowo podawali nazwę użytkownika/hasło na język zlokalizowane serwery.   

## <a name="april-2019"></a>2019 kwietnia
**Aktualizacja agenta:** 
* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.1.46.0) 
   1. Rozwiązywanie alertów procesu Sprawdź zduplikowane nazwy SPN dla usług AD FS

## <a name="march-2019"></a>Marca 2019 r
**Aktualizacja agenta:** 
* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.1.41.0)  
   1. Kolekcja wersji platformy .NET
   2. Ulepszanie zbierania liczników wydajności w przypadku braku określonych kategorii
   3. Naprawienie usterki na zapobieganiu podczas duplikowania wielu wystąpień Monitoring Agent

* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.1.41.0) 
   1. Integrowanie i uaktualniania skryptów test usług AD FS przy użyciu ADFSToolBox
   2. Implementowanie kolekcji wersji platformy .NET
   3. Ulepszanie zbierania liczników wydajności w przypadku braku określonych kategorii
   4. Naprawienie usterki na zapobieganiu podczas duplikowania wielu wystąpień Monitoring Agent


## <a name="november-2018"></a>Listopada 2018 r.
**Nowe funkcje w wersji ogólnie dostępnej:** 
* Program Azure AD Connect Health do celów synchronizacji — zdiagnozować i rozwiązać błędy synchronizacji zduplikowanym atrybutem z poziomu portalu

**Aktualizacja agenta:** 
* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.1.24.0) 
   1. Transport Layer Security (TLS) protocol w wersji 1.2 zgodności i wymuszania
   2. Ograniczenia liczby niepotrzebnych alertów wykazu globalnego
   3. Poprawki błędów rejestracji agenta kondycji

* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.1.24.0)  
   1. Transport Layer Security (TLS) protocol w wersji 1.2 zgodności i wymuszania
   2. Obsługa testów ADFSRequestToken zlokalizowanym systemie operacyjnym
   3. Rozwiązany problem blokowania EventHandler agenta diagnostyki
   4. Poprawki błędów rejestracji agenta kondycji

## <a name="august-2018"></a>Sierpień 2018 r. 
*  Agent platformy Azure AD Connect Health do celów synchronizacji (w wersji 3.1.7.0) zwolniony z usługi Azure AD Connect w wersji 1.1.880.0    
   1. Poprawka dla [zwalnia wysokiej problem procesora CPU agenta za pomocą programu .NET Framework KB monitorowania](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Czerwiec 2018 r. 
**Nowe funkcje w wersji zapoznawczej:** 
* Program Azure AD Connect Health do celów synchronizacji — zdiagnozować i rozwiązać błędy synchronizacji zduplikowanym atrybutem z poziomu portalu 

**Aktualizacja agenta:** 
* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.1.7.0)    
  1. Poprawka dla [zwalnia wysokiej problem procesora CPU agenta za pomocą programu .NET Framework KB monitorowania](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.1.7.0)  
  1. Poprawka dla [zwalnia wysokiej problem procesora CPU agenta za pomocą programu .NET Framework KB monitorowania](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Wyniki poprawki na serwerze pomocniczym 2016 serwera usług AD FS testów
   
* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.1.2.0)  
  1. Poprawka dla agenta, zarządzanie pamięcią i powiązanych alertów specjalnie dla wersji 3.0.244.0


## <a name="may-2018"></a>Maj 2018 r.
**Aktualizacja agenta:**
* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.0.244.0)
  1. Agent ochrony prywatności poprawy  
  2. Ogólne ulepszenia i poprawki błędów

* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.0.244.0)
  1. Usługa diagnostyczna agenta oraz pokrewne ulepszenia modułu programu PowerShell
  2. Agent ochrony prywatności poprawy  
  3. Ogólne ulepszenia i poprawki błędów

* Agent platformy Azure AD Connect Health do celów synchronizacji (w wersji 3.0.164.0) zwolniony z usługi Azure AD Connect w wersji 1.1.819.0 
  1. Agent ochrony prywatności poprawy  
  2. Ogólne ulepszenia i poprawki błędów


## <a name="march-2018"></a>Marca 2018 r.
**Nowe funkcje w wersji zapoznawczej:**
* Usługa Azure AD Connect Health dla usług AD FS — raport ryzykownych adresów IP i alertów.

**Aktualizacja agenta:**

* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agenta 
  2. Ogólne ulepszenia i poprawki błędów
* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agenta 
  2. Ogólne ulepszenia i poprawki błędów
* Agent platformy Azure AD Connect Health do celów synchronizacji (w wersji 3.0.129.0) zwolniony z usługi Azure AD Connect w wersji 1.1.750.0  
  1. Ulepszenia dostępności agenta 
  2. Ogólne ulepszenia i poprawki błędów

## <a name="december-2017"></a>Grudnia 2017 r.
**Aktualizacja agenta:**

* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.0.145.0)
  1. Ulepszenia dostępności agenta 
  2. Dodano nowe polecenia rozwiązywania problemów z agentem
  3. Ogólne ulepszenia i poprawki błędów
* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.0.145.0)
  1. Dodano nowe polecenia rozwiązywania problemów z agentem
  2. Ulepszenia dostępności agenta 
  3. Ogólne ulepszenia i poprawki błędów
  
## <a name="october-2017"></a>Października 2017 r.
**Aktualizacja agenta:**

 * Agent platformy Azure AD Connect Health do celów synchronizacji (w wersji 3.0.129.0) zwolniony z usługi Azure AD Connect w wersji 1.1.649.0
<br></br> Rozwiązano problem ze zgodnością w wersji między Azure AD Connect i agenta Azure AD Connect Health do celów synchronizacji. Ten problem ma wpływ na klientów, którzy wykonują program Azure AD Connect miejscowe uaktualnienie do wersji 1.1.647.0, ale obecnie ma agenta kondycji wersji 3.0.127.0. Po uaktualnieniu agenta programu Health nie może wysyłać dane o usługi Azure AD Connect synchronizacji kondycji do usługi Azure AD Health. Dzięki tej poprawce Agent kondycji wersji 3.0.129.0 jest instalowany podczas uaktualnienia w miejscu program Azure AD Connect. Agent kondycji wersji 3.0.129.0 nie ma problem ze zgodnością za pomocą usługi Azure AD Connect w wersji 1.1.649.0.

## <a name="july-2017"></a>Lipca 2017 r.
**Aktualizacja agenta:**

* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.0.68.0)
  1. Ogólne ulepszenia i poprawki błędów
  2. Obsługa należących do suwerennej chmury
* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.0.68.0)
  1. Ogólne ulepszenia i poprawki błędów
  2. Obsługa należących do suwerennej chmury
* Agent platformy Azure AD Connect Health do celów synchronizacji (w wersji 3.0.68.0) zwolniony z usługi Azure AD Connect w wersji 1.1.614.0
  1. Obsługa platformy Microsoft Azure Government Cloud i Microsoft Cloud w Niemczech

## <a name="april-2017"></a>Kwietnia 2017 r.      
**Aktualizacja agenta:**

* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 3.0.12.0)
  1. Ogólne ulepszenia i poprawki błędów
* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 3.0.12.0)
  1. Liczniki wydajności przekazywanie ulepszenia
  2. Ogólne ulepszenia i poprawki błędów

## <a name="october-2016"></a>Października 2016 r.
**Aktualizacja agenta:**

* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 2.6.408.0)
* Ulepszenia wykrywania adresów IP klientów w żądań uwierzytelniania
* Poprawki błędów związane z alertami
* Agent platformy Azure AD Connect Health dla usług AD DS (wersja 2.6.408.0)
* Poprawki błędów, związane z alertami.
* Agent platformy Azure AD Connect Health do celów synchronizacji (w wersji 2.6.353.0) zwolniony z usługi Azure AD Connect w wersji 1.1.281.0
* Podaj wymagane dane dla raportów o błędach synchronizacji
* Poprawki błędów związane z alertami

**Nowe funkcje w wersji zapoznawczej:**

* Raporty o błędach synchronizacji programu Azure AD Connect

**Nowe funkcje:**

* Usługa Azure AD Connect Health dla usług AD FS — pole adres IP jest dostępna w raport dotyczący 50 użytkowników najczęściej nieprawidłowo podawali nazwę użytkownika/hasło.

## <a name="july-2016"></a>Lipca 2016 r.
**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health dla usług AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Styczeń 2016
**Aktualizacja agenta:**

* Agent platformy Azure AD Connect Health dla usług AD FS (wersja 2.6.91.1512)

**Nowe funkcje:**

* [Test narzędzia łączności dla usługi Azure AD Connect kondycji agentów](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Listopad 2015
**Nowe funkcje:**

* Obsługa [kontrola dostępu oparta na rolach](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health do celów synchronizacji](how-to-connect-health-sync.md).

**Rozwiązano problemy:**

* Poprawki błędów błędy występujące podczas rejestracji agenta.

## <a name="september-2015"></a>Września 2015 r.
**Nowe funkcje:**

* Niewłaściwa nazwy użytkownika hasło raportów dla usług AD FS
* Pomocy technicznej, aby skonfigurować serwer Proxy HTTP nieuwierzytelniony
* Obsługa, aby skonfigurować agenta w instalacji Server core
* Ulepszenia alertów dla usług AD FS
* Przekaż poprawę Azure AD Connect Health Agent dla usług AD FS do obsługi łączności i danych.

**Rozwiązano problemy:**

* Poprawki w szczegółowych danych o użyciu dla typów usługi AD FS błędów.

## <a name="june-2015"></a>Czerwiec 2015
**Początkowa wersja programu Azure AD Connect Health dla usług AD FS i serwera Proxy usług AD FS.**

**Nowe funkcje:**

* Alerty monitorowania serwerów usług AD FS i serwera Proxy usług AD FS z powiadomienia e-mail.
* Łatwy dostęp do topologia usług AD FS i wzorców w liczniki wydajności programu AD FS.
* Trend w liczba pomyślnych żądań tokenów na serwerach usług AD FS, pogrupowane według aplikacji, metod uwierzytelniania, żądania sieci lokalizacji itp.
* Trendy w żądaniu nie powiodło się na serwerach usług AD FS, pogrupowane według aplikacji, błąd typy itp.
* Łatwiejsze wdrażanie agenta przy użyciu poświadczeń administratora globalnego usługi Azure AD.  

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [monitorować swoje lokalne tożsamości infrastruktury i usług synchronizacji w chmurze](whatis-hybrid-identity-health.md).

