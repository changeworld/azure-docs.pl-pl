---
title: Informacje o wersji usługi Azure Data Box Gateway 1905| Dokumenty firmy Microsoft
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania bramy azure data box z uruchomieniu ogólnej dostępności.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 345666b53336cc6959a65eefd4b0f2475bea8c80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078614"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1905-release-notes"></a>Informacje o usłudze Azure Data Box Edge i usłudze Azure Data Box Gateway 1905

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy dotyczące wersji 1905 dla usługi Azure Data Box Edge i usługi Azure Data Box Gateway.

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem bramy data box edge/data box należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Ta wersja odpowiada wersjom oprogramowania:

- **Data Box Gateway 1905 (1.6.887.626)**
- **Data Box Edge 1905 (1.6.887.626)**

> [!NOTE]
> Aktualizacja 1905 może być stosowana tylko do urządzeń Data Box Edge, na których jest uruchomiona wersja GA oprogramowania.

## <a name="whats-new"></a>Co nowego?

- **Ulepszenia rejestrowania field programmable Gate Array (FPGA)** — w tej wersji dokonaliśmy ulepszeń rejestrowania i alertów związanych z FPGA. Jest to wymagana aktualizacja dla usługi Data Box Edge, jeśli funkcja obliczeń krawędzi jest korzystać z funkcji FPGA. Aby uzyskać więcej informacji, zobacz jak [przekształcać dane za pomocą obliczeń krawędzi na krawędzi pola danych](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Znane problemy w wersji GA

W tej wersji nie odnotowano żadnych nowych problemów. Wszystkie wymienione wydania problemy zostały przeniesione z poprzednich wydań. Aby wyświetlić listę znanych problemów, przejdź do [znanego wydania ga](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Przygotowywanie do wdrażania usługi Azure Data Box Edge](data-box-edge-deploy-prep.md)
