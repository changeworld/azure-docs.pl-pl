---
title: Ustawienia zasad zapory aplikacji sieci Web z usługami frontonu platformy Azure
description: Poznaj zaporę aplikacji sieci Web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 8f51cb6944221416b098a9b953db417053155f1e
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849103"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Ustawienia zasad zapory aplikacji sieci Web z usługami frontonu platformy Azure

Zasady zapory aplikacji sieci Web (WAF) umożliwiają kontrolowanie dostępu do aplikacji sieci Web za pomocą zestawu niestandardowych i zarządzanych reguł. Nazwa zasad WAF musi być unikatowa. Jeśli spróbujesz użyć istniejącej nazwy, zostanie wyświetlony błąd walidacji. Istnieje wiele ustawień poziomu zasad, które mają zastosowanie do wszystkich reguł określonych dla tych zasad, zgodnie z opisem w tym artykule.

## <a name="waf-state"></a>Stan WAF

Zasady WAFymi dla drzwi przednich mogą znajdować się w jednym z następujących dwóch stanów:
- **Dostępny** Gdy zasady są włączone, WAF aktywnie bada żądania przychodzące i podejmuje odpowiednie działania zgodnie z definicjami reguł
- **Wyłączone:** — gdy zasady są wyłączone, inspekcja WAF jest wstrzymana. Żądania przychodzące spowodują obejście WAF i są wysyłane do zaplecza na podstawie routingu przed drzwiami.

## <a name="waf-mode"></a>Tryb WAF

Zasady WAF można skonfigurować tak, aby były uruchamiane w następujących dwóch trybach:

- **Tryb wykrywania** Po uruchomieniu w trybie wykrywania WAF nie przyjmuje żadnych akcji innych niż Monitor i rejestruje żądanie i dopasowaną regułę WAF do dzienników WAF. Włącz diagnostykę rejestrowania dla drzwi z przodu (w przypadku korzystania z portalu można to osiągnąć, przechodząc do sekcji **Diagnostyka** w Azure Portal).

- **Tryb zapobiegania** Gdy program jest skonfigurowany do uruchamiania w trybie zapobiegania, WAF wykonuje określoną akcję, jeśli żądanie jest zgodne z regułą. Wszystkie dopasowane żądania są również rejestrowane w dziennikach WAF.

## <a name="waf-response-for-blocked-requests"></a>WAF odpowiedź dla zablokowanych żądań

Domyślnie, gdy WAF blokuje żądanie ze względu na dopasowaną regułę, zwraca kod stanu 403 z- **żądanie jest zablokowane** . Ciąg odwołania jest również zwracany do rejestrowania.

Można zdefiniować niestandardowy kod stanu odpowiedzi i komunikat odpowiedzi, gdy żądanie jest blokowane przez WAF. Obsługiwane są następujące niestandardowe kody stanu:

- 200 OK
- 403 Zabronione
- Metoda 405 nie jest dozwolona
- 406 nie akceptowalny
- 429 zbyt wiele żądań

Niestandardowy kod stanu odpowiedzi i komunikat odpowiedzi są ustawieniem poziomu zasad. Po jego skonfigurowaniu wszystkie zablokowane żądania otrzymują ten sam niestandardowy stan odpowiedzi i komunikat odpowiedzi.

## <a name="uri-for-redirect-action"></a>Identyfikator URI akcji przekierowania

Musisz zdefiniować identyfikator URI, aby przekierować żądania do, jeśli wybrano akcję **przekierowania** dla dowolnej reguły zawartej w zasadach WAF. Ten identyfikator URI przekierowania musi być prawidłową lokacją HTTP (S) i po skonfigurowaniu wszystkie żądania zgodne z akcją "REDIRECT" zostaną przekierowane do określonej lokacji.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak definiować [odpowiedzi niestandardowe](waf-front-door-configure-custom-response-code.md) WAF
