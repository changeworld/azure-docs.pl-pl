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
ms.openlocfilehash: c12fff63cdb7241d89e7511a3dac2ff9c1363ae6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540486"
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania
* **Subskrypcja platformy Azure** — Aby wdrożyć więcej niż kilka wystąpień obliczeniowych, należy wziąć pod uwagę subskrypcji zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* **Ceny i dostępności** -rozmiarów maszyn wirtualnych te są oferowane tylko w standardowej warstwy cenowej. Sprawdź [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) dostępność w regionach platformy Azure. 
* **Limit przydziału rdzeni** — może być konieczne zwiększyć limit przydziału rdzeni w ramach subskrypcji platformy Azure z wartości domyślnej. Twoja subskrypcja może także ograniczać liczbę rdzeni, które można wdrożyć w maszynie Wirtualnej rodzinach o określonym rozmiarze, łącznie z serii h. Aby zażądać zwiększenia limitu przydziału [Otwórz żądanie obsługi klienta online](../articles/azure-supportability/how-to-create-azure-support-request.md) bez dodatkowych opłat. (Domyślne limity mogą się różnić w zależności od kategorii subskrypcji).
  
  > [!NOTE]
  > Jeśli masz zapotrzebowanie na pojemność, skontaktuj się z działem pomocy technicznej systemu Azure. Przydziały dla platformy Azure dotyczą środków limitów, nie w zakresie pojemności. Niezależnie od limitu przydziału opłaty są naliczane tylko za rdzenie użycie.
  > 
  > 
* **Sieć wirtualna** — Azure [sieci wirtualnej](https://azure.microsoft.com/documentation/services/virtual-network/) nie muszą korzystać z wystąpień obliczeniowych. Jednak dla wielu wdrożeń należy co najmniej oparte na chmurze sieć wirtualną platformy Azure lub połączenia lokacja lokacja — Jeśli potrzebujesz dostępu do zasobów w środowisku lokalnym. W razie potrzeby, Utwórz nową sieć wirtualną do wdrożenia wystąpienia. Dodawanie maszyn wirtualnych intensywnie do sieci wirtualnej w grupie koligacji nie jest obsługiwane.
* **Zmiana rozmiaru** — ze względu na ich specjalistycznego sprzętu, można tylko zmienić rozmiar mocy obliczeniowej wystąpień w ramach tej samej rodziny rozmiaru (seria H lub mocy obliczeniowej serii A). Na przykład można tylko zmienić rozmiar Maszynie wirtualnej serii H z jednego wystąpienia serii H rozmiaru do innego. Ponadto zmiana rozmiaru z innych niż intensywnych obliczeń rozmiaru rozmiarowi intensywnych obliczeń nie jest obsługiwane.  

## <a name="rdma-capable-instances"></a>Wystąpienia z obsługą technologii RDMA
Podzbiór wystąpień mocy obliczeniowej (H16r, H16mr, A8 i A9) są wyposażone w interfejs sieciowy, łączność dostępu (RDMA) zdalnego pamięci. (Rozmiary wybranych serii N oznaczone ciągiem "r", takich jak NC24r również są funkcją RDMA). Ten interfejs jest oprócz interfejsu standardowa sieci platformy Azure dostępne dla innych rozmiarów maszyn wirtualnych. 
  
Ten interfejs umożliwia obsługą dostępu RDMA wystąpień do komunikowania się za pośrednictwem sieci InfiniBand (IB), działające na stawki FDR H16r, H16mr i obsługą dostępu RDMA seria maszyn wirtualnych i QDR stawki za maszyny wirtualne A8 i A9. Te możliwości RDMA może zwiększyć skalowalność i wydajność niektórych aplikacji interfejsu przekazywania komunikatów (MPI).

> [!NOTE]
> IP over IB nie jest obsługiwana na platformie Azure. Tylko RDMA over IB jest obsługiwane.
>

