---
title: Dodawanie zapory aplikacji sieci web w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument zawiera implementowania zaleceń Centrum zabezpieczeń Azure **Dodawanie zapory aplikacji sieci web** i **Finalizuj ochronę aplikacji**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2018
ms.author: terrylan
ms.openlocfilehash: e28a1f6b865dae3abe2cb9dfac2921c6a2034491
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Dodawanie zapory aplikacji sieci web w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure może zalecić Dodawanie zapory aplikacji sieci web (WAF) z partnerem firmy Microsoft w celu zabezpieczenia aplikacji sieci web. Ten dokument przeprowadzi Cię przez przykładem zastosowania tego zalecenia.

Wszelkie publicznego połączonej adresu IP (IP poziomie wystąpienia lub IP równoważenia obciążenia) zawierający sieciową grupę zabezpieczeń skojarzoną z portami Otwórz przychodzący sieci web (80,443) jest wyświetlane zalecenie zapory aplikacji sieci Web.

Centrum zabezpieczeń zaleca obsługi administracyjnej zapory aplikacji sieci Web pomagających chronić przed atakami przeznaczonych dla aplikacji sieci web na maszynach wirtualnych i na usługi aplikacji zewnętrznych środowisk (ASE) wdrożone w obszarze [izolowany](https://azure.microsoft.com/pricing/details/app-service/windows/) plan usługi. Plan Izolowany umożliwia hostowanie aplikacji w prywatnym dedykowanym środowisku platformy Azure. Jest to doskonałe rozwiązanie w przypadku aplikacji, które wymagają bezpiecznego połączenia z siecią lokalną lub dodatkowej wydajności i skali. Oprócz aplikacji w izolowanym środowisku aplikacja musi mieć zewnętrzny adres IP usługi równoważenia obciążenia. Aby dowiedzieć się więcej na temat ASE, zobacz [dokumentację środowiska usługi aplikacji](../app-service/environment/intro.md).

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
1. W obszarze **zalecenia**, wybierz pozycję **zabezpieczenia aplikacji sieci web za pomocą zapory aplikacji sieci web**.
   ![Zabezpieczanie aplikacji sieci web][1]
2. W obszarze **zabezpieczenia aplikacji sieci web za pomocą zapory aplikacji sieci web**, wybierz aplikację sieci web. **Dodawanie zapory aplikacji sieci Web** otwiera.
   ![Dodawanie zapory aplikacji sieci Web][2]
3. Możesz użyć istniejących zapory aplikacji sieci web, jeśli jest dostępny, lub można utworzyć nowy. W tym przykładzie nie dostępnych żadnych istniejących WAFs, utworzymy zapory aplikacji sieci Web.
4. Aby utworzyć zapory aplikacji sieci Web, należy wybrać z listy partnerów zintegrowane rozwiązanie. W tym przykładzie mamy wybierz **zapory aplikacji sieci Web Barracuda**.
5. **Zapora aplikacji sieci Web barracuda** otwiera informacjami o rozwiązaniem partnerskim. Wybierz pozycję **Utwórz**.

   ![Blok informacji zapory][3]

6. **Zapora aplikacji sieci Web** otwiera, w którym można przeprowadzić **konfiguracji maszyny Wirtualnej** kroki i podaj **informacji zapory aplikacji sieci Web**. Wybierz **konfiguracji maszyny Wirtualnej**.
7. W obszarze **konfiguracji maszyny Wirtualnej**, wprowadź informacje wymagane do aż maszynę wirtualną, która uruchamia zapory aplikacji sieci Web.
   ![Konfiguracja maszyny Wirtualnej][4]
8. Wróć do **Nowa Zapora aplikacji sieci Web** i wybierz **informacji zapory aplikacji sieci Web**. W obszarze **informacji zapory aplikacji sieci Web**, należy skonfigurować zapory aplikacji sieci Web, sama. Krok 7 można skonfigurować maszyny wirtualnej, na którym działa zapory aplikacji sieci Web i krok 8 umożliwia udostępnianie zapory aplikacji sieci Web, sama.

## <a name="finalize-application-protection"></a>Finalizowanie ochrony aplikacji
1. Wróć do **zalecenia**. Wygenerowano nowy wpis po utworzeniu zapory aplikacji sieci Web, nazywany **Finalizuj ochronę aplikacji**. Ten wpis informuje o tym, trzeba wykonać proces faktycznie Podłączanie zapory aplikacji sieci Web w sieci wirtualnej platformy Azure, dzięki czemu można chronić aplikacji.

   ![Finalizowanie ochrony aplikacji][5]

2. Wybierz **Finalizuj ochronę aplikacji**. Zostanie otwarty nowy blok. Widać, czy istnieje aplikacja sieci web, która musi mieć ruch przekierowane.
3. Wybierz aplikację sieci web. Zostanie otwarty blok zawierający kroki dla finalizowanie konfiguracji zapory aplikacji sieci web. Wykonaj kroki, a następnie wybierz **ograniczania ruchu**. Centrum zabezpieczeń następnie wykonuje w górę okablowania.

   ![Ogranicz ruch][6]

> [!NOTE]
> Wiele aplikacji sieci web w Centrum zabezpieczeń można chronić, dodając te aplikacje do istniejących wdrożeń zapory aplikacji sieci Web.
>
>

Dzienniki z tym zapory aplikacji sieci Web teraz są w pełni zintegrowane. Centrum zabezpieczeń można uruchomić automatycznie zbierania i analizowania dzienników, aby go powierzchni alerty zabezpieczeń ważnych dla Ciebie.

## <a name="next-steps"></a>Kolejne kroki
Ten dokument pokazano sposób wykonania zalecenia Centrum zabezpieczeń "Dodaj aplikację sieci web". Aby dowiedzieć się więcej o konfigurowaniu zapory aplikacji sieci web, zobacz następujące tematy:

* [Konfigurowanie zapory aplikacji sieci Web (WAF) do środowiska usługi aplikacji](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
