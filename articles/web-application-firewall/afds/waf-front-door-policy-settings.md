---
title: Ustawienia zasad zapory aplikacji sieci Web z drzwiami frontowymi platformy Azure
description: Dowiedz się Zapora aplikacji sieci Web (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932611"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Ustawienia zasad zapory aplikacji sieci Web w drzwiach frontowych platformy Azure

Zasady zapory aplikacji sieci Web (WAF) umożliwiają kontrolowanie dostępu do aplikacji sieci web przez zestaw reguł niestandardowych i zarządzanych. Nazwa zasad WAF musi być unikatowa. Jeśli spróbujesz użyć istniejącej nazwy, zostanie wyświetlony błąd sprawdzania poprawności. Istnieje wiele ustawień poziomu zasad, które mają zastosowanie do wszystkich reguł określonych dla tej zasady, zgodnie z opisem w tym artykule.

## <a name="waf-state"></a>Stan WAF

Zasady WAF dla drzwi frontowych mogą znajdować się w jednym z następujących dwóch stanów:
- **Włączono:** Gdy zasada jest włączona, WAF aktywnie sprawdza przychodzące żądania i podejmuje odpowiednie działania zgodnie z definicjami reguł
- **Wyłączone:** — gdy zasada jest wyłączona, inspekcja WAF jest wstrzymana. Przychodzące żądania ominie WAF i są wysyłane do zaplecza na podstawie routingu drzwiami frontowymi.

## <a name="waf-mode"></a>Tryb WAF

Zasady WAF można skonfigurować tak, aby działały w następujących dwóch trybach:

- **Tryb wykrywania** Po uruchomieniu w trybie wykrywania WAF nie podejmuje żadnych akcji innych niż monitor i rejestrować żądanie i jego dopasowaną regułę WAF do dzienników WAF. Włącz diagnostykę rejestrowania dla drzwi ami frontowymi (podczas korzystania z portalu można to osiągnąć, przechodząc do sekcji **Diagnostyka** w witrynie Azure portal).

- **Tryb zapobiegania** Po skonfigurowaniu do uruchamiania w trybie zapobiegania, WAF wykonuje określoną akcję, jeśli żądanie pasuje do reguły. Wszystkie dopasowane żądania są również rejestrowane w dziennikach WAF.

## <a name="waf-response-for-blocked-requests"></a>Odpowiedź WAF dla zablokowanych żądań

Domyślnie, gdy WAF blokuje żądanie z powodu dopasowanej reguły, zwraca kod stanu 403 z — **żądanie jest zablokowany** komunikat. Ciąg odwołania jest również zwracany do rejestrowania.

Można zdefiniować niestandardowy kod stanu odpowiedzi i komunikat odpowiedzi, gdy żądanie jest blokowane przez WAF. Obsługiwane są następujące niestandardowe kody stanu:

- 200 ok.
- 403 Zakazane
- 405 Metoda niedozwolona
- 406 Niedopuszczalne
- 429 Zbyt wiele żądań

Niestandardowy kod stanu odpowiedzi i komunikat odpowiedzi jest ustawieniem poziomu zasad. Po skonfigurowaniu wszystkie zablokowane żądania otrzymują ten sam stan odpowiedzi niestandardowej i komunikat odpowiedzi.

## <a name="uri-for-redirect-action"></a>Identyfikator URI do akcji przekierowania

Wymagane jest zdefiniowanie identyfikatora URI, aby przekierować żądania do, jeśli akcja **REDIRECT** jest zaznaczona dla dowolnej z reguł zawartych w zasadach WAF. Ten identyfikator URI przekierowania musi być prawidłową witryną HTTP(S), a po skonfigurowaniu wszystkie żądania pasujące reguły z akcją "PRZEKIEROWANIE" zostaną przekierowane do określonej lokacji.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak zdefiniować [niestandardowe odpowiedzi](waf-front-door-configure-custom-response-code.md) WAF
