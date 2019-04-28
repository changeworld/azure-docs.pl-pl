---
title: Integracja bramy aplikacji przy użyciu usługi Azure Security Center | Dokumentacja firmy Microsoft
description: Ta strona zawiera informacje dotyczące sposobu Application Gateway jest zintegrowany z usługi Azure Security Center.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: victorh
ms.openlocfilehash: 10f115b64f0bd3f7e557da2bedbf3327d0ef483d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122303"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Omówienie integracji między bramy aplikacji i usługi Azure Security Center

Dowiedz się, jak usługa Application Gateway i usługi Security Center chronić zasobów aplikacji sieci web. Zapory aplikacji sieci web (WAF) Application gateway integruje się z [usługi Security Center](../security-center/security-center-intro.md) zapewnienia bezproblemowego widoku, aby zapobiec, wykrywanie i reagowanie na zagrożenia do niechronionych aplikacji internetowych w danym środowisku.

## <a name="overview"></a>Omówienie

Brama aplikacji zapory aplikacji internetowych jest zalecenia w usłudze Security Center chroni aplikacje sieci web przed programami wykorzystującymi luki i lukami w zabezpieczeniach. Zasoby sieci Web jest włączona, które nie są chronione przez zaporę aplikacji sieci Web są wyświetlane w usłudze security center jako zalecenia o wysokim znaczeniu. Zalecenia dotyczące zapory aplikacji sieci web są wyświetlane w **Przegląd** w obszarze **aplikacji**.

![Integracja z usługą security center][1]

Kliknięcie żadnych zaleceń, że dotyczące zapory aplikacji sieci web otwiera nową stronę przedstawiający szczegóły zalecenia.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Dodawanie zapory aplikacji sieci web do istniejącego zasobu

Przejdź do **wszystkich usług** > **bezpieczeństwo i Obsługa tożsamości** > **usługi Security Center** i **Security Center — Przegląd**, kliknij przycisk **aplikacji**. Na **Security Center — aplikacje**, tabela zawiera listę aplikacji, które usługa Security Center wykryła w Twojej subskrypcji.

![aplikacje internetowe][3]

Klikając w aplikacji sieci web dzięki rozwiązaniu krytycznego problemu, możesz uzyskać **kondycja zabezpieczeń aplikacji** strony. Na poniższej ilustracji, aplikacji sieci web, które nie są chronione przez zaporę aplikacji sieci web. 

![zasoby sieci Web, które nie są chronione][2]

Kliknij przycisk **Dodaj zaporę aplikacji sieci web** w obszarze **zalecenia** otworzyć **Dodaj zaporę aplikacji sieci Web** strony.

Jeśli nie masz istniejącej bramy aplikacji lub chcesz utworzyć nowe konto, kliknij przycisk **Utwórz nowy** i **Tworzenie nowej zapory aplikacji sieci Web**i kliknij przycisk **Microsoft — usługa Application Gateway** . To przeprowadzi Cię przez kroki, aby utworzyć bramę aplikacji. W tym momencie aplikacji sieci web jest dodawany jako chronionego zasobu, a teraz usługę Security Center śledzi, że ten zasób jest chroniony przez zaporę aplikacji sieci web. To nie można dodać go jako składową puli zaplecza.

Jeśli masz istniejącą bramę aplikacji, możesz wybrać go w folderze **używanie istniejącego rozwiązania**

![Strona Dodawanie zapory aplikacji sieci Web][4]

Dodawanie aplikacji internetowej bramy aplikacji za pomocą usługi Security Center nie powoduje dodania zasobu jako składową puli zaplecza. Jest to niezbędne w zasobie bramy aplikacji bezpośrednio.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Dodaj zasób do istniejącej zapory aplikacji sieci web

Przejdź do **wszystkich usług** > **bezpieczeństwo i Obsługa tożsamości** > **usługi Security Center** i **Security Center — Przegląd**, kliknij przycisk **rozwiązania partnerskie**. Istniejące bramy aplikacji obsługującej usługi Security Center, Pokaż w **rozwiązań partnerskich** strony.

![Rozwiązania partnerów][7]

Kliknij przycisk **Połącz aplikację** otworzyć **łączenie aplikacji**, w tym miejscu są podane opcje, aby wybrać istniejące aplikacje. Wybierz aplikacje do ochrony i kliknij przycisk **OK**. Nie powoduje dodania aplikacji sieci web do puli zaplecza bramy aplikacji. Spowoduje to ustawienie zasobów jako chronionego zasobu, dzięki temu usługa Security Center można go. Można dodać zasobu jako składową puli zaplecza, należy to zrobić w usłudze application gateway, z bieżącej strony, możesz kliknąć pozycję **konsoli rozwiązania** podjąć w celu zasobu bramy aplikacji, w którym można dodać aplikację sieci web Pula zaplecza.

![aplikacje rozwiązania partnerskie][6]

## <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Usługa Security Center będzie śledził aplikacje dodawane do bramy aplikacji jako chronionego zasobu.  Monitoruje kondycję tego zasobu i gwarantuje, że jest chroniony przez usługę application gateway. Następnym krokiem jest dodać prywatny adres IP, publiczny adres IP lub karty Sieciowej maszyny wirtualnej do puli zaplecza bramy aplikacji. Dopóki nie odbywa się dodatkowe zalecenia **finalizowanie ochrony aplikacji** jest wyświetlany, dopóki zasób nie zostanie dodany.

![Strona Dodawanie zapory aplikacji sieci Web][5]

## <a name="security-alerts"></a>Alerty zabezpieczeń

W usłudze Security Center, przejdź do **wykrywania** > **alerty zabezpieczeń**.  W tym miejscu możesz znaleźć alerty zapory aplikacji sieci Web dla bram aplikacji. Alerty są podzielone według reguł zapory aplikacji sieci Web.

![Alerty zabezpieczeń][8]

Klikając regułę będzie stanowić Lista alertów daną regułę zapory aplikacji sieci Web. Każdy alert zawiera dodatkowe szczegóły dotyczące wyszukiwania. Szczegółowe informacje zawierają łącza do usługi application gateway.
 
![Szczegóły alertu][9]

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak włączyć zapory aplikacji sieci web w istniejącej bramie aplikacji, odwiedź stronę [tworzenia lub aktualizacji usługi Azure Application Gateway przy użyciu zapory aplikacji sieci web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png