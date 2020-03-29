---
title: Omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych
description: Dowiedz się więcej o tym, jak hosty dedykowane platformy Azure mogą być używane do wdrażania maszyn wirtualnych.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970726"
---
# <a name="azure-dedicated-hosts"></a>Dedykowane hosty platformy Azure

Azure Dedicated Host to usługa, która zapewnia serwery fizyczne — mogą obsługiwać jedną lub więcej maszyn wirtualnych — dedykowane jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne używane w naszych centrach danych, dostarczane jako zasób. Można aprowizować dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio do hostów aprowizowanych, niezależnie od konfiguracji najlepiej spełnia twoje potrzeby.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Następne kroki

- Dedykowanego hosta można wdrożyć za pomocą [interfejsu wiersza polecenia platformy Azure,](dedicated-hosts-cli.md) [portalu](dedicated-hosts-portal.md)i [programu PowerShell.](../windows/dedicated-hosts-powershell.md)

- Aby uzyskać więcej informacji, zobacz [przegląd dedykowanych hostów.](dedicated-hosts.md)

- Istnieje przykładowy szablon, znaleziono [tutaj,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)który używa zarówno stref i domen błędów dla maksymalnej odporności w regionie.

- Można również zaoszczędzić na kosztach dzięki [zarezerwowane wystąpienie hostów dedykowanych platformy Azure.](../prepay-dedicated-hosts-reserved-instances.md)
