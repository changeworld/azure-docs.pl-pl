---
title: Łączenie danych zscaler z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane Zscaler z usługą Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587995"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Łączenie dostępu internetowego Zscaler z usługą Azure Sentinel

> [!IMPORTANT]
> Łącznik danych Zscaler w usłudze Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak połączyć urządzenie Zscaler Internet Access z usługą Azure Sentinel. Zscaler łącznik danych umożliwia łatwe połączenie dzienników Zscaler Internet Access (ZIA) z usługą Azure Sentinel, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i usprawnić badania. Korzystanie z programu Zscaler na platformie Azure Sentinel zapewni Ci więcej informacji na temat korzystania z Internetu w organizacji i zwiększy jej możliwości obsługi zabezpieczeń. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Konfigurowanie programu Zscaler do wysyłania wiadomości CEF

1. Na urządzeniu Zscaler należy ustawić te wartości, tak aby urządzenie wysyła niezbędne dzienniki w formacie niezbędnym do agenta usługi Azure Syslog, na podstawie agenta usługi Log Analytics. Można zmodyfikować te parametry w urządzeniu, tak długo, jak można również zmodyfikować je w demon syslogu na agenta wartowniczych platformy Azure.
    - Protokół = TCP
    - Port = 514
    - Format = CEF
    - Adres IP — upewnij się, że wysyłasz wiadomości CEF na adres IP maszyny wirtualnej, którą przeznaczyłeś w tym celu.
 Aby uzyskać więcej informacji, zobacz [Przewodnik wdrażania zscaler i usługi Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > To rozwiązanie obsługuje Syslog RFC 3164 lub RFC 5424.


1. Aby użyć odpowiedniego schematu w usłudze Log Analytics `CommonSecurityLog`dla zdarzeń cef, wyszukaj .
1. Przejdź do [kroku 3: Sprawdź poprawność łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć zscaler dostęp internetowy do usługi Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


