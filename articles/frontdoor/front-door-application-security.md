---
title: Drzwi frontowe platformy Azure — zabezpieczenia warstwy aplikacji | Dokumenty firmy Microsoft
description: Ten artykuł pomaga zrozumieć, w jaki sposób usługi Azure Front Door umożliwia ochronę i zabezpieczanie zaplecza aplikacji
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e458926930c1b95d48886559551878fc6c9d0673
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471799"
---
# <a name="application-layer-security-with-front-door"></a>Zabezpieczenia warstwy aplikacji z drzwiami przednimi
Usługa Azure Front Door zapewnia możliwość ochrony aplikacji sieci web w celu ochrony aplikacji sieci web przed atakami sieciowymi i typowymi lukami w zabezpieczeniach sieci Web, takimi jak iniekcja SQL lub skrypty krzyżowe (XSS). Włączone dla http(s) front-ends, zabezpieczenia warstwy aplikacji drzwi frontowych jest globalnie dystrybuowane i zawsze włączone, zatrzymując złośliwe ataki na krawędzi sieci platformy Azure, z dala od zaplecza. Dzięki dodatkowej optymalizacji bezpieczeństwa i wydajności drzwi frontowe zapewniają użytkownikom końcowym szybkie i bezpieczne środowisko internetowe.

## <a name="application-protection"></a>Ochrona aplikacji
Ochrona aplikacji drzwi frontowych jest skonfigurowana w każdym środowisku brzegowym na całym świecie, zgodnie z aplikacjami, i automatycznie blokuje ruch nie-http(s) przed dotarciem do aplikacji internetowych. Nasza wielodostępna architektura rozproszona umożliwia globalną ochronę na dużą skalę bez utraty wydajności. W przypadku obciążeń http(ów) usługa ochrony aplikacji sieci web usługi drzwiach frontowych udostępnia bogaty aparat reguł dla reguł niestandardowych, wstępnie skonfigurowany zbiór reguł przeciwko typowym atakom i szczegółowe rejestrowanie dla wszystkich żądań, które są zgodne z regułą. Obsługiwane są tylko elastyczne akcje, w tym zezwalanie, blokowanie lub rejestrowanie.

## <a name="custom-access-control-rules"></a>Niestandardowe reguły kontroli dostępu
- **Lista zablokowanych i lista zablokowanych adresów IP:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci web na podstawie listy adresów IP klienta. Obsługiwane są zarówno ip v4, jak i IP v6
- **Kontrola dostępu oparta na danych geograficznych:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji internetowych na podstawie kodu kraju, z którego pochodzi adres IP klienta
- **Filtrowanie parametrów HTTP:** Można skonfigurować niestandardowe reguły dostępu na podstawie pasujących parametrów żądań http(s), w tym nagłówków, adresów URL i ciągów zapytań

## <a name="azure-managed-rules"></a>Reguły zarządzane przez platformę Azure
- Wstępnie skonfigurowany zestaw reguł dotyczących typowych głównych luk w zabezpieczeniach OWASP jest domyślnie włączony. W wersji zapoznawczej zestaw reguł obejmuje sprawdzanie żądań sqli i xss. Dodatkowe zasady zostaną dodane. Możesz rozpocząć działanie tylko do dziennika, aby sprawdzić poprawność wstępnie skonfigurowanych reguł pracy zgodnie z oczekiwaniami dla aplikacji 

## <a name="rate-limiting"></a>Rate limiting (Ograniczanie szybkości)
- Reguła kontroli szybkości ma na celu ograniczenie nieprawidłowego ruchu z dowolnego adresu IP klienta.  Można ustawić próg liczby żądań sieci web dozwolonych przez adres IP klienta w czasie trwania jednej minuty.

## <a name="centralized-protection-policy"></a>Scentralizowane zasady ochrony
- Można zdefiniować kilka reguł ochrony i dodać je do zasad w kolejności priorytetu. Reguły niestandardowe mają wyższy priorytet niż zarządzanystawie reguł, aby zezwolić na wyjątki. Jedna zasada jest skojarzona z aplikacją sieci web.  Te same zasady ochrony aplikacji sieci web są replikowane na wszystkie serwery brzegowe we wszystkich lokalizacjach, zapewniają spójne zasady zabezpieczeń we wszystkich regionach

## <a name="configuration"></a>Konfigurowanie
- Podczas podglądu można użyć interfejsów API REST, programu PowerShell lub interfejsu wiersza polecenia do tworzenia i wdrażania reguł i zasad ochrony aplikacji drzwiach frontowych. Dostęp do portalu będzie obsługiwany, zanim usługa będzie ogólnie dostępna. 


## <a name="monitoring"></a>Monitorowanie
Drzwi frontowe umożliwia monitorowanie aplikacji sieci web przed atakami przy użyciu metryk w czasie rzeczywistym, które są zintegrowane z usługą Azure Monitor do śledzenia alertów i łatwego monitorowania trendów.

## <a name="pricing"></a>Cennik
Zabezpieczenia warstwy aplikacji drzwi przednich są bezpłatne podczas podglądu.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
