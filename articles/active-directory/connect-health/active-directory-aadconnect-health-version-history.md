---
title: Historia wersji programu Azure AD Connect Health
description: Ten dokument zawiera opis wydanie dla usługi Azure AD Connect Health i dołączył w tych wersjach.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 3e3cebd47a259c7c297d6df0eaf76a350111f2e3
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319269"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: historia wersji
Zespół usługi Azure Active Directory regularnie aktualizuje Azure AD Connect Health z nowych funkcji. W tym artykule wymieniono wersje i funkcje, które zostały wydane.

## <a name="june-2018"></a>2018 czerwca 
**Nowe funkcje w wersji zapoznawczej:** 
* Azure AD Connect Health do celów synchronizacji - zdiagnozować i rozwiązać błędy synchronizacji zduplikowany atrybut z portalu 

**Aktualizacja agenta:** 
*   Agent Azure AD Connect Health dla usług AD FS (wersja 3.1.2.0)  
   1. Poprawka dla agenta zarządzania pamięcią i powiązanych alertów przeznaczonym dla wersji 3.0.244.0


## <a name="may-2018"></a>Maj 2018 r.
**Aktualizacja agenta:**
*   Agent Azure AD Connect Health dla usług AD DS (wersja 3.0.244.0)
 1. Poprawa ochrony prywatności agenta  
 2. Ogólne ulepszenia i poprawki błędów

*   Agent Azure AD Connect Health dla usług AD FS (wersja 3.0.244.0)
 1. Usługa agenta diagnostyki i powiązane ulepszenia modułu programu PowerShell
 2. Poprawa ochrony prywatności agenta  
 3. Ogólne ulepszenia i poprawki błędów

* Agent Azure AD Connect Health zwolnione z programem Azure AD Connect wersji 1.1.819.0 synchronizacji (wersja 3.0.164.0)
 1. Poprawa ochrony prywatności agenta  
 2. Ogólne ulepszenia i poprawki błędów


## <a name="march-2018"></a>2018 marca
**Nowe funkcje w wersji zapoznawczej:**
* Azure AD Connect Health dla usług AD FS - raport ryzykownych adresów IP i alertów.

**Aktualizacja agenta:**

*   Agent Azure AD Connect Health dla usług AD DS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agenta 
  2. Ogólne ulepszenia i poprawki błędów
*   Agent Azure AD Connect Health dla usług AD FS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agenta 
  2. Ogólne ulepszenia i poprawki błędów
* Agent Azure AD Connect Health do celów synchronizacji (wersja 3.0.176.0)
  1. Ulepszenia dostępności agenta 
  2. Ogólne ulepszenia i poprawki błędów

## <a name="december-2017"></a>2017 grudnia
**Aktualizacja agenta:**

*   Agent Azure AD Connect Health dla usług AD DS (wersja 3.0.145.0)
  1. Ulepszenia dostępności agenta 
  2. Dodano nowe polecenia rozwiązywania problemów z agenta
  3. Ogólne ulepszenia i poprawki błędów
*   Agent Azure AD Connect Health dla usług AD FS (wersja 3.0.145.0)
  1. Dodano nowe polecenia rozwiązywania problemów z agenta
  2. Ulepszenia dostępności agenta 
  3. Ogólne ulepszenia i poprawki błędów
  
## <a name="october-2017"></a>2017 października
**Aktualizacja agenta:**

 * Agent Azure AD Connect Health zwolnione z programem Azure AD Connect wersji 1.1.649.0 synchronizacji (wersja 3.0.129.0)
<br></br> Rozwiązany problem ze zgodnością wersji między Azure AD Connect i agenta Azure AD Connect Health dla synchronizacji. Ten problem ma wpływ na klientów, którzy są Wykonywanie uaktualnienia w miejscu Azure AD Connect do wersji 1.1.647.0, ale obecna wersja 3.0.127.0 agenta kondycji. Po uaktualnieniu agenta programu Health nie może wysyłać dane kondycji dotyczące usługi Azure AD Connect usługi synchronizacji z usługą Azure AD Health. Z tej poprawki wersja 3.0.129.0 agenta kondycji jest instalowany podczas uaktualnienia w miejscu Azure AD Connect. Agent kondycji wersji 3.0.129.0 nie ma problem ze zgodnością z programem Azure AD Connect wersji 1.1.649.0.

## <a name="july-2017"></a>2017 lipca
**Aktualizacja agenta:**

*   Agent Azure AD Connect Health dla usług AD DS (wersja 3.0.68.0)
  1. Ogólne ulepszenia i poprawki błędów
  2. Obsługa suwerennych chmury
*   Agent Azure AD Connect Health dla usług AD FS (wersja 3.0.68.0)
  1. Ogólne ulepszenia i poprawki błędów
  2. Obsługa suwerennych chmury
* Agent Azure AD Connect Health zwolnione z programem Azure AD Connect wersji 1.1.614.0 synchronizacji (wersja 3.0.68.0)
  1. Obsługa platformy Microsoft Azure dla instytucji rządowych chmury i Niemczech firmy Microsoft w chmurze

## <a name="april-2017"></a>Kwietnia 2017      
**Aktualizacja agenta:**

*   Agent Azure AD Connect Health dla usług AD FS (wersja 3.0.12.0)
  1. Ogólne ulepszenia i poprawki błędów
*   Agent Azure AD Connect Health dla usług AD DS (wersja 3.0.12.0)
  1. Liczniki wydajności Przekaż ulepszenia
  2. Ogólne ulepszenia i poprawki błędów

## <a name="october-2016"></a>Października 2016
**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla usług AD FS (wersja 2.6.408.0)
1. Ulepszenia w wykrywaniu adresów IP klientów w żądania uwierzytelniania
2. Poprawki błędów związane z alertami
* Agent Azure AD Connect Health dla usług AD DS (wersja 2.6.408.0)
1. Poprawki błędów związane z alertami.
* Agent Azure AD Connect Health zwolnione z programem Azure AD Connect wersji 1.1.281.0 synchronizacji (wersja 2.6.353.0)
1. Podaj wymagane dane raportów o błędach synchronizacji
2. Poprawki błędów związane z alertami

**Nowe funkcje w wersji zapoznawczej:**

* Raporty o błędach synchronizacji dla usługi Azure AD Connect

**Nowe funkcje:**

* Azure AD Connect Health dla usług AD FS — pole adresu IP jest dostępna w raporcie o najwyższym 50 użytkowników z zły nazwy użytkownika i hasła.

## <a name="july-2016"></a>Lipca 2016 r.
**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health dla usług AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>Styczeń 2016
**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla usług AD FS (wersja 2.6.91.1512)

**Nowe funkcje:**

* [Narzędzie Test łączności dla usługi Azure AD Connect kondycji agentów](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Listopad 2015
**Nowe funkcje:**

* Obsługa [kontrola dostępu oparta na rolach](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health dla synchronizacji](active-directory-aadconnect-health-sync.md).

**Rozwiązane problemy:**

* Wprowadzono poprawki błędów podczas rejestracji agenta.

## <a name="september-2015"></a>Wrzesień 2015
**Nowe funkcje:**

* Niewłaściwa nazwy użytkownika hasło raportów dla usług AD FS
* Pomocy technicznej, aby skonfigurować serwer Proxy HTTP nieuwierzytelniony
* Pomocy technicznej w celu skonfigurowania agenta w instalacji Server core
* Ulepszenia alerty dla usług AD FS
* Przekaż ulepszenia Azure AD Connect Health Agent dla usług AD FS dla danych i łączności.

**Rozwiązane problemy:**

* Poprawki błędów w szczegółowe informacje dotyczące użycia dla typów AD FS błędów.

## <a name="june-2015"></a>Czerwiec 2015
**Początkowa wersja programu Azure AD Connect Health dla usług AD FS i serwera Proxy usług AD FS.**

**Nowe funkcje:**

* Alerty dotyczące monitorowania serwerów usług AD FS i serwera Proxy usług AD FS z powiadomienia e-mail.
* Łatwy dostęp do topologii usług AD FS i wzorce w liczniki wydajności usługi AD FS.
* Trend w pomyślnych żądań tokenów na serwerach usług AD FS pogrupowane według aplikacje, metody uwierzytelniania, żądania sieci lokalizacji itp.
* Trendy w żądaniu nie powiodło się na serwerach usług AD FS pogrupowane według aplikacji, błąd typy itp.
* Łatwiejsze wdrażanie agenta przy użyciu poświadczeń administratora globalnego usługi Azure AD.  

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [monitorowanie lokalnej tożsamości infrastruktury i synchronizacji usług w chmurze](active-directory-aadconnect-health.md).

