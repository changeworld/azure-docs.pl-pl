---
title: Monitorowanie kondycji zasobów Azure CDN | Dokumentacja firmy Microsoft
description: Informacje o sposobie monitorowania kondycji zasobów platformy Azure CDN przy użyciu kondycja zasobów Azure.
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
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842895"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorowanie kondycji zasobów Azure CDN
  
Kondycja zasobów sieci CDN w warstwie Azure jest podzbiorem [kondycji zasobów platformy Azure](../resource-health/resource-health-overview.md).  Kondycja zasobów Azure służy do monitorowania kondycji zasobów w sieci CDN i odbierania możliwością wskazówki dotyczące rozwiązywania problemów.

>[!IMPORTANT] 
>Kondycja zasobów usługi Azure CDN tylko aktualnie kont kondycji globalne dostarczania CDN i funkcje interfejsu API.  Kondycja zasobów usługi Azure CDN nie weryfikuje poszczególnych punktów końcowych usługi CDN.
>
>Sygnalizuje, że źródła danych usługi Azure CDN kondycja zasobów mogą być opóźnione do 15 minut.

## <a name="how-to-find-azure-cdn-resource-health"></a>Jak znaleźć kondycja zasobów Azure CDN

1. W [portalu Azure](https://portal.azure.com), przejdź do swojego profilu CDN.

2. Kliknij przycisk **ustawienia** przycisku.

    ![Przycisk ustawień](./media/cdn-resource-health/cdn-profile-settings.png)

3. W obszarze *pomocy technicznej i rozwiązywania problemów*, kliknij przycisk **kondycja zasobów**.

    ![Kondycja zasobów w sieci CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Możesz również znaleźć CDN zasobów wymienionych w *kondycja zasobów* kafelka w *Pomoc i obsługa techniczna* bloku.  Szybki dostęp do *Pomoc i obsługa techniczna* klikając kółku **?** w prawym górnym rogu portalu.
>
> ![Pomoc i obsługa techniczna](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN specyficzne wiadomości

Stany związane z usługi Azure CDN kondycja zasobów znajduje się poniżej.

|Komunikat | Zalecane działanie |
|---|---|
|Użytkownik może mieć zatrzymana, usunięte lub błędnie skonfigurowane co najmniej jednego z punktami końcowymi CDN | Użytkownik może mieć zatrzymana, usunięte lub błędnie skonfigurowane co najmniej jednego z punktami końcowymi CDN.|
|Niestety, Usługa zarządzania CDN jest obecnie niedostępna | Zaglądaj tu, aby zobaczyć aktualizacje stanu; Jeśli problem będzie występował po w oczekiwanym czasie rozpoznania, skontaktuj się z pomocą techniczną.|
|Niestety, punktami końcowymi CDN może mieć wpływ bieżących problemów niektóre z naszych dostawców usługi CDN | Zaglądaj tu, aby zobaczyć aktualizacje stanu; Użyj narzędzia do rozwiązywania problemów, aby dowiedzieć się jak przetestować punkt początkowy i punkt końcowy CDN; Jeśli problem będzie występował po w oczekiwanym czasie rozpoznania, skontaktuj się z pomocą techniczną. |
|Niestety, zmiany w konfiguracji punktu końcowego CDN występują opóźnienia w przepłynie | Zaglądaj tu, aby zobaczyć aktualizacje stanu; Zmiany w konfiguracji nie są w pełni propagowane w oczekiwanym czasie, należy się z pomocą techniczną.|
|Niestety, wystąpiły problemy podczas ładowania dodatkowego portalu | Zaglądaj tu, aby zobaczyć aktualizacje stanu; Jeśli problem będzie występował po w oczekiwanym czasie rozpoznania, skontaktuj się z pomocą techniczną.|
Niestety, wystąpiły problemy z niektórych naszych dostawców sieci CDN | Zaglądaj tu, aby zobaczyć aktualizacje stanu; Jeśli problem będzie występował po w oczekiwanym czasie rozpoznania, skontaktuj się z pomocą techniczną. |

## <a name="next-steps"></a>Następne kroki

- [Zapoznanie się z informacjami o kondycji zasobów platformy Azure](../resource-health/resource-health-overview.md)
- [Rozwiązywanie problemów z sieci CDN kompresji](./cdn-troubleshoot-compression.md)
- [Rozwiązywanie problemów z błędami 404](./cdn-troubleshoot-endpoint.md)