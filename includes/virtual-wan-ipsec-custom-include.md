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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168381"
---
Podczas pracy z niestandardowymi zasadami protokołu IPsec należy pamiętać o następujących wymaganiach:

* **IKE** — w przypadku usługi IKE można wybrać dowolny parametr z szyfrowania IKE oraz dowolny parametr z ike integrity oraz dowolny parametr z grupy DH.
* **Protokół IPsec** — w przypadku protokołu IPsec można wybrać dowolny parametr z szyfrowania IPsec oraz dowolny parametr z integralności protokołu IPsec oraz doskonałe usługi utajnienia utajnienia utajnienia utajnienia zabezpieczeń. Jeśli którykolwiek z parametrów szyfrowania IPsec lub IPsec Integrity jest GCM, parametry dla obu ustawień muszą być GCM.

>[!NOTE]
> W niestandardowych zasadach protokołu IPsec nie ma pojęcia obiektu odpowiadającego i inicjatora (w przeciwieństwie do domyślnych zasad protokołu IPsec). Obie strony (lokalnie i brama sieci VPN platformy Azure) będą używać tych samych ustawień dla fazy 1 i IKE Phase 2. Obsługiwane są protokoły IKEv1 i IKEv2. Nie ma obsługi platformy Azure tylko jako obiekt odpowiadający.
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
