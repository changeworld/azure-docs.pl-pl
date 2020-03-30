---
title: Mechanizmy kontroli zabezpieczeń dla usługi Azure API Management
description: Lista kontrolna kontroli zabezpieczeń do oceny zarządzania interfejsami API
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751124"
---
# <a name="security-controls-for-api-management"></a>Mechanizmy kontroli zabezpieczeń dla zarządzania interfejsami API

Ten artykuł dokumentuje mechanizmy zabezpieczeń wbudowane w zarządzanie interfejsami API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktu końcowego usługi| Nie | |  |
| Obsługa wtrysku sieci wirtualnej| Tak | |  |
| Obsługa izolacji sieci i zaporowania| Tak | Korzystanie z sieciowych grup zabezpieczeń (NSG) i usługi Azure Application Gateway (lub innego urządzenia programowego). |  |
| Wymuszone wsparcie tunelowania| Tak | Sieć platformy Azure zapewnia wymuszone tunelowanie. |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | | |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | [Dzienniki aktywności usługi Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) | |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | [Dzienniki diagnostyczne usługi Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) i (opcjonalnie) [usługa Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Uwierzytelnianie| Tak | |  |
| Autoryzacja| Tak | |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Poufne dane, takie jak certyfikaty, klucze i wartości o nazwie tajne są szyfrowane za pomocą kluczy wystąpienia usługi zarządzanej przez usługę. |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Wszystkie klucze szyfrowania są na wystąpienie usługi i są zarządzane przez usługę. |  |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | [Szyfrowanie trasy ekspresowej](../expressroute/index.yml) i sieci wirtualnej jest dostarczane przez [sieć Azure](../virtual-network/index.yml). |  |
| Szyfrowane wywołania interfejsu API| Tak | Wywołania płaszczyzny zarządzania są nawiązywaj za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml) za pośrednictwem protokołu TLS. Wymagany jest prawidłowy token internetowy JSON (JWT).  Wywołania płaszczyzny danych mogą być zabezpieczone za pomocą protokołu TLS i jednego z obsługiwanych mechanizmów uwierzytelniania (na przykład certyfikatu klienta lub JWT). |   |
 |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | Korzystanie z [zestawu zasobów DevOps usługi Azure API Management](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Luka w zabezpieczeniach skanuje fałszywe alarmy

W tej sekcji dokumenty są typowe luki w zabezpieczeniach, które nie mają wpływu na usługę Azure API Management.

| Problemy               | Opis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed jest luką w implementacji rozszerzenia TLS SessionTicket znalezionego w niektórych produktach F5. Umożliwia wyciek ("krwawienie") do 31 bajtów danych z niezainicjowanej pamięci. Jest to spowodowane przez stos TLS dopełnienie session id, przekazywane z klienta, z danymi, aby go 32 bitów długości. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)