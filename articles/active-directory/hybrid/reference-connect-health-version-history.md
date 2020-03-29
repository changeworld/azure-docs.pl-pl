---
title: Historia wersji programu Azure AD Connect Health
description: W tym dokumencie opisano wersje dla usługi Azure AD Connect Health i co zostało uwzględnione w tych wersjach.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace8d170a5ba48c00775c3b376df8bb70a337d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897041"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: historia wersji
Zespół usługi Azure Active Directory regularnie aktualizuje usługę Azure AD Connect Health o nowe funkcje i funkcje. W tym artykule wymieniono wersje i funkcje, które zostały wydane.  

> [!NOTE]
> Agenci connect health są aktualizowane automatycznie po wydaniu nowej wersji. Upewnij się, że ustawienia automatycznego uaktualniania są włączone w witrynie Azure portal.
>

Usługa Azure AD Connect Health for Sync jest zintegrowana z instalacją usługi Azure AD Connect. Dowiedz się więcej o [historii wersji usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) Aby uzyskać opinię o funkcjach, zagłosuj na kanał [głosowy użytkownika connect health](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>Lipiec 2019 r.
**Aktualizacja agenta**
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.1.59.0) 
   1. Zmiana tekstu w testwindowsTransport
   2. Zmiany przekazywania rp usług AD FS
   
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.1.56.0) 
   1. Dodaj test TestWindowsTransport i usuń punkty końcowe WsTrust w teście CheckOffice365Endpoints
   2. Informacje o systemach operacyjnych i sieciach .NET
   3. Zwiększ rozmiar wysyłania wiadomości konfiguracyjnych RP do 1 MB.
   4. Poprawki błędów
   
* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.1.56.0) 
   1. Informacje o systemach operacyjnych i sieciach .NET 
   2. Poprawki błędów

## <a name="may-2019"></a>Maj 2019 r.
**Aktualizacja agenta:** 
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.1.51.0) 
   1. Poprawka błędu, aby odróżnić wiele logowania, które współużytkują ten sam identyfikator żądania klienta.
   2. Poprawka błędu w celu przeanalizowania błędów nazwy użytkownika/hasła na zlokalizowanych serwerach językowych.   

## <a name="april-2019"></a>Kwiecień 2019 r.
**Aktualizacja agenta:** 
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.1.46.0) 
   1. Napraw proces alertu Sprawdź duplikaty SPN dla usługi ADFS

## <a name="march-2019"></a>Marzec 2019 r.
**Aktualizacja agenta:** 
* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.1.41.0)  
   1. Kolekcja wersji platformy .NET
   2. Poprawa zbierania liczników wydajności w przypadku braku niektórych kategorii
   3. Poprawka błędu dotycząca zapobiegania powstawaniu wielu wystąpień agenta monitorowania

* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.1.41.0) 
   1. Integracja i uaktualnianie skryptów testowych usług AD FS przy użyciu usługi ADFSToolBox
   2. Implementowanie kolekcji wersji platformy .NET
   3. Poprawa zbierania liczników wydajności w przypadku braku niektórych kategorii
   4. Poprawka błędu dotycząca zapobiegania powstawaniu wielu wystąpień agenta monitorowania


## <a name="november-2018"></a>Listopad 2018 r.
**Nowe funkcje GA:** 
* Kondycja usługi Azure AD Connect dla synchronizacji — diagnozowanie i korygowanie błędów synchronizacji zduplikowanych atrybutów z portalu

**Aktualizacja agenta:** 
* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.1.24.0) 
   1. Zgodność i wymuszanie protokołu TLS (Transport Layer Security) w wersji 1.2
   2. Redukcja szumów alarmowych wykazu globalnego
   3. Poprawki błędów rejestracji agenta kondycji

* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.1.24.0)  
   1. Zgodność i wymuszanie protokołu TLS (Transport Layer Security) w wersji 1.2
   2. Obsługa test-ADFSRequestToken dla zlokalizowanego systemu operacyjnego
   3. Rozwiązano problem z blokowaniem agenta diagnostycznego EventHandler
   4. Poprawki błędów rejestracji agenta kondycji

## <a name="august-2018"></a>Sierpień 2018 r. 
*  Agent usługi Azure AD Connect Dla Synchronizacji (wersja 3.1.7.0) wydany z usługą Azure AD Connect w wersji 1.1.880.0    
   1. Poprawka dla [wysokiego problemu z procesorem cpu agenta monitorowania z .NET Framework KB zwalnia](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Czerwiec 2018 r. 
**Nowe funkcje podglądu:** 
* Kondycja usługi Azure AD Connect dla synchronizacji — diagnozowanie i korygowanie błędów synchronizacji zduplikowanych atrybutów z portalu 

**Aktualizacja agenta:** 
* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.1.7.0)    
  1. Poprawka dla [wysokiego problemu z procesorem cpu agenta monitorowania z .NET Framework KB zwalnia](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.1.7.0)  
  1. Poprawka dla [wysokiego problemu z procesorem cpu agenta monitorowania z .NET Framework KB zwalnia](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Poprawki wyników testów na serwerze pomocniczym usługi ADFS Server 2016
   
* Agent kondycji usługi Azure AD Connect dla usług AD FS (wersja 3.1.2.0)  
  1. Poprawka do zarządzania pamięcią agenta i powiązanych alertów specjalnie dla wersji 3.0.244.0


## <a name="may-2018"></a>Maj 2018 r.
**Aktualizacja agenta:**
* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.0.244.0)
  1. Poprawa prywatności agentów  
  2. Poprawki błędów i ogólne ulepszenia

* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.0.244.0)
  1. Usługa diagnostyki agenta i związane z nimi ulepszenia modułu Programu PowerShell
  2. Poprawa prywatności agentów  
  3. Poprawki błędów i ogólne ulepszenia

* Agent usługi Azure AD Connect dla systemu synchronizacji (wersja 3.0.164.0) wydany z usługą Azure AD Connect w wersji 1.1.819.0 
  1. Poprawa prywatności agentów  
  2. Poprawki błędów i ogólne ulepszenia


## <a name="march-2018"></a>Marzec 2018 r.
**Nowe funkcje podglądu:**
* Kondycja usługi Azure AD Connect dla usług AD FS — ryzykowny raport IP i alerty.

**Aktualizacja agenta:**

* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agentów 
  2. Poprawki błędów i ogólne ulepszenia
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agentów 
  2. Poprawki błędów i ogólne ulepszenia
* Agent usługi Azure AD Connect dla systemu synchronizacji (wersja 3.0.129.0) wydany z usługą Azure AD Connect w wersji 1.1.750.0  
  1. Ulepszenia dostępności agentów 
  2. Poprawki błędów i ogólne ulepszenia

## <a name="december-2017"></a>Grudzień 2017
**Aktualizacja agenta:**

* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.0.145.0)
  1. Ulepszenia dostępności agentów 
  2. Dodano nowe polecenia rozwiązywania problemów z agentami
  3. Poprawki błędów i ogólne ulepszenia
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.0.145.0)
  1. Dodano nowe polecenia rozwiązywania problemów z agentami
  2. Ulepszenia dostępności agentów 
  3. Poprawki błędów i ogólne ulepszenia
  
## <a name="october-2017"></a>Październik 2017
**Aktualizacja agenta:**

 * Agent usługi Azure AD Connect dla systemu synchronizacji (wersja 3.0.129.0) wydany z usługą Azure AD Connect w wersji 1.1.649.0
<br></br> Rozwiązaliśmy problem ze zgodnością wersji między usługą Azure AD Connect a agentem kondycji usługi Azure AD Connect dla synchronizacji. Ten problem dotyczy klientów, którzy wykonują uaktualnienie usługi Azure AD Connect w miejscu do wersji 1.1.647.0, ale obecnie ma agenta kondycji w wersji 3.0.127.0. Po uaktualnieniu agent kondycji nie może już wysyłać danych o kondycji usługi Azure AD Connect Synchronizacja do usługi Azure AD Health Service. Dzięki tej poprawce agent kondycji w wersji 3.0.129.0 jest instalowany podczas uaktualniania usługi Azure AD Connect w miejscu. Usługa Health Agent w wersji 3.0.129.0 nie ma problemu ze zgodnością z usługą Azure AD Connect w wersji 1.1.649.0.

## <a name="july-2017"></a>Lipiec 2017
**Aktualizacja agenta:**

* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.0.68.0)
  1. Poprawki błędów i ogólne ulepszenia
  2. Obsługa suwerennej chmury
* Agent usługi Azure AD Connect dla usług AD FS (wersja 3.0.68.0)
  1. Poprawki błędów i ogólne ulepszenia
  2. Obsługa suwerennej chmury
* Agent usługi Azure AD Connect dla systemu synchronizacji (wersja 3.0.68.0) wydany z usługą Azure AD Connect w wersji 1.1.614.0
  1. Pomoc techniczna dla platformy Microsoft Azure Government Cloud i Microsoft Cloud Germany

## <a name="april-2017"></a>Kwiecień 2017      
**Aktualizacja agenta:**

* Agent kondycji usługi Azure AD Connect dla usług AD FS (wersja 3.0.12.0)
  1. Poprawki błędów i ogólne ulepszenia
* Agent usługi Azure AD Connect dla usług AD DS (wersja 3.0.12.0)
  1. Ulepszenia przekazywania liczników wydajności
  2. Poprawki błędów i ogólne ulepszenia

## <a name="october-2016"></a>Październik 2016
**Aktualizacja agenta:**

* Agent usługi Azure AD Connect dla usług AD FS (wersja 2.6.408.0)
* Ulepszenia w wykrywaniu adresów IP klientów w żądaniach uwierzytelniania
* Poprawki dotyczące alertów
* Agent usługi Azure AD Connect dla usług AD DS (wersja 2.6.408.0)
* Poprawki błędów związane z alertami.
* Agent usługi Azure AD Connect dla systemu synchronizacji (wersja 2.6.353.0) wydany z usługą Azure AD Connect w wersji 1.1.281.0
* Podaj wymagane dane dla raportów błędów synchronizacji
* Poprawki dotyczące alertów

**Nowe funkcje podglądu:**

* Raporty o błędach synchronizacji dla usługi Azure AD Connect

**Nowe funkcje:**

* Usługa Azure AD Connect Health for AD FS — pole adresu IP jest dostępne w raporcie o 50 najlepszych użytkownikach ze złą nazwą użytkownika/hasłem.

## <a name="july-2016"></a>Lipiec 2016
**Nowe funkcje podglądu:**

* [Kondycja usługi Azure AD Connect dla usług AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Styczeń 2016
**Aktualizacja agenta:**

* Agent usługi Azure AD Connect dla usług AD FS (wersja 2.6.91.1512)

**Nowe funkcje:**

* [Narzędzie testowe łączności dla agentów kondycji usługi Azure AD Connect](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Listopad 2015
**Nowe funkcje:**

* Obsługa [kontroli dostępu opartej](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) na rolach

**Nowe funkcje podglądu:**

* [Kondycja usługi Azure AD Connect do synchronizacji](how-to-connect-health-sync.md).

**Naprawiono problemy:**

* Poprawki błędów dla błędów widocznych podczas rejestracji agenta.

## <a name="september-2015"></a>Wrzesień 2015
**Nowe funkcje:**

* Nieprawidłowy raport hasła nazwy użytkownika dla usług AD FS
* Obsługa konfigurowania nieuwierzytywego serwera proxy HTTP
* Obsługa konfigurowania agenta na rdzeniu serwera
* Ulepszenia alertów dla usług AD FS
* Ulepszenia agenta kondycji usługi Azure AD Connect dla usług AD FS dla łączności i przekazywania danych.

**Naprawiono problemy:**

* Poprawki błędów w usłudze Usage Insights dla typów błędów usług AD FS.

## <a name="june-2015"></a>Czerwiec 2015
**Początkowa wersja usługi Azure AD Connect Health dla usług AD FS i serwera proxy usług AD FS.**

**Nowe funkcje:**

* Alerty dotyczące monitorowania serwerów proxy usług AD FS i AD FS z powiadomieniami e-mail.
* Łatwy dostęp do topologii i wzorców usług AD FS w licznikach wydajności usług AD FS.
* Tendencja w pomyślnych żądaniach tokenów na serwerach usług AD FS pogrupowanych według aplikacji, metod uwierzytelniania, lokalizacji sieci żądania itp.
* Trendy w nieudanych żądaniach na serwerach USŁUG AD FS pogrupowanych według aplikacji, typów błędów itp.
* Prostsze wdrażanie agenta przy użyciu poświadczeń administratora globalnego usługi Azure AD.  

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [monitorowaniu lokalnej infrastruktury tożsamości i usług synchronizacji w chmurze.](whatis-hybrid-identity-health.md)

