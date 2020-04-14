---
title: Debugowanie nagłówków HTTP dla usługi Azure CDN firmy Microsoft | Dokumenty firmy Microsoft
description: Nagłówki żądań pamięci podręcznej debugowania zawierają dodatkowe informacje na temat zasad pamięci podręcznej zastosowanych do żądanego zasobu. Te nagłówki są specyficzne dla usługi Azure CDN firmy Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260416"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debugowanie nagłówka HTTP dla usługi Azure CDN firmy Microsoft
Nagłówek odpowiedzi debugowania , zawiera szczegółowe informacje o tym, `X-Cache`z jakiej warstwy stosu sieci CDN została wyświetlona zawartość. Ten nagłówek jest specyficzny dla usługi Azure CDN firmy Microsoft.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek | Opis
-------|------------
X-Cache: TCP_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z pamięci podręcznej krawędzi sieci CDN. 
X-Cache: TCP_REMOTE_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z regionalnej pamięci podręcznej SIECI CDN (warstwa osłony pochodzenia)
X-Cache: TCP_MISS | Ten nagłówek jest zwracany, gdy istnieje miss pamięci podręcznej, a zawartość jest obsługiwana z pochodzenia. 


