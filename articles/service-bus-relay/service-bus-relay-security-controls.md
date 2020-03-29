---
title: Kontrolki zabezpieczeń dla przekaźnika usługi Azure Service Bus
description: Te artykuły zawiera listę kontrolną wbudowanych zabezpieczeń do oceny usługi Azure Service Bus Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514021"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Kontrolki zabezpieczeń dla przekaźnika usługi Azure Service Bus

W tym artykule dokumentuje zabezpieczenia wbudowane w usługę Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktu końcowego usługi| Nie |  |   |
| Obsługa izolacji sieci i zaporowania| Nie |  |   |
| Wymuszone wsparcie tunelowania| Nie dotyczy | Przekaźnik jest tunelem TLS  |   |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | |   |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Sukces połączenia / awaria i błędy i rejestrowane.  |   |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Uwierzytelnianie| Tak | Przez SAS. | [Uwierzytelnianie i autoryzacja usługi Azure Relay](relay-authentication-and-authorization.md) |
| Autoryzacja|  Tak | Przez SAS. | [Uwierzytelnianie i autoryzacja usługi Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft |  Nie dotyczy | Przekaźnik jest gniazdem internetowym i nie utrwala danych. |   |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Używa tylko certyfikatów TLS firmy Microsoft.  |   |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |   |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Usługa wymaga protokołu TLS. |   |
| Szyfrowane wywołania interfejsu API| Tak | protokołu HTTPS. |


## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | Za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)