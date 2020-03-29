---
title: Łączenie danych aktywności platformy Azure z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane aktywności platformy Azure z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124985"
---
# <a name="connect-data-from-azure-activity-log"></a>Łączenie danych z dziennika aktywności platformy Azure

Dzienniki [usługi Azure Activity](../azure-monitor/platform/platform-logs-overview.md) można przesyłać strumieniowo za pomocą jednego kliknięcia. Dziennik aktywności to dziennik subskrypcji, który rejestruje i wyświetla zdarzenia na poziomie subskrypcji na platformie Azure, od danych operacyjnych usługi Azure Resource Manager po aktualizacje zdarzeń kondycji usługi. Za pomocą dziennika działania, można określić "co, kto i kiedy" dla każdej operacji zapisu (PUT, POST, DELETE) wykonywane na zasoby w ramach subskrypcji. Można również dowiedzieć się o stanie operacji i innych odpowiednich właściwości. Dziennik działania nie zawiera operacji odczytu (GET) ani operacji dla zasobów korzystających z modelu Classic/"RDFE". 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia współautora do obszaru roboczego usługi Log Analytics.
- Użytkownik musi mieć uprawnienia czytnika do dowolnej subskrypcji, której dzienniki chcesz przesyłać strumieniowo do usługi Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Konfigurowanie łącznika aktywności platformy Azure

1. Z menu nawigacji usługi Azure Sentinel wybierz polecenie **Łączniki danych**. Z listy łączników kliknij pozycję **Działanie platformy Azure**, a następnie przycisk Otwórz stronę **łącznika** w prawym dolnym dolnym przycisku.

2. W obszarze **Instrukcje** kartę kliknij **łącze Konfigurowanie dzienników aktywności platformy Azure >.**

3. W okienku **dziennika aktywności platformy Azure** wybierz subskrypcje, których dzienniki chcesz przesyłać strumieniowo do usługi Azure Sentinel. 

4. W okienku subskrypcji otwierany po prawej stronie kliknij pozycję **Połącz**.

5. Aby użyć odpowiedniego schematu w usłudze Log `AzureActivity` Analytics dla alertów aktywności platformy Azure, wpisz w oknie kwerendy.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć dziennik aktywności platformy Azure z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij wykrywanie zagrożeń za pomocą usługi Azure Sentinel przy użyciu [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.
