---
title: Szyfrowanie danych usługi inicjowania obsługi administracyjnej urządzeń platformy Azure w spoczynku za pomocą kluczy zarządzanych przez klienta| Dokumenty firmy Microsoft
description: Szyfrowanie danych w spoczynku za pomocą kluczy zarządzanych przez klienta dla usługi inicjowania obsługi urządzeń
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675145"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Szyfrowanie danych w spoczynku za pomocą kluczy zarządzanych przez klienta dla usługi inicjowania obsługi urządzeń

## <a name="overview"></a>Omówienie

Usługa inicjowania obsługi administracyjnej urządzeń (DPS) obsługuje szyfrowanie danych w spoczynku za pomocą kluczy zarządzanych przez klienta (CMK), znanych również jako Bring your own key (BYOK). DPS zapewnia szyfrowanie danych w spoczynku i podczas przesyłania. Domyślnie dps używa kluczy zarządzanych przez firmę Microsoft do szyfrowania danych. Dzięki obsłudze cmk klienci mają teraz możliwość szyfrowania danych w spoczynku za pomocą klucza szyfrowania zarządzanego przez klientów za pomocą [usługi Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Ta funkcja wymaga utworzenia nowego DPS w jednym z następujących regionów: Wschodnie stany USA, Zachodnie stany USA 2 lub Południowo-środkowe stany USA. Aby wypróbować tę funkcję, skontaktuj się z nami za pośrednictwem [pomocy technicznej firmy Microsoft](https://azure.microsoft.com/support/create-ticket/). Udostępnianie nazwy firmy i identyfikatora subskrypcji podczas kontaktowania się z pomocą techniczną firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze inicjowania obsługi administracyjnej urządzeń](https://docs.microsoft.com/azure/iot-dps/)

* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)