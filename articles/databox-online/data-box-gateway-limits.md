---
title: Brama pola danych platformy Azure ogranicza | Dokumentacja firmy Microsoft
description: Opis ograniczeń systemowych i zalecane rozmiary Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755230"
---
# <a name="azure-data-box-gateway-limits"></a>Limity bramy pola danych platformy Azure

Te limity wziąć pod uwagę wdrażania i obsługi rozwiązania Microsoft Azure Data Box Gateway. 


## <a name="data-box-gateway-service-limits"></a>Limity usługi bramy pola danych

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limity urządzeń bramy pola danych

W poniższej tabeli opisano limity dla urządzenia bramy pola danych.

| Opis | Wartość |
|---|---|
|Nie. pliki na urządzenie |100 milionów <br> Limit to ~ 25 milionów plików dla każdego 2 TB miejsca na dysku maksymalny limit na 100 mln |
|Nie. akcji na urządzenie |24 |
|Nie. udziałów na kontener usługi Azure storage |1 |
|Maksymalny rozmiar pliku zapisywane do udziału|Dla urządzenia wirtualnego 2 TB maksymalny rozmiar pliku wynosi 500 GB. <br> Maksymalny rozmiar pliku zwiększa się wraz z rozmiar dysku danych w poprzednim współczynnik aż do napotkania maksymalnie 5 TB. |

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
