---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168381"
---
Podczas pracy z niestandardowymi zasadami protokołu IPsec należy pamiętać o następujących wymaganiach:

* **IKE** -dla protokołu IKE można wybrać dowolny parametr z szyfrowania IKE oraz dowolny parametr z integralności IKE i dowolny parametr z grupy DH.
* **IPSec** — w przypadku protokołu IPSec można wybrać dowolny parametr z szyfrowania IPSec oraz dowolny parametr z integralności IPSec i PFS. W przypadku GCM dowolnego z parametrów szyfrowania IPsec lub integralności protokołu IPsec parametry obu ustawień muszą mieć wartość GCM.

>[!NOTE]
> W przypadku niestandardowych zasad protokołu IPsec nie ma koncepcji obiektu odpowiadającego ani inicjatora (w przeciwieństwie do domyślnych zasad protokołu IPsec). Obie strony (lokalna i Brama sieci VPN platformy Azure) będą używać tych samych ustawień dla usług IKE phase 1 i IKE Phase 2. Obsługiwane są zarówno protokoły IKEv1, jak i IKEv2. Brak obsługi platformy Azure jako obiektu odpowiadającego.
>

**Dostępne ustawienia i parametry**

| Ustawienie | Parametry |
|--- |--- |
| Szyfrowanie IKE | AES256, AES192, AES128 |
| Integralność IKE | SHA384, SHA256, SHA1 |
| Grupa DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Szyfrowanie IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Integralność IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Grupa PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
