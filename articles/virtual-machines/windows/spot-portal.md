---
title: Wdrażanie maszyn wirtualnych platformy Azure spot za pomocą portalu
description: Dowiedz się, jak używać programu Azure PowerShell do wdrażania maszyn wirtualnych w miejscu, aby zaoszczędzić na kosztach.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547382"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Wdrażanie maszyn wirtualnych w miejscu przy użyciu witryny Azure portal

Korzystanie z [maszyn wirtualnych spot](spot-vms.md) pozwala na wykorzystanie naszej niewykorzystanej pojemności przy znacznych oszczędnościach kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure będzie eksmitować maszyny wirtualne spot. W związku z tym maszyny wirtualne spot są idealne dla obciążeń, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperów/testów, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych punktowych są zmienne na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz Ceny maszyn wirtualnych dla [systemów Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [Maszyny wirtualne spot - Cennik](spot-vms.md#pricing).

Masz możliwość, aby ustawić maksymalną cenę jesteś gotów zapłacić, za godzinę, dla maszyny Wirtualnej. Maksymalna cena maszyny Wirtualnej spot można ustawić w dolarach amerykańskich (USD), przy użyciu miejsc po przecinku do 5. Na przykład wartość `0.05701`będzie maksymalna cena $0.05701 USD za godzinę. Jeśli ustawisz maksymalną `-1`cenę, maszyna wirtualna nie zostanie eksmitowana na podstawie ceny. Cena za maszynę wirtualną będzie bieżącą ceną spot lub ceną standardowej maszyny Wirtualnej, która kiedykolwiek jest mniejsza, o ile dostępna jest pojemność i przydział.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Proces tworzenia maszyny wirtualnej korzystającej z maszyn wirtualnych punktowych jest taki sam, jak opisano w [przewodniku Szybki start](quick-create-portal.md). Podczas wdrażania maszyny Wirtualnej, można użyć wystąpienia spot platformy Azure.


Na karcie **Podstawy** w sekcji **Szczegóły wystąpienia** **nie** jest ustawieniem domyślnym dla korzystania z wystąpienia spot platformy Azure.

![Przechwytywanie ekranu w celu wybrania nie, nie używaj wystąpienia spot platformy Azure](media/spot-portal/no.png)

Wybierz opcję **Tak**, sekcja rozwija się i możesz wybrać [typ eksmisji i zasady eksmisji.](spot-vms.md#eviction-policy) 

![Przechwytywanie ekranu w celu wybrania tak, użyj wystąpienia spot platformy Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Następne kroki

Maszyny wirtualne punktowe można również tworzyć za pomocą programu [PowerShell](spot-powershell.md).