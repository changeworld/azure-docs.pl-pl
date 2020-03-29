---
title: Możliwości ochrony DDoS usługi Azure CDN | Dokumenty firmy Microsoft
description: Usługa Azure CDN firmy Microsoft jest chroniona przez usługę DDoS Protection basic bez dodatkowych kosztów
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: magattus
ms.openlocfilehash: 9cd688de861015cc12d1f98ed71e5376e5f574db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593888"
---
# <a name="azure-cdn-ddos-protection"></a>Ochrona ddos usługi Azure CDN

Sieć dostarczania zawartości zapewnia ochronę przed atakami DDoS zgodnie z projektem. Oprócz globalnej zdolności do absorbowania ataków wolumetrycznych usługa Azure CDN ma dodatkową ochronę DDoS, jak opisano poniżej, bez dodatkowych kosztów.

## <a name="azure-cdn-from-microsoft"></a>Usługa Azure CDN firmy Microsoft

Usługa Azure CDN firmy Microsoft jest chroniona przez [usługę Azure Basic DDoS](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview). Jest domyślnie zintegrowany z usługą Azure CDN z platformy Microsoft i bez dodatkowych kosztów. Pełna skala i pojemność usługi Azure CDN z globalnie wdrożonej sieci firmy Microsoft zapewnia odporność na typowe ataki warstwy sieciowej poprzez zawsze włączone monitorowanie ruchu i ograniczanie emisji w czasie rzeczywistym. Podstawowa ochrona przed atakami DDoS chroni również przed najczęstszymi, często występującymi powodziami zapytań DNS warstwy 7 oraz atakami wolumetrycznymi warstwy 3 i 4, które są ukierunkowane na punkty końcowe sieci CDN. Ta usługa ma również udokumentowane doświadczenie w ochronie przedsiębiorstw i usług konsumenckich firmy Microsoft przed atakami na dużą skalę.

## <a name="azure-cdn-from-verizon"></a>Usługa Azure CDN firmy Verizon

Usługa Azure CDN firmy Verizon jest chroniona przez zastrzeżoną platformę ograniczającą zagrożenie DDoS firmy Verzion. Jest domyślnie zintegrowany z usługą Azure CDN firmy Verizon i nie ma żadnych dodatkowych kosztów. Zapewnia podstawową ochronę przed najczęstszymi, często występującymi powodziami zapytań DNS warstwy 7 oraz atakami wolumetrycznymi warstwy 3 i 4, które są ukierunkowane na punkty końcowe sieci CDN.

## <a name="azure-cdn-from-akamai"></a>Usługa Azure CDN firmy Akamai

Usługa Azure CDN firmy Akamai jest chroniona przez zastrzeżoną platformę łagodzenia DDoS firmy Akamai. Jest domyślnie zintegrowany z usługą Azure CDN firmy Akamai bez dodatkowych kosztów. Zapewnia podstawową ochronę przed najczęstszymi, często występującymi powodziami zapytań DNS warstwy 7 oraz atakami wolumetrycznymi warstwy 3 i 4, które są ukierunkowane na punkty końcowe sieci CDN.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure DDoS](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview). 
