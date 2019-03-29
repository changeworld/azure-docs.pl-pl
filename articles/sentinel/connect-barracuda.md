---
title: Zbieranie danych Barracuda w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane Barracuda w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b4d1830e705e1813c8448787e587d720eaf5ddbd
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574722"
---
# <a name="connect-your-barracuda-appliance"></a>Połącz urządzenie Barracuda 

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Barracuda zapory aplikacji sieci Web (WAF) łącznik umożliwia łatwe łączenie dzienników Barracuda za pomocą usługi Azure przez wartownika, aby wyświetlić pulpity nawigacyjne, tworzyć niestandardowe alerty i lepsze badanie. To zapewnia lepszy wgląd w sieci swojej organizacji i zwiększa możliwości operacji zabezpieczeń. Wartownik Azure wykorzystuje natywna integracja pomiędzy usługą **Barracuda** i Microsoft Azure OMS, aby zapewnić bezproblemową integrację. 


> [!NOTE]
> 
> - Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="configure-and-connect-barracuda-waf"></a>Skonfigurować i połączyć z zapory aplikacji internetowych Barracuda
Zapory aplikacji internetowych barracuda można integrować i Eksportuj dzienniki bezpośrednio do [ASI] za pomocą serwera usługi Azure pakietu OMS.
1. Przejdź do [przepływu konfiguracji zapory aplikacji internetowych Barracuda](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie przy użyciu tych parametrów:
    - **Identyfikator obszaru roboczego**: Skopiuj wartość Identyfikatora obszaru roboczego ze strony łącznika Azure przez wartownika Barracuda.
    - **Klucz podstawowy**: Skopiuj wartość klucz podstawowy na stronie łącznika Azure przez wartownika Barracuda.
2. W portalu Azure przez wartownika przejdź do obszaru roboczego, na którym wdrożono przez wartownika platformy Azure i wybierz wielokropek (...) na końcu wiersza i wybierz pozycję **Zaawansowane ustawienia**. 
1. Wybierz **danych** i następnie **Syslog**.
1. Upewnij się, funkcji, można ustawić w Barracuda istnieje i ustawić ważność i kliknij przycisk **Zapisz**.
6. Aby użyć odpowiednich schematu w usłudze Log Analytics dla zdarzeń Barracuda, wyszukaj **CommonSecurityLog**.


## <a name="validate-connectivity"></a>Zweryfikuj łączność

Może upłynąć zgłaszane 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń Barracuda na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

