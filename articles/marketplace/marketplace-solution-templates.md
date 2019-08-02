---
title: Przewodnik publikowania szablonów rozwiązań aplikacji platformy Azure | Portal Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania szablonu rozwiązania w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: 8d02d942fce7bd51a116cc4c19eac9faca0060ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561492"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplikacje platformy Azure: Przewodnik publikowania oferty szablonu rozwiązania

Szablony rozwiązań to jeden z głównych sposobów publikowania rozwiązania w portalu Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tej oferty. 

Użyj aplikacji platformy Azure: typ oferty szablonu rozwiązania, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną. Możesz zautomatyzować Inicjowanie obsługi jednej lub wielu maszyn wirtualnych za pomocą usługi Azure Apps: szablony rozwiązań. Możesz również udostępnić zasoby sieciowe i magazynowe. Azure Apps: typ oferty szablonów rozwiązań zapewnia korzyści automatyzacji dla pojedynczych maszyn wirtualnych i całych rozwiązań opartych na IaaS.

Te szablony rozwiązań to oferty transakcji, które są wdrażane i rozliczane za pomocą witryny Marketplace. Wywołanie akcji, którą widzi użytkownik, to "Pobierz teraz".


## <a name="requirements-for-solution-templates"></a>Wymagania dotyczące szablonów rozwiązań

| **Wymagania** | **Szczegóły**  |
| ---------------  | -----------  |
|Rozliczenia i pomiary    |  Zasoby będą obsługiwane w ramach subskrypcji platformy Azure klienta. Maszyny wirtualne z płatnością zgodnie z rzeczywistym użyciem (PAYGO) będą w firmie Microsoft rozliczane za pośrednictwem subskrypcji platformy Azure (PAYGO) klienta.  <br/> W przypadku dołączenia do własnej licencji (BYOL), podczas gdy firma Microsoft będzie rozliczać koszty infrastruktury związane z subskrypcją klienta, należy bezpośrednio wprowadzić opłaty za licencję na oprogramowanie klienta.   |
|Wirtualny dysk twardy (VHD) zgodny z platformą Azure  |   Maszyny wirtualne muszą być wbudowane w system Windows lub Linux.  Aby uzyskać więcej informacji, [Zobacz Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Przypisanie użycia klienta | Włączenie przypisywania użycia klienta jest wymagane we wszystkich szablonach rozwiązań opublikowanych w portalu Azure Marketplace. Aby uzyskać więcej informacji na temat przypisywania użycia klientów i sposobu ich włączania, zobacz temat przypisanie [użycia klienta przez partnera platformy Azure](./azure-partner-customer-usage-attribution.md).  |
| Użyj funkcji Dyski zarządzane | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) jest opcją domyślną dla utrwalonych dysków maszyn wirtualnych IaaS na platformie Azure. W szablonach rozwiązań należy używać Managed Disks. <br> <br> 1. Postępuj [](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) zgodnie ze wskazówkami i [przykładami](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) dotyczącymi używania Managed disks w SZABLONach usługi Azure ARM w celu zaktualizowania szablonów rozwiązań. <br> <br> 2. Postępuj zgodnie z poniższymi instrukcjami, aby zaimportować źródłowy wirtualny dysk twardy Managed Disks na konto magazynu w celu opublikowania dysku VHD jako obrazu w portalu Marketplace: <br> <ul> <li> [Program PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Interfejs wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Kolejne kroki
Jeśli jeszcze tego nie zrobiono, [zarejestruj](https://azuremarketplace.microsoft.com/sell) się w portalu Marketplace.

Jeśli jesteś zarejestrowanym użytkownikiem i tworzysz nową ofertę lub pracujesz nad istniejącym, zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com) , aby utworzyć lub zakończyć swoją ofertę.
