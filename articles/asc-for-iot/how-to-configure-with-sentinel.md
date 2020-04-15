---
title: Konfigurowanie wartownika (wersja zapoznawcza)
description: W tym artykule wyjaśniono, jak skonfigurować usługę Azure Sentinel do odbierania danych z usługi Azure Security Center dla rozwiązania IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: fe8e4c1b08f96e5f6b2fc7649f7a4361616b7c87
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311316"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Łączenie danych z usługi Azure Security Center dla IoT z usługą Azure Sentinel (wersja zapoznawcza)

> [!IMPORTANT]
> Usługa Azure Security Center dla łącznika danych IoT w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku dowiesz się, jak połączyć usługę Azure Security Center dla danych IoT z usługą Azure Sentinel.

> [!div class="checklist"]
> * Wymagania wstępne
> * Ustawienia połączenia
> * Widok alertu usługi Log Analytics

Połącz alerty z usługi Azure Security Center dla IoT i przesyłaj je bezpośrednio do usługi Azure Sentinel.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do **odczytu** i **zapisu** obszaru roboczego.
- **Usługa Azure Security Center for IoT** musi być włączona w odpowiednich centrach IoT Hub.Azure Security Center for IoT must be **enabled** on your relevant IoT Hub(s).
- Musisz mieć uprawnienia do **odczytu** i **zapisu** w **centrum Usługi Azure IoT,** z którą chcesz się połączyć.
- W **grupie zasobów usługi Azure IoT Hub**muszą być również uprawnienia do **odczytu** i **zapisu.**

> [!NOTE]
> Aby wysyłać alerty o zasobach platformy Azure Resource Standard, musisz mieć uruchomione licencjonowanie warstwy Azure Security Center Standard w ramach subskrypcji. Dzięki bezpłatnemu licencjonowaniu warstwy wymaganemu dla usługi Azure Security Center dla IoT tylko usługi Azure Security Center dla alertów związanych z IoT będą przekazywane do usługi Azure Sentinel.

## <a name="connect-to-azure-security-center-for-iot"></a>Łączenie się z centrum zabezpieczeń platformy Azure dla usługi IoT

1. W usłudze Azure Sentinel wybierz **łączniki danych,** a następnie kliknij kafelek **Usługi Azure Security Center dla IoT.**
1. U dołu prawego okienka kliknij pozycję **Otwórz stronę łącznika**.
1. Kliknij **przycisk Połącz**, obok każdej subskrypcji usługi IoT Hub, której alerty i alerty o urządzeniu chcesz przesyłać strumieniowo do usługi Azure Sentinel.
    - Jeśli usługa Azure Security Center dla IoT nie jest włączona w tym centrum, zostanie wyświetlony komunikat ostrzeżenia Włącz. Kliknij **łącze Włącz,** aby uruchomić i włączyć usługę.
1. Możesz zdecydować, czy alerty z usługi Azure Security Center dla IoT mają automatycznie generować zdarzenia w usłudze Azure Sentinel. W obszarze **Tworzenie zdarzeń**wybierz pozycję **Włącz,** aby włączyć regułę do automatycznego tworzenia zdarzeń z wygenerowanych alertów.  Tę regułę można zmienić lub edytować w regułach **Analytics** > **Active.**

> [!NOTE]
>Odświeżenie listy koncentratorów może potrwać co najmniej 10 sekund po dokonaniu zmian w połączeniu.

## <a name="log-analytics-alert-display"></a>Wyświetlanie alertów usługi Log Analytics

Aby użyć odpowiedniego schematu w usłudze Log Analytics do wyświetlania alertów usługi Azure Security Center dla alertów IoT:

1. Otwórz **dzienniki** > **SecurityInsights** > **SecurityAlert**lub wyszukaj **SecurityAlert**.
1. Filtruj, aby wyświetlić tylko usługi Azure Security Center dla alertów generowanych przez IoT przy użyciu następującego filtru kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Uwagi dotyczące usług

Po nawiązaniu połączenia usługi IoT Hub dane koncentratora są dostępne w usłudze Azure Sentinel około 15 minut później.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć usługę Azure Security Center dla usługi IoT z usługą Azure Sentinel. Aby dowiedzieć się więcej o wykrywaniu zagrożeń i dostępie do danych zabezpieczeń, zobacz następujące artykuły:

- Dowiedz się, jak korzystać z usługi Azure Sentinel, aby [uzyskać wgląd w dane i potencjalne zagrożenia.](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)

- Dowiedz się, jak uzyskać dostęp do [danych zabezpieczeń IoT](how-to-security-data-access.md)
