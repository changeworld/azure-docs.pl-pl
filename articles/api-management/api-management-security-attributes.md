---
title: Wspólne atrybuty zabezpieczeń dla usługi Azure API Management
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi API Management
services: api-management
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 040d628f9fe89f68a1c5ab91a1522c6a3cb724d9
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/25/2019
ms.locfileid: "64508140"
---
# <a name="common-security-attributes-for-api-management"></a>Wspólne atrybuty zabezpieczeń dla usługi API Management

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowaną w usługi API Management.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak (tylko w przypadku szyfrowania po stronie usługi) | Dane poufne, takie jak certyfikaty, klucze i wartości o nazwie klucza tajnego są zaszyfrowane przy użyciu zarządzanego przez usługę, na klucze wystąpienia usługi. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | [Expressroute](../expressroute/index.yml) i sieci wirtualnej, szyfrowanie jest obsługiwane przez [sieci platformy Azure](../virtual-network/index.yml). |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie | Wszystkie klucze szyfrowania są za wystąpienie usługi i usługi zarządzane. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Yes | Wywołania płaszczyzny zarządzania odbywa się za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml) za pośrednictwem protokołu TLS. Nieprawidłowy token sieci web JSON (JWT) jest wymagana.  Wywołania płaszczyzny danych mogą być chronione przy użyciu protokołu TLS i jeden z mechanizmów uwierzytelniania obsługiwanych (np. certyfikat klienta lub JWT).
 |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Yes | |
| Izolacja sieci i zapory pomocy technicznej| Yes | Przy użyciu sieciowych grup zabezpieczeń (NSG) i Azure Application Gateway (lub inne urządzenie programowe) odpowiednio. |
| Obsługa tunelowania wymuszonego| Yes | Sieć Azure udostępnia wymuszonym tunelowaniem. |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | |
| Autoryzacja| Yes | |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Yes | [Dzienniki aktywności w usłudze Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Rejestrowanie płaszczyzny danych i inspekcji| Yes | [Dzienniki diagnostyczne platformy Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) i (opcjonalnie) [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Za pomocą [usługi Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Luki w zabezpieczeniach skanuje wyników fałszywie dodatnich

W tej sekcji omówiono powszechnych luk w zabezpieczeniach, które nie dotyczą usługi Azure API Management.

| Luka w zabezpieczeniach               | Opis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed Usterka w implementacji znaleziono w przypadku niektórych produktów F5 rozszerzenia TLS SessionTicket. Umożliwia ona wyciek ("jest") do 31 bajtów danych z niezainicjowanej pamięci. Jest to spowodowane przez stos protokołu TLS dopełnienie identyfikator sesji, przekazywane z klienta z danymi, aby stał się 32-bitowy długie. |