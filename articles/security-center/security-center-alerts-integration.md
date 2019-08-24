---
title: Integracja produktów zabezpieczeń platformy Azure w Azure Security Center | Microsoft Docs
description: W tym temacie przedstawiono produkty zabezpieczeń platformy Azure, które zostały zintegrowane z Azure Security Center.
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
ms.openlocfilehash: beac7d08a973dd8dc7e450840669bfd5687e76ed
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013304"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integracja produktów zabezpieczeń platformy Azure w Azure Security Center

Azure Security Center zapewnia dodatkowe licencje firmy Microsoft na korzystanie z następujących produktów zabezpieczeń:

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF<a name="azure-waf"></a>

Usługa Azure Application Gateway oferuje zaporę aplikacji internetowej (WAF), która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach.

Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Application Gateway WAF opiera się na podstawowym zestawie reguł 3,0 lub 2.2.9 w projekcie zabezpieczeń aplikacji sieci Web. WAF jest automatycznie aktualizowana w celu ochrony przed nowymi lukami w zabezpieczeniach, bez konieczności dodatkowej konfiguracji. Alerty WAF są przesyłane strumieniowo do Security Center. Aby uzyskać więcej informacji na temat alertów wygenerowanych przez WAF, zobacz [zasady i reguły reguł KSR aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS Protection<a name="azure-ddos"></a>

Ataki rozproszonego typu "odmowa usługi" (DDoS) są znane, aby można je było łatwo wykonywać. Stają się one doskonałym problemem z zabezpieczeniami, szczególnie jeśli przenosisz aplikacje do chmury. 

Atak DDoS polega na próbie wyczerpania zasobów aplikacji, przez co aplikacja staje się niedostępna dla zwykłych użytkowników. Ataki DDoS mogą wskazywać każdy punkt końcowy, który można uzyskać za pomocą Internetu.

Azure DDoS Protection, w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji, należy zapewnić ochronę przed atakami DDoS. DDoS Protection oferuje różne warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure DDoS Protection przegląd](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Standard DDoS Protection może ograniczyć ataki następujących typów:

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto atak objętościowy**|Celem tego ataku jest zalanie warstwy sieci przy użyciu dużej ilości pozornie uzasadnionego ruchu. Obejmuje to zalewania protokołu UDP, zalewania wzpełnień oraz inne zalewania pakietów sfałszowanych. DDoS Protection Standard ogranicza te potencjalne ataki z obsługą wielodostępności przez przeciąganie i szybkie ich kontrolę przy użyciu globalnej skali sieci.|
|**Wykryto atak z wykorzystaniem protokołu**|Te ataki sprawiają, że element docelowy jest niedostępny, przez wykorzystanie słabego poziomu stosów w warstwach 3 i 4. Obejmuje to ataki na zalanie, ataki na odbicie i inne ataki protokołów. DDoS Protection Standard ogranicza te ataki, różnią się od złośliwego i wiarygodnego ruchu, współpracując z klientem i blokując złośliwy ruch.|
|**Wykryto atak na warstwę zasobów (aplikacji)**|Te ataki są docelowymi pakietami aplikacji sieci Web, aby przerwać przesyłanie danych między hostami. Ataki obejmują naruszenia protokołu HTTP, wstrzykiwanie kodu SQL, skrypty między lokacjami i inne ataki warstwy 7. Aby chronić przed atakami, Użyj usługi Azure Application Gateway WAF z opcją DDoS Protection Standard. Istnieją także oferty WAF innych firm dostępne w witrynie Azure Marketplace.|
