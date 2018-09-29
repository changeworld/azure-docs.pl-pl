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
ms.openlocfilehash: 0363b719e2587226282257e19e58e3c4e55c2219
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454539"
---
## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołu SSH i klucze

Protokół SSH jest protokołem szyfrowanego połączenia, który umożliwia bezpieczne logowania za pośrednictwem niezabezpieczonych połączeń. Protokół SSH jest domyślnym protokołem połączenia maszyn wirtualnych systemu Linux hostowanych na platformie Azure. Chociaż protokół SSH, sama udostępnia połączenie szyfrowane, przy użyciu haseł z połączeniami SSH nadal pozostawia to maszyna wirtualna narażony na atak metodą siłową ataków lub zgadywania haseł. Większe bezpieczeństwo i preferowaną metodą nawiązywania połączenia z maszyną wirtualną przy użyciu protokołu SSH jest za pomocą pary kluczy publiczny prywatny, znany także jako *kluczy SSH*. 

* *Klucza publicznego* znajduje się na maszynie Wirtualnej systemu Linux lub dowolnej innej usługi, którego chcesz użyć z kryptografii klucza publicznego.

* *Klucza prywatnego* jest przedstawiany do maszyny Wirtualnej systemu Linux po wprowadzeniu połączenie SSH, aby zweryfikować swoją tożsamość. Klucz prywatny należy chronić. Nie należy go udostępniać.

W zależności od zasad zabezpieczeń organizacji można ponownie użyć pojedynczego pary kluczy publiczny prywatny dostęp do wielu maszyn wirtualnych platformy Azure i usług. Nie potrzebujesz oddzielnych pary kluczy dla każdej maszyny Wirtualnej lub usługi, które chcesz uzyskać dostęp. 

Klucz publiczny można udostępniać wszystkim, ale tylko użytkownik (lub lokalna infrastruktura zabezpieczeń) powinny mieć klucz prywatny.