---
title: Monitorowanie Eksploratora danych platformy Azure przy użyciu kondycji zasobów
description: Usługa Azure Resource Health służy do monitorowania zasobów eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: ee42f45d8c96e908061142b5a8e6b2e5cee21993
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262064"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Monitorowanie Eksploratora danych platformy Azure przy użyciu kondycji zasobów (wersja zapoznawcza)

[Kondycja zasobów](/azure/service-health/resource-health-overview) dla usługi Azure Data Explorer informuje o kondycji zasobu usługi Azure Data Explorer i zawiera zalecenia dotyczące rozwiązywania problemów. Kondycja zasobów jest aktualizowana co 1-2 minuty i raportuje bieżącą i przeszłą kondycję zasobów. 

Kondycja zasobów określa kondycję zasobu usługi Azure Data Explorer, badając różne kontrole stanu kondycji, takie jak:
* Dostępność zasobów
* Błędy zapytań

## <a name="access-resource-health-reporting"></a>Raportowanie kondycji zasobów programu Access

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz **pozycję Monitor** z listy usług.
1. Wybierz **pozycję Kondycja** > **zasobu kondycji usługi**.
1. W menu **rozwijanym Subskrypcja** wybierz subskrypcję. W **menu rozwijanym Typ zasobu** wybierz pozycję **Azure Data Explorer**.
1. Wynikowy tabela zawiera listę wszystkich zasobów w wybranej subskrypcji. Każdy zasób będzie miał ikonę stanu kondycji wskazującą kondycję zasobu.
1. Wybierz zasób, aby wyświetlić jego [stan kondycji zasobów](#resource-health-status) i zalecenia.

    ![Omówienie](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Stan kondycji zasobu

Kondycja zasobu jest wyświetlana z jednym z następujących stanów, dostępne, niedostępne i nieznane.

### <a name="available"></a>Dostępne

Stan kondycji **Dostępne** wskazuje, że zasób usługi Azure Data Explorer jest w dobrej kondycji i nie ma żadnych problemów.

![Dostępne](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Niedostępny

Stan kondycji **niedostępne** wskazuje, że istnieje ciągły problem z zasobem usługi Azure Data Explorer, który powoduje, że jest niedostępny dla zapytań i pozyskiwania. Na przykład węzły w zasobie Usługi Azure Data Explorer mogły nieoczekiwanie ponownie uruchomić komputer. Jeśli twój zasób usługi Azure Data Explorer pozostaje w tym stanie przez dłuższy czas, skontaktuj się z pomocą techniczną.

![Niedostępny](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Nieznane

Stan kondycji **Nieznany** wskazuje, że **kondycja zasobów** nie otrzymała informacji o tym zasobie usługi Azure Data Explorer przez więcej niż 10 minut. Ten stan nie jest ostatecznym wskazaniem kondycji zasobów usługi Azure Data Explorer, ale jest ważnym punktem danych w procesie rozwiązywania problemów. Jeśli klaster usługi Azure Data Explorer działa zgodnie z oczekiwaniami, stan zmieni się na **Dostępne** w ciągu kilku minut. **Nieznany** stan kondycji może sugerować, że zdarzenie w platformie ma wpływ na zasób. 

> [!TIP]
> Kondycja zasobu klastra usługi Azure Data Explorer będzie **nieznany,** jeśli jest w stanie zatrzymania.

![Nieznane](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Informacje historyczne

W **okienku Kondycja zasobów** > **historii kondycji**dostęp do maksymalnie czterech tygodni informacji o stanie kondycji zasobów. Wybierz strzałkę, aby uzyskać dodatkowe informacje na temat problemów ze zdarzeniami kondycji zgłoszonych w tym okienku. 

![Historia](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie alertów kondycji zasobów](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Samouczek: Pozyskiwania i wykonywania zapytań danych monitorowania danych w Eksploratorze danych platformy Azure](ingest-data-no-code.md)
* [Monitorowanie wydajności, kondycji i użycia usługi Azure Data Explorer za pomocą metryk](using-metrics.md)