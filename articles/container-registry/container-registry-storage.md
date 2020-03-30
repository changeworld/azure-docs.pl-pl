---
title: Przechowywanie obrazów kontenerów
description: Szczegółowe informacje na temat sposobu przechowywania obrazów kontenerów platformy Docker w rejestrze kontenerów platformy Azure, w tym zabezpieczeń, nadmiarowości i pojemności.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456206"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Magazyn obrazów kontenerów w rejestrze kontenerów platformy Azure

Każdy rejestr kontenerów [platformy Basic, Standard i Premium](container-registry-skus.md) zapewnia korzyści z zaawansowanych funkcji magazynu platformy Azure, takich jak szyfrowanie w spoczynku w celu zapewnienia bezpieczeństwa danych obrazu i nadmiarowości geograficznej w celu ochrony danych obrazu. W poniższych sekcjach opisano zarówno funkcje, jak i limity magazynu obrazów w rejestrze kontenerów platformy Azure (ACR).

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Wszystkie obrazy kontenerów w rejestrze są szyfrowane w spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go w locie, gdy użytkownik lub twoje aplikacje i usługi ściągają obraz.

## <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Platforma Azure używa schematu magazynu geograficznie nadmiarowego, aby chronić przed utratą obrazów kontenerów. Usługa Azure Container Registry automatycznie replikuje obrazy kontenerów do wielu geograficznie odległych centrów danych, zapobiegając ich utracie w przypadku awarii magazynu regionalnego.

## <a name="geo-replication"></a>Replikacja geograficzna

W przypadku scenariuszy wymagających jeszcze większej pewności dostępności należy rozważyć użycie funkcji [replikacji geograficznej](container-registry-geo-replication.md) w rejestrach premium. Replikacja geograficzna pomaga chronić przed utratą dostępu do rejestru w przypadku *całkowitej* awarii regionalnej, a nie tylko awarii magazynu. Replikacja geograficzna zapewnia również inne korzyści, takie jak magazyn obrazów w pobliżu sieci, aby szybciej wypychać i ściągać w scenariuszach rozwoju rozproszonego lub wdrażania.

## <a name="image-limits"></a>Limity obrazów

W poniższej tabeli opisano obraz kontenera i limity magazynu w miejscu dla rejestrów kontenerów platformy Azure.

| Zasób | Limit |
| -------- | :---- |
| Repozytoria | Bez ograniczeń |
| Obrazy | Bez ograniczeń |
| Warstwy | Bez ograniczeń |
| Tagi | Bez ograniczeń|
| Magazyn | 5 TB |

Bardzo duża liczba repozytoriów i tagów może mieć wpływ na wydajność rejestru. Okresowo usuwaj nieużywane repozytoria, znaczniki i obrazy w ramach procedury konserwacji rejestru. Usunięte zasoby rejestru, takie jak repozytoria, obrazy i tagi, *nie mogą* zostać odzyskane po usunięciu. Aby uzyskać więcej informacji na temat usuwania zasobów rejestru, zobacz [Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Koszty magazynu

Aby uzyskać szczegółowe informacje na temat cen, zobacz [Cennik rejestru kontenerów platformy Azure][pricing].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat różnych jednostek SKU rejestru kontenerów platformy Azure (podstawowe, standardowe, premium), zobacz [Jednostki SKU rejestru kontenerów platformy Azure.](container-registry-skus.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
