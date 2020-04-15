---
title: Przestarzałe tls 1.0 i 1.1 w IoT Hub | Dokumenty firmy Microsoft
description: Wytyczne dotyczące usuwania TLS 1.0 i 1.1 i obsługiwanych szyfrów w Centrum IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381295"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Wycofanie TLS 1.0 i 1.1 w Uchwale IoT Hub

Aby zapewnić najlepsze w swojej klasie szyfrowanie, Usługa IoT Hub przechodzi do usługi Transport Layer Security (TLS) 1.2 jako mechanizm szyfrowania wybrany dla urządzeń i usług IoT. 

## <a name="timeline"></a>Oś czasu

Centrum IoT będzie nadal obsługiwać TLS 1.0/1.1 do odwołania. Zaleca się jednak, aby wszyscy klienci jak najszybciej przeprowadzić migrację do protokołu TLS 1.2.

## <a name="supported-ciphers"></a>Obsługiwane szyfry

Oś czasu dostępności różnych szyfrów używanych w uzgadnianiu TLS jest następująca:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (obecnie obsługiwane)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (będzie wspierany w drugiej połowie 2020 r.)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (będzie wspierany w drugiej połowie 2020 r.)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (będzie wspierany w drugiej połowie 2020 r.)

## <a name="customer-feedback"></a>Opinie klientów

Chociaż wymuszanie protokołu TLS 1.2 jest najlepszym w swojej klasie wyborem szyfrowania w całej branży i zostanie włączone zgodnie z planem, nadal chcielibyśmy usłyszeć od klientów o ich konkretnych wdrożeniach i trudnościach z przyjęciem protokołu TLS 1.2. W tym celu możesz wysłać [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)swoje komentarze do .
