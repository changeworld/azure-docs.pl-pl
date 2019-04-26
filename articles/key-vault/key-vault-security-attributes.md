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
ms.openlocfilehash: 3ccfc38136ba3e8ec7c6130658032b7565988e5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461422"
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
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes | Przy użyciu protokołu HTTPS. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Yes | Za pomocą punktów końcowych usługi sieci wirtualnej (Vnet). |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i zapory pomocy technicznej| Yes | Korzystanie z reguł zapory sieci wirtualnej. |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Za pomocą usługi Log Analytics. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Autoryzacja| Yes | Za pomocą zasad dostępu magazynu kluczy. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja na płaszczyźnie kontroli i zarządzania nimi.| Yes | Za pomocą usługi Log Analytics. |
| Rejestrowanie płaszczyzny danych i inspekcji| Yes | Za pomocą usługi Log Analytics. |

## <a name="access-controls"></a>Kontrole dostępu

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Kontroli dostępu do płaszczyzny zarządzania/kontroli | Yes | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontroli dostępu do płaszczyzny danych (na każdym poziomie usługi) | Yes | Zasady dostępu magazynu kluczy |