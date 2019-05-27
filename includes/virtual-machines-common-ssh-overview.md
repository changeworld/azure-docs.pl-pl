---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bca78e2963f19b60071b1b27c8dc65c76818e10e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166092"
---
## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołu SSH i klucze

[SSH](https://www.ssh.com/ssh/) to szyfrowanego połączenia, który umożliwia bezpieczne logowania za pośrednictwem niezabezpieczonych połączeń. Protokół SSH jest domyślnym protokołem połączenia maszyn wirtualnych systemu Linux hostowanych na platformie Azure. Chociaż protokół SSH, sama udostępnia połączenie szyfrowane, przy użyciu haseł z połączeniami SSH nadal pozostawia to maszyna wirtualna narażony na atak metodą siłową ataków lub zgadywania haseł. Większe bezpieczeństwo i preferowaną metodą nawiązywania połączenia z maszyną wirtualną przy użyciu protokołu SSH jest za pomocą pary kluczy publiczny prywatny, znany także jako *kluczy SSH*. 

* *Klucza publicznego* znajduje się na maszynie Wirtualnej systemu Linux lub dowolnej innej usługi, którego chcesz użyć z kryptografii klucza publicznego.

* *Klucza prywatnego* pozostaje w systemie lokalnym. Klucz prywatny należy chronić. Nie należy go udostępniać.

Kiedy używasz klienta SSH połączyć się z systemem Linux maszyny Wirtualnej (który ma klucz publiczny) zdalnej maszynie Wirtualnej testy klienta, aby upewnić się, że posiada on klucza prywatnego. Jeśli klient ma klucz prywatny, że udzielono mu dostęp do maszyny Wirtualnej. 

W zależności od zasad zabezpieczeń organizacji można ponownie użyć pojedynczego pary kluczy publiczny prywatny dostęp do wielu maszyn wirtualnych platformy Azure i usług. Nie potrzebujesz oddzielnych pary kluczy dla każdej maszyny Wirtualnej lub usługi, które chcesz uzyskać dostęp. 

Klucz publiczny można udostępniać wszystkim, ale tylko użytkownik (lub lokalna infrastruktura zabezpieczeń) powinny mieć klucz prywatny.