---
title: Szyfrowanie danych QnA Maker w spoczynku
titleSuffix: Azure Cognitive Services
description: Szyfrowanie danych QnA Maker w spoczynku .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372096"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>Szyfrowanie danych QnA Maker w spoczynku

Program QnA Maker automatycznie szyfruje dane, gdy są one utrwalone w chmurze, co pomaga osiągnąć cele w zakresie zabezpieczeń i zgodności z przepisami.

## <a name="about-encryption-key-management"></a>Zarządzanie kluczami szyfrowania — informacje

Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Istnieje również opcja zarządzania subskrypcją za pomocą własnych kluczy. Klucze zarządzane przez klienta (CMK) oferują większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Można również przeprowadzić inspekcję kluczy szyfrowania używanych do ochrony danych.

Program QnA Maker korzysta z pomocy technicznej cmk z usługi Azure search. Musisz utworzyć [cmk w usłudze Azure Search przy użyciu usługi Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). To wystąpienie platformy Azure powinny być skojarzone z usługą QnA Maker, aby włączyć cmk.

> [!IMPORTANT]
> Your Azure Search service resource must have been created after January 2019 and cannot be in the free (shared) tier. Nie ma pomocy technicznej do konfigurowania kluczy zarządzanych przez klienta w witrynie Azure portal.

## <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta

Usługa QnA Maker korzysta z cmk z usługi Azure Search. Wykonaj następujące kroki, aby włączyć cmks:

1. Utwórz nowe wystąpienie usługi Azure Search i włącz wymagania wstępne wymienione w [wymaganiach wstępnych klucza zarządzanego przez klienta dla usługi Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Wyświetlanie ustawień szyfrowania](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Podczas tworzenia zasobu programu QnA Maker jest on automatycznie skojarzony z wystąpieniem usługi Azure Search. Nie można tego używać z cmk. Aby korzystać z cmk, należy skojarzyć nowo utworzone wystąpienie usługi Azure Search, który został utworzony w kroku 1. W szczególności należy zaktualizować i `AzureSearchAdminKey` `AzureSearchName` w zasobie QnA Maker.

   ![Wyświetlanie ustawień szyfrowania](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Następnie utwórz nowe ustawienie aplikacji:
   * **Nazwa**: Ustaw to na`CustomerManagedEncryptionKeyUrl`
   * **Wartość:** Jest to wartość, która została naniesa przez krok 1 podczas tworzenia wystąpienia usługi Azure Search.

   ![Wyświetlanie ustawień szyfrowania](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Po zakończeniu uruchom ponownie środowisko wykonawcze. Teraz usługa QnA Maker jest obsługiwana przez CMK.

## <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są dostępne we wszystkich regionach usługi Azure Search.

## <a name="next-steps"></a>Następne kroki

* [Szyfrowanie w usłudze Azure Search przy użyciu obiektów CMK w usłudze Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Szyfrowanie danych w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
