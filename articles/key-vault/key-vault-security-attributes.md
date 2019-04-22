---
title: Wspólne atrybuty zabezpieczeń dla usługi Azure Key Vault
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure Key Vault
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: fa36a3c1eb6bda109c7985fa7cade496d2ccf9f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677803"
---
# <a name="common-security-attributes-for-azure-key-vault"></a>Wspólne atrybuty zabezpieczeń dla usługi Azure Key Vault

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Wszystkie obiekty są szyfrowane. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Cała komunikacja jest za pomocą zaszyfrowanych wywołań interfejsu API |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Klient kontroluje wszystkie klucze w ich usługi Key Vault. Jeśli określono klucze modułu HSM kopii zabezpieczeń sprzętowych FIPS poziom 2 przez sprzętowy moduł zabezpieczeń chroni klucza, certyfikatu lub klucza tajnego. |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes | Przy użyciu protokołu HTTPS. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes | Za pomocą punktów końcowych usługi sieci wirtualnej (Vnet). |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Korzystanie z reguł zapory sieci wirtualnej. |
| Obsługa wymuszonego tunelowania | Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Za pomocą usługi Log Analytics. |

## <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes | Za pomocą zasad dostępu magazynu kluczy. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja na płaszczyźnie kontroli i zarządzania nimi.| Yes | Za pomocą usługi Log Analytics. |
| Rejestrowanie i inspekcja na płaszczyźnie danych| Yes | Za pomocą usługi Log Analytics. |

## <a name="access-controls"></a>Kontrole dostępu

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Kontroli dostępu do płaszczyzny zarządzania/kontroli | Yes | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontroli dostępu do płaszczyzny danych (na każdym poziomie usługi) | Yes | Zasady dostępu magazynu kluczy |