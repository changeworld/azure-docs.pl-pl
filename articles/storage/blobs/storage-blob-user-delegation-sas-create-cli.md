---
title: Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników przy użyciu poświadczeń usługi Azure Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371993"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob z interfejsem wiersza polecenia platformy Azure.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Zainstalowanie najnowszej wersji interfejsu wiersza polecenia platformy Azure

Aby użyć interfejsu wiersza polecenia platformy Azure do zabezpieczenia sygnatury dostępu Współdzielonego za pomocą poświadczeń usługi Azure AD, najpierw upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników przy użyciu interfejsu wiersza polecenia platformy Azure, upewnij się, że zainstalowano wersję 2.0.78 lub nowszą. Aby sprawdzić zainstalowaną wersję, użyj `az --version` polecenia.

## <a name="sign-in-with-azure-ad-credentials"></a>Logowanie się przy użyciu poświadczeń usługi Azure AD

Zaloguj się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz temat [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Przypisywanie uprawnień za pomocą funkcji RBAC

Aby utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników z usługi Azure PowerShell, konto usługi Azure AD używane do logowania się do interfejsu wiersza polecenia platformy Azure musi być przypisane roli, która zawiera akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** To uprawnienie umożliwia kontu usługi Azure AD żądanie *klucza delegowania użytkownika.* Klucz delegowania użytkowników służy do podpisywania sygnatury dostępu Współdzielonego delegowania użytkowników. Rola zapewniająca akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** musi być przypisana na poziomie konta magazynu, grupy zasobów lub subskrypcji.

Jeśli nie masz wystarczających uprawnień do przypisywania ról RBAC do podmiotu zabezpieczeń usługi Azure AD, może być konieczne zwrócenie się do właściciela konta lub administratora o przypisanie niezbędnych uprawnień.

Poniższy przykład przypisuje **rolę współautora danych obiektu Blob magazynu,** która obejmuje akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Rola jest o zakresie na poziomie konta magazynu.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Aby uzyskać więcej informacji na temat wbudowanych ról, które obejmują akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** zobacz [Wbudowane role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Zabezpieczanie sygnatury dostępu Współdzielonego za pomocą poświadczeń usługi Azure AD

Podczas tworzenia sygnatury dostępu Współdzielonego delegowania użytkownika za pomocą interfejsu wiersza polecenia platformy Azure klucz delegowania użytkownika, który jest używany do podpisywania sygnatury dostępu Współdzielonego jest tworzony dla Ciebie niejawnie. Czas rozpoczęcia i czas wygaśnięcia określony dla sygnatury dostępu Współdzielonego są również używane jako czas rozpoczęcia i czasu wygaśnięcia klucza delegowania użytkownika.

Ponieważ maksymalny interwał, w którym klucz delegowania użytkownika jest prawidłowy, wynosi 7 dni od daty rozpoczęcia, należy określić czas wygaśnięcia sygnatury dostępu Współdzielonego, który znajduje się w ciągu 7 dni od godziny rozpoczęcia. Sygnatura dostępu Współdzielonego jest nieprawidłowa po wygaśnięciu klucza delegowania użytkownika, więc sygnatury dostępu Współdzielonego z czasem wygaśnięcia większym niż 7 dni będzie nadal ważny tylko przez 7 dni.

Podczas tworzenia sygnatury `--auth-mode login` `--as-user parameters` dostępu Współdzielonego delegowania użytkownika, i są wymagane. Określ *logowanie* dla parametru, `--auth-mode` tak aby żądania wykonane w usłudze Azure Storage były autoryzowane przy użyciu poświadczeń usługi Azure AD. Określ `--as-user` parametr, aby wskazać, że zwracany sygnatura dostępu Współdzielonego powinna być sygnaturą dostępu Współdzielonego delegowania użytkowników.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera

Aby utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera z interfejsem wiersza polecenia platformy Azure, wywołanie [polecenia az storage container generate-sas.](/cli/azure/storage/container#az-storage-container-generate-sas)

Obsługiwane uprawnienia do delegowania użytkownika Sygnatury dostępu Współdzielonego w kontenerze obejmują Dodawanie, Tworzenie, Usuwanie, Lista, Odczyt i Zapis. Uprawnienia można określić pojedynczo lub połączyć. Aby uzyskać więcej informacji na temat tych uprawnień, zobacz [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas).

Poniższy przykład zwraca token sygnatury dostępu Współdzielonego delegowania użytkownika dla kontenera. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Zwrócony token sygnatury dostępu Współdzielonego delegowania użytkowników będzie podobny do:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla obiektu blob

Aby utworzyć sygnatury dostępu Współdzielonego delegowania użytkownika dla obiektu blob z interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure, wywołanie polecenia [az storage generate-sas.](/cli/azure/storage/blob#az-storage-blob-generate-sas)

Obsługiwane uprawnienia do delegowania użytkownika Sygnatury dostępu Współdzielonego w obiekcie blob obejmują Dodawanie, Tworzenie, Usuwanie, Odczyt i Zapis. Uprawnienia można określić pojedynczo lub połączyć. Aby uzyskać więcej informacji na temat tych uprawnień, zobacz [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas).

Następująca składnia zwraca sygnaturę dostępu Współdzielonego delegowania użytkowników dla obiektu blob. W przykładzie `--full-uri` określa parametr, który zwraca identyfikator URI obiektu blob z tokenem sygnatury dostępu Współdzielonego. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

Zwrócony identyfikator URI delegowania użytkowników będzie podobny do:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Sygnatura dostępu Współdzielonego delegowania użytkowników nie obsługuje definiowania uprawnień za pomocą zasad dostępu przechowywanego.

## <a name="revoke-a-user-delegation-sas"></a>Odwoływanie delegowania użytkownika SYGNATURA DOSTĘPU WSPÓŁDZIELONEGO

Aby odwołać sygnaturę dostępu Współdzielonego delegowania użytkownika z interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure, [wywołanie polecenia odwołania kluczy magazynu az.](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) To polecenie odwołuje wszystkie klucze delegowania użytkowników skojarzone z określonym kontem magazynu. Wszelkie podpisy dostępu współdzielonego skojarzone z tymi kluczami są unieważniane.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Zarówno klucz delegowania użytkownika, jak i przypisania roli RBAC są buforowane przez usługę Azure Storage, więc może wystąpić opóźnienie między rozpoczęciem procesu odwołania a nieprawidłowym identyfikatorem sygnatury dostępu Współdzielonego delegacji istniejącego użytkownika.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników (INTERFEJS API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)
