---
title: Ustawienia zasad dla zapory aplikacji sieci web za pomocą usługi Azure drzwi
description: Dowiedz się, zapory aplikacji sieci web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459372"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Ustawienia zasad dla zapory aplikacji sieci web za pomocą usługi Azure drzwi

Zasady zapory aplikacji sieci Web (WAF) umożliwia kontrolowanie dostępu do aplikacji sieci web przez zestaw reguł niestandardowych i zarządzane. Nazwa zasad zapory aplikacji sieci Web musi być unikatowa. Zostanie wyświetlony błąd sprawdzania poprawności, Jeśli spróbujesz użyć istniejącej nazwy. Istnieje wiele ustawień poziomu zasad, które są stosowane do wszystkich reguł określone dla tej zasady, zgodnie z opisem w tym artykule.

## <a name="waf-state"></a>Stan zapory aplikacji sieci Web

Zasady zapory aplikacji sieci Web drzwiami frontowymi może być w jednym z następujących dwóch stanów:
- **Włączone:** Po włączeniu zasady zapory aplikacji sieci Web jest aktywnie sprawdzanie żądań przychodzących i pobiera odpowiednie działania zgodnie z definicjami reguł
- **Wyłączone:** — w przypadku zasad jest wyłączona, zapory aplikacji sieci Web kontroli zostało wstrzymane. Przychodzące żądania pomijał zapory aplikacji sieci Web i są wysyłane do zaplecza, oparte na wejściu routingu.

## <a name="waf-mode"></a>Tryb zapory aplikacji sieci Web

Zasady zapory aplikacji sieci Web można skonfigurować do uruchamiania w następujących dwóch trybach:

- **Tryb wykrywania** uruchomiony w trybie wykrywania, zapory aplikacji sieci Web nie akcje inne niż monitora i logowania żądanie i jego dopasowane reguły zapory aplikacji sieci Web do dzienników zapory aplikacji sieci Web. Włącz rejestrowanie danych diagnostycznych dla drzwiami frontowymi (przy użyciu portalu, można to osiągnąć, przechodząc do **diagnostyki** sekcji w witrynie Azure portal).

- **Tryb zapobiegania** po skonfigurowaniu do pracy w trybie zapobiegania zapory aplikacji sieci Web ma określoną akcję, jeśli żądanie jest zgodny z regułą. Wszelkie żądania dopasowane również są rejestrowane w dziennikach zapory aplikacji sieci Web.

## <a name="waf-response-for-blocked-requests"></a>Zapora aplikacji sieci Web odpowiedź zablokowane żądania

Domyślnie blokowaniu przez zaporę aplikacji sieci Web żądania z powodu dopasowane reguły zwraca kod stanu 403 z - **żądania jest zablokowany** wiadomości. Ciąg odwołania jest także zwracany do rejestrowania.

Gdy żądanie jest blokowany przez zaporę aplikacji sieci Web można zdefiniować kod stanu odpowiedzi niestandardowych i komunikatu odpowiedzi. Następujące kody stanu niestandardowe są obsługiwane:

- 200    OK
- 403 Zabroniony
- Metoda 405 nie jest dozwolona
- 406 Nie dopuszczalne
- 429 zbyt wiele żądań

Niestandardowe odpowiedzi kodu i odpowiedź komunikatu o stanie jest ustawienie poziomie zasad. Po skonfigurowaniu, wszystkie zablokowane żądania uzyskać samą stanu odpowiedzi niestandardowych i komunikatu odpowiedzi.

## <a name="uri-for-redirect-action"></a>Identyfikator URI przekierowania akcji

Konieczne jest określenie identyfikatora URI przekierowywania żądań do if **PRZEKIEROWANIA** wybrano akcję dla każdej z zasad zawartych w zasadach zapory aplikacji sieci Web. Przekierowanie to identyfikator URI musi być prawidłową witryną HTTP (S), a po skonfigurowaniu, wszystkie żądania reguł dopasowania za pomocą akcji "PRZEKIERUJ" nastąpi przekierowanie do określonej lokacji.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak zdefiniować zapory aplikacji sieci Web [odpowiedzi niestandardowych](waf-front-door-configure-custom-response-code.md)
