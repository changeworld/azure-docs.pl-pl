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
ms.openlocfilehash: e5714f60b7fdd790f3af8e31250c41038110fd08
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047915"
---
# <a name="application-layer-security-with-front-door"></a>Zabezpieczenia warstwy aplikacji przy wejściu
Usługa Azure Service drzwiami frontowymi zapewnia możliwość ochrony aplikacji sieci web, zabezpieczenie aplikacji sieci web przed atakami sieciowymi oraz typowe luki luk w zabezpieczeniach sieci web jak wstrzykiwanie kodu SQL lub obejmujące wiele lokacji skryptów (XSS). Włączone dla frontonów HTTP (s), zabezpieczeń warstwy aplikacji drzwiami frontowymi jest globalnie rozproszone i zawsze, zatrzymywanie złośliwych ataków na sieć platformy Azure urządzenia brzegowe, daleko od zaplecza. Większe bezpieczeństwo i optymalizacji wydajności wejściu zapewnia szybkie i bezpieczne sieci web środowisk użytkowników końcowych.

## <a name="application-protection"></a>Ochrona aplikacji
Ochrona aplikacji drzwiami frontowymi jest konfigurowany na każde środowisko brzegowych na całym świecie, tworzone są aplikacje i automatycznie blokuje bez — ruch HTTP (s) z docieranie do aplikacji sieci web. Nasza architektura rozproszona wielodostępnych włącza globalnego ochronę na dużą skalę, bez obniżania oczekiwanego poziomu wydajności. W przypadku obciążeń HTTP (s) usługa ochrony aplikacji sieci web drzwiami frontowymi zapewnia aparat reguł zaawansowanych reguł niestandardowych, zestawu wstępnie skonfigurowanych reguł przed typowymi atakami i szczegółowe rejestrowanie dla wszystkich żądań który jest zgodny z regułą. Zezwalaj na elastyczne działań, w tym bloku lub dziennika obsługiwane są tylko.

## <a name="custom-access-control-rules"></a>Zasady kontroli dostępu niestandardowych
- **List elementów zabronionych adresów IP i dozwolonych:** można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci web, w oparciu o listę adresów IP klienta. Obsługiwane są zarówno IPv4 i IPv6
- **Kontrola dostępu oparta na geograficznej:** można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci web, w oparciu o adres IP klienta należy z numer kierunkowy kraju
- **Parametry HTTP filtrowania:** może skonfigurować reguły niestandardowego dostępu na podstawie zgodności parametry żądania HTTP (s), włącznie z nagłówkami, adres URL i ciągów zapytania

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

- Dowiedz się, jak [tworzenie drzwiami frontowymi](quickstart-create-front-door.md).
- Dowiedz się, [działania drzwiami frontowymi](front-door-routing-architecture.md).
