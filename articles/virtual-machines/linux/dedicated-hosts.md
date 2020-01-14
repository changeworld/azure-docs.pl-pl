---
title: Omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych
description: Dowiedz się więcej o tym, jak można używać dedykowanych hostów platformy Azure do wdrażania maszyn wirtualnych.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 940f27612b26c5baace7a19d2212fa6d8899e6e5
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834909"
---
# <a name="azure-dedicated-hosts"></a>Dedykowane hosty platformy Azure

Dedykowany Host platformy Azure to usługa, która zapewnia serwerom fizycznym możliwość hostowania co najmniej jednej maszyny wirtualnej — dedykowanej dla jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne, które są używane w centrach danych, udostępniane jako zasób. Można udostępnić dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio na hostach, w których konfiguracja najlepiej odpowiada Twoim potrzebom.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Następne kroki

- Można wdrożyć dedykowanego hosta za pomocą [interfejsu wiersza polecenia platformy Azure](dedicated-hosts-cli.md), [portalu](dedicated-hosts-portal.md)i [programu PowerShell](../windows/dedicated-hosts-powershell.md).

- Aby uzyskać więcej informacji, zobacz Omówienie [dedykowanych hostów](dedicated-hosts.md) .

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.