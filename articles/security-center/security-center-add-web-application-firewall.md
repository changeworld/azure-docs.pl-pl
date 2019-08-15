---
title: Dodawanie zapory aplikacji sieci Web w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób wdrażania Azure Security Center zalecenia **Dodaj zaporę aplikacji sieci Web** i **Zakończ ochronę aplikacji**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "60706241"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Dodawanie zapory aplikacji sieci Web w programie Azure Security Center
W Azure Security Center może być zalecane dodanie zapory aplikacji sieci Web (WAF) od partnera firmy Microsoft w celu zabezpieczenia aplikacji sieci Web. W tym dokumencie przedstawiono przykład sposobu zastosowania tego zalecenia.

Zalecenie WAF jest pokazane dla dowolnego publicznego adresu IP (na poziomie wystąpienia adresu IP lub IP ze zrównoważonym obciążeniem), który ma skojarzoną sieciową grupę zabezpieczeń z otwartymi portami sieci Web dla ruchu przychodzącego (80 443).

Security Center zaleca się, aby pomóc w obrony przed atakami ukierunkowanymi na aplikacje sieci Web na maszynach wirtualnych i zewnętrznych środowiskach App Service (ASE) wdrożonych [](https://azure.microsoft.com/pricing/details/app-service/windows/) w ramach planu usług izolowanych. Plan Izolowany umożliwia hostowanie aplikacji w prywatnym dedykowanym środowisku platformy Azure. Jest to doskonałe rozwiązanie w przypadku aplikacji, które wymagają bezpiecznego połączenia z siecią lokalną lub dodatkowej wydajności i skali. Oprócz aplikacji znajdującej się w środowisku izolowanym aplikacja musi mieć zewnętrzny adres IP modułu równoważenia obciążenia. Aby dowiedzieć się więcej na temat środowiska ASE, zapoznaj się z [dokumentacją App Service Environment](../app-service/environment/intro.md).

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnikiem krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
1. Wobszarze rekomendacje wybierz pozycję **bezpieczna aplikacja internetowa przy użyciu zapory aplikacji sieci Web**.
   ![Zabezpieczanie aplikacji sieci Web][1]
2. W obszarze **Zabezpieczanie aplikacji sieci Web przy użyciu zapory aplikacji sieci**Web wybierz aplikację sieci Web. **Dodaj zaporę aplikacji sieci Web** .
   ![Dodawanie zapory aplikacji internetowej][2]
3. Jeśli jest dostępna, możesz użyć istniejącej zapory aplikacji sieci Web lub utworzyć nową. W tym przykładzie nie ma dostępnych istniejących sieciowi, więc tworzymy WAF.
4. Aby utworzyć WAF, wybierz rozwiązanie z listy zintegrowanych partnerów. W tym przykładzie wybieramy **zaporę aplikacji sieci Web Barracuda**.
5. Zostanie otwarta **Zapora aplikacji sieci Web Barracuda** dostarczająca informacje o rozwiązaniu partnerskim. Wybierz pozycję **Utwórz**.

   ![Blok informacji o zaporze][3]

6. Zostanie otwarta **Nowa Zapora aplikacji sieci Web** , na której można wykonać kroki **konfiguracji maszyny wirtualnej** i podać **informacje WAF**. Wybierz pozycję **Konfiguracja maszyny wirtualnej**.
7. W obszarze **Konfiguracja maszyny wirtualnej**wprowadź informacje wymagane do uruchomienia maszyny wirtualnej, na której działa WAF.

   ![Konfiguracja maszyny wirtualnej][4]
   
8. Wróć do **nowej zapory aplikacji sieci Web** i wybierz pozycję **informacje WAF**. W obszarze **WAF Information (informacje**) skonfigurujesz sam WAF. Krok 7 pozwala skonfigurować maszynę wirtualną, na której uruchamiane są WAF i krok 8 pozwala na samodzielne udostępnienie WAF.

## <a name="finalize-application-protection"></a>Zakończ ochronę aplikacji
1. Wróć do **rekomendacji**. Wygenerowano nowy wpis po utworzeniu WAF o nazwie **Finalize Application Protection**. Ten wpis informuje o tym, że musisz ukończyć proces rzeczywistego przyłączenia WAF w ramach platformy Virtual Network Azure, aby umożliwić mu ochronę aplikacji.

   ![Zakończ ochronę aplikacji][5]

2. Wybierz pozycję **finalizowanie ochrony aplikacji**. Zostanie otwarty nowy blok. Zobaczysz, że istnieje aplikacja sieci Web, która musi mieć swój ruch kierowany.
3. Wybierz aplikację sieci Web. Zostanie otwarty blok z instrukcjami dotyczącymi kończenia instalacji zapory aplikacji sieci Web. Wykonaj kroki, a następnie wybierz pozycję **Ogranicz ruch**. Security Center następnie wykonuje okablowanie.

   ![Ogranicz ruch][6]

> [!NOTE]
> W Security Center można chronić wiele aplikacji sieci Web, dodając te aplikacje do istniejących wdrożeń WAF.
>
>

Dzienniki z tego WAF są teraz w pełni zintegrowane. Security Center może rozpocząć automatyczne zbieranie i analizowanie dzienników, dzięki czemu mogą one nawiązać ważne alerty zabezpieczeń.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób implementacji zalecenia Security Center "Dodawanie aplikacji sieci Web". Aby dowiedzieć się więcej o konfigurowaniu zapory aplikacji sieci Web, zobacz następujące tematy:

* [Konfigurowanie zapory aplikacji internetowej za pomocą środowiska App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — zawiera wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
