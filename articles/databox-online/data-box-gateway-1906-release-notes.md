---
title: Usługa Azure Data Box Gateway & informacje o wersji usługi Azure Data Box 1906| Dokumenty firmy Microsoft
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania dla bramy azure data box i usługi Azure Data Box Edge z systemem 1906 wersji.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099488"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Informacje o usłudze Azure Data Box Edge i usłudze Azure Data Box Gateway 1906

W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy dotyczące wersji 1906 dla usługi Azure Data Box Edge i usługi Azure Data Box Gateway.

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem bramy data box edge/data box należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Ta wersja odpowiada wersjom oprogramowania:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Aktualizację 1906 można zastosować tylko do urządzeń data box edge z ogólną dostępnością (GA) lub wersją oprogramowania z 1905 roku.

## <a name="whats-new"></a>Co nowego?

- **Poprawka błędu w przepływie pracy zarządzania kluczami odzyskiwania** — we wcześniejszej wersji wystąpił błąd, z powodu którego klucz odzyskiwania nie był zastosowany. Ten błąd został naprawiony w tej wersji. Zdecydowanie zaleca się zastosowanie tej aktualizacji, ponieważ klucz odzyskiwania umożliwia odzyskanie danych na urządzeniu, w przypadku, gdy urządzenie nie uruchamia się. Aby uzyskać więcej informacji, zobacz jak [zapisać klucz odzyskiwania podczas wdrażania usługi Data Box Edge lub Data Box Gateway](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Wprowadzono ulepszenia rejestrowania field programmable Gate Array (FPGA)** — od wersji 1905 wprowadzono ulepszenia rejestrowania i alertów związane z FPGA. Nadal jest to wymagana aktualizacja dla usługi Data Box Edge, jeśli funkcja obliczeń krawędzi jest korzystać z funkcji FPGA. Aby uzyskać więcej informacji, zobacz jak [przekształcać dane za pomocą obliczeń krawędzi na krawędzi pola danych](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Znane problemy w wersji GA

W tej wersji nie odnotowano żadnych nowych problemów. Wszystkie wymienione wydania problemy zostały przeniesione z poprzednich wydań. Aby wyświetlić listę znanych problemów, przejdź do [znanego wydania ga](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Przygotowywanie do wdrażania usługi Azure Data Box Edge](data-box-edge-deploy-prep.md)
