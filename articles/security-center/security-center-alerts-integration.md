---
title: Integracja usługi Security Center z produktami zabezpieczeń platformy Azure | Dokumentacja firmy Microsoft
description: Ten temat przedstawia informacje o produktów zabezpieczeń platformy Azure, które zostały zintegrowane z usługą Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571740"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>Usługa Security Center integracji z produktami zabezpieczeń platformy Azure z usługą ASC

Security Center zapewnia klientom dodatkowe licencje firmy Microsoft, aby dołączyć ich wyników do usługi Security Center i wyświetlać je w sposób skonsolidowany.

* [Usługa Azure zapory aplikacji sieci Web](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Usługa Azure zapory aplikacji sieci Web <a name="azure-waf"></a>

Usługa Azure Application Gateway oferuje zaporę aplikacji internetowej (WAF), która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach.

Aplikacje internetowe coraz częściej są objęci złośliwych ataków wykorzystujących powszechnie znane luki w zabezpieczeniach. Brama aplikacji zapory aplikacji internetowych opiera się na podstawowych reguł Ustaw (CRS) 3.0 lub 2.2.9 z Otwórz sieci Web aplikacji Security Project (OWASP). Zapory aplikacji internetowych jest aktualizowana automatycznie, aby zapewnić ochronę przed nowych luk w zabezpieczeniach przy użyciu nie wykonywania dodatkowych czynności konfiguracyjnych. Alerty wygenerowane przez zaporę aplikacji sieci Web są przesyłane strumieniowo do usługi Security Center. Aby uzyskać więcej informacji na temat alertów generowanych przez zaporę aplikacji sieci Web, zobacz ten [artykułu](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

Service (DDoS) ataków Distributed denial of są znane, aby były łatwe do wykonania. W związku z tym stają się kwestią doskonałe zabezpieczenia w przypadku klientów, którzy są przenoszone do ich aplikacji w chmurze. 

Atak DDoS polega na próbie wyczerpania zasobów aplikacji, przez co aplikacja staje się niedostępna dla zwykłych użytkowników. Ataki DDoS mogą określać docelową dowolnego punktu końcowego, w którym można się skontaktować za pośrednictwem Internetu.

Usługa Azure DDoS protection, w połączeniu z najlepszych rozwiązań projektowania aplikacji zapewniają ochronę przed atakami DDoS. Usługa Azure DDoS protection zapewnia różnych warstwach usług. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Standard ochrony przed atakami DDoS pozwala ograniczyć następujące rodzaje ataków:

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto atak pojemności**|Celem takiego ataku jest zalać warstwy sieciowej znaczną ilość ruchu pozornie uzasadnione. Zawiera powodzie, powodzie wzmocnienia i innych powodzie sfałszowane pakietów UDP. Standard ochrony przed atakami DDoS zmniejsza tych potencjalnych ataków wielu gigabajtów, przejmowania i czyszczenia danych, w skali globalnej sieci platformy Azure, automatycznie.|
|**Wykryto atak protokołu**|Te ataki obiekt docelowy renderowania niedostępne przez wykorzystanie słabości w warstwie 3 i stosu protokołu 4 warstwy. Obejmuje, SYN ataków powódź odbicie ataków i inne ataki protokołu. Standard ochrony przed atakami DDoS zmniejsza te ataki rozróżnianiu ruchu złośliwego i uzasadnione, interakcji z klientem i blokowania złośliwego ruchu.|
|**Wykryto atak warstwy zasobu (aplikacji)**|Te ataki docelowych, pakiety aplikacji sieci web, w celu zakłócania przesyłania danych między hostami. Przed atakami obejmują HTTP naruszeniami protokołu, SQL iniekcji, skryptów między witrynami i inne ataki warstwy 7. Za pomocą usługi Azure Application Gateway zapory aplikacji sieci web, przed atakami DDoS Protection w warstwie standardowa do obrony przed te ataki. Brak dostępnych również ofert zapory aplikacji sieci web innych firm w witrynie Azure Marketplace.|
