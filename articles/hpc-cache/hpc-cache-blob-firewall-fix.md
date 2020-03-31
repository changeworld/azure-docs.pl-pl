---
title: Obejście ustawień zapory pamięci masowej
description: Ustawienie zapory sieciowej konta magazynu może spowodować błąd podczas tworzenia obiektu docelowego magazynu obiektów Blob platformy Azure w pamięci podręcznej HPC usługi Azure. W tym artykule przedstawiono obejście tego ograniczenia, dopóki nie zostanie prowadzona poprawka oprogramowania.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174410"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Obchodzenie ustawień zapory konta magazynu obiektów blob

Określone ustawienie używane w zapory kont magazynu może spowodować niepowodzenie tworzenia miejsca docelowego magazynu obiektów Blob. Zespół pamięci podręcznej HPC platformy Azure pracuje nad rozwiązaniem problemu z oprogramowaniem, ale można obejść ten problem, postępując zgodnie z instrukcjami w tym artykule.

Ustawienie zapory, które umożliwia dostęp tylko z "wybranych sieci" może uniemożliwić pamięci podręcznej tworzenie miejsca docelowego magazynu obiektów Blob. Ta konfiguracja znajduje się na stronie **Ustawień zapory i sieci wirtualnych** konta magazynu.

Problem polega na tym, że usługa pamięci podręcznej używa sieci wirtualnej usługi ukrytej, która jest oddzielona od środowisk klienta. Jawnie nie można autoryzować tej sieci w celu uzyskania dostępu do konta magazynu.

Podczas tworzenia obiektu docelowego magazynu obiektów Blob usługa pamięci podręcznej używa tej sieci, aby sprawdzić, czy kontener jest pusty. Jeśli zapora nie zezwala na dostęp z ukrytej sieci, sprawdzanie kończy się niepowodzeniem, a tworzenie obiektu docelowego magazynu zakończy się niepowodzeniem.

Aby obejść ten problem, tymczasowo zmień ustawienia zapory podczas tworzenia miejsca docelowego magazynu:

1. Przejdź do strony **Zapory i sieci wirtualnych** konta magazynu i zmień ustawienie "Zezwalaj na dostęp z" na **Wszystkie sieci**.
1. Utwórz miejsce docelowe magazynu obiektów blob w pamięci podręcznej HPC platformy Azure.
1. Po pomyślnym utworzeniu obiektu docelowego magazynu zmień ustawienie zapory konta z powrotem na **Wybrane sieci**.

Usługa HpC Cache usługi Azure nie używa sieci wirtualnej usługi, aby uzyskać dostęp do gotowego miejsca docelowego magazynu.

Aby uzyskać pomoc dotyczącą tego obejścia, skontaktuj się z [usługą i pomocą techniczną firmy Microsoft](hpc-cache-support-ticket.md).
