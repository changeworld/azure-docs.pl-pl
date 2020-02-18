---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Ten artykuł ułatwia planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault. Znany również jako BYOK lub własny klucz.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 00d2d38801929454110b41be88d133e3af232af7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425752"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importuj klucze chronione przez moduł HSM do Key Vault

Aby uzyskać gwarancję, w przypadku korzystania z Azure Key Vault można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń), które nigdy nie opuszczają granicy modułu HSM. Ten scenariusz jest często nazywany *własnym kluczem*lub BYOK. W celu ochrony kluczy Azure Key Vault używa rodziny oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń (zweryfikowany poziom 2 trybu FIPS 140-2).

Ta funkcja jest niedostępna dla platformy Azure w Chinach.

> [!NOTE]
> Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](key-vault-overview.md)  
> Aby zapoznać się z samouczkiem wprowadzającym, które obejmuje tworzenie magazynu kluczy dla kluczy chronionych przez moduł HSM, zobacz [co to jest Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>Obsługiwane sprzętowych modułów zabezpieczeń

Transferowanie kluczy chronionych przez moduł HSM do Key Vault jest obsługiwane za pośrednictwem dwóch różnych metod, w zależności od używanej sprzętowych modułów zabezpieczeń. Skorzystaj z poniższej tabeli, aby określić, która metoda powinna zostać użyta do generowania sprzętowych modułów zabezpieczeń, a następnie przenieść własne klucze chronione przez moduł HSM do użycia z Azure Key Vault. 

|Nazwa dostawcy modułu HSM|Obsługiwane modele HSM|Obsługiwana metoda modułu HSM — transfer klucza|
|---|---|---|
|Firmy Thales|<ul><li>Rodzina SafeNet Luna modułu HSM 7 z oprogramowaniem układowym w wersji 7,3 lub nowszej</li></ul>| [Użyj nowej metody BYOK (wersja zapoznawcza)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Oprogramowanie wspomagające nCipher|<ul><li>Rodzina sprzętowego nshield sprzętowych modułów zabezpieczeń</li></ul>|[Użyj starszej metody BYOK](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [Key Vault najlepszych](key-vault-best-practices.md) rozwiązań, aby zapewnić bezpieczeństwo, trwałość i monitorowanie kluczy.
