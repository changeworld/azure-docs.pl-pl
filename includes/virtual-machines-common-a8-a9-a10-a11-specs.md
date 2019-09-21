---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 0930fa371500125c15cd969b9e9f4b7a2853612e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174978"
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania
* **Subskrypcja platformy Azure** — aby wdrożyć więcej niż kilka wystąpień intensywnie korzystających z obliczeń, weź pod uwagę subskrypcję z płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* **Cennik i dostępność** — te rozmiary maszyn wirtualnych są oferowane tylko w warstwie cenowej standardowa. Sprawdź dostępność [produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) w regionach świadczenia usługi Azure. 
* **Przydział rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni w ramach subskrypcji platformy Azure z wartości domyślnej. Twoja subskrypcja może również ograniczyć liczbę rdzeni, które można wdrożyć w niektórych rodzinach rozmiarów maszyn wirtualnych, w tym dla serii H. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../articles/azure-supportability/how-to-create-azure-support-request.md) . (Domyślne limity mogą się różnić w zależności od kategorii subskrypcji).
  
  > [!NOTE]
  > Skontaktuj się z pomocą techniczną platformy Azure, jeśli potrzebujesz dużej pojemności. Przydziały platformy Azure to limity kredytowe, a nie gwarancje wydajności. Niezależnie od limitu przydziału opłata jest naliczana tylko za używane rdzenie.
  > 
  > 
* **Sieć wirtualna** — [Sieć wirtualna](https://azure.microsoft.com/documentation/services/virtual-network/) platformy Azure nie jest wymagana do korzystania z wystąpień intensywnie korzystających z obliczeń. Jednak w przypadku wielu wdrożeń potrzebna jest co najmniej oparta na chmurze usługa Azure Virtual Network lub połączenie lokacja-lokacja, jeśli trzeba uzyskać dostęp do zasobów lokalnych. W razie potrzeby utwórz nową sieć wirtualną, aby wdrożyć wystąpienia. Dodawanie maszyn wirtualnych intensywnie korzystających z obliczeń do sieci wirtualnej w grupie koligacji nie jest obsługiwane.
* Zmiana **rozmiaru** — ze względu na ich wyspecjalizowany sprzęt można zmienić tylko wystąpienia intensywnie korzystające z obliczeń w obrębie tej samej rodziny (serii H lub serii A). Na przykład można zmienić rozmiar maszyny wirtualnej serii H tylko z jednego rozmiaru serii H na inny. Ponadto nie jest obsługiwane Zmienianie rozmiaru z intensywnie korzystającej z wielkości obliczeniowej na rozmiar intensywnie korzystający z obliczeń.  

## <a name="rdma-capable-instances"></a>Wystąpienia z obsługą technologii RDMA
Podzestaw wystąpień intensywnie korzystających z obliczeń (A8, A9, H16r, H16mr, HB i HC) ma interfejs sieciowy do łączności z funkcją zdalnego bezpośredniego dostępu do pamięci (RDMA). Wybrane rozmiary serii N wynoszące "r", takie jak konfiguracje NC24rs (NC24rs_v2 i NC24rs_v3), są również zgodne z funkcją RDMA. Ten interfejs jest poza standardowym interfejsem sieciowym platformy Azure dostępnym dla innych rozmiarów maszyn wirtualnych. 
  
Ten interfejs umożliwia wystąpienia z obsługą funkcji RDMA, które komunikują się za pośrednictwem sieci InfiniBand (IB), działającą w stawkach EDR w przypadku HB, HC, FDR stawek dla maszyn wirtualnych z serii N, H16mr i RDMA i częstotliwości QDR dla maszyn wirtualnych A8 i A9. Te możliwości RDMA umożliwiają zwiększenie skalowalności i wydajności niektórych aplikacji MPI (Message Passing Interface). Aby uzyskać więcej informacji na temat szybkości, zobacz szczegóły w tabelach na tej stronie.

> [!NOTE]
> Na platformie Azure funkcja IP over IB jest obsługiwana tylko na maszynach wirtualnych z obsługą wirtualizacji SR-IOV (SR-IOV dla InfiniBand, obecnie HB i HC). Funkcja RDMA przez IB jest obsługiwana dla wszystkich wystąpień z obsługą funkcji RDMA.
>

