---
title: Środki kontroli zabezpieczeń usługi Azure Storage
description: Lista kontrolna zabezpieczeń do oceny usługi Azure Storage
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061116"
---
# <a name="security-controls-for-azure-storage"></a>Środki kontroli zabezpieczeń usługi Azure Storage

Ten artykuł dokumentuje kontrolki zabezpieczeń wbudowane w usługę Azure Storage. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Zobacz [Szyfrowanie usługi magazynowania przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Obsługa standardowych mechanizmów HTTPS/TLS.  Użytkownicy mogą również szyfrować dane przed ich przesłaniem do usługi. |
| Szyfrowane wywołania interfejsu API| Tak |  |

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak |  |
| Obsługa wtrysku sieci wirtualnej| Nie dotyczy |  |
| Obsługa izolacji sieci i zaporowania| Tak | |
| Wymuszone wsparcie tunelowania| Nie dotyczy |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Metryki usługi Azure Monitor|
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania | Tak | Dziennik aktywności usługi Azure Resource Manager |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Dzienniki diagnostyczne usługi.|

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | Usługa Azure Active Directory, klucz udostępniony, token dostępu współdzielonego. |
| Autoryzacja| Tak | Obsługa autoryzacji za pośrednictwem rbac, list ACL POSIX i tokenów SAS |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | Obsługa wersji dostawcy zasobów za pośrednictwem interfejsów API usługi Azure Resource Manager |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../../security/fundamentals/security-controls.md)