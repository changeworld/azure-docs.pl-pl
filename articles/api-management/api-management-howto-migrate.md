---
title: Jak przeprowadzić migrację usługi Azure API Management między regionami
description: Dowiedz się, jak przeprowadzić migrację wystąpienia zarządzania interfejsami API z jednego regionu do drugiego.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073463"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Jak przeprowadzić migrację usługi Azure API Management między regionami
Aby przeprowadzić migrację wystąpień usługi API Management z jednego regionu platformy Azure do innego, można użyć funkcji [tworzenia kopii zapasowych i przywracania.](api-management-howto-disaster-recovery-backup-restore.md) Należy wybrać tę samą warstwę cenową zarządzania interfejsami API w regionach źródłowych i docelowych. 

> [!NOTE]
> Tworzenie kopii zapasowych i przywracanie nie będzie działać podczas migracji między różnymi typami chmury. W tym celu musisz wyeksportować zasób [jako szablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Następnie dostosuj wyeksportowany szablon dla docelowego regionu platformy Azure i ponownie utwórz zasób. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opcja 1: Użyj innej nazwy wystąpienia usługi API Management

1. W regionie docelowym utwórz nowe wystąpienie usługi API Management z tą samą warstwą cenową co źródłowe wystąpienie usługi API Management. Nowe wystąpienie powinno mieć inną nazwę. 
1. Tworzenie kopii zapasowych istniejącego wystąpienia zarządzania interfejsami API na koncie magazynu.
1. Przywróć kopię zapasową utworzoną w kroku 2 do nowego wystąpienia zarządzania interfejsami API utworzonego w nowym regionie w kroku 1.
1. Jeśli masz domenę niestandardową wskazującą wystąpienie zarządzania interfejsami API regionu źródłowego, zaktualizuj cname domeny niestandardowej, aby wskazywało nowe wystąpienie usługi API Management. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opcja 2: Użyj tej samej nazwy wystąpienia usługi API Management

> [!NOTE]
> Ta opcja spowoduje przestoje podczas migracji.

1. Łącze zapasowe wystąpienia zarządzania interfejsami API w regionie źródłowym na koncie magazynu.
1. Usuń zarządzanie interfejsami API w regionie źródłowym. 
1. Utwórz nowe wystąpienie zarządzania interfejsami API w regionie docelowym o takiej samej nazwie jak w regionie źródłowym.
1. Przywróć kopię zapasową utworzoną w kroku 1 do nowego wystąpienia zarządzania interfejsami API w regionie docelowym.  


## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki
* Aby uzyskać więcej informacji na temat funkcji tworzenia kopii zapasowych i przywracania, zobacz [jak zaimplementować odzyskiwanie po awarii](api-management-howto-disaster-recovery-backup-restore.md).
* Aby uzyskać informacje na temat migracji zasobów platformy Azure, zobacz [wskazówki dotyczące migracji między regionami platformy Azure](https://github.com/Azure/Azure-Migration-Guidance).