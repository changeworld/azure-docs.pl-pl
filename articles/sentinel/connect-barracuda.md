---
title: Łączenie danych Barracuda z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane Barracuda z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: b3ca93d9e70456d25d5f78b2ca1fde8e4ea24f8d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240201"
---
# <a name="connect-your-barracuda-appliance"></a>Łączenie urządzenia z programem Barracuda 



Łącznik zapory aplikacji sieci Web Barracuda (WAF) umożliwia łatwe łączenie dzienników Barracuda z platformą Azure, w celu wyświetlania pulpitów nawigacyjnych, tworzenia alertów niestandardowych i ulepszania badania. Zapewnia to dokładniejszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Korzystanie z platformy Azure w celu zapewnienia bezproblemowej integracji obejmuje natywną integrację między **Barracuda** i agentem log Analytics. 


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurowanie i łączenie rozwiązania Barracuda WAF
Zapora aplikacji sieci Web Barracuda umożliwia integrację i eksportowanie dzienników bezpośrednio do funkcji wskaźnikowej platformy Azure za pośrednictwem agenta Log Analytics.
1. Przejdź do [przepływu konfiguracji usługi Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie przy użyciu następujących parametrów:
    - **Identyfikator obszaru roboczego**: Skopiuj wartość identyfikatora obszaru roboczego ze strony łącznika usługi Azure nazwa_obszaru_roboczego Barracuda.
    - **Klucz podstawowy**: Skopiuj wartość klucza podstawowego ze strony łącznika usługi Azure wskaźnik Barracuda.
2. W portalu wskaźnikowym platformy Azure przejdź do obszaru roboczego, w którym wdrożono platformę Azure, i wybierz wielokropek (...) na końcu wiersza, a następnie wybierz pozycję **Ustawienia zaawansowane**. 
1. Wybierz pozycję **dane** , a następnie **Dziennik**systemowy.
1. Upewnij się, że obiekt ustawiony w Barracuda istnieje i ustaw ważność, a następnie kliknij przycisk **Zapisz**.
6. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń Barracuda, wyszukaj ciąg **CommonSecurityLog** i **barracuda_CL**.


## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń Barracuda z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

