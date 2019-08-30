---
title: Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza) — Azure Storage
description: Dowiedz się, jak utworzyć sygnaturę dostępu współdzielonego użytkownika przy użyciu poświadczeń Azure Active Directory w usłudze Azure Storage przy użyciu interfejsu wiersza polecenia platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 6ea4dbf07c8ef99c43dbe7add1ae9270056f708c
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164325"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure

Aby użyć interfejsu wiersza polecenia platformy Azure do zabezpieczenia sygnatury dostępu współdzielonego przy użyciu poświadczeń usługi Azure AD, najpierw upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-with-azure-ad-credentials"></a>Zaloguj się przy użyciu poświadczeń usługi Azure AD

Zaloguj się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Przypisywanie uprawnień przy użyciu RBAC

Aby można było utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika z Azure PowerShell, konto usługi Azure AD używane do logowania się do interfejsu wiersza polecenia platformy Azure musi mieć przypisaną rolę obejmującą akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . To uprawnienie umożliwia kontu usługi Azure AD zażądanie *klucza delegowania użytkownika*. Klucz delegowania użytkownika służy do podpisywania sygnatury dostępu współdzielonego delegowania użytkownika. Rola dostarczająca akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** musi być przypisana na poziomie konta magazynu, grupy zasobów lub subskrypcji.

Jeśli nie masz wystarczających uprawnień do przypisywania ról RBAC do podmiotu zabezpieczeń usługi Azure AD, może być konieczne poproszenie właściciela konta lub administratora, aby przypisał odpowiednie uprawnienia.

Poniższy przykład przypisuje rolę **współautor danych obiektów blob magazynu** , która obejmuje akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Rola jest objęta zakresem na poziomie konta magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Aby uzyskać więcej informacji na temat wbudowanych ról zawierających akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** , zobacz [wbudowane role dla zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Zabezpieczanie sygnatury dostępu współdzielonego przy użyciu poświadczeń usługi Azure AD

Gdy tworzysz sygnaturę dostępu współdzielonego użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure, klucz delegowania użytkownika używany do podpisywania sygnatury dostępu współdzielonego jest tworzony niejawnie. Czas rozpoczęcia i czas wygaśnięcia określony dla sygnatury dostępu współdzielonego są również używane jako czas rozpoczęcia i czas wygaśnięcia klucza delegowania użytkownika.

Ze względu na to, że maksymalny interwał ważności klucza delegowania użytkownika wynosi 7 dni od daty rozpoczęcia, należy określić czas wygaśnięcia dla sygnatury dostępu współdzielonego w ciągu 7 dni od czasu rozpoczęcia. Sygnatura dostępu współdzielonego jest nieprawidłowa po wygaśnięciu klucza delegowania użytkownika, więc sygnatura dostępu współdzielonego o godzinie przekraczającej 7 dni będzie nadal ważna tylko przez 7 dni.

W `--auth-mode login` przypadku tworzenia sygnatury dostępu współdzielonego `--as-user parameters` użytkownika wymagane są i. Określ *nazwę logowania* dla `--auth-mode` parametru, aby żądania kierowane do usługi Azure Storage były autoryzowane przy użyciu poświadczeń usługi Azure AD. `--as-user` Określ parametr wskazujący, że zwracany sygnatura dostępu współdzielonego powinien być sygnaturą dostępu współdzielonego delegowaną przez użytkownika.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera

Aby utworzyć sygnaturę dostępu współdzielonego delegowania użytkowników dla kontenera za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage Container Generate-SAS](/cli/azure/storage/container#az-storage-container-generate-sas) .

Uprawnienia obsługiwane w przypadku delegowania SYGNATURy dostępu użytkownika w kontenerze obejmują dodawanie, tworzenie, usuwanie, wyświetlanie, Odczyt i zapis. Uprawnienia można określić pojedynczo lub łącznie. Aby uzyskać więcej informacji o tych uprawnieniach, zobacz [Tworzenie skojarzeń zabezpieczeń delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas).

Poniższy przykład zwraca token użytkownika delegowania sygnatury dostępu współdzielonego dla kontenera. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Zwrócony token sygnatury dostępu współdzielonego użytkownika będzie podobny do:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla obiektu BLOB

Aby utworzyć sygnaturę dostępu współdzielonego dla delegowania użytkowników dla obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage BLOB Generate-SAS](/cli/azure/storage/blob#az-storage-blob-generate-sas) .

Obsługiwane uprawnienia do delegowania przez użytkownika funkcji sygnatury dostępu współdzielonego w obiekcie blob obejmują dodawanie, tworzenie, usuwanie, Odczyt i zapis. Uprawnienia można określić pojedynczo lub łącznie. Aby uzyskać więcej informacji o tych uprawnieniach, zobacz [Tworzenie skojarzeń zabezpieczeń delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas).

Poniższa składnia zwraca sygnaturę dostępu współdzielonego delegowania użytkownika dla obiektu BLOB. W przykładzie określono `--full-uri` parametr, który zwraca identyfikator URI obiektu BLOB z dołączonym tokenem SAS. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Zwrócony identyfikator URI sygnatury dostępu współdzielonego użytkownika będzie wyglądać podobnie do:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Sygnatura dostępu współdzielonego użytkownika nie obsługuje definiowania uprawnień z przechowywanymi zasadami.

## <a name="revoke-a-user-delegation-sas"></a>Odwoływanie sygnatury dostępu współdzielonego użytkownika

Aby odwołać sygnaturę dostępu współdzielonego delegowania użytkownika z interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account REVOKE-delegowanie-Keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) . To polecenie odwołuje wszystkie klucze delegowania użytkowników skojarzone z określonym kontem magazynu. Wszystkie sygnatury dostępu współdzielonego skojarzone z tymi kluczami są unieważnione.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Zarówno przypisanie klucza delegowania użytkownika, jak i roli RBAC są buforowane przez usługę Azure Storage, dzięki czemu może wystąpić opóźnienie między zainicjowaniem procesu odwoływania a istniejącym skojarzeniem SAS delegowania użytkownika.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie sygnatury dostępu współdzielonego (API REST) delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas)
- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)
