---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147007"
---
>[!NOTE]
>Od 1 lipca 2018 r. z usługi Azure VPN Gateway zostanie usunięta obsługa techniczna protokołów TLS 1.0 i 1.1. Usługa VPN Gateway będzie obsługiwać tylko protokół TLS 1.2. Aby zachować pomocy technicznej, zobacz [aktualizacji, aby włączyć obsługę TLS1.2](#tls1).

Ponadto następujące starsze algorytmy również staną się przestarzałe protokołu TLS 1 lipca 2018 r.:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Jak włączyć obsługę protokołu TLS 1.2 w Windows 7 i Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
