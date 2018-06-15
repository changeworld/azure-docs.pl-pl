---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 919582745d166cf8b3f3937f9bac4fc0dc1fe64f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "31613410"
---
## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołu SSH oraz kluczy

SSH jest protokołem zaszyfrowanego połączenia, który umożliwia bezpiecznego logowania za pośrednictwem niezabezpieczonego połączenia. Jest domyślnym protokołem połączenia dla maszyn wirtualnych systemu Linux hostowanych w systemie Azure. SSH sam zapewnia połączenie szyfrowane, jednak za pomocą hasła przy użyciu połączeń SSH nadal pozostawia maszyny Wirtualnej podatne na siłowych ataków lub zgadywania haseł. Jest większe bezpieczeństwo i preferowaną metodę połączenie z maszyną wirtualną przy użyciu protokołu SSH za pomocą pary kluczy publiczno prywatnych, znanej także jako kluczy SSH. 

* *Klucz publiczny* znajduje się w sieci maszyny Wirtualnej systemu Linux lub innych usług, które mają być używane z kryptografii klucza publicznego.

* *Klucza prywatnego* jest co przedstawienie do maszyny Wirtualnej systemu Linux podczas tworzenia połączenia SSH, aby zweryfikować swoją tożsamość. Klucz prywatny należy chronić. Nie należy go udostępniać.

W zależności od zasad zabezpieczeń w organizacji można ponownie użyć pojedynczego pary kluczy prywatny publiczny dostęp do wielu usług i maszyn wirtualnych platformy Azure. Nie trzeba oddzielne pary kluczy dla każdej maszyny Wirtualnej lub usługi, które chcesz uzyskać dostęp. 

Klucz publiczny można udostępniać wszystkim, ale tylko Ty (lub lokalna infrastruktura zabezpieczeń) posiadasz klucz prywatny.