---
title: Przewodnik po publikacji szablonów rozwiązań platformy Azure | Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania szablonu rozwiązania w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/25/2019
ms.author: dsindona
ms.openlocfilehash: c84436015ad37b57f6603551f1d328ac76181836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288737"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Usługi Azure Applications: Przewodnik publikowania oferty szablonów rozwiązań

Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Skorzystaj z tego przewodnika, aby zrozumieć wymagania dotyczące tej oferty. 

Użyj aplikacji platformy Azure: typ oferty szablonu rozwiązania, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną. Można zautomatyzować inicjowanie obsługi administracyjnej co najmniej jednej maszyny wirtualnej przy użyciu aplikacji platformy Azure: szablony rozwiązań. Można również aprowizować zasoby sieciowe i magazynowe. Aplikacje platformy Azure: szablony rozwiązań oferują typ zapewnia korzyści automatyzacji dla pojedynczych maszyn wirtualnych i całych rozwiązań opartych na IaaS.

Te szablony rozwiązań nie są ofertami transakcji, ale mogą służyć do wdrażania płatnych ofert maszyn wirtualnych rozliczanych za pośrednictwem komercyjnego portalu Microsoft. Wywołanie działania, które użytkownik widzi jest "Get It Now".


## <a name="requirements-for-solution-templates"></a>Wymagania dotyczące szablonów rozwiązań

| **Wymagania** | **Szczegóły**  |
| ---------------  | -----------  |
|Rozliczenia i pomiary    |  Zasoby zostaną aprowied w subskrypcji platformy Azure klienta. Maszyny wirtualne pay-as-you-go (PAYGO) będą transagowane z klientem za pośrednictwem firmy Microsoft, rozliczane za pośrednictwem subskrypcji platformy Azure klienta (PAYGO).  <br/> W przypadku licencji "bring-your-own-license" (BYOL), podczas gdy firma Microsoft rozlicza koszty infrastruktury poniesione w związku z subskrypcją klienta, użytkownik bezpośrednio dokonuje płatności za licencjonowanie oprogramowania na rzecz klienta.   |
|Wirtualny dysk twardy zgodny z platformą Azure (VHD)  |   Maszyny wirtualne muszą być zbudowane w systemie Windows lub Linux.  Aby uzyskać więcej informacji, [zobacz Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Atrybucja użycia klienta | Włączenie atrybucji użycia klienta jest wymagane we wszystkich szablonach rozwiązań opublikowanych w portalu Azure Marketplace. Aby uzyskać więcej informacji na temat atrybucji użycia klienta i sposobu jej włączania, zobacz [Atrybucja użycia klienta partnera platformy Azure](./azure-partner-customer-usage-attribution.md).  |
| Korzystanie z funkcji Dyski zarządzane | [Dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) to domyślna opcja dla utrwalonych dysków maszyn wirtualnych IaaS na platformie Azure. Dysków zarządzanych należy używać w szablonach rozwiązań. <br> <br> 1. Postępuj zgodnie [ze wskazówkami](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) i [przykładami](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) dotyczącymi używania dysków zarządzanych w szablonach arm platformy Azure do aktualizowania szablonów rozwiązań. <br> <br> 2. Postępuj zgodnie z poniższymi instrukcjami, aby zaimportować bazowy dysk wirtualny dysków zarządzanych na konto Magazynu, aby opublikować dysk VHD jako obraz w portalu Marketplace: <br> <ul> <li> [Powershell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Cli](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiłeś, [zarejestruj się](https://azuremarketplace.microsoft.com/sell) na rynku.

Jeśli jesteś zarejestrowany i tworzysz nową ofertę lub pracujesz nad istniejącą, zaloguj się do [portalu Cloud Partner,](https://cloudpartner.azure.com) aby utworzyć lub uzupełnić ofertę.
