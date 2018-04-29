---
title: Obraz magazynu w rejestrze kontenera platformy Azure
description: Szczegółowe informacje o tym jak obrazy usługi Docker kontenera są przechowywane w rejestrze kontenera platformy Azure, łącznie z zabezpieczeń, nadmiarowości i wydajności.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 92e60b4213cb80d193a7c35f68b8f9fd099481d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="container-image-storage-in-azure-container-registry"></a>Kontener magazynu obrazu w rejestrze kontenera platformy Azure

Każdy [podstawowa, standardowa i Premium](container-registry-skus.md) kontenera platformy Azure rejestru korzyści z magazynu Azure zaawansowane funkcje, takie jak szyfrowania na rest dla obrazu bezpieczeństwo danych i nadmiarowość geograficzna ochrony danych obrazu. W poniższych sekcjach opisano funkcje i limity przechowywania obrazów w rejestrze kontenera platformy Azure (ACR).

## <a name="encryption-at-rest"></a>Szyfrowanie na rest

Wszystkie obrazy kontenera w rejestrze są szyfrowane, gdy. Azure automatycznie szyfruje obraz przed przekazaniem jej i odszyfrowuje je na bieżąco, gdy użytkownik lub aplikacje i usługi ściągnięcia obrazu.

## <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Azure wykorzystuje schemat magazynu geograficznie nadmiarowego w celu ochrony przed utratą obrazów kontenera. Rejestru kontenera Azure automatycznie replikuje obrazów kontenera do wielu centrów danych odległymi geograficznie, uniemożliwia ich utraty awarii regionalnych magazynu.

## <a name="geo-replication"></a>Replikacja geograficzna

W scenariuszach wymagających jeszcze więcej zapewnienie wysokiej dostępności, rozważ użycie [— replikacja geograficzna](container-registry-geo-replication.md) funkcji rejestrów Premium. Replikacja geograficzna pomaga zabezpieczyć się przed utraty dostępu do rejestru w przypadku *całkowita* regionalnej awarii, nie tylko błąd magazynu. Replikacja geograficzna zapewnia inne korzyści, zbyt, takich jak obraz Zamknij sieci magazynu dla szybciej wypychanie i w przypadku rozproszonej scenariusze projektowania lub wdrożenia.

## <a name="image-limits"></a>Limity obrazu

W poniższej tabeli opisano limity obrazu i magazynu kontenera, w przypadku rejestrów kontenera platformy Azure.

| Zasób | Limit |
| -------- | :---- |
| Repozytoria | Bez ograniczeń |
| Obrazy | Bez ograniczeń |
| Warstwy | Bez ograniczeń |
| Tagi | Bez ograniczeń|
| Magazyn | 5 TB |

Bardzo dużej liczby repozytoria i tagi może wpłynąć na wydajność rejestru. Okresowo usuwanie nieużywanych repozytoriów, znaczników i obrazów przy użyciu [interfejsu wiersza polecenia Azure](/cli/azure/acr), ACR [interfejsu API REST](/rest/api/containerregistry/), lub [portalu Azure] [ portal] jako część procedury obsługi z rejestru. Usunięte rejestru zasobów, takich jak obrazy, znaczników i repozytoriów *nie* odzyskane po usunięciu.

## <a name="storage-cost"></a>Koszt magazynowania

Aby uzyskać szczegółowe informacje o cenach, zobacz [cennik rejestru kontenera Azure][pricing].

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o różnych Azure kontenera rejestru jednostki SKU (Basic, Standard i Premium), zobacz [jednostki SKU rejestru kontenera Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
