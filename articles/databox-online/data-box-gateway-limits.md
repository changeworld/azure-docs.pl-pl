---
title: Limity bramy usługi Azure Data Box Gateway | Dokumenty firmy Microsoft
description: W tym artykule opisano limity systemowe i zalecane rozmiary bramy usługi Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60755230"
---
# <a name="azure-data-box-gateway-limits"></a>Limity bramy usługi Azure Data Box

Należy wziąć pod uwagę te limity podczas wdrażania i obsługi rozwiązania usługi Microsoft Azure Data Box Gateway. 


## <a name="data-box-gateway-service-limits"></a>Limity usług bramy pola danych

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limity urządzeń bramy pól danych

W poniższej tabeli opisano limity dla urządzenia Bramy pola danych.

| Opis | Wartość |
|---|---|
|Nie. plików na urządzenie |100 milionów <br> Limit wynosi ~ 25 milionów plików na każde 2 TB miejsca na dysku z maksymalnym limitem na 100 milionów |
|Nie. udziałów na urządzeniu |24 |
|Nie. udziałów na kontener magazynu platformy Azure |1 |
|Maksymalny rozmiar pliku zapisany w udziale|W przypadku urządzenia wirtualnego o pojemności 2 TB maksymalny rozmiar pliku to 500 GB. <br> Maksymalny rozmiar pliku zwiększa się wraz z rozmiarem dysku danych w poprzednim stosunku, aż osiągnie maksymalnie 5 TB. |

## <a name="azure-storage-limits"></a>Limity magazynu platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru konta magazynu platformy Azure i rozmiaru obiektu

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektu platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
