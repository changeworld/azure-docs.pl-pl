---
title: Rozwiązanie VMware według CloudSimple — publiczny adres IP platformy Azure
description: Dowiedz się więcej na temat publicznych adresów IP i ich korzyści dla rozwiązań VMware według CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812525"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Publiczny adres IP CloudSimple — Omówienie

Publiczny adres IP umożliwia zasobom internetowym komunikowanie się z ruchem przychodzącym do zasobów w chmurze prywatnej w prywatnym adresie IP. Prywatny adres IP to maszyna wirtualna lub programowy moduł równoważenia obciążenia. Prywatny adres IP znajduje się w prywatnej chmurze vCenter. Publiczny adres IP umożliwia udostępnianie usług uruchomionych w chmurze prywatnej w Internecie.

Publiczny adres IP jest przydzielany do prywatnego adresu IP, dopóki nie zostanie przypisany. Publiczny adres IP można przypisać tylko do jednego prywatnego adresu IP.

Zasób skojarzony z publicznym adresem IP zawsze używa publicznego adresu IP na potrzeby dostępu do Internetu. Domyślnie na publicznym adresie IP jest dozwolony tylko wychodzący dostęp do Internetu.  Odmowa ruchu przychodzącego na publicznym adresie IP.  Aby zezwolić na ruch przychodzący, Utwórz regułę zapory dla publicznego adresu IP na określonym porcie.

## <a name="benefits"></a>Korzyści

Użycie publicznego adresu IP do komunikacji przychodzącej zapewnia następujące informacje:

* Ochrona przed atakami typu "odmowa usługi" (DDoS). Ta ochrona jest automatycznie włączona dla publicznego adresu IP.
* Zawsze włączone monitorowanie ruchu i łagodzenie w czasie rzeczywistym typowych ataków na poziomie sieci. Te zabezpieczenia są tymi samymi zabezpieczeniami, które są używane przez firmę Microsoft Usługi online.
* Cała Skala sieci globalnej platformy Azure. Za pomocą tej sieci można dystrybuować i ograniczać ruch związany z atakami między regionami.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przydzielić publiczny adres IP](https://docs.azure.cloudsimple.com/public-ips/)
