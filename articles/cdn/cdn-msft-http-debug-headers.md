---
title: Debugowanie nagłówków HTTP dla usługi Azure CDN firmy Microsoft | Dokumenty firmy Microsoft
description: Nagłówki żądań pamięci podręcznej debugowania zawierają dodatkowe informacje na temat zasad pamięci podręcznej zastosowanych do żądanego zasobu. Te nagłówki są specyficzne dla usługi Azure CDN firmy Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814073"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debugowanie nagłówka HTTP dla usługi Azure CDN firmy Microsoft
Nagłówek odpowiedzi debugowania , zawiera szczegółowe informacje o tym, `X-Cache`z jakiej warstwy stosu sieci CDN została wyświetlona zawartość. Ten nagłówek jest specyficzny dla usługi Azure CDN firmy Microsoft.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek | Opis
-------|------------
X-Cache: TCP_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z pamięci podręcznej krawędzi sieci CDN. 
X-Cache: TCP_REMOTE_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z regionalnej pamięci podręcznej SIECI CDN (warstwa osłony pochodzenia)
X-Cache: TCP_MISS | Ten nagłówek jest zwracany, gdy istnieje miss pamięci podręcznej, a zawartość jest obsługiwana z pochodzenia. 


