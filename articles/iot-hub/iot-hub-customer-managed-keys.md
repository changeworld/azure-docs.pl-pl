---
title: Usługa Azure IoT Hub szyfrowanie danych w spoczynku przez klucze zarządzane przez klienta | Microsoft Docs
description: Szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta dla IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 1bb55d593878026bb3e57014a317b4fc0158d734
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913136"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta dla IoT Hub

IoT Hub obsługuje szyfrowanie danych przechowywanych przy użyciu kluczy zarządzanych przez klienta (CMK), znanych również jako funkcje zapewniania własnego klucza (BYOK), pomoc techniczna dla IoT Hub platformy Azure. Usługa Azure IoT Hub zapewnia szyfrowanie danych magazynowanych i przesyłanych. Domyślnie program IoT Hub szyfruje dane przy użyciu kluczy zarządzanych przez firmę Microsoft. Dzięki obsłudze CMK klienci mają teraz możliwość szyfrowania danych przechowywanych przy użyciu klucza szyfrowania klucza zarządzanego przez klientów przy użyciu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Ta funkcja wymaga utworzenia nowej IoT Hub (warstwa podstawowa lub standardowa) w jednym z następujących regionów: Wschodnie stany USA, zachodnie stany USA 2 lub Południowo-środkowe stany USA. Aby wypróbować tę możliwość, skontaktuj się z nami za pomocą [pomocy technicznej firmy Microsoft](https://azure.microsoft.com/support/create-ticket/). Skontaktuj się z pomocą techniczną firmy Microsoft, aby udostępnić nazwę firmy i Identyfikator subskrypcji.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)