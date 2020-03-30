---
title: Seria NV — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii NV.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 7fd9fff87564b790915acf01373c078c2012371e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273874"
---
# <a name="nv-series"></a>Seria NV

Maszyny wirtualne z serii NV są zasilane przez procesory graficzne [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) i technologię NVIDIA GRID dla aplikacji akcelerowanych na komputerach stacjonarnych i pulpitów wirtualnych, w których klienci są w stanie wizualizować swoje dane lub symulacje. Użytkownicy są w stanie wizualizować swoje grafiki intensywne przepływy pracy w wystąpieniach NV, aby uzyskać doskonałe możliwości grafiki i dodatkowo uruchomić pojedyncze obciążenia precyzji, takie jak kodowanie i renderowanie. Maszyny wirtualne z serii NV są również obsługiwane przez procesory Intel Xeon E5-2690 v3 (Haswell).

Każdy procesor graficzny w wystąpieniach NV jest wyposażony w licencję GRID. Ta licencja zapewnia elastyczność używania wystąpienia NV jako wirtualnej stacji roboczej dla pojedynczego użytkownika lub 25 równoczesnych użytkowników może połączyć się z maszyną wirtualną w przypadku scenariusza aplikacji wirtualnej.

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
|---|---|---|---|---|---|---|---|---|---|
| Standardowa_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standardowa_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standardowa_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

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
