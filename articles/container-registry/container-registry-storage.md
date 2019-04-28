---
title: Magazyn obrazów w usłudze Azure Container Registry
description: Szczegółowe informacje dotyczące sposobu obrazów kontenerów platformy Docker są przechowywane w usłudze Azure Container Registry, w tym zabezpieczeń, nadmiarowości i wydajności.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: danlep
ms.openlocfilehash: 55c84907ab41f6da9d7a0989c68a1c1f90c5e424
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827276"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Przechowywanie obrazów kontenerów w usłudze Azure Container Registry

Każdy [Basic, Standard i Premium](container-registry-skus.md) korzyści związane z rejestru kontenerów platformy Azure z systemem funkcje zaawansowane usługi Azure storage, takie jak szyfrowanie w spoczynku, obraz danych zabezpieczeń i geograficznej nadmiarowości ochronę danych obrazu. W następujących sekcjach opisano funkcje i limity magazyn obrazów w usłudze Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Encryption-at-rest

Wszystkie obrazy kontenerów w rejestrze są szyfrowane, gdy. Azure automatycznie szyfruje obraz przed przekazaniem jej i odszyfrowuje je na bieżąco, gdy użytkownik lub aplikacji i usług Ściągnij obraz.

## <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Platforma Azure używa schematu dla magazynu geograficznie nadmiarowego w celu ochrony przed utratą obrazów kontenerów. Usługa Azure Container Registry automatycznie replikuje obrazów kontenerów w wielu centrach danych odległym geograficznie, co uniemożliwia ich utraty awarii magazynu regionalnego.

## <a name="geo-replication"></a>Replikacja geograficzna

W scenariuszach wymagających jeszcze bardziej zapewnienie wysokiej dostępności, należy rozważyć użycie [geografickou replikaci](container-registry-geo-replication.md) funkcji rejestrach w warstwie Premium. Replikacja geograficzna chroni przed utratą dostępu do rejestru w przypadku powstania *całkowita* regionalnej awarii, nie tylko wystąpił błąd magazynu. Replikacja geograficzna zapewnia inne korzyści, obrazem pobliskiej sieci magazynu na potrzeby szybciej wypchnięć i ściąga w rozproszonych scenariuszach tworzenia i wdrażania.

## <a name="image-limits"></a>Limity obrazu

W poniższej tabeli opisano limity obrazu i magazynu kontenera w miejscu, aby rejestry kontenerów platformy Azure.

| Resource | Limit |
| -------- | :---- |
| Repozytoria | Bez ograniczeń |
| Obrazy | Bez ograniczeń |
| Warstwy | Bez ograniczeń |
| Tagi | Bez ograniczeń|
| Magazyn | 5 TB |

Bardzo dużej liczby repozytoriów i tagów może wpłynąć na wydajność rejestru. Okresowo usuwać nieużywane repozytoriów, tagi i obrazy w ramach procedury obsługi Twojego rejestru. Usunięto rejestru zasobów, takich jak repozytoriów, obrazów i tagów *nie* zostać odzyskane po usunięciu. Aby uzyskać więcej informacji na temat usuwania zasobów rejestru zobacz [usuwanie obrazów kontenerów w usłudze Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Koszt usługi Storage

Aby uzyskać szczegółowe informacje o cenach, zobacz [cennika usługi Azure Container Registry][pricing].

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat różnych Azure kontenera rejestru jednostek SKU (Basic, Standard i Premium), zobacz [jednostki SKU rejestru kontenerów platformy Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
