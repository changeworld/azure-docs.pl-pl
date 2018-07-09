---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep
ms.custom: include file
ms.openlocfilehash: 296e92d803bb69376f286aa60cfb4a955b08010f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "34669350"
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania
* **Subskrypcja platformy Azure** — Aby wdrożyć więcej niż kilka wystąpień obliczeniowych, należy wziąć pod uwagę subskrypcji zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* **Ceny i dostępności** -rozmiarów maszyn wirtualnych te są oferowane tylko w standardowej warstwy cenowej. Sprawdź [dostępne produkty według regionu] (https://azure.microsoft.com/regions/services/) dostępność w regionach platformy Azure. 
* **Limit przydziału rdzeni** — może być konieczne zwiększyć limit przydziału rdzeni w ramach subskrypcji platformy Azure z wartości domyślnej. Twoja subskrypcja może także ograniczać liczbę rdzeni, które można wdrożyć w maszynie Wirtualnej rodzinach o określonym rozmiarze, łącznie z serii h. Aby zażądać zwiększenia limitu przydziału [Otwórz żądanie obsługi klienta online](../articles/azure-supportability/how-to-create-azure-support-request.md) bez dodatkowych opłat. (Domyślne limity mogą się różnić w zależności od kategorii subskrypcji).
  
  > [!NOTE]
  > Jeśli masz zapotrzebowanie na pojemność, skontaktuj się z działem pomocy technicznej systemu Azure. Przydziały dla platformy Azure dotyczą środków limitów, nie w zakresie pojemności. Niezależnie od limitu przydziału opłaty są naliczane tylko za rdzenie użycie.
  > 
  > 
* **Sieć wirtualna** — Azure [sieci wirtualnej](https://azure.microsoft.com/documentation/services/virtual-network/) nie muszą korzystać z wystąpień obliczeniowych. Jednak dla wielu wdrożeń należy co najmniej oparte na chmurze sieć wirtualną platformy Azure lub połączenia lokacja lokacja — Jeśli potrzebujesz dostępu do zasobów w środowisku lokalnym. W razie potrzeby, Utwórz nową sieć wirtualną do wdrożenia wystąpienia. Dodawanie maszyn wirtualnych intensywnie do sieci wirtualnej w grupie koligacji nie jest obsługiwane.
* **Zmiana rozmiaru** — ze względu na ich specjalistycznego sprzętu, można tylko zmienić rozmiar mocy obliczeniowej wystąpień w ramach tej samej rodziny rozmiaru (seria H lub mocy obliczeniowej serii A). Na przykład można tylko zmienić rozmiar Maszynie wirtualnej serii H z jednego wystąpienia serii H rozmiaru do innego. Ponadto zmiana rozmiaru z innych niż intensywnych obliczeń rozmiaru rozmiarowi intensywnych obliczeń nie jest obsługiwane.  

## <a name="rdma-capable-instances"></a>Wystąpienia z obsługą dostępu RDMA
Podzbiór wystąpień mocy obliczeniowej (H16r, H16mr, A8 i A9) są wyposażone w interfejs sieciowy, łączność dostępu (RDMA) zdalnego pamięci. (Rozmiary wybranych serii N oznaczone ciągiem "r", takich jak NC24r również są funkcją RDMA). Ten interfejs jest oprócz interfejsu standardowa sieci platformy Azure dostępne dla innych rozmiarów maszyn wirtualnych. 
  
Ten interfejs umożliwia obsługą dostępu RDMA wystąpień do komunikowania się za pośrednictwem sieci InfiniBand (IB), działające na stawki FDR H16r, H16mr i obsługą dostępu RDMA seria maszyn wirtualnych i QDR stawki za maszyny wirtualne A8 i A9. Te możliwości RDMA może zwiększyć skalowalność i wydajność niektórych aplikacji interfejsu przekazywania komunikatów (MPI).

> [!NOTE]
> IP over IB nie jest obsługiwana na platformie Azure. Tylko RDMA over IB jest obsługiwane.
>

Wdróż obsługą dostępu RDMA maszyn wirtualnych HPC, w tym samym zestawie dostępności lub zestawie skalowania maszyn wirtualnych (Jeśli używasz modelu wdrażania usługi Azure Resource Manager) lub samej usługi w chmurze (Jeśli używasz klasycznego modelu wdrażania). Jeśli używasz zestawu skalowania maszyn wirtualnych, upewnij się, ograniczenie wdrożenia do pojedynczej grupy umieszczania; na przykład w szablonie usługi Resource Manager, należy ustawić *singlePlacementGroup* właściwości *true*. Wykonaj dodatkowe wymagania z funkcją RDMA HPC maszyn wirtualnych dostęp do sieci RDMA na platformie Azure.