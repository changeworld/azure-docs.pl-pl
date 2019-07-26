---
title: Atrybuty zabezpieczeń dla usługi Azure API Management
description: Lista kontrolna atrybutów zabezpieczeń do oceny API Management
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442278"
---
# <a name="security-attributes-for-api-management"></a>Atrybuty zabezpieczeń dla API Management

W tym artykule opisano atrybuty zabezpieczeń wbudowane w API Management.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania)| Tak (tylko szyfrowanie po stronie usługi) | Dane poufne, takie jak certyfikaty, klucze i wartości nazwane tajne, są szyfrowane przy użyciu zarządzanych przez usługę usług według kluczy wystąpienia usługi. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes | Usługa [Express Route](../expressroute/index.yml) i szyfrowanie sieci wirtualnej są udostępniane przez [Sieć Azure](../virtual-network/index.yml). |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie | Wszystkie klucze szyfrowania są dla każdego wystąpienia usługi i są zarządzane przez usługę. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania płaszczyzny zarządzania są nawiązywane za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) za pośrednictwem protokołu TLS. Wymagany jest prawidłowy token sieci Web JSON (JWT).  Wywołania płaszczyzny danych mogą być zabezpieczone przy użyciu protokołu TLS i jednego z obsługiwanych mechanizmów uwierzytelniania (na przykład certyfikatu klienta lub tokenu JWT).
 |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i obsługa zapór| Tak | Odpowiednio przy użyciu grup zabezpieczeń sieci (sieciowej grupy zabezpieczeń) i platformy Application Gateway Azure (lub innego oprogramowania). |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia tunelowanie wymuszone. |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | |
| Authorization| Tak | |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | [Azure Monitor dzienników aktywności](../azure-monitor/platform/activity-logs-overview.md) |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | [Azure monitor dzienniki diagnostyczne](../azure-monitor/platform/diagnostic-logs-overview.md) i (opcjonalnie) [Application Insights platformy Azure](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | Korzystanie z [zestawu Resource Kit usługi Azure API Management DevOps](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Luka w zabezpieczeniach skanowania fałszywych pozytywów

W tej części udokumentowano typowe luki w zabezpieczeniach, które nie wpływają na API Management platformy Azure.

| Luka w zabezpieczeniach               | Opis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed jest luką w zabezpieczeniach w implementacji rozszerzenia SessionTicket protokołu TLS znalezionego w niektórych F5. Pozwala na wycieki ("wykrwawinie") do 31 bajtów danych z niezainicjowanej pamięci. Jest to spowodowane tym, że stos TLS uzupełnia identyfikator sesji, który został przesłany przez klienta z danymi, aby zapewnić 32 bitów. |
