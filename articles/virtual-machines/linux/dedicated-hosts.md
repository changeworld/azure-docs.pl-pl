---
title: Omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych
description: Dowiedz się więcej o tym, jak można używać dedykowanych hostów platformy Azure do wdrażania maszyn wirtualnych.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: f4a5e78afdf774af056d7c9b8a108c3418c2caa4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036390"
---
# <a name="preview-azure-dedicated-hosts"></a>Wersja zapoznawcza: dedykowane hosty platformy Azure

Dedykowany Host platformy Azure to usługa, która zapewnia serwerom fizycznym możliwość hostowania co najmniej jednej maszyny wirtualnej — dedykowanej dla jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne, które są używane w centrach danych, udostępniane jako zasób. Można udostępnić dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio na hostach, w których konfiguracja najlepiej odpowiada Twoim potrzebom.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


virtual-machines-common-dedicated-hosts-preview.md

## <a name="next-steps"></a>Następne kroki

- Można wdrożyć dedykowanego hosta za pomocą [interfejsu wiersza polecenia platformy Azure](dedicated-hosts-cli.md), [portalu](dedicated-hosts-portal.md)i [programu PowerShell](../windows/dedicated-hosts-powershell.md).

- Aby uzyskać więcej informacji, zobacz Omówienie [dedykowanych hostów](dedicated-hosts.md) .

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.