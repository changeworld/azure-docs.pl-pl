---
title: Przestarzałe przestarzałe tls 1.0 i 1.1 w Usłudze obsługi administracyjnej Usługi IoT Hub i device provisioning service (DPS) | Dokumenty firmy Microsoft
description: Wytyczne dotyczące usuwania TLS 1.0 i 1.1 i obsługiwanych szyfrów w Uodochu IoT hub i DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402792"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Wycofanie protokołu TLS 1.0 i 1.1 w Usłudze obsługi administracyjnej usługi IoT Hub i device provisioning

Aby zapewnić najlepsze w swojej klasie szyfrowanie, centrum IoT Hub i usługa inicjowania obsługi urządzeń (DPS) przechodzą do usługi TLS (Transport Layer Security) 1.2 jako mechanizm szyfrowania wybrany dla urządzeń i usług IoT. 

## <a name="supported-ciphers"></a>Obsługiwane szyfry

Oś czasu dostępności różnych szyfrów używanych w uzgadnianiu TLS jest następująca:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (obecnie obsługiwane)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (będzie wspierany w drugiej połowie 2020 r.)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (będzie wspierany w drugiej połowie 2020 r.)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (będzie wspierany w drugiej połowie 2020 r.)


## <a name="customer-feedback"></a>Opinie klientów

Chociaż wymuszanie protokołu TLS 1.2 jest najlepszym w swojej klasie wyborem szyfrowania w całej branży i zostanie włączone zgodnie z planem, nadal chcielibyśmy usłyszeć od klientów o ich konkretnych wdrożeniach i trudnościach z przyjęciem protokołu TLS 1.2. W tym celu możesz wysłać [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)swoje komentarze do .
