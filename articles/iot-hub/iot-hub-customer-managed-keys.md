---
title: Szyfrowanie danych usługi Azure IoT Hub w spoczynku za pomocą kluczy zarządzanych przez klienta| Dokumenty firmy Microsoft
description: Szyfrowanie danych w spoczynku za pomocą kluczy zarządzanych przez klienta dla Usługi IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370580"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Szyfrowanie danych w spoczynku za pomocą kluczy zarządzanych przez klienta dla Usługi IoT Hub

Usługa IoT Hub obsługuje szyfrowanie danych w spoczynku za pomocą kluczy zarządzanych przez klienta (CMK), znanych również jako Bring your own key (BYOK), obsługujących usługę Azure IoT Hub. Usługa Azure IoT Hub zapewnia szyfrowanie danych w spoczynku i podczas przesyłania. Domyślnie Usługa IoT Hub używa kluczy zarządzanych przez firmę Microsoft do szyfrowania danych. Dzięki obsłudze cmk klienci mają teraz możliwość szyfrowania danych w spoczynku za pomocą klucza szyfrowania zarządzanego przez klientów za pomocą [usługi Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Ta funkcja wymaga utworzenia nowego centrum IoT Hub (warstwy podstawowej lub standardowej) w jednym z następujących regionów: Wschodnie stany USA, Zachodnie stany USA 2, południowo-środkowe stany USA lub us Gov. Aby wypróbować tę funkcję, skontaktuj się z nami za pośrednictwem [pomocy technicznej firmy Microsoft](https://azure.microsoft.com/support/create-ticket/). Udostępnianie nazwy firmy i identyfikatora subskrypcji podczas kontaktowania się z pomocą techniczną firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o U. Centrum IoT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
