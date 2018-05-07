---
title: Integracja bramy aplikacji z Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera informacje dotyczące integracji brama aplikacji w Centrum zabezpieczeń Azure.
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
ms.openlocfilehash: b3a4abf4d0f408cdb49020d831b50d943c3467dd
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Omówienie integracji między bramą aplikacji i Centrum zabezpieczeń Azure

Dowiedz się, jak bramy aplikacji i Centrum zabezpieczeń pomaga chronić zasobów aplikacji sieci web. Zapora aplikacji sieci web dla aplikacji bramy (WAF) integruje się z [Centrum zabezpieczeń](../security-center/security-center-intro.md) bezproblemowe widoku, aby zapobiec, wykrywania i reagowania na zagrożenia do aplikacji sieci web niechronione w danym środowisku.

## <a name="overview"></a>Przegląd

Bramy aplikacji zapory aplikacji sieci Web jest zalecenia w Centrum zabezpieczeń do ochrony aplikacji sieci web z luki w zabezpieczeniach i luk w zabezpieczeniach. Zasoby sieci Web jest włączona, które nie są chronione przez zapory aplikacji sieci Web są wyświetlane w Centrum zabezpieczeń jako zalecenia o wysokim znaczeniu. Zalecenia dotyczące zapory aplikacji sieci web zostaną wyświetlone na **omówienie** w obszarze **aplikacji**.

![Integracja z Centrum zabezpieczeń][1]

Klikając przycisk żadnych zaleceń, że dotyczące zapory aplikacji sieci web otwiera nową stronę zawierającego szczegóły zalecenia.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Dodawanie zapory aplikacji sieci web do istniejącego zasobu

Przejdź do **wszystkie usługi** > **bezpieczeństwo i Obsługa tożsamości** > **Centrum zabezpieczeń** i na **Centrum zabezpieczeń — omówienie**, kliknij przycisk **aplikacji**. Na **Centrum zabezpieczeń — aplikacje**, tabela zawiera listę aplikacji, wykrytych przez Centrum zabezpieczeń w ramach subskrypcji.

![aplikacje sieci Web][3]

Klikając aplikacji sieci web z krytyczny problem, możesz uzyskać **kondycja zabezpieczeń aplikacji** strony. Na poniższej ilustracji, aplikacji sieci web, która nie jest chroniony przez zapory aplikacji sieci web. 

![zasoby sieci Web, które nie są chronione][2]

Kliknij przycisk **Dodawanie zapory aplikacji sieci web** w obszarze **zalecenia** otworzyć **Dodawanie zapory aplikacji sieci Web** strony.

Jeśli nie masz istniejącą bramę aplikacji lub chcesz utworzyć nową, kliknij przycisk **Utwórz nowy** i na **Tworzenie nowej zapory aplikacji sieci Web**i kliknij przycisk **Microsoft - bramy aplikacji** . Powoduje to przejście kroki, aby utworzyć bramę aplikacji. W tym momencie aplikacji sieci web jest dodawany jako zasobu chronionego, Centrum zabezpieczeń teraz śledzi, że ten zasób jest chroniony przez zapory aplikacji sieci web. To nie Dodaj jako członka puli wewnętrznej bazy danych.

Jeśli masz istniejącą bramę aplikacji, można go w **użyć istniejącego rozwiązania**

![Strona Dodawanie zapory aplikacji sieci Web][4]

Dodawanie aplikacji sieci web do bramy aplikacji za pomocą Centrum zabezpieczeń nie dodaje zasobu jako członka puli wewnętrznej bazy danych. Musi to dla zasobu bramy aplikacji bezpośrednio.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Dodaj zasób do istniejących zapory aplikacji sieci web

Przejdź do **wszystkie usługi** > **bezpieczeństwo i Obsługa tożsamości** > **Centrum zabezpieczeń** i na **Centrum zabezpieczeń — omówienie**, kliknij przycisk **rozwiązania partnerskie**. Pokaż istniejącej bramy aplikacji obsługującej Centrum zabezpieczeń w **rozwiązań partnerskich** strony.

![rozwiązania partnerskie][7]

Kliknij przycisk **Połącz aplikację** otworzyć **łączenie aplikacji**, w tym miejscu są podane opcji, aby wybrać istniejące aplikacje. Wybierz aplikacje do ochrony i kliknij przycisk **OK**. Nie dodaje aplikacji sieci web do puli zaplecza bramy aplikacji. Zasoby to ustawienie jako chroniony zasób, Centrum zabezpieczeń można śledzić. Aby dodać zasobu jako członka puli wewnętrznej bazy danych, należy to zrobić na bramie aplikacji, z bieżącej strony, możesz kliknąć **Konsola rozwiązań** podjąć w celu zasobu bramy aplikacji, którym można dodać aplikacji sieci web Pula zaplecza.

![aplikacje rozwiązania partnerskie][6]

## <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Centrum zabezpieczeń śledzi aplikacji dodanych do bramy aplikacji jako chronionego zasobu.  Monitoruje kondycję tego zasobu i zapewnia, że jest chroniony przez bramę aplikacji. Następnym krokiem jest dodawanie prywatnego adresu IP, publiczny adres IP lub karty Sieciowej maszyny wirtualnej do puli zaplecza bramy aplikacji. Dopóki nie jest to dodatkowe zalecenia **Finalizuj ochronę aplikacji** jest wyświetlany, dopóki zasób nie zostanie dodany.

![Strona Dodawanie zapory aplikacji sieci Web][5]

## <a name="security-alerts"></a>Alerty zabezpieczeń

W Centrum zabezpieczeń, przejdź do **wykrywania** > **alerty zabezpieczeń**.  W tym miejscu możesz znaleźć alerty zapory aplikacji sieci Web z bramy aplikacji. Alerty są przerwane przez reguły zapory aplikacji sieci Web.

![alerty zabezpieczeń][8]

Kliknięcie reguła określi Lista alertów dla tej określonej reguły zapory aplikacji sieci Web. Każdy alert zawiera dodatkowe szczegóły niezgodności. Szczegóły zawierają łącza do bramy aplikacji.
 
![Szczegóły alertu][9]

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak włączyć zapory aplikacji sieci web na istniejącą bramę aplikacji, odwiedź stronę [Tworzenie lub aktualizacja bramy aplikacji Azure z zapory aplikacji sieci web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png