---
title: Seria NVv3 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii NVv3.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: d74b00a4bade956d3a511a47b0a6b0011b9fb212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267429"
---
# <a name="nvv3-series"></a>Seria NVv3

Maszyny wirtualne serii NVv3 są zasilane przez procesory graficzne [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) i technologię NVIDIA GRID z procesorami Intel E5-2690 v4 (Broadwell) i technologią Intel Hyper-Threading. Te maszyny wirtualne są przeznaczone dla aplikacji graficznych akcelerowanych gpu i pulpitów wirtualnych, gdzie klienci chcą wizualizować swoje dane, symulować wyniki, aby wyświetlić, pracować nad CAD lub renderować i przesyłać strumieniowo zawartość. Ponadto te maszyny wirtualne mogą uruchamiać pojedyncze obciążenia precyzyjne, takie jak kodowanie i renderowanie. Maszyny wirtualne NVv3 obsługują pamięć masową premium i są wyposażone w dwukrotnie większą pamięć systemową (RAM) w porównaniu z poprzednią serią NV.  

Każdy procesor graficzny w wystąpieniach NVv3 jest wyposażony w licencję GRID. Ta licencja zapewnia elastyczność używania wystąpienia NV jako wirtualnej stacji roboczej dla pojedynczego użytkownika lub 25 równoczesnych użytkowników może połączyć się z maszyną wirtualną w przypadku scenariusza aplikacji wirtualnej.

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 procesor GPU = połowa karty M60.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii N platformy Azure, należy zainstalować sterowniki gpu nvidia.

[Rozszerzenie sterownika GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub zarządzaj nim za pomocą witryny Azure portal lub narzędzi, takich jak szablony programu Azure PowerShell lub Usługi Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterowników procesora graficznego NVIDIA, aby](./extensions/hpccompute-gpu-windows.md) zapoznać się z obsługiwanymi systemami operacyjnymi i krokami wdrażania. Aby uzyskać ogólne informacje na temat rozszerzeń maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](./extensions/overview.md).

Jeśli zdecydujesz się zainstalować sterowniki GPU NVIDIA ręcznie, zobacz [Konfiguracja sterowników GPU serii N dla systemu Windows](./windows/n-series-driver-setup.md) lub N dla systemu [Linux](./linux/n-series-driver-setup.md) dla obsługiwanych systemów operacyjnych, sterowników, instrukcji instalacji i weryfikacji.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
