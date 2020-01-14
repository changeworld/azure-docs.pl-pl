---
title: Omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych
description: Dowiedz się więcej o tym, jak można używać dedykowanych hostów platformy Azure do wdrażania maszyn wirtualnych.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 81749b8fb8b9e3f1a10b65b758b532773bdebdac
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833894"
---
# <a name="azure-dedicated-hosts"></a>Dedykowane hosty platformy Azure

Dedykowany Host platformy Azure to usługa, która zapewnia serwerom fizycznym możliwość hostowania co najmniej jednej maszyny wirtualnej — dedykowanej dla jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne, które są używane w centrach danych, udostępniane jako zasób. Można udostępnić dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio na hostach, w których konfiguracja najlepiej odpowiada Twoim potrzebom.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Następne kroki

- Dedykowany Host można wdrożyć przy użyciu [Azure PowerShell](dedicated-hosts-powershell.md), [portalu](dedicated-hosts-portal.md)i [interfejsu wiersza polecenia platformy Azure](../linux/dedicated-hosts-cli.md).

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.
