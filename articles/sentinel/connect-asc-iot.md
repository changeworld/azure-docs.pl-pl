---
title: Łączenie usługi Azure Security Center dla usługi IoT z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć usługę Azure Security Center dla danych IoT z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588641"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Łączenie danych z usługi Azure Security Center dla usługi IoT z usługą Azure Sentinel 


> [!IMPORTANT]
> Łącznik danych Usługi Azure Security Center dla IoT jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Za pomocą łącznika Usługi Azure Security Center dla IoT można przesyłać strumieniowo wszystkie zdarzenia usługi Azure Security Center dla zdarzeń IoT do usługi Azure Sentinel. 

## <a name="prerequisites"></a>Wymagania wstępne

- Uprawnienia do **odczytu** i **zapisu** w obszarze roboczym, na którym wdrożono usługę Azure Sentinel
- **Usługa Azure Security Center dla IoT** musi być **włączona** w odpowiednich centrach IoT Hub
- Uprawnienia do **odczytu** i **zapisu** w **centrum Usługi Azure IoT,** z którą chcesz się połączyć
- Uprawnienia do **odczytu** i **zapisu** w **grupie zasobów usługi Azure IoT Hub**

> [!NOTE]
> Chociaż należy włączyć licencję **warstwy** Standard usługi Azure Security Center w ramach subskrypcji, aby przesyłać strumieniowo alerty zasobów IoT do usługi Azure Sentinel, wystarczy włączyć licencję warstwy **Bezpłatna** usługa Azure Security Center w ramach subskrypcji, aby wyświetlić usługi Azure Security Center dla alertów IoT w usłudze Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Łączenie się z centrum zabezpieczeń platformy Azure dla usługi IoT

1. W usłudze Azure Sentinel wybierz **łączniki danych,** a następnie kliknij kafelek **Usługi Azure Security Center dla IoT.**
1. W prawym dolnym okienku kliknij pozycję **Otwórz stronę łącznika**. 
1. Kliknij **przycisk Połącz**, obok każdej subskrypcji usługi IoT Hub, której alerty i alerty o urządzeniu chcesz przesyłać strumieniowo do usługi Azure Sentinel. 
    - Jeśli usługa Azure Security Center dla IoT nie jest włączona w tym centrum, zostanie wyświetlony komunikat ostrzeżenia **Włącz.** Kliknij **łącze Włącz,** aby uruchomić usługę. 
1. Możesz zdecydować, czy alerty z usługi Azure Security Center dla IoT mają automatycznie generować zdarzenia w usłudze Azure Sentinel. W obszarze **Tworzenie zdarzeń**wybierz pozycję **Włącz,** aby włączyć domyślną regułę analityczną do automatycznego tworzenia zdarzeń z alertów generowanych w połączonej usłudze zabezpieczeń. Tę regułę można zmienić lub edytować w regułach **Analytics** > **Active.**

> [!NOTE]
> Odświeżenie listy koncentratorów może zająć trochę czasu po dokonaniu zmian w połączeniu. 

## <a name="log-analytics-alert-display"></a>Wyświetlanie alertów usługi Log Analytics

Aby użyć odpowiedniego schematu w usłudze Log Analytics do wyświetlania alertów usługi Azure Security Center dla alertów IoT:

1. Otwórz **dzienniki** > **SecurityInsights** > **SecurityAlert**lub wyszukaj **SecurityAlert**. 
2. Filtruj, aby wyświetlić tylko usługi Azure Security Center dla alertów generowanych przez IoT przy użyciu następującego filtru kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Uwagi dotyczące usług

Po nawiązaniu połączenia usługi IoT Hub dane koncentratora są dostępne w usłudze Azure Sentinel około 15 minut później.


## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć usługę Azure Security Center dla danych IoT z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)
