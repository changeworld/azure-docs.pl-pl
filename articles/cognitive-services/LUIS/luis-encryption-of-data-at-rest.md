---
title: Language Understanding service encryption of data at rest
titleSuffix: Azure Cognitive Services
description: Language Understanding service encryption of data at rest.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372338"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Language Understanding service encryption of data at rest

Usługa Language Understanding automatycznie szyfruje dane, gdy są one utrwalone w chmurze. Szyfrowanie usługi Language Understanding chroni dane i pomaga spełnić zobowiązania dotyczące zabezpieczeń i zgodności organizacji.

## <a name="about-cognitive-services-encryption"></a>Szyfrowanie usług Cognitive Services — informacje

Dane są szyfrowane i odszyfrowywane przy użyciu [256-bitowego](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) szyfrowania AES zgodnego ze standardem [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane dla Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu lub aplikacji, aby skorzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Zarządzanie kluczami szyfrowania — informacje

Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Istnieje również opcja zarządzania subskrypcją za pomocą własnych kluczy. Klucze zarządzane przez klienta (CMK) oferują większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Można również przeprowadzić inspekcję kluczy szyfrowania używanych do ochrony danych.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Istnieje również opcja zarządzania subskrypcją za pomocą własnych kluczy. Klucze zarządzane przez klienta (CMK), znane również jako Bring your own key (BYOK), oferują większą elastyczność w tworzeniu, obracaniu, wyłączania i odwoływaniu kontroli dostępu. Można również przeprowadzić inspekcję kluczy szyfrowania używanych do ochrony danych.

Do przechowywania kluczy zarządzanych przez klienta należy używać usługi Azure Key Vault. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Zasób usług Cognitive Services i magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie usługi Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Klucze zarządzane przez klienta do rozumienia języka

Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta usługi LUIS](https://aka.ms/cogsvc-cmk)Service . To zajmie około 3-5 dni roboczych, aby usłyszeć z powrotem na temat statusu wniosku. W zależności od zapotrzebowania, może być umieszczony w kolejce i zatwierdzone jako miejsce staje się dostępne. Po zatwierdzeniu do korzystania z cmk z usługą LUIS, należy utworzyć nowy zasób opisu języka z witryny Azure portal i wybierz E0 jako warstwy cenowej. Nowa jednostka SKU będzie działać tak samo jak jednostka SKU F0, która jest już dostępna z wyjątkiem cmk. Użytkownicy nie będą mogli uaktualnić z F0 do nowej jednostki SKU E0.

Zasoby E0 są dostępne tylko dla usługi tworzenia i że warstwa E0 początkowo będzie obsługiwana tylko w regionie zachodnie stany USA.

![Obraz subskrypcji usługi LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są obecnie dostępne w regionie **Zachodnie stany USA.**

### <a name="limitations"></a>Ograniczenia

Istnieją pewne ograniczenia podczas korzystania z warstwy E0 z istniejącymi/wcześniej utworzonymi aplikacjami:

* Migracja do zasobu E0 zostanie zablokowana. Użytkownicy będą mogli migrować swoje aplikacje tylko do zasobów F0. Po migracji istniejącego zasobu do F0 można utworzyć nowy zasób w warstwie E0. Dowiedz się więcej o [migracji tutaj](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* Przenoszenie aplikacji do lub z zasobu E0 zostanie zablokowane. Obejście tego ograniczenia polega na wyeksportowaniu istniejącej aplikacji i zaimportowaniu jej jako zasobu E0.
* Funkcja sprawdzania pisowni Bing nie jest obsługiwana.
* Rejestrowanie ruchu użytkownika końcowego jest wyłączone, jeśli aplikacja jest E0.
* Funkcja tworzenia witryny rozpoznawania mowy z usługi Azure Bot nie jest obsługiwana dla aplikacji w warstwie E0. Ta funkcja jest dostępna za pośrednictwem usługi Azure Bot Service, która nie obsługuje cmk.
* Funkcja tworzenia gruntowania mowy z portalu wymaga usługi Azure Blob Storage. Aby uzyskać więcej informacji, zobacz [przywładzaj własną pamięć masową](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta

Nowy zasób usług Cognitive Services jest zawsze szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. Nie jest możliwe włączenie kluczy zarządzanych przez klienta w czasie tworzenia zasobu. Klucze zarządzane przez klienta są przechowywane w usłudze Azure Key Vault, a magazyn kluczy musi być aprowizowany za pomocą zasad dostępu, które przyznają uprawnienia klucza do tożsamości zarządzanej skojarzonej z zasobem usług Cognitive Services. Tożsamość zarządzana jest dostępna tylko po utworzeniu zasobu przy użyciu warstwy cenowej dla cmk.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Azure Key Vault for Cognitive Services, zobacz:

- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Key Vault dla usług Cognitive Services z witryny Azure portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Włączenie kluczy zarządzanych przez klienta umożliwi również systemowi przypisaną tożsamość zarządzaną, funkcję usługi Azure AD. Po włączeniu tożsamości zarządzanej przypisanej przez system ten zasób zostanie zarejestrowany w usłudze Azure Active Directory. Po zarejestrowaniu, tożsamość zarządzana otrzyma dostęp do usługi Key Vault wybranej podczas konfiguracji klucza zarządzanego przez klienta. Więcej informacji na temat [tożsamości zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)można dowiedzieć się więcej.

> [!IMPORTANT]
> Jeśli wyłączysz tożsamości zarządzane przypisane przez system, dostęp do magazynu kluczy zostanie usunięty, a wszelkie dane zaszyfrowane za pomocą kluczy klienta nie będą już dostępne. Wszystkie funkcje zależne od tych danych przestaną działać.

> [!IMPORTANT]
> Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. Podczas konfigurowania kluczy zarządzanych przez klienta w witrynie Azure portal, tożsamość zarządzana jest automatycznie przypisywana w ramach okładek. Jeśli następnie przenieść subskrypcję, grupę zasobów lub zasób z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z zasobem nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą już nie działać. Aby uzyskać więcej informacji, zobacz **Przenoszenie subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowywanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault

Aby włączyć klucze zarządzane przez klienta, należy użyć usługi Azure Key Vault do przechowywania kluczy. Należy włączyć właściwości **Usuwanie nietrwałe** i **Nie przeczyszczaj** w magazynie kluczy.

Tylko klucze RSA o rozmiarze 2048 są obsługiwane za pomocą szyfrowania usług Cognitive Services. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)usługi Azure Key Vault .

### <a name="rotate-customer-managed-keys"></a>Obracanie kluczy zarządzanych przez klienta

Klucz zarządzany przez klienta można obrócić w usłudze Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować zasób usług Cognitive Services, aby użyć nowego klucza URI. Aby dowiedzieć się, jak zaktualizować zasób w celu użycia nowej wersji klucza w witrynie Azure portal, zobacz sekcję **"Aktualizowanie wersji klucza** w [obszarze Konfigurowanie kluczy zarządzanych przez klienta dla usług Cognitive Services za pomocą portalu Azure](../Encryption/cognitive-services-encryption-keys-portal.md).

Obracanie klucza nie powoduje ponownego szyfrowania danych w zasobie. Nie ma żadnych dalszych działań wymaganych od użytkownika.

### <a name="revoke-access-to-customer-managed-keys"></a>Cofanie dostępu do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) lub [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Cofnięcie dostępu skutecznie blokuje dostęp do wszystkich danych w zasobie usług Cognitive Services, ponieważ klucz szyfrowania jest niedostępny dla usług Cognitive Services.

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza zarządzanego przez klienta usługi SERVICE](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
