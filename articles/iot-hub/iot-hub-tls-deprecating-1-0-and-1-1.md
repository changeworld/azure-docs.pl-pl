---
title: Wycofanie protokołu TLS 1,0 i 1,1 w IoT Hub i usłudze Device Provisioning Service (DPS) | Microsoft Docs
description: Wytyczne dotyczące wycofania protokołu TLS 1,0 i 1,1 oraz obsługiwane szyfry w IoT Hub i DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912158"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Wycofanie protokołu TLS 1,0 i 1,1 w usłudze IoT Hub i usługi Device Provisioning

Aby zapewnić najlepszą w swojej klasie funkcję szyfrowania, IoT Hub i usługi Device Provisioning Service (DPS) są przenoszone do Transport Layer Security (TLS) 1,2 jako mechanizm szyfrowania wybrany dla urządzeń i usług IoT. W związku z tym Starsza obsługa protokołu TLS 1,0 i TLS 1,1, a także kilka niezalecanych w starszej wersji szyfrów zostanie wycofana w **1 lipca 2020**.


## <a name="impact"></a>Wpływ
W oparciu o określone okoliczności i konfiguracje klientów wycofanie protokołu TLS 1,0 i 1,1 oraz niezalecane starsze szyfry mogą mieć wpływ na zmianę w przypadku urządzeń i usług IoT komunikujących się z IoT Hub lub DPS. W niektórych przypadkach urządzenia i usługi, które są niezgodne z tymi zmianami, nie będą mogły połączyć się z IoT Hub lub DPS po powyższej dacie wycinania.


## <a name="supported-ciphers"></a>Obsługiwane szyfry

Oś czasu dostępności różnych szyfrów używanych w uzgadnianiu protokołu TLS jest następująca:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (obecnie obsługiwane)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (będzie obsługiwana w drugiej połowie 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (będzie obsługiwana w drugiej połowie 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (będzie obsługiwana w drugiej połowie 2020)


## <a name="customer-feedback"></a>Opinie klientów

Chociaż Wymuszanie protokołu TLS 1,2 to Najlepsza w swojej klasie funkcja szyfrowania, która zostanie włączona zgodnie z harmonogramem, nadal będziemy chcieli poznać klientów odnośnie do ich określonych wdrożeń i problemów, które przyjmą protokół TLS 1,2. W tym celu można wysłać komentarze do [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
