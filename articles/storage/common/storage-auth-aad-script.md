---
title: Uruchom polecenia wiersza polecenia platformy Azure lub programu PowerShell przy użyciu tożsamości usługi Azure AD, aby dostęp do usługi Azure Storage (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Program PowerShell i interfejsu wiersza polecenia platformy Azure obsługuje zalogować się przy użyciu tożsamości usługi Azure AD, aby uruchamiać polecenia na kontenery usługi Azure Storage i kolejki oraz ich dane. Token dostępu jest podana w sesji i autoryzowanie wywołania operacji. Uprawnienia są zależne od roli przypisanej do tożsamości usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/20/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 984185febf770ae10a021d129b0ef6c43da4d0f1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032771"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell (wersja zapoznawcza)

Usługa Azure Storage udostępnia rozszerzenia (wersja zapoznawcza) dla wiersza polecenia platformy Azure i programu PowerShell, które umożliwiają użytkownikowi Zaloguj się i uruchamianie poleceń skryptu z tożsamością usługi Azure Active Directory (Azure AD). Tożsamość usługi Azure AD może być użytkownika, grupy lub nazwy głównej usługi aplikacji, lub może być [tożsamości usługi zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md). Możesz przypisywać uprawnienia dostępu do zasobów magazynu tożsamości usługi Azure AD za pomocą kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzaj prawa dostępu do danych usługi Azure Storage za pomocą funkcji RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

Po zalogowaniu do wiersza polecenia platformy Azure lub programu PowerShell przy użyciu tożsamości usługi Azure AD, token dostępu jest zwracany do uzyskiwania dostępu do usługi Azure Storage w ramach tej tożsamości. Ten token jest następnie automatycznie używany przez interfejs wiersza polecenia lub programu PowerShell można autoryzować operacji dotyczących usługi Azure Storage. Dla obsługiwanych operacji nie trzeba przekazać klucz konta lub token sygnatury dostępu Współdzielonego za pomocą polecenia.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="supported-operations"></a>Obsługiwane operacje

Rozszerzenia (wersja zapoznawcza) są obsługiwane dla operacji na kontenerach i kolejek. Jakie operacje może wywołać zależy od uprawnienia udzielone do tożsamości usługi Azure AD za pomocą którego możesz Zaloguj się do wiersza polecenia platformy Azure lub programu PowerShell. Uprawnienia do kontenerów usługi Azure Storage lub kolejki są przypisywane przy użyciu kontroli dostępu opartej na rolach (RBAC). Na przykład jeśli rola czytnik danych jest przypisany do odpowiedniej tożsamości, następnie można uruchomić poleceń skryptu, które odczytują dane z kontenera lub kolejki. Jeśli rola Współautor danych jest przypisany do odpowiedniej tożsamości, można uruchomić poleceń skryptu, które odczytu, zapisu lub usuwania kontenera lub kolejki lub danych, które zawierają. 

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji magazynu platformy Azure, kontenera lub kolejki, zobacz [uprawnień do wywoływania operacji REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Wywołanie polecenia interfejsu wiersza polecenia przy użyciu tożsamości usługi Azure AD

Aby zainstalować rozszerzenia w wersji zapoznawczej dla wiersza polecenia platformy Azure:

1. Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom `az --version` Aby sprawdzić zainstalowaną wersją.
2. Uruchom następujące polecenie, aby zainstalować rozszerzenia w wersji zapoznawczej: 

    ```azurecli
    az extension add -n storage-preview
    ```

Rozszerzenia w wersji zapoznawczej dodaje nowy `--auth-mode` parametr obsługiwane polecenia:

- Ustaw `--auth-mode` parametr `login` logować się za pomocą tożsamości usługi Azure AD.
- Ustaw `--auth-mode` parametr do starszego `key` znajdują się wartości, aby spróbować odpytać do klucza konta, gdy nie parametry uwierzytelniania dla konta. 

Na przykład, aby pobrać obiekt blob w wiersza polecenia platformy Azure przy użyciu tożsamości usługi Azure AD, najpierw uruchom `az login`, następnie wywołaj polecenie `--auth-mode` równa `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

Zmienna środowiskowa skojarzone z `--auth-mode` parametr `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Wywołania poleceń programu PowerShell przy użyciu tożsamości usługi Azure AD

Program Azure PowerShell obsługuje logowanie się przy użyciu tożsamości usługi Azure AD przy użyciu jednego z następujących modułów w wersji zapoznawczej tylko: 

- 4.4.0-Preview 
- 4.4.1-Preview 

Aby zalogować się przy użyciu tożsamości usługi Azure AD za pomocą programu Azure PowerShell:

1. Odinstaluj wszystkie poprzednie instalacje programu Azure PowerShell:

    - Usuń wszystkie poprzednie instalacje programu Azure PowerShell z Windows przy użyciu **aplikacje i funkcje** w obszarze **ustawienia**.
    - Usuń wszystkie **Azure*** modułów z `%Program Files%\WindowsPowerShell\Modules`.

1. Upewnij się, że masz najnowszą wersję zainstalowanego modułu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Zamknij i Otwórz okno programu PowerShell po zainstalowaniu modułu PowerShellGet. 

1. Zainstaluj usługi AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

1. Zainstaluj jeden z modułów (wersja zapoznawcza):

    ```powershell
    Install-Module Azure.Storage –Repository PSGallery -RequiredVersion 4.4.1-preview  –AllowPrerelease –AllowClobber –Force 
    ```
1. Zamknij i Otwórz okno programu PowerShell.
1. Wywołaj [poleceniem New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) polecenia cmdlet, aby utworzyć kontekst i obejmują `-UseConnectedAccount` parametru. 
1. Aby wywoływać polecenia cmdlet przy użyciu tożsamości usługi Azure AD, należy przekazać nowo utworzony kontekst do polecenia cmdlet.

Poniższy przykład przedstawia listę obiektów blob w kontenerze za pomocą programu Azure PowerShell za pomocą tożsamości usługi Azure AD. Koniecznie Zastąp symbol zastępczy nazwy konta i kontenera przy użyciu własnych wartości: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName storagesamples -UseConnectedAccount 
Get-AzureStorageBlob -Container sample-container -Context $ctx 
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat ról RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).
- Aby dowiedzieć się więcej o korzystaniu z zarządzanych tożsamości dla zasobów platformy Azure z usługą Azure Storage, zobacz [uwierzytelnienia dostępu do obiektów blob i kolejki przy użyciu platformy Azure zarządzanych tożsamości dla zasobów platformy Azure (wersja zapoznawcza)](storage-auth-aad-msi.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z magazynu aplikacji](storage-auth-aad-app.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob platformy Azure, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
