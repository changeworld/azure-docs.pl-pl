---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506375"
---
## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołu SSH i klucze

Protokół SSH jest protokołem szyfrowanego połączenia, który umożliwia bezpieczne logowania za pośrednictwem niezabezpieczonych połączeń. Protokół SSH jest domyślnym protokołem połączenia maszyn wirtualnych systemu Linux hostowanych na platformie Azure. Chociaż protokół SSH, sama udostępnia połączenie szyfrowane, przy użyciu haseł z połączeniami SSH nadal pozostawia to maszyna wirtualna narażony na atak metodą siłową ataków lub zgadywania haseł. Większe bezpieczeństwo i preferowaną metodą nawiązywania połączenia z maszyną wirtualną przy użyciu protokołu SSH jest za pomocą pary kluczy publiczny prywatny, znany także jako *kluczy SSH*. 

* *Klucza publicznego* znajduje się na maszynie Wirtualnej systemu Linux lub dowolnej innej usługi, którego chcesz użyć z kryptografii klucza publicznego.

* *Klucza prywatnego* na możesz system lokalny jest używany przez klienta SSH do zweryfikowania Twojej tożsamości podczas nawiązywania połączenia z maszyną Wirtualną systemu Linux. Klucz prywatny należy chronić. Nie należy go udostępniać.

W zależności od zasad zabezpieczeń organizacji można ponownie użyć pojedynczego pary kluczy publiczny prywatny dostęp do wielu maszyn wirtualnych platformy Azure i usług. Nie potrzebujesz oddzielnych pary kluczy dla każdej maszyny Wirtualnej lub usługi, które chcesz uzyskać dostęp. 

Klucz publiczny można udostępniać wszystkim, ale tylko użytkownik (lub lokalna infrastruktura zabezpieczeń) powinny mieć klucz prywatny.