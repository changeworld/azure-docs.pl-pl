---
title: Dodawanie zapory aplikacji sieci web w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób implementowania zaleceń Centrum zabezpieczeń Azure **Dodaj zaporę aplikacji sieci web** i **finalizowanie ochrony aplikacji**.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706241"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Dodawanie zapory aplikacji sieci web w usłudze Azure Security Center
Usługa Azure Security Center może zalecić sytuacja: dodajesz zapory aplikacji sieci web (WAF) od partnera firmy Microsoft w celu zabezpieczenia aplikacji sieci web. Ten dokument zawiera opis przykładu przekonamy się sposób zastosowania tego zalecenia.

Dla wszelkich publicznych umożliwiający dostęp do Internetu adresu IP (adres IP na poziomie wystąpienia lub adres IP ze zrównoważonym obciążeniem) zawierającej sieciową grupę zabezpieczeń skojarzoną z portami Otwórz przychodzącego ruchu internetowego (80,443) jest wyświetlane zalecenie zapory aplikacji sieci Web.

Usługa Security Center zaleca obsługi administracyjnej zapory aplikacji sieci Web, aby pomóc Ci chronić przed atakami przeznaczone dla aplikacji sieci web na maszynach wirtualnych i na zewnętrzne środowisk usługi App Service (ASE) wdrożonej w ramach [izolowany](https://azure.microsoft.com/pricing/details/app-service/windows/) planu usług. Plan Izolowany umożliwia hostowanie aplikacji w prywatnym dedykowanym środowisku platformy Azure. Jest to doskonałe rozwiązanie w przypadku aplikacji, które wymagają bezpiecznego połączenia z siecią lokalną lub dodatkowej wydajności i skali. Oprócz aplikacji w izolowanym środowisku aplikacja musi mieć zewnętrzny adres IP modułu równoważenia obciążenia. Aby dowiedzieć się więcej na temat środowiska ASE, zobacz [dokumentacja środowiska usługi App Service](../app-service/environment/intro.md).

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W obszarze **zalecenia**, wybierz opcję **zabezpieczenie aplikacji sieci web przy użyciu zapory aplikacji sieci web**.
   ![Zabezpieczanie aplikacji sieci web][1]
2. W obszarze **zabezpieczaniu aplikacji sieci web przy użyciu zapory aplikacji sieci web**, wybierz aplikację sieci web. **Dodawanie zapory aplikacji sieci Web** zostanie otwarty.
   ![Dodawanie zapory aplikacji internetowej][2]
3. Możesz użyć istniejącej zapory aplikacji sieci web, jeśli jest dostępny, lub można utworzyć nowy. W tym przykładzie Brak dostępnych nie istniejących sieciowi, dzięki czemu możemy utworzyć zapory aplikacji sieci Web.
4. Aby utworzyć zapory aplikacji sieci Web, wybierz rozwiązanie z listy zintegrowanych partnerów. W tym przykładzie wybierzemy **zapory aplikacji internetowych Barracuda**.
5. **Zapora aplikacji sieci Web firmy barracuda** otwiera udostępnia informacji na temat rozwiązania partnerskiego. Wybierz pozycję **Utwórz**.

   ![Blok informacji Zapora][3]

6. **Nowa Zapora aplikacji sieci Web** zostanie otwarty, w którym można przeprowadzić **konfiguracji maszyny Wirtualnej** kroki i podaj **informacji zapory aplikacji sieci Web**. Wybierz **konfiguracji maszyny Wirtualnej**.
7. W obszarze **konfiguracji maszyny Wirtualnej**, wprowadź informacje wymagane do uruchomienia maszyny wirtualnej, która uruchamia zapory aplikacji internetowych.

   ![Konfiguracja maszyny Wirtualnej][4]
   
8. Wróć do **Nowa Zapora aplikacji sieci Web** i wybierz **informacji zapory aplikacji sieci Web**. W obszarze **informacji zapory aplikacji sieci Web**, konfigurowanie zapory aplikacji sieci Web, sam. Krok 7 można skonfigurować maszyny wirtualnej, na którym jest uruchamiany zapory aplikacji internetowych, a krok 8 umożliwia aprowizowanie zapory aplikacji sieci Web, sam.

## <a name="finalize-application-protection"></a>Finalizowanie ochrony aplikacji
1. Wróć do **zalecenia**. Wygenerowano nowy wpis, po utworzeniu aplikacji internetowych, o nazwie **finalizowanie ochrony aplikacji**. Ten wpis informuje o tym, musisz ukończyć proces faktycznie podłączyliśmy zapory aplikacji sieci Web w usłudze Azure Virtual Network tak, aby je chronić aplikację.

   ![Finalizowanie ochrony aplikacji][5]

2. Wybierz **finalizowanie ochrony aplikacji**. Zostanie otwarty nowy blok. Widać, że jest aplikacją sieci web, która musi mieć ruch skierowany.
3. Wybierz aplikację sieci web. Zostanie otwarty blok zawierający kroki dla finalizowanie konfiguracji zapory aplikacji sieci web. Wykonaj kroki, a następnie wybierz **ograniczania ruchu**. Usługa Security Center następnie wykonuje okablowania up.

   ![Ogranicz ruch][6]

> [!NOTE]
> Możesz chronić wiele aplikacji sieci web w usłudze Security Center, dodając te aplikacje do istniejących wdrożeń zapory aplikacji sieci Web.
>
>

Dzienniki, w tym zapory aplikacji sieci Web są teraz w pełni zintegrowane. Zbieranie i analizowanie dzienników tak, aby go powierzchni ważne alerty zabezpieczeń dla Ciebie automatycznie, można uruchomić usługi Security Center.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie pokazano sposób implementacji zalecenia usługi Security Center "Dodaj aplikację sieci web." Aby dowiedzieć się więcej na temat konfigurowania zapory aplikacji sieci web, zobacz następujące tematy:

* [Konfigurowanie zapory aplikacji internetowej za pomocą środowiska App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
