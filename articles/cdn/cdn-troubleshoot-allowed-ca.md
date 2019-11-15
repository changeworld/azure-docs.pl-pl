---
title: Dozwolony urząd certyfikacji do włączania niestandardowego protokołu HTTPS w Azure CDN
description: Jeśli używasz własnego certyfikatu do włączenia protokołu HTTPS w domenie niestandardowej, musisz użyć dozwolonego urzędu certyfikacji (CA), aby go utworzyć.
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
ms.openlocfilehash: 5462502514a3e327913122fe99fd699856891216
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083104"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Dozwolone urzędy certyfikacji do włączania niestandardowego protokołu HTTPS w Azure CDN

W przypadku domeny niestandardowej usługi Azure Content Delivery Network (CDN) w **standardzie Azure CDN z punktu końcowego firmy Microsoft** po [włączeniu funkcji HTTPS przy użyciu własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)należy utworzyć certyfikat SSL za pomocą dozwolonego urzędu certyfikacji. W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

> [!NOTE]
> Opcja korzystania z własnego certyfikatu w celu włączenia niestandardowego protokołu HTTPS jest dostępna tylko w przypadku **Azure CDN Standard z profilów firmy Microsoft** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

