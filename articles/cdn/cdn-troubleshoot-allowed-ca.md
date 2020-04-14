---
title: Zezwalane urząd certyfikacji na włączanie niestandardowego protokołu HTTPS w usłudze Azure CDN
description: Jeśli używasz własnego certyfikatu do włączania protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259975"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Dozwolone urzędy certyfikacji umożliwiające włączenie niestandardowego protokołu HTTPS w usłudze Azure CDN

Po [włączeniu funkcji HTTPS](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) należy spełnić określone wymagania dotyczące certyfikatów przy użyciu własnego certyfikatu dla domeny niestandardowej usługi Azure Content Delivery Network (CDN). **Standard usługi Azure CDN firmy Microsoft z** profilu firmy Microsoft wymaga certyfikatu jednego z zatwierdzonych urzędów certyfikacji (CA) na poniższej liście. Jeśli certyfikat z niezatwierdzony urząd certyfikacji lub certyfikat z podpisem własnym jest używany, żądanie zostanie odrzucone. **Usługa Azure CDN Standard firmy Verizon** i **Azure CDN Premium z** profili Verizon akceptuje dowolny prawidłowy certyfikat z dowolnego ważnego urzędu certyfikacji.

> [!NOTE]
> Opcja użycia własnego certyfikatu w celu włączenia funkcji HTTPS domeny niestandardowej *nie* jest dostępna dla **usługi Azure CDN Standard z profilów Akamai.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

