---
title: Debugowanie nagłówków HTTP dla Azure CDN firmy Microsoft | Microsoft Docs
description: Nagłówki żądań pamięci podręcznej debugowania zawierają dodatkowe informacje dotyczące zasad pamięci podręcznej stosowanych do żądanego elementu zawartości. Te nagłówki są specyficzne dla Azure CDN od firmy Microsoft.
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
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814073"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debuguj nagłówek HTTP dla Azure CDN firmy Microsoft
Nagłówek `X-Cache`odpowiedzi na Debugowanie, zawiera szczegółowe informacje dotyczące warstwy stosu sieci CDN, z której dana zawartość została obsłużona. Ten nagłówek jest specyficzny dla Azure CDN firmy Microsoft.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

nagłówek | Opis
-------|------------
Pamięć podręczna: TCP_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z pamięci podręcznej usługi CDN Edge. 
Pamięć podręczna: TCP_REMOTE_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z regionalnej pamięci podręcznej CDN (warstwa osłon pochodzenia)
Pamięć podręczna: TCP_MISS | Ten nagłówek jest zwracany, gdy występuje chybień pamięci podręcznej, a zawartość jest obsługiwana z punktu początkowego. 


