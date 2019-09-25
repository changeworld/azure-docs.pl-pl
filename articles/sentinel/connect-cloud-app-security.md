---
title: Łączenie Cloud App Security danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć Cloud App Security dane z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240120"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Łączenie danych z Microsoft Cloud App Security 



Można przesyłać strumieniowo dzienniki z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do usługi Azure wskaźnikowego za pomocą jednego kliknięcia. To połączenie umożliwia przesyłanie strumieniowe alertów z Cloud App Security do usługi Azure wskaźnikowej. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń

## <a name="connect-to-cloud-app-security"></a>Połącz z Cloud App Security

Jeśli masz już Cloud App Security, upewnij się, że jest [włączona w sieci](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Jeśli Cloud App Security zostanie wdrożona i pozyskuje dane, dane alertów można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Cloud App Security** .

1. Wybierz dzienniki, które chcesz przesłać do usługi Azure wskaźnikowego, możesz wybrać **alerty**. 

1. Możesz wybrać, czy alerty od Microsoft Cloud App Security mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

1. Kliknij przycisk **Połącz**.

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Cloud App Security, Wyszukaj pozycję **SecurityAlert**.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Microsoft Cloud App Security z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
