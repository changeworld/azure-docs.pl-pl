---
title: Integracja produktów zabezpieczeń platformy Azure w Azure Security Center | Microsoft Docs
description: W tych tematach przedstawiono produkty zabezpieczeń platformy Azure, które zostały zintegrowane z usługą Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 847748d1c56221119d8f74a2aee716ee08448e28
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335820"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integracja produktów zabezpieczeń platformy Azure w Azure Security Center

Security Center oferuje klientom z dodatkowymi licencjami firmy Microsoft, którzy mogą dołączać swoje wnioski do Security Center i wyświetlać je w sposób skonsolidowany.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF<a name="azure-waf"></a>

Usługa Azure Application Gateway oferuje zaporę aplikacji internetowej (WAF), która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach.

Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Application Gateway WAF opiera się na podstawowym zestawie reguł (KSR) 3,0 lub 2.2.9 w programie Open Web Application Security Project (OWASP). WAF jest automatycznie aktualizowana w celu ochrony przed nowymi lukami w zabezpieczeniach, bez konieczności dodatkowej konfiguracji. Alerty wygenerowane przez WAF są przesyłane strumieniowo do Security Center. Aby uzyskać więcej informacji na temat alertów wygenerowanych przez WAF, zobacz ten [artykuł](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS<a name="azure-ddos"></a>

Ataki rozproszonego typu "odmowa usługi" (DDoS) są znane, aby można je było łatwo wykonywać. W związku z tym staną się bardzo doskonałym problemem z zabezpieczeniami w przypadku klientów, którzy przenosili swoje aplikacje do chmury. 

Atak DDoS polega na próbie wyczerpania zasobów aplikacji, przez co aplikacja staje się niedostępna dla zwykłych użytkowników. Ataki DDoS mogą wskazywać każdy punkt końcowy, który można uzyskać za pomocą Internetu.

Usługa Azure DDoS Protection, w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji, zapewnia ochronę przed atakami na DDoS. Usługa Azure DDoS Protection oferuje różne warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure DDoS Protection przegląd](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Standard DDoS Protection może ograniczyć ataki następujących typów:

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto atak objętościowy**|Celem tego ataku jest zalanie warstwy sieci przy użyciu dużej ilości pozornie uzasadnionego ruchu. Obejmuje to zalewania protokołu UDP, zalewania wzpełnień oraz inne zalewania pakietów sfałszowanych. DDoS Protection Standard ogranicza te potencjalne ataki z obsługą wielodostępności przez przeabsorbowanie i ich kontrolę przy użyciu globalnej skali sieci platformy Azure.|
|**Wykryto atak z wykorzystaniem protokołu**|Te ataki sprawiają, że element docelowy jest niedostępny, wykorzystując słabą lukę w stosie protokołu warstwy 3 i 4. Obejmuje to ataki, ataki na zalanie się w przypadku zalewania i ataków. DDoS Protection Standard ogranicza te ataki, różnią się od złośliwego i wiarygodnego ruchu, współpracując z klientem i blokując złośliwy ruch.|
|**Wykryto atak na warstwę zasobów (aplikacji)**|Te ataki są docelowymi pakietami aplikacji sieci Web, aby przerwać przesyłanie danych między hostami. Ataki obejmują naruszenia protokołu HTTP, wstrzykiwanie kodu SQL, skrypty między lokacjami i inne ataki warstwy 7. Aby chronić przed atakami, użyj zapory aplikacji sieci Web platformy Azure Application Gateway ze standardem DDoS Protection. Istnieją także oferty zapory aplikacji sieci Web innych firm dostępne w portalu Azure Marketplace.|
