---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: c6ef868d80e628f9120acc0775179e89ccd03674
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979111"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| procesorów wirtualnych na [subskrypcji](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10 000 |
| [Współadministratorzy](../articles/billing-add-change-azure-subscription-administrator.md) na subskrypcję |200 |200 |
| [Konta magazynu](../articles/storage/common/storage-quickstart-create-account.md) na region na subskrypcję<sup>2</sup> |200 |250 |
| [Usługi w chmurze](../articles/cloud-services/cloud-services-choose-me.md) na subskrypcję |20 |200 |
| [Sieci lokalne](http://msdn.microsoft.com/library/jj157100.aspx) na subskrypcję |10 |500 |
| Serwery usługi SQL Database na subskrypcję |6 |200 |
| Serwery DNS na subskrypcję |9 |100 |
| Zastrzeżone adresy IP na subskrypcję |20 |100 |
| Certyfikaty usług hostowanych na subskrypcję |199 |199 |
| [Grupy koligacji](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) na subskrypcję |256 |256 |


<sup>1</sup>wystąpieniom bardzo małe wystąpienia liczby jako jeden procesor wirtualny vCPU kierunku limit procesorów wirtualnych, pomimo za pomocą częściowej rdzeń procesora CPU.

<sup>2</sup>limit kont magazynu obejmuje konta magazynu warstwy standardowa i Premium. Jeśli potrzebujesz więcej niż 200 kont magazynu w danym regionie, należy wykonać żądanie za pośrednictwem [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/). Zespół usługi Azure Storage przejrzy Twój przypadek biznesowy i może zatwierdzić do 250 kont magazynu dla danego regionu. 

