---
title: Wspólne atrybuty zabezpieczeń usługi Azure Resource Manager
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002256"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Atrybuty zabezpieczeń usługi Azure Resource Manager

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usłudze Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak |  |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | HTTPS/TLS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| ND | Usługa Azure Resource Manager przechowuje żadnej zawartości klientów tylko danych formantu. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| Yes | |
| Wywołania interfejsu API szyfrowane| Tak | |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Yes | |
| Izolacja sieci i zapory pomocy technicznej| Nie |  |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Nie | |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | [Usługa Azure Active Directory](/azure/active-directory) na podstawie.|
| Autoryzacja| Yes | |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Yes | Dzienniki aktywności udostępniają wszystkie operacje zapisu w magazynie (PUT, POST, DELETE) wykonywanych względem zasobów; zobacz [wyświetlanie dzienników aktywności do inspekcji akcje na zasobach](resource-group-audit.md). |
| Rejestrowanie płaszczyzny danych i inspekcji| ND | |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak |  |
