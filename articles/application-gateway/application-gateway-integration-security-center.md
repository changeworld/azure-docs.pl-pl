---
title: Integracja bramy aplikacji z usługą Azure Security Center | Dokumenty firmy Microsoft
description: Ta strona zawiera informacje o tym, jak brama aplikacji jest zintegrowana z usługą Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277188"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Omówienie integracji między bramą aplikacji a usługą Azure Security Center

Dowiedz się, jak usługa Brama aplikacji i centrum zabezpieczeń pomagają chronić zasoby aplikacji sieci web. Zapora aplikacji bramy aplikacji sieci web (WAF) integruje się z [usługą Security Center,](../security-center/security-center-intro.md) aby zapewnić bezproblemowy widok w celu zapobiegania zagrożeniom niechronionym aplikacjom internetowym w twoim środowisku, wykrywania ich i reagowania na niechronione aplikacje sieci Web.

## <a name="overview"></a>Omówienie

WAF bramy aplikacji jest zaleceniem w ucho w ucho w centrum zabezpieczeń w celu ochrony aplikacji internetowych przed exploitami i lukami w zabezpieczeniach. Zasoby z włączoną obsługą sieci Web, które nie są chronione przez waf, są wyświetlane w centrum zabezpieczeń jako zalecenia o wysokiej ważności. Zalecenia dotyczące zapór aplikacji sieci web są wyświetlane na stronie **Przegląd** w obszarze **Aplikacje**.

![integracja z centrum zabezpieczeń][1]

Kliknięcie wszelkich zaleceń dotyczących zapory aplikacji sieci web otwiera nową stronę z informacjami o rekomendacji.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Dodawanie zapory aplikacji sieci Web do istniejącego zasobu

Przejdź do **pozycji Wszystkie usługi** > **Security + Identity** > **Security Center** i w Centrum zabezpieczeń **— przegląd**kliknij pozycję **Aplikacje**. W **u centrum zabezpieczeń — aplikacje**tabela zawiera listę aplikacji wykrytych przez centrum zabezpieczeń w ramach subskrypcji.

![aplikacje internetowe][3]

Klikając na aplikację sieci web z krytycznym problemem, można uzyskać **stronę kondycji zabezpieczeń aplikacji.** Na poniższej ilustracji aplikacja sieci web, która nie jest chroniona przez zaporę aplikacji sieci web. 

![zasoby internetowe nie są chronione][2]

Kliknij **pozycję Dodaj zaporę aplikacji sieci Web** w obszarze **Zalecenia,** aby otworzyć stronę Dodaj **zaporę aplikacji sieci Web.**

Jeśli nie masz istniejącej bramy aplikacji lub chcesz utworzyć nową, kliknij przycisk **Utwórz nowy** i w **usłudze Utwórz nową Zaporę aplikacji sieci Web**, a następnie kliknij pozycję Microsoft — **Brama aplikacji**. Spowoduje to przejście przez kroki, aby utworzyć bramę aplikacji. W tym momencie aplikacja sieci web jest dodawana jako zasób chroniony, Usługa Security Center śledzi teraz, że ten zasób jest chroniony przez zaporę aplikacji sieci web. Nie powoduje to dodania go jako elementu członkowskiego puli wewnętrznej bazy danych.

Jeśli masz istniejącą bramę aplikacji, możesz ją wybrać w obszarze **Użyj istniejącego rozwiązania**

![Strona, aby dodać zaporę aplikacji sieci Web][4]

Dodanie aplikacji sieci web do bramy aplikacji za pośrednictwem usługi Security Center nie powoduje dodania zasobu jako elementu członkowskiego puli wewnętrznej bazy danych. Należy to zrobić bezpośrednio w zasobie bramy aplikacji.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Dodawanie zasobu do istniejącej zapory aplikacji sieci Web

Przejdź do **pozycji Wszystkie usługi** > **Security + Identity** > **Security Center** i w U usług Security Center — **przegląd**kliknij pozycję **Rozwiązania partnerów**. Istniejące bramy aplikacji obsługujące centrum zabezpieczeń są wyświetlane na stronie **Rozwiązania dla partnerów.**

![rozwiązania partnerskie][7]

Kliknij **pozycję Połącz aplikację,** aby otworzyć **aplikację Łącz,** w tym miejscu dostępne są opcje wyboru istniejących aplikacji. Wybierz aplikacje do ochrony i kliknij przycisk **OK**. Nie powoduje to dodania aplikacji sieci web do puli wewnętrznej bazy danych bramy aplikacji. Spowoduje to ustawienie zasobów jako zasobu chronionego, dzięki czemu usługa Security Center może je śledzić. Aby dodać zasób jako element członkowski puli wewnętrznej bazy danych, należy to zrobić na bramie aplikacji, z bieżącej strony można kliknąć **konsolę rozwiązania,** która ma zostać pobrana do zasobu bramy aplikacji, gdzie można dodać aplikację sieci web do puli wewnętrznej bazy danych.

![aplikacje dla rozwiązań partnerskich][6]

## <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Usługa Security Center śledzi aplikacje dodane do bramy aplikacji jako zasób chroniony.  Monitoruje kondycję tego zasobu i zapewnia, że jest chroniony przez bramę aplikacji. Następnym krokiem jest dodanie prywatnego adresu IP, publicznego adresu IP lub karty sieciowej maszyny wirtualnej do puli wewnętrznej bazy danych bramy aplikacji. Dopóki nie zostanie to zrobione, zostanie wyświetlone dodatkowe zalecenie **finalizuj ochronę aplikacji,** dopóki nie zostanie dodany zasób.

![Strona, aby dodać zaporę aplikacji sieci Web][5]

## <a name="security-alerts"></a>Alerty zabezpieczeń

W Centrum zabezpieczeń przejdź do**alertów zabezpieczeń** **WYKRYWANIA** > .  Tutaj znajdziesz alerty WAF dla bram aplikacji. Alerty są podzielone według reguły WAF.

![alerty zabezpieczeń][8]

Wybranie reguły zapewni listę alertów dla tej konkretnej reguły WAF. Każdy alert zawiera dodatkowe szczegóły dotyczące znalezienia. Szczegóły zawierają łącze do bramy aplikacji.
 
![szczegóły alertu][9]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć zaporę aplikacji sieci Web na istniejącej bramie aplikacji, odwiedź stronę [Tworzenie lub aktualizowanie bramy aplikacji platformy Azure za pomocą zapory aplikacji sieci Web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png