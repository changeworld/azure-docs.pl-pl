---
title: Wycofanie protokołu TLS 1,0 i 1,1 w IoT Hub i usłudze Device Provisioning Service (DPS) | Microsoft Docs
description: Wytyczne dotyczące wycofania protokołu TLS 1,0 i 1,1 oraz obsługiwane szyfry w IoT Hub i DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722787"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Wycofanie protokołu TLS 1,0 i 1,1 w usłudze IoT Hub i usługi Device Provisioning

Aby zapewnić najlepszą w swojej klasie funkcję szyfrowania, IoT Hub i usługi Device Provisioning Service (DPS) są przenoszone do Transport Layer Security (TLS) 1,2 jako mechanizm szyfrowania wybrany dla urządzeń i usług IoT. W związku z tym Starsza obsługa protokołu TLS 1,0 i TLS 1,1, a także kilka niezalecanych w starszej wersji szyfrów zostanie wycofana w **1 lipca 2020**.


## <a name="impact"></a>Wpływ
W oparciu o określone okoliczności i konfiguracje klientów wycofanie protokołu TLS 1,0 i 1,1 oraz niezalecane starsze szyfry mogą mieć wpływ na zmianę w przypadku urządzeń i usług IoT komunikujących się z IoT Hub lub DPS. W niektórych przypadkach urządzenia i usługi, które są niezgodne z tymi zmianami, nie będą mogły połączyć się z IoT Hub lub DPS po powyższej dacie wycinania.


## <a name="supported-ciphers"></a>Obsługiwane szyfry

Podczas uzgadniania protokołu TLS dozwolone są tylko następujące szyfry:

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Opinie klientów

Mimo że wymuszenie stosowania protokołu TLS 1.2 jest najlepszą w swojej klasie opcją szyfrowania stosowaną w całym przemyśle i zostanie ona wprowadzona zgodnie z harmonogramem, chcemy poznać opinie klientów dotyczące ich określonych wdrożeń i problemów podczas wprowadzania protokołu TLS 1.2. W tym celu można wysłać komentarze do [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).