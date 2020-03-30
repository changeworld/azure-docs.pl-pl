---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71168632"
---
## <a name="overview-of-ssh-and-keys"></a>Omówienie SSH i klawiszy

[SSH](https://www.ssh.com/ssh/) to szyfrowany protokół połączenia, który umożliwia bezpieczne logowania za nawiązywanie niezabezpieczonych połączeń. SSH jest domyślnym protokołem połączenia dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure. Chociaż sam SSH zapewnia szyfrowane połączenie, przy użyciu haseł z połączeniami SSH nadal pozostawia maszynę wirtualną narażone na ataki brute-force lub zgadywania haseł. Bezpieczniejszą i preferowaną metodą łączenia się z maszyną wirtualną przy użyciu SSH jest użycie pary kluczy publiczno-prywatnych, znanej również jako *klucze SSH.* 

* *Klucz publiczny* jest umieszczany na maszynie wirtualnej systemu Linux lub innej usłudze, której chcesz używać z kryptografią klucza publicznego.

* *Klucz prywatny* pozostaje w systemie lokalnym. Klucz prywatny należy chronić. Nie należy go udostępniać.

Gdy używasz klienta SSH do łączenia się z maszyną wirtualną z systemem Linux (która ma klucz publiczny), zdalna maszyna wirtualna testuje klienta, aby upewnić się, że posiada klucz prywatny. Jeśli klient ma klucz prywatny, jest to udzielony dostęp do maszyny Wirtualnej. 

W zależności od zasad zabezpieczeń organizacji można ponownie użyć jednej pary kluczy publiczno-prywatnych, aby uzyskać dostęp do wielu maszyn wirtualnych i usług platformy Azure. Nie potrzebujesz osobnej pary kluczy dla każdej maszyny Wirtualnej lub usługi, do której chcesz uzyskać dostęp. 

Klucz publiczny może być udostępniony każdemu, ale tylko Ty (lub twoja lokalna infrastruktura zabezpieczeń) powinien posiadać klucz prywatny.