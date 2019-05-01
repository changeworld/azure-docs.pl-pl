---
title: VMware Solution by CloudSimple - Azure publiczny adres IP
description: Dowiedz się więcej o publicznych adresów IP i ich zalet na VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a366ec821e5e9663908f790ab3a88424fa956075
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576973"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple publicznych adresami IP — omówienie

Publiczny adres IP umożliwia zasobom internetowym komunikowanie się dla ruchu przychodzącego do zasobów chmury prywatnej na prywatny adres IP. Prywatny adres IP jest maszyną wirtualną lub programowego modułu równoważenia obciążenia. Prywatny adres IP znajduje się na vCenter chmury prywatnej. Publiczny adres IP umożliwia udostępnianie usług działających w chmurze prywatnej do Internetu.

Publiczny adres IP jest przeznaczona do prywatnego adresu IP, dopóki nie można cofnąć przypisania. Publiczny adres IP można przypisać tylko jeden prywatny adres IP.

Zasób skojarzony z publicznym adresem IP zawsze używa publicznego adresu IP, aby uzyskać dostęp do Internetu. Domyślnie tylko wychodzącego dostępu do Internetu jest dozwolona na publiczny adres IP.  Ruch przychodzący na publiczny adres IP zostanie odrzucone.  Aby zezwolić na ruch przychodzący, Utwórz regułę zapory dla publicznego adresu IP określonego portu.

## <a name="benefits"></a>Korzyści

Za pomocą publicznego adresu IP do komunikowania się ruchu przychodzącego zapewnia:

* Rozproszona odmowa zapobieganie atakom service (DDoS). Ta ochrona jest automatycznie włączona dla publicznego adresu IP.
* Zawsze włączone ruchu monitorowania i w czasie rzeczywistym środki zaradcze typowych ataków na poziomie sieci. Te zabezpieczenia są tego samego mechanizmów obronnych posługują się usługi online firmy Microsoft.
* Skalę całej sieci globalnej platformy Azure. Sieci może służyć do dystrybucji, a także zmniejszyć ruch ataku w regionach.  

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [przydzielony publiczny adres IP](https://docs.azure.cloudsimple.com/publicips/)
