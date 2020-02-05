---
title: Azure VMware Solutions (Automatyczna synchronizacja) — publiczny adres IP
description: Informacje o publicznych adresach IP i ich korzyściach związanych z rozwiązaniami VMware platformy Azure (Automatyczna synchronizacja)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024980"
---
# <a name="avs-public-ip-address-overview"></a>Przegląd informacji o publicznym adresie IP dotyczącej automatycznej synchronizacji

Publiczny adres IP pozwala zasobom internetowym komunikować się z ruchem przychodzącym w celu automatycznej synchronizacji zasobów chmury prywatnej przy użyciu prywatnego adresu IP. Prywatny adres IP to maszyna wirtualna lub programowy moduł równoważenia obciążenia w programie vCenter w chmurze prywatnej chmury. Publiczny adres IP pozwala uwidocznić usługi działające w chmurze prywatnej automatycznej synchronizacji z Internetem.

Publiczny adres IP jest przydzielany do prywatnego adresu IP, dopóki nie zostanie przypisany. Publiczny adres IP można przypisać tylko do jednego prywatnego adresu IP.

Zasób skojarzony z publicznym adresem IP zawsze używa publicznego adresu IP na potrzeby dostępu do Internetu. Domyślnie na publicznym adresie IP jest dozwolony tylko wychodzący dostęp do Internetu.  Odmowa ruchu przychodzącego na publicznym adresie IP.  Aby zezwolić na ruch przychodzący, Utwórz regułę zapory dla publicznego adresu IP na określonym porcie.

## <a name="benefits"></a>Korzyści

Użycie publicznego adresu IP do komunikacji przychodzącej zapewnia następujące informacje:

* Ochrona przed atakami typu "odmowa usługi" (DDoS). Ta ochrona jest automatycznie włączona dla publicznego adresu IP.
* Zawsze włączone monitorowanie ruchu i łagodzenie w czasie rzeczywistym typowych ataków na poziomie sieci. Te zabezpieczenia są tymi samymi zabezpieczeniami, które są używane przez firmę Microsoft Usługi online.
* Cała Skala sieci globalnej platformy Azure. Za pomocą tej sieci można dystrybuować i ograniczać ruch związany z atakami między regionami.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przydzielić publiczny adres IP](public-ips.md)