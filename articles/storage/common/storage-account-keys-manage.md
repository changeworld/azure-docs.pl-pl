---
title: Zarządzanie kluczami dostępu do konta
titleSuffix: Azure Storage
description: Dowiedz się, jak wyświetlać i obracać klucze dostępu do konta magazynu oraz zarządzać nimi.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 68d0ef14a7b5967d6a4535da85d32128c3a12a19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486731"
---
# <a name="manage-storage-account-access-keys"></a>Zarządzanie kluczami dostępu do konta magazynu

Podczas tworzenia konta magazynu platforma Azure generuje 2 512-bitowe klucze dostępu do konta magazynu. Te klucze mogą służyć do autoryzowania dostępu do danych na koncie magazynu za pośrednictwem autoryzacji klucza współużytkowanego.

Firma Microsoft zaleca, aby zarządzać kluczami dostępu za pomocą Azure Key Vault i regularnie obracać i generować klucze. Za pomocą Azure Key Vault można łatwo obrócić klucze bez zakłócania swoich aplikacji. Możesz również ręcznie obrócić klucze.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Wyświetlanie kluczy dostępu i parametrów połączenia

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Zarządzanie kluczami dostępu za pomocą Azure Key Vault

Firma Microsoft zaleca używanie Azure Key Vault do zarządzania i obracania kluczy dostępu. Twoja aplikacja może bezpiecznie uzyskiwać dostęp do kluczy w Key Vault, dzięki czemu można uniknąć zapisywania ich w kodzie aplikacji. Aby uzyskać więcej informacji na temat używania Key Vault do zarządzania kluczami, zobacz następujące artykuły:

- [Zarządzanie kluczami konta magazynu za pomocą Azure Key Vault i programu PowerShell](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Zarządzanie kluczami konta magazynu przy użyciu Azure Key Vault i interfejsu wiersza polecenia platformy Azure](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ręcznie Obróć klucze dostępu

Firma Microsoft zaleca, aby okresowo obrócić klucze dostępu w celu zapewnienia bezpieczeństwa konta magazynu. Jeśli to możliwe, użyj Azure Key Vault do zarządzania kluczami dostępu. Jeśli nie używasz Key Vault, musisz ręcznie obrócić klucze.

Przypisane są dwa klucze dostępu, dzięki czemu można obrócić klucze. Posiadanie dwóch kluczy gwarantuje, że aplikacja utrzymuje dostęp do usługi Azure Storage w trakcie całego procesu.

> [!WARNING]
> Ponowne generowanie kluczy dostępu może mieć wpływ na wszystkie aplikacje lub usługi platformy Azure, które są zależne od klucza konta magazynu. Wszyscy klienci korzystający z klucza konta do uzyskiwania dostępu do konta magazynu muszą zostać zaktualizowani, aby używać nowego klucza, w tym usług Media Services, chmury, pulpitu i aplikacji mobilnych, oraz graficznych aplikacji interfejsu użytkownika dla usługi Azure Storage, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

Wykonaj ten proces, aby obrócić klucze konta magazynu:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby użyć klucza pomocniczego.
2. Ponownie wygeneruj podstawowy klucz dostępu do konta magazynu. W bloku **klucze dostępu** w Azure Portal kliknij pozycję **Generuj ponownie Klucz1**, a następnie kliknij przycisk **tak** , aby potwierdzić, że chcesz wygenerować nowy klucz.
3. Zaktualizuj parametry połączenia w kodzie, wprowadzając odwołanie do nowego podstawowego klucza dostępu.
4. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu.

> [!NOTE]
> Firma Microsoft zaleca używanie tylko jednego z kluczy we wszystkich aplikacjach w tym samym czasie. Jeśli używasz klucza 1 w niektórych miejscach i w innym miejscu niż 2, nie będziesz w stanie obrócić swoich kluczy bez utraty dostępu do aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta usługi Azure Storage](storage-account-overview.md)
- [Tworzenie konta magazynu](storage-quickstart-create-account.md)
