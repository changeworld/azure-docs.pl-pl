---
title: Dozwolone urzędów certyfikacji umożliwiające niestandardowego protokołu HTTPS w usłudze Azure Service wejściu | Dokumentacja firmy Microsoft
description: Jeśli używasz własnego certyfikatu, aby włączyć protokół HTTPS w domenie niestandardowej, musi być go utworzyć urzędu dozwolonych certyfikacji (CA).
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
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330753"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Dozwolone urzędów certyfikacji umożliwiające niestandardowego protokołu HTTPS w usłudze Azure Service drzwi

Dla domeny niestandardowej usługi Azure Service drzwiami frontowymi podczas możesz [Włączanie funkcji protokołu HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), należy użyć urzędu certyfikacji dozwolonych (CA) do utworzenia certyfikatu SSL. W przeciwnym razie jeśli używasz innego niż dozwolone urzędu certyfikacji lub certyfikatu z podpisem własnym, Twoje żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
