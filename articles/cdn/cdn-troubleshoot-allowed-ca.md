---
title: Zezwalane urząd certyfikacji na włączanie niestandardowego protokołu HTTPS w usłudze Azure CDN
description: Jeśli używasz własnego certyfikatu do włączania protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
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
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919978"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Dozwolone urzędy certyfikacji umożliwiające włączenie niestandardowego protokołu HTTPS w usłudze Azure CDN

Po [włączeniu funkcji HTTPS](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) należy spełnić określone wymagania dotyczące certyfikatów przy użyciu własnego certyfikatu dla domeny niestandardowej usługi Azure Content Delivery Network (CDN). **Standard usługi Azure CDN firmy Microsoft z** profilu firmy Microsoft wymaga certyfikatu jednego z zatwierdzonych urzędów certyfikacji (CA) na poniższej liście. Jeśli certyfikat z niezatwierdzony urząd certyfikacji lub certyfikat z podpisem własnym jest używany, żądanie zostanie odrzucone. **Usługa Azure CDN Standard firmy Verizon** i **Azure CDN Premium z** profili Verizon akceptuje dowolny prawidłowy certyfikat z dowolnego ważnego urzędu certyfikacji.

> [!NOTE]
> Opcja użycia własnego certyfikatu w celu włączenia funkcji HTTPS domeny niestandardowej *nie* jest dostępna dla **usługi Azure CDN Standard z profilów Akamai.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

