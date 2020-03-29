---
title: Szyfrowanie danych w stanie spoczynku
titleSuffix: Azure Cognitive Services
description: Szyfrowanie danych w stanie spoczynku.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372239"
---
# <a name="translator-encryption-of-data-at-rest"></a>Szyfrowanie danych w stanie spoczynku

Translator automatycznie szyfruje dane, które są przekazywane do tworzenia niestandardowych modeli tłumaczenia, gdy są zachowywane w chmurze, pomagając spełnić cele zabezpieczeń i zgodności organizacji.

## <a name="about-cognitive-services-encryption"></a>Szyfrowanie usług Cognitive Services — informacje

Dane są szyfrowane i odszyfrowywane przy użyciu [256-bitowego](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) szyfrowania AES zgodnego ze standardem [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane dla Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu lub aplikacji, aby skorzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Zarządzanie kluczami szyfrowania — informacje

Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Jeśli używasz warstwy cenowej, która obsługuje klucze zarządzane przez klienta, można zobaczyć ustawienia szyfrowania **zasobu** w sekcji Szyfrowanie [w witrynie Azure portal](https://portal.azure.com), jak pokazano na poniższej ilustracji.

![Wyświetlanie ustawień szyfrowania](../media/cognitive-services-encryption/encryptionblade.png)

W przypadku subskrypcji, które obsługują tylko klucze szyfrowania zarządzane przez firmę Microsoft, nie będzie mieć sekcji **szyfrowanie.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Istnieje również opcja zarządzania subskrypcją za pomocą własnych kluczy. Klucze zarządzane przez klienta (CMK), znane również jako Bring your own key (BYOK), oferują większą elastyczność w tworzeniu, obracaniu, wyłączania i odwoływaniu kontroli dostępu. Można również przeprowadzić inspekcję kluczy szyfrowania używanych do ochrony danych.

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne dla wszystkich warstw cenowych dla usługi Translator. Aby poprosić o możliwość korzystania z kluczy zarządzanych przez klienta, wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta tłumacza,](https://aka.ms/cogsvc-cmk) wysłuchanie statusu żądania zajmie około 3-5 dni roboczych. W zależności od zapotrzebowania, może być umieszczony w kolejce i zatwierdzone jako miejsce staje się dostępne. Po zatwierdzeniu do korzystania z cmk z usługą Translator, należy utworzyć nowy zasób translatora. Po utworzeniu zasobu translatora można użyć usługi Azure Key Vault do skonfigurowania tożsamości zarządzanej.

Wykonaj następujące kroki, aby włączyć klucze zarządzane przez klienta dla translatora:

1. Utwórz nowy regionalny zasób Translator Text lub regionalnych usług Cognitive Services. To nie będzie działać z zasobem globalnym.
2. Włączono tożsamość zarządzaną w witrynie Azure portal i dodaj informacje o kluczu zarządzanym przez klienta.
3. Utwórz nowy obszar roboczy w uzywniu usługi Custom Translator i skojarz te informacje o subskrypcji.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta

Do przechowywania kluczy zarządzanych przez klienta należy używać usługi Azure Key Vault. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Zasób usług Cognitive Services i magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie usługi Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Nowy zasób usług Cognitive Services jest zawsze szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. Nie jest możliwe włączenie kluczy zarządzanych przez klienta w czasie tworzenia zasobu. Klucze zarządzane przez klienta są przechowywane w usłudze Azure Key Vault, a magazyn kluczy musi być aprowizowany za pomocą zasad dostępu, które przyznają uprawnienia klucza do tożsamości zarządzanej skojarzonej z zasobem usług Cognitive Services. Tożsamość zarządzana jest dostępna natychmiast po utworzeniu zasobu.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Azure Key Vault for Cognitive Services, zobacz:

- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Key Vault dla usług Cognitive Services z witryny Azure portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Włączenie kluczy zarządzanych przez klienta umożliwi również systemowi przypisaną tożsamość zarządzaną, funkcję usługi Azure AD. Po włączeniu tożsamości zarządzanej przypisanej przez system ten zasób zostanie zarejestrowany w usłudze Azure Active Directory. Po zarejestrowaniu, tożsamość zarządzana otrzyma dostęp do usługi Key Vault wybranej podczas konfiguracji klucza zarządzanego przez klienta. Więcej informacji na temat [tożsamości zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)można dowiedzieć się więcej.

> [!IMPORTANT]
> Jeśli wyłączysz tożsamości zarządzane przypisane przez system, dostęp do magazynu kluczy zostanie usunięty, a wszelkie dane zaszyfrowane za pomocą kluczy klienta nie będą już dostępne. Wszystkie funkcje zależne od tych danych przestaną działać. Wszystkie wdrożone modele również zostaną cofnięte. Wszystkie przesłane dane zostaną usunięte z usługi Custom Translator. Jeśli zarządzane tożsamości są ponownie włączone, nie zostanie automatycznie ponownie wdrożyć model dla Ciebie.

> [!IMPORTANT]
> Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. Podczas konfigurowania kluczy zarządzanych przez klienta w witrynie Azure portal, tożsamość zarządzana jest automatycznie przypisywana w ramach okładek. Jeśli następnie przenieść subskrypcję, grupę zasobów lub zasób z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z zasobem nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą już nie działać. Aby uzyskać więcej informacji, zobacz **Przenoszenie subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowywanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault

Aby włączyć klucze zarządzane przez klienta, należy użyć usługi Azure Key Vault do przechowywania kluczy. Należy włączyć właściwości **Usuwanie nietrwałe** i **Nie przeczyszczaj** w magazynie kluczy.

Tylko klucze RSA o rozmiarze 2048 są obsługiwane za pomocą szyfrowania usług Cognitive Services. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)usługi Azure Key Vault .

> [!NOTE]
> Jeśli cały magazyn kluczy zostanie usunięty, dane nie będą już wyświetlane, a wszystkie modele zostaną cofnięte. Wszystkie przesłane dane zostaną usunięte z usługi Custom Translator. 

### <a name="revoke-access-to-customer-managed-keys"></a>Cofanie dostępu do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) lub [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Cofnięcie dostępu skutecznie blokuje dostęp do wszystkich danych w zasobie usług Cognitive Services, a modele zostaną cofnięte, ponieważ klucz szyfrowania jest niedostępny przez usługi Cognitive Services. Wszystkie przesłane dane zostaną również usunięte z usługi Custom Translator.


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
