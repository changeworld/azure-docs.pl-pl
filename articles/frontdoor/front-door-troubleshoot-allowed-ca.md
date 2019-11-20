---
title: Dozwolony urząd certyfikacji do włączania niestandardowego protokołu HTTPS w usłudze Azure Front-drzwiczk
description: Jeśli używasz własnego certyfikatu do włączenia protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
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
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184579"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Dozwolone urzędy certyfikacji do włączania niestandardowego protokołu HTTPS w usłudze Azure Front-drzwiczk

W przypadku domeny niestandardowej usługi Azure Front drzwiczk, po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)należy utworzyć certyfikat SSL za pomocą dozwolonego urzędu certyfikacji. W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
