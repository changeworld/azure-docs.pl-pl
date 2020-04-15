---
title: 'Uwierzytelnianie przekazywane usługi Azure AD: historia wersji | Dokumenty firmy Microsoft'
description: Ten artykuł zawiera listę wszystkich wersji agenta uwierzytelniania przekazywania usługi Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75b127f8429650d46af9f171ed7ff03692f1499e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379906"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Agent uwierzytelniania przekazywania usługi Azure AD: historia wydania wersji wersji 
 
Agenci zainstalowane lokalnie, które umożliwiają uwierzytelnianie przekazywane są regularnie aktualizowane w celu zapewnienia nowych możliwości. W tym artykule wymieniono wersje i funkcje, które są dodawane po wprowadzeniu nowych funkcji. Agenci uwierzytelniania przekazywania są aktualizowane automatycznie po wydaniu nowej wersji. 

Oto powiązane tematy: 

- [Logowanie użytkownika za pomocą uwierzytelniania przekazywania usługi Azure AD](how-to-connect-pta.md) 
- [Instalacja agenta uwierzytelniania przekazywanego usługi Azure AD](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Stan wydania: 
04/09/2020: Wydany do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Dodano obsługę kierowania na środowiska w chmurze po instalacji. Pakiet można przypiąć do danego środowiska chmury.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Stan wydania 
22.01.2019: Premiera do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Dodano obsługę niezawodnych kanałów usługi Service Bus w celu dodania kolejnej warstwy odporności połączenia dla połączeń wychodzących 
- Wymuszanie protokołu TLS 1.2 podczas rejestracji agenta 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Stan wydania 
4/10/2018: Wydany do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Obsługa połączenia z gniazdem internetowym 
- Ustawianie protokołu TLS 1.2 jako domyślnego protokołu dla agenta 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stan wydania 
31.01.2018: Wydany do pobrania  
### <a name="fixed-issues"></a>Rozwiązane problemy 

- Naprawiono błąd, który powodował pewne przecieki pamięci w agencie. 
- Zaktualizowano wersję usługi Azure Service Bus, która zawiera poprawkę błędu w przypadku problemów z limitem czasu łącznika. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stan wydania 
26.11.2017: Wydany do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Dodano obsługę połączeń opartych na websocket między agentem a usługami Azure AD w celu zwiększenia odporności połączeń 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Stan wydania 
25.11.2017: Wydany do pobrania  
### <a name="fixed-issues"></a>Rozwiązane problemy 
- Naprawiono błędy związane z pamięcią podręczną DNS dla domyślnych scenariuszy proxy 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Stan wydania 
10/17/2017: Wydany do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Dodano funkcję pamięci podręcznej DNS dla połączeń wychodzących w celu zwiększenia odporności na błędy DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Stan wydania 
31.08.2017: Wydany do pobrania  
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 
- Wersja ga agenta uwierzytelniania przekazywania usługi Azure AD 

## <a name="next-steps"></a>Następne kroki

- [Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory](how-to-connect-pta.md)