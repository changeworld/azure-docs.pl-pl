---
title: Monitorowanie kondycji zasobów usługi Azure CDN | Dokumentacja firmy Microsoft
description: Informacje o sposobie monitorowania kondycji zasobów usługi Azure CDN przy użyciu usługi Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad4bf7ae97a08f89b9d82e1d4e025a5bd5d47fc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324693"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorowanie kondycji zasobów usługi Azure CDN
  
Kondycja zasobów CDN Azure jest podzbiorem [usługa Azure resource health](../resource-health/resource-health-overview.md).  Usługa Azure resource health służy do monitorowania kondycji zasobów CDN i odbierania wiarygodne wskazówki dotyczące rozwiązywania problemów.

>[!IMPORTANT] 
>Kondycja zasobów w usłudze Azure CDN tylko obecnie kont kondycji globalne dostarczanie CDN i funkcje interfejsu API.  Kondycja zasobów w usłudze Azure CDN nie weryfikuje poszczególnych punktów końcowych usługi CDN.
>
>Sygnalizuje, że źródło danych kondycja zasobów Azure CDN może być opóźniona do 15 minut.

## <a name="how-to-find-azure-cdn-resource-health"></a>Jak znaleźć kondycja zasobów Azure CDN

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego profilu CDN.

2. Kliknij przycisk **ustawienia** przycisku.

    ![Przycisk Ustawienia](./media/cdn-resource-health/cdn-profile-settings.png)

3. W obszarze *pomoc techniczna i rozwiązywanie problemów z*, kliknij przycisk **Resource health**.

    ![Kondycja zasobów w sieci CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Możesz również znaleźć CDN zasobów wymienionych w *Resource health* Kafelek w *Pomoc i obsługa techniczna* bloku.  Szybki dostęp do *Pomoc i obsługa techniczna* , klikając kółku **?** w prawym górnym rogu portalu.
>
> ![Pomoc i obsługa techniczna](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Usługa Azure komunikaty dotyczące usługi CDN

Stany dotyczące kondycji zasobów usługi Azure CDN można znaleźć poniżej.

|Komunikat | Zalecana akcja |
|---|---|
|Co najmniej jeden punkt końcowy sieci CDN mógł zostać zatrzymany, usunięty lub nieprawidłowo skonfigurowany | Co najmniej jeden punkt końcowy sieci CDN mógł zostać zatrzymany, usunięty lub nieprawidłowo skonfigurowany.|
|Niestety, usługa zarządzania sieci CDN jest aktualnie niedostępna | Sprawdź tutaj aktualizacje stanu; Jeśli problem będzie nadal występować po oczekiwanym czasie rozwiązania problemu, skontaktuj się z pomocą techniczną.|
|Niestety, na punkty końcowe sieci CDN mogą mieć wpływ trwające problemy z niektórymi z naszych dostawców sieci CDN | Sprawdź tutaj aktualizacje stanu; Użyj narzędzia do rozwiązywania problemów, aby dowiedzieć się jak testować pochodzenie i punkt końcowy usługi CDN; Jeśli problem będzie nadal występować po oczekiwanym czasie rozwiązania problemu, skontaktuj się z pomocą techniczną. |
|Niestety, w przypadku zmiany konfiguracji punktu końcowego sieci CDN występują opóźnienia propagacji | Sprawdź tutaj aktualizacje stanu; Jeśli zmiany konfiguracji nie są w pełni rozpropagowane w oczekiwanym czasie, skontaktuj się z pomocą techniczną.|
|Niestety, wystąpiły problemy z ładowaniem portalu dodatkowego | Sprawdź tutaj aktualizacje stanu; Jeśli problem będzie nadal występować po oczekiwanym czasie rozwiązania problemu, skontaktuj się z pomocą techniczną.|
Niestety, występują problemy z niektórymi z naszych dostawców sieci CDN | Sprawdź tutaj aktualizacje stanu; Jeśli problem będzie nadal występować po oczekiwanym czasie rozwiązania problemu, skontaktuj się z pomocą techniczną. |

## <a name="next-steps"></a>Kolejne kroki

- [Zawiera omówienie usługi Azure resource health](../resource-health/resource-health-overview.md)
- [Rozwiązywanie problemów z kompresją CDN](./cdn-troubleshoot-compression.md)
- [Rozwiązywanie problemów z błędami 404](./cdn-troubleshoot-endpoint.md)