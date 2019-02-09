---
title: Ogranicza platformy Azure krawędź pola danych | Dokumentacja firmy Microsoft
description: Opis ograniczeń systemowych i zalecane rozmiary krawędź pola danych platformy Azure firmy Microsoft.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 30e0c37d3d0c03e77b6dab9c06c0a50bff27e8bc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967510"
---
# <a name="azure-data-box-edge-limits-preview"></a>Limity krawędź pola danych platformy Azure (wersja zapoznawcza)

Te limity wziąć pod uwagę wdrażania i obsługi rozwiązania do krawędzi ramki danych platformy Azure firmy Microsoft.

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed wdrożeniem tego rozwiązania zapoznaj się z [warunkami użytkowania dotyczącymi wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="data-box-edge-service-limits"></a>Limity usługi krawędź pola danych

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Limity urządzeń krawędź pola danych

W poniższej tabeli opisano limity dla urządzenia usługi Edge pola danych.

| Opis | Wartość |
|---|---|
|Nie. pliki na urządzenie |100 milionów |
|Nie. akcji na urządzenie |24 |
|Nie. udziałów na kontener |1 |
|Maksymalny rozmiar pliku zapisywane do udziału| 5 TB |

## <a name="azure-storage-limits"></a>Limity usługi Azure storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Przekazywanie danych do zastrzeżenia

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Rozmiar konta magazynu platformy Azure i obiekt ograniczeń rozmiarów

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
