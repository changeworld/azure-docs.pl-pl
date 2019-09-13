---
title: Kontrolki zabezpieczeń dla usługi Azure Storage
description: Lista kontrolna zabezpieczeń na potrzeby oceny usługi Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb14c19e8816d53c7d9385563f916bee5d4a6af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886803"
---
# <a name="security-controls-for-azure-storage"></a>Kontrolki zabezpieczeń dla usługi Azure Storage

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure Storage. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: Klucze zarządzane przez firmę Microsoft | Tak |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Obsługa standardowych mechanizmów protokołu HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich przesłaniem do usługi. |
| Wywołania interfejsu API są szyfrowane| Tak |  |

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| ND |  |
| Izolacja sieci i obsługa zapór| Tak | |
| Obsługa tunelowania wymuszonego| ND |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Dostępne są teraz metryki Azure Monitor, dzienniki rozpoczynające Podgląd |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania | Tak | Azure Resource Manager dziennik aktywności |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Dzienniki diagnostyczne usługi i rejestrowanie Azure Monitor rozpoczynające się w wersji zapoznawczej  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Authorization| Tak | Obsługa autoryzacji za pośrednictwem list kontroli dostępu RBAC, POSIX i tokenów SAS |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Obsługa wersji dostawcy zasobów przy użyciu interfejsów API Azure Resource Manager |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).