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
ms.openlocfilehash: cac6bc9895f2b8778f2b27cc6b1dff4d4b898ae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471527"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Dozwolone urzędy certyfikacji umożliwiające włączenie niestandardowego protokołu HTTPS w drzwiach frontowych platformy Azure

W przypadku domeny niestandardowej usługi Azure Front Door po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)należy użyć dozwolonego urzędu certyfikacji (CA) do utworzenia certyfikatu SSL. W przeciwnym razie jeśli używany jest niedozwolony urząd certyfikacji lub certyfikat z podpisem własnym, żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
