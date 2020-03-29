---
title: Monitorowanie kondycji zasobów usługi Azure cdn| Dokumenty firmy Microsoft
description: Dowiedz się, jak monitorować kondycję zasobów usługi Azure CDN przy użyciu usługi Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593436"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorowanie kondycji zasobów usługi Azure cdn
  
Kondycja zasobów usługi Azure CDN jest podzbiorem [kondycji zasobów platformy Azure.](../resource-health/resource-health-overview.md)  Kondycja zasobów platformy Azure służy do monitorowania kondycji zasobów usługi CDN i otrzymywania wskazówek dotyczących rozwiązywania problemów.

>[!IMPORTANT] 
>Kondycja zasobów usługi Azure CDN tylko obecnie uwzględnia kondycję globalnej sieci dostarczania sieci CDN i możliwości interfejsu API.  Kondycja zasobów usługi Azure CDN nie weryfikuje poszczególnych punktów końcowych usługi CDN.
>
>Sygnały, które zasilają kondycję zasobów usługi Azure CDN, mogą być opóźnione o maksymalnie 15 minut.

## <a name="how-to-find-azure-cdn-resource-health"></a>Jak znaleźć kondycję zasobów usługi Azure CDN

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do swojego profilu usługi CDN.

2. Kliknij przycisk **Ustawienia.**

    ![Przycisk Ustawienia](./media/cdn-resource-health/cdn-profile-settings.png)

3. W obszarze *Obsługa + rozwiązywanie problemów*kliknij pozycję **Kondycja zasobów**.

    ![Kondycja zasobów sieci CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Można również znaleźć zasoby sieci CDN wymienione w kafelku *Kondycja zasobów* w bloku *Pomoc + pomoc.*  Możesz szybko dostać się do *Pomocy + wsparcie,* klikając zakreślone? **?** w prawym górnym rogu portalu.
>
> ![Pomoc i obsługa techniczna](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Komunikaty specyficzne dla usługi Azure cdn

Stany związane z kondycją zasobów usługi Azure CDN można znaleźć poniżej.

|Komunikat | Zalecana akcja |
|---|---|
|Co najmniej jeden punkt końcowy sieci CDN mógł zostać zatrzymany, usunięty lub nieprawidłowo skonfigurowany | Co najmniej jeden punkt końcowy sieci CDN mógł zostać zatrzymany, usunięty lub nieprawidłowo skonfigurowany.|
|Niestety, usługa zarządzania sieci CDN jest aktualnie niedostępna | Sprawdź tutaj aktualizacje stanu; Jeśli problem będzie się powtarzał po oczekiwanym czasie rozwiązania, skontaktuj się z pomocą techniczną.|
|Niestety, na punkty końcowe sieci CDN mogą mieć wpływ trwające problemy z niektórymi z naszych dostawców sieci CDN | Sprawdź tutaj aktualizacje stanu; Użyj narzędzia Rozwiązywanie problemów, aby dowiedzieć się, jak przetestować pochodzenie i punkt końcowy sieci CDN; Jeśli problem będzie się powtarzał po oczekiwanym czasie rozwiązania, skontaktuj się z pomocą techniczną. |
|Niestety, w przypadku zmiany konfiguracji punktu końcowego sieci CDN występują opóźnienia propagacji | Sprawdź tutaj aktualizacje stanu; Jeśli zmiany konfiguracji nie są w pełni propagowane w oczekiwanym czasie, skontaktuj się z pomocą techniczną.|
|Niestety, wystąpiły problemy z ładowaniem portalu dodatkowego | Sprawdź tutaj aktualizacje stanu; Jeśli problem będzie się powtarzał po oczekiwanym czasie rozwiązania, skontaktuj się z pomocą techniczną.|
Niestety, występują problemy z niektórymi z naszych dostawców sieci CDN | Sprawdź tutaj aktualizacje stanu; Jeśli problem będzie się powtarzał po oczekiwanym czasie rozwiązania, skontaktuj się z pomocą techniczną. |

## <a name="next-steps"></a>Następne kroki

- [Przeczytaj omówienie kondycji zasobów platformy Azure](../resource-health/resource-health-overview.md)
- [Rozwiązywanie problemów z kompresją cdn](./cdn-troubleshoot-compression.md)
- [Rozwiązywanie problemów z 404 błędami](./cdn-troubleshoot-endpoint.md)