---
title: Połącz dane Barracuda z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane Barracuda z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588488"
---
# <a name="connect-your-barracuda-appliance"></a>Podłącz urządzenie Barracuda 



Łącznik Zapory aplikacji sieci Web Barracuda (WAF) umożliwia łatwe łączenie dzienników Barracuda z usługą Azure Sentinel, wyświetlanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i usprawnianie badań. Zapewnia to lepszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Usługa Azure Sentinel korzysta z natywnej integracji między **agentem Barracuda** i log analytics, aby zapewnić bezproblemową integrację. 


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurowanie i podłączanie Barracuda WAF
Barracuda Web Application Firewall może integrować i eksportować dzienniki bezpośrednio do usługi Azure Sentinel za pośrednictwem agenta usługi Log Analytics.
1. Przejdź do [przepływu konfiguracji Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie, używając następujących parametrów:
    - **Identyfikator obszaru roboczego:** skopiuj wartość identyfikatora obszaru roboczego ze strony łącznika usługi Azure Sentinel Barracuda.
    - **Klucz podstawowy:** skopiuj wartość klucza podstawowego ze strony łącznika usługi Azure Sentinel Barracuda.
1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla zdarzeń Barracuda, wyszukaj **pole CommonSecurityLog** i **barracuda_CL**.


## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia Barracuda z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


