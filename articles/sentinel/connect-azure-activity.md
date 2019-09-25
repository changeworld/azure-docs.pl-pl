---
title: Łączenie danych aktywności platformy Azure z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane aktywności platformy Azure z wskaźnikiem kontrolnym platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 807a5ede3feee115b1a8dc51fe14966731fc7784
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240787"
---
# <a name="connect-data-from-azure-activity-log"></a>Połącz dane z dziennika aktywności platformy Azure



Dzienniki [aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md) można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia. Dziennik aktywności jest dziennikiem subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Obejmuje to szereg danych, od Azure Resource Manager dane operacyjne do aktualizacji na zdarzeniach Service Health. Korzystając z dziennika aktywności, można określić, kto i kiedy w każdej operacji zapisu (PUT, POST, DELETE) zajęło zasoby w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. Dziennik aktywności nie obejmuje operacji odczytu (GET) ani operacji dla zasobów, które używają modelu klasycznego/"FRONTONu". 


## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń


## <a name="connect-to-azure-activity-log"></a>Łączenie z dziennikiem aktywności platformy Azure

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Dziennik aktywności platformy Azure** .

2. W okienku dziennik aktywności platformy Azure wybierz subskrypcje, które chcesz przesłać do usługi Azure wskaźnikowej. 

3. Kliknij przycisk **Połącz**.

4. Aby użyć odpowiedniego schematu w Log Analytics dla alertów aktywności platformy Azure, Wyszukaj pozycję **Azure**.


 

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia dziennika aktywności platformy Azure z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
