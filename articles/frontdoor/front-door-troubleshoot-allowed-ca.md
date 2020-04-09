---
title: Zezwalane urząd certyfikacji na włączanie niestandardowych usług HTTPS w drzwiach frontowych platformy Azure
description: Jeśli używasz własnego certyfikatu do włączania protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874674"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Dozwolone urzędy certyfikacji umożliwiające włączenie niestandardowego protokołu HTTPS w drzwiach frontowych platformy Azure

W przypadku domeny niestandardowej usługi Azure Front Door po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)należy użyć dozwolonego urzędu certyfikacji (CA) do utworzenia certyfikatu TLS/SSL. W przeciwnym razie jeśli używany jest niedozwolony urząd certyfikacji lub certyfikat z podpisem własnym, żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
