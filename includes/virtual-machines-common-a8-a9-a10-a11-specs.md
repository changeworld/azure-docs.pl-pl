---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep;amverma
ms.custom: include file
ms.openlocfilehash: 5cbc19d5aade2bbcc8b8dca277352d1b17d1d35a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755187"
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
Podzbiór wystąpień obliczeniowych (A8, A9, H16r, H16mr, HB i HC) są wyposażone w interfejs sieciowy, łączność dostępu (RDMA) zdalnego pamięci. Wybrane rozmiary serii N oznaczone ciągiem "r", takich jak konfiguracja NC24rs (NC24rs_v2 i nc24rs_v3 w warstwie) są również funkcją RDMA. Ten interfejs jest oprócz interfejsu standardowa sieci platformy Azure dostępne dla innych rozmiarów maszyn wirtualnych. 
  
Ten interfejs umożliwia obsługą dostępu RDMA wystąpień do komunikowania się za pośrednictwem sieci InfiniBand (IB), operacyjne stawek EDR HB, połączenia Hybrydowego, przepustowości fdr wraz ze stawkami za usługi H16r, H16mr i obsługą dostępu RDMA seria maszyn wirtualnych i QDR stawki za maszyny wirtualne A8 i A9. Te możliwości RDMA może zwiększyć skalowalność i wydajność niektórych aplikacji interfejsu przekazywania komunikatów (MPI). Aby uzyskać więcej informacji o szybkości Zobacz szczegółowe informacje w tabelach na tej stronie.

> [!NOTE]
> Na platformie Azure IP over IB jest obsługiwane tylko dla maszyn wirtualnych, o których (obecnie HB i HC) z włączoną funkcję SR-IOV. RDMA za pośrednictwem IB jest obsługiwana dla wszystkich wystąpień z funkcją RDMA.
>

