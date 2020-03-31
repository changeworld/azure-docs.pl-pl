---
title: Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników przy użyciu poświadczeń usługi Azure Active Directory przy użyciu programu PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536177"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą programu PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą programu Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

Aby utworzyć sygnaturę dostępu Współdzielonego delegowania użytkowników za pomocą programu PowerShell, zainstaluj w wersji 1.10.0 lub nowszej modułu Az.Storage. Wykonaj następujące kroki, aby zainstalować najnowszą wersję modułu:

1. Odinstaluj wszystkie poprzednie instalacje programu Azure PowerShell:

    - Usuń wszystkie poprzednie instalacje programu Azure PowerShell z systemu Windows przy użyciu ustawienia **funkcji & aplikacje** w obszarze **Ustawienia**.
    - Usuń wszystkie moduły `%Program Files%\WindowsPowerShell\Modules`platformy **Azure** z pliku .

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell po zainstalowaniu programu PowerShellGet.

1. Zainstaluj najnowszą wersję programu Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Upewnij się, że zainstalowano usługę Azure PowerShell w wersji 3.2.0 lub nowszej. Uruchom następujące polecenie, aby zainstalować najnowszą wersję modułu programu Azure Storage PowerShell:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell.

Aby sprawdzić, która wersja modułu Az.Storage jest zainstalowana, uruchom następujące polecenie:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Aby uzyskać więcej informacji dotyczących instalowania programu Azure PowerShell, zobacz [Instalowanie programu Azure PowerShell za pomocą programu PowerShellGet.](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Logowanie się do programu Azure PowerShell za pomocą usługi Azure AD

Wywołanie polecenia [Connect-AzAccount,](/powershell/module/az.accounts/connect-azaccount) aby zalogować się za pomocą konta usługi Azure AD:

```powershell
Connect-AzAccount
```

Aby uzyskać więcej informacji na temat logowania się za pomocą programu PowerShell, zobacz [Logowanie się za pomocą programu Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Przypisywanie uprawnień za pomocą funkcji RBAC

Aby utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników z usługi Azure PowerShell, konto usługi Azure AD używane do logowania się do programu PowerShell musi mieć przypisaną rolę, która zawiera akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** To uprawnienie umożliwia kontu usługi Azure AD żądanie *klucza delegowania użytkownika.* Klucz delegowania użytkowników służy do podpisywania sygnatury dostępu Współdzielonego delegowania użytkowników. Rola zapewniająca akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** musi być przypisana na poziomie konta magazynu, grupy zasobów lub subskrypcji. Aby uzyskać więcej informacji na temat uprawnień RBAC do tworzenia sygnatury dostępu Współdzielonego delegowania użytkowników, zobacz sekcję **Przypisywanie uprawnień z rbac** w [temacie Tworzenie sygnatury dostępu współdzielonego delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas).

Jeśli nie masz wystarczających uprawnień do przypisywania ról RBAC do podmiotu zabezpieczeń usługi Azure AD, może być konieczne zwrócenie się do właściciela konta lub administratora o przypisanie niezbędnych uprawnień.

Poniższy przykład przypisuje **rolę współautora danych obiektu Blob magazynu,** która obejmuje akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Rola jest o zakresie na poziomie konta magazynu.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Aby uzyskać więcej informacji na temat wbudowanych ról, które obejmują akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** zobacz [Wbudowane role zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Zabezpieczanie sygnatury dostępu Współdzielonego za pomocą poświadczeń usługi Azure AD

Podczas tworzenia sygnatury dostępu Współdzielonego delegowania użytkowników za pomocą programu Azure PowerShell klucz delegowania użytkowników, który jest używany do podpisywania sygnatury dostępu Współdzielonego jest tworzony dla Ciebie niejawnie. Czas rozpoczęcia i czas wygaśnięcia określony dla sygnatury dostępu Współdzielonego są również używane jako czas rozpoczęcia i czasu wygaśnięcia klucza delegowania użytkownika. 

Ponieważ maksymalny interwał, w którym klucz delegowania użytkownika jest prawidłowy, wynosi 7 dni od daty rozpoczęcia, należy określić czas wygaśnięcia sygnatury dostępu Współdzielonego, który znajduje się w ciągu 7 dni od godziny rozpoczęcia. Sygnatura dostępu Współdzielonego jest nieprawidłowa po wygaśnięciu klucza delegowania użytkownika, więc sygnatury dostępu Współdzielonego z czasem wygaśnięcia większym niż 7 dni będzie nadal ważny tylko przez 7 dni.

Aby utworzyć sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu `-UseConnectedAccount` blob za pomocą programu Azure PowerShell, należy najpierw utworzyć nowy obiekt kontekstu usługi Azure Storage, określając parametr. Parametr `-UseConnectedAccount` określa, że polecenie tworzy obiekt kontekstu w ramach konta usługi Azure AD, z którym zalogowano się.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera

Aby zwrócić token sygnatury dostępu Współdzielonego delegowania użytkownika dla kontenera, należy wywołać polecenie [New-AzStorageContainerSASToken,](/powershell/module/az.storage/new-azstoragecontainersastoken) przekazując w obiekcie kontekstu usługi Azure Storage, który został utworzony wcześniej.

Poniższy przykład zwraca token sygnatury dostępu Współdzielonego delegowania użytkownika dla kontenera. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Zwrócony token sygnatury dostępu Współdzielonego delegowania użytkowników będzie podobny do:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla obiektu blob

Aby zwrócić token sygnatury dostępu Współdzielonego delegowania użytkownika dla obiektu blob, należy wywołać polecenie [New-AzStorageBlobSASToken,](/powershell/module/az.storage/new-azstorageblobsastoken) przekazując w obiekcie kontekstu usługi Azure Storage, który został utworzony wcześniej.

Następująca składnia zwraca sygnaturę dostępu Współdzielonego delegowania użytkowników dla obiektu blob. W przykładzie `-FullUri` określa parametr, który zwraca identyfikator URI obiektu blob z tokenem sygnatury dostępu Współdzielonego. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Zwrócony identyfikator URI delegowania użytkowników będzie podobny do:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Sygnatura dostępu Współdzielonego delegowania użytkowników nie obsługuje definiowania uprawnień za pomocą zasad dostępu przechowywanego.

## <a name="revoke-a-user-delegation-sas"></a>Odwoływanie delegowania użytkownika SYGNATURA DOSTĘPU WSPÓŁDZIELONEGO

Aby odwołać sygnaturę dostępu Współdzielonego delegowania użytkowników z programu Azure PowerShell, wywołanie polecenia **Revoke-AzStorageAccountUserDelegationKeys.** To polecenie odwołuje wszystkie klucze delegowania użytkowników skojarzone z określonym kontem magazynu. Wszelkie podpisy dostępu współdzielonego skojarzone z tymi kluczami są unieważniane.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Zarówno klucz delegowania użytkownika, jak i przypisania roli RBAC są buforowane przez usługę Azure Storage, więc może wystąpić opóźnienie między rozpoczęciem procesu odwołania a nieprawidłowym identyfikatorem sygnatury dostępu Współdzielonego delegacji istniejącego użytkownika.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników (INTERFEJS API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)
