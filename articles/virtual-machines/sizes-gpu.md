---
title: Rozmiary maszyn wirtualnych platformy Azure — gpu | Dokumenty firmy Microsoft
description: Wyświetla listę różnych rozmiarów zoptymalizowanych pod kątem procesora GPU dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepustowości magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913586"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora graficznego

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU to wyspecjalizowane maszyny wirtualne dostępne z pojedynczymi lub wieloma procesorami graficznymi NVIDIA. Te rozmiary są przeznaczone do obciążeń wymagających dużej mocy obliczeniowej, grafiki i wizualizacji. Ten artykuł zawiera informacje o liczbie i typie procesorów GPU, procesorów wirtualnych, dysków z danymi i kart sieciowych. Przepustowość magazynu i przepustowość sieci są również uwzględniane dla każdego rozmiaru w tej grupie.

- Rozmiary [serii NC,](nc-series.md) [serii NCv2,](ncv2-series.md) [serii NCv3](ncv3-series.md) są zoptymalizowane pod kątem aplikacji i algorytmów wymagających dużej mocy obliczeniowej i sieci. Niektóre przykłady to cuda i opencl oparte aplikacji i symulacji, AI i deep learning. Seria NCv3 koncentruje się na wysokowydajnych obciążeniach obliczeniowych z procesorem graficznym Nvidia Tesla V100. Seria NC wykorzystuje procesor Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell), a maszyny wirtualne serii NCv2 i NCv3 wykorzystują procesor Intel Xeon E5-2690 v4 (Broadwell).

- [Rozmiary serii ND](nd-series.md)i [serii NDv2](ndv2-series.md) koncentrują się na szkoleniach i scenariuszach wnioskowania dla głębokiego uczenia się. Używają procesora graficznego NVIDIA Tesla P40 i procesora Intel Xeon E5-2690 v4 (Broadwell). Seria NDv2 wykorzystuje procesor Intel Xeon Platinum 8168 (Skylake).

- Rozmiary [serii NV](nv-series.md) i [NVv3](nvv3-series.md) są zoptymalizowane i zaprojektowane do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania i scenariuszy VDI przy użyciu takich struktur jak OpenGL i DirectX. Te maszyny wirtualne są wspierane przez GPU NVIDIA Tesla M60.

- [Seria NVv4](nvv4-series.md) Rozmiary maszyn wirtualnych zoptymalizowane i zaprojektowane z myślą o VDI i wizualizacji zdalnej. Dzięki podzielonym na partycje procesorom graficznym NVv4 oferuje odpowiedni rozmiar dla obciążeń wymagających mniejszych zasobów GPU. Te maszyny wirtualne są wspierane przez PROCESOR GRAFICZNY AMD Radeon Instinct MI25. Maszyny wirtualne NVv4 obsługują obecnie tylko system operacyjny windowsa dla gości.

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii N platformy Azure, należy zainstalować sterowniki gpu nvidia.

[Rozszerzenie sterownika GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub zarządzaj nim za pomocą witryny Azure portal lub narzędzi, takich jak szablony programu Azure PowerShell lub Usługi Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterowników procesora graficznego NVIDIA, aby](/azure/virtual-machines/extensions/hpccompute-gpu-windows) zapoznać się z obsługiwanymi systemami operacyjnymi i krokami wdrażania. Aby uzyskać ogólne informacje na temat rozszerzeń maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](/azure/virtual-machines/extensions/overview).

Jeśli zdecydujesz się zainstalować sterowniki GPU NVIDIA ręcznie, zobacz [Konfiguracja sterowników GPU serii N dla systemu Windows](/azure/virtual-machines/windows/n-series-driver-setup) lub N dla systemu [Linux](/azure/virtual-machines/linux/n-series-driver-setup) dla obsługiwanych systemów operacyjnych, sterowników, instrukcji instalacji i weryfikacji.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- Aby uzyskać informacje o dostępności maszyn wirtualnych z serii N, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/regions/services/).

- Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania Menedżera zasobów.

- Maszyny wirtualne z serii N różnią się typem usługi Azure Storage, które obsługują dla swoich dysków. Maszyny wirtualne NC i NV obsługują tylko dyski maszyn wirtualnych, które są obsługiwane przez standardową pamięć masową (HDD). Maszyny wirtualne NCv2, NCv3, ND, NDv2 i NVv2 obsługują tylko dyski maszyn wirtualnych, które są wspierane przez magazyn dysków w wersji Premium (SSD).

- Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych z serii N, należy wziąć pod uwagę subskrypcję płatności zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- Może być konieczne zwiększenie przydziału rdzeni (na region) w subskrypcji platformy Azure i zwiększenie oddzielnego przydziału dla rdzeni NC, NCv2, NCv3, ND, NDv2, NV lub NVv2. Aby zażądać zwiększenia przydziału, [otwórz bezpłatne żądanie obsługi klienta online.](../azure-portal/supportability/how-to-create-azure-support-request.md) Domyślne limity mogą się różnić w zależności od kategorii subskrypcji.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
