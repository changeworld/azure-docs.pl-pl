---
title: Debugowanie nagłówków HTTP dla Azure CDN firmy Microsoft | Microsoft Docs
description: Nagłówki żądań pamięci podręcznej debugowania zawierają dodatkowe informacje dotyczące zasad pamięci podręcznej stosowanych do żądanego elementu zawartości. Te nagłówki są specyficzne dla Azure CDN od firmy Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: be6655c2c5e6b2acca82b4a1b20a3cdf84e0251b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707528"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debuguj nagłówek HTTP dla Azure CDN firmy Microsoft
Nagłówek `X-Cache`odpowiedzi na Debugowanie, zawiera szczegółowe informacje dotyczące warstwy stosu sieci CDN, z której dana zawartość została obsłużona. Ten nagłówek jest specyficzny dla Azure CDN firmy Microsoft.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

nagłówek | Opis
-------|------------
Pamięć podręczna: TCP_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z pamięci podręcznej usługi CDN Edge. 
Pamięć podręczna: TCP_REMOTE_HIT | Ten nagłówek jest zwracany, gdy zawartość jest obsługiwana z regionalnej pamięci podręcznej CDN (warstwa osłon pochodzenia)
Pamięć podręczna: TCP_MISS | Ten nagłówek jest zwracany, gdy występuje chybień pamięci podręcznej, a zawartość jest obsługiwana z punktu początkowego. 


