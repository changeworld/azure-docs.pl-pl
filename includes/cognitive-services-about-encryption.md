---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372173"
---
## <a name="about-cognitive-services-encryption"></a>Szyfrowanie usług Cognitive Services — informacje

Dane są szyfrowane i odszyfrowywane przy użyciu [256-bitowego](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) szyfrowania AES zgodnego ze standardem [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane dla Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu lub aplikacji, aby skorzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Zarządzanie kluczami szyfrowania — informacje

Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Jeśli używasz warstwy cenowej, która obsługuje klucze zarządzane przez klienta, można zobaczyć ustawienia szyfrowania **zasobu** w sekcji Szyfrowanie [w witrynie Azure portal](https://portal.azure.com), jak pokazano na poniższej ilustracji.

![Wyświetlanie ustawień szyfrowania](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

W przypadku subskrypcji, które obsługują tylko klucze szyfrowania zarządzane przez firmę Microsoft, nie będzie mieć sekcji **szyfrowanie.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Istnieje również opcja zarządzania subskrypcją za pomocą własnych kluczy. Klucze zarządzane przez klienta (CMK), znane również jako Bring your own key (BYOK), oferują większą elastyczność w tworzeniu, obracaniu, wyłączania i odwoływaniu kontroli dostępu. Można również przeprowadzić inspekcję kluczy szyfrowania używanych do ochrony danych.
