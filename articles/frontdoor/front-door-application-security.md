---
title: Usługa Azure drzwiami frontowymi — zabezpieczeń warstwy aplikacji | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak usługa drzwiami frontowymi Azure umożliwia do ochrony i bezpiecznego zaplecza aplikacji
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
ms.openlocfilehash: c7b99548e2fe1ad0c1cab39953e28a97e7ebff4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193921"
---
# <a name="application-layer-security-with-front-door"></a>Zabezpieczenia warstwy aplikacji przy wejściu
Usługa Azure Service drzwiami frontowymi zapewnia możliwość ochrony aplikacji sieci web, zabezpieczenie aplikacji sieci web przed atakami sieciowymi oraz typowe luki luk w zabezpieczeniach sieci web jak wstrzykiwanie kodu SQL lub obejmujące wiele lokacji skryptów (XSS). Włączone dla frontonów HTTP (s), zabezpieczeń warstwy aplikacji drzwiami frontowymi jest globalnie rozproszone i zawsze, zatrzymywanie złośliwych ataków na sieć platformy Azure urządzenia brzegowe, daleko od zaplecza. Większe bezpieczeństwo i optymalizacji wydajności wejściu zapewnia szybkie i bezpieczne sieci web środowisk użytkowników końcowych.

## <a name="application-protection"></a>Ochrona aplikacji
Ochrona aplikacji drzwiami frontowymi jest konfigurowany na każde środowisko brzegowych na całym świecie, tworzone są aplikacje i automatycznie blokuje bez — ruch HTTP (s) z docieranie do aplikacji sieci web. Nasza architektura rozproszona wielodostępnych włącza globalnego ochronę na dużą skalę, bez obniżania oczekiwanego poziomu wydajności. W przypadku obciążeń HTTP (s) usługa ochrony aplikacji sieci web drzwiami frontowymi zapewnia aparat reguł zaawansowanych reguł niestandardowych, zestawu wstępnie skonfigurowanych reguł przed typowymi atakami i szczegółowe rejestrowanie dla wszystkich żądań który jest zgodny z regułą. Zezwalaj na elastyczne działań, w tym bloku lub dziennika obsługiwane są tylko.

## <a name="custom-access-control-rules"></a>Zasady kontroli dostępu niestandardowych
- **Zezwalaj na adresów IP, listy i listy zablokowanych:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci web, w oparciu o listę adresów IP klienta. Obsługiwane są zarówno IPv4 i IPv6
- **Kontrola dostępu na podstawie geograficznych:** Możesz skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci web na podstawie kodu kraju, Data utworzenia adresu IP klienta
- **Parametry protokołu HTTP, filtrowania:** Możesz skonfigurować reguły niestandardowego dostępu na podstawie zgodności parametry żądania HTTP (s), łącznie z nagłówkami, adres URL i ciągi zapytań

## <a name="azure-managed-rules"></a>Zasady usługi Azure managed
- Wstępnie skonfigurowany zestaw reguł względem znanych luk OWASP najważniejsze jest domyślnie włączona. W wersji zapoznawczej zestaw reguł zawiera sqli i xss żądania kontroli. Dodatkowe zasady zostaną dodane. Możesz uruchomić za pomocą dziennika akcji tylko do sprawdzania poprawności pracy wstępnie skonfigurowanych reguł, zgodnie z oczekiwaniami dla aplikacji 

## <a name="rate-limiting"></a>Ograniczanie szybkości
- Reguły kontroli stawka jest ograniczenie nietypowe duży ruch z dowolnego adresu IP klienta.  Można ustawić wartości progowej liczby żądań sieci web dozwolone przez IP klienta podczas okresu jednej minuty.

## <a name="centralized-protection-policy"></a>Zasady ochrony scentralizowane
- Możesz zdefiniować kilka zasad ochrony i dodaj je do zasad w kolejności priorytetu. Niestandardowe reguły mają wyższy priorytet niż zarządzanego zestawu reguł, aby zezwolić na wyjątki. Jednych zasad jest skojarzona z aplikacją sieci web.  Te same zasady ochrony aplikacji sieci web są replikowane do wszystkich serwerów brzegowych we wszystkich lokalizacjach, upewnij się, zasady zabezpieczeń spójne we wszystkich regionach

## <a name="configuration"></a>Konfigurowanie
- W trakcie okresu zapoznawczego może używać interfejsów API REST, programu PowerShell lub interfejsu wiersza polecenia do tworzenia i wdrażania zasad ochrony aplikacji drzwiami frontowymi i zasad. Dostępu do portalu zostanie dodana, zanim usługa jest ogólnie dostępna. 


## <a name="monitoring"></a>Monitorowanie
Drzwiami frontowymi umożliwia monitorowanie aplikacji sieci web przed atakami przy użyciu metryk czasu rzeczywistego, które są zintegrowane z usługą Azure Monitor w celu śledzenia alertów i łatwego monitorowania trendów.

## <a name="pricing"></a>Cennik
Zabezpieczenia warstwy aplikacji drzwiami frontowymi jest bezpłatne w wersji zapoznawczej.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
