---
title: Uruchom polecenia wiersza polecenia platformy Azure lub programu PowerShell przy użyciu tożsamości usługi Azure AD, aby dostęp do usługi Azure Storage (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Program PowerShell i interfejsu wiersza polecenia platformy Azure obsługuje zalogować się przy użyciu tożsamości usługi Azure AD, aby uruchamiać polecenia na kontenery usługi Azure Storage i kolejki oraz ich dane. Token dostępu jest podana w sesji i autoryzowanie wywołania operacji. Uprawnienia są zależne od roli przypisanej do tożsamości usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 253edccef064e729e96bceac619458cf4c585ae4
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522489"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell (wersja zapoznawcza)

Usługa Azure Storage udostępnia rozszerzenia (wersja zapoznawcza) dla wiersza polecenia platformy Azure i programu PowerShell, które umożliwiają użytkownikowi Zaloguj się i uruchamianie poleceń skryptu z tożsamością usługi Azure Active Directory (Azure AD). Tożsamość usługi Azure AD może być użytkownika, grupy lub nazwy głównej usługi aplikacji, lub może być [tożsamości usługi zarządzanej](../../active-directory/managed-service-identity/overview.md). Możesz przypisywać uprawnienia dostępu do zasobów magazynu tożsamości usługi Azure AD za pomocą kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzaj prawa dostępu do danych usługi Azure Storage za pomocą funkcji RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

Po zalogowaniu do wiersza polecenia platformy Azure lub programu PowerShell przy użyciu tożsamości usługi Azure AD, token dostępu jest zwracany do uzyskiwania dostępu do usługi Azure Storage w ramach tej tożsamości. Ten token jest następnie automatycznie używany przez interfejs wiersza polecenia lub programu PowerShell można autoryzować operacji dotyczących usługi Azure Storage. Dla obsługiwanych operacji nie trzeba przekazać klucz konta lub token sygnatury dostępu Współdzielonego za pomocą polecenia.

> [!IMPORTANT]
> Tej wersji zapoznawczej jest przeznaczony tylko do użytku nieprodukcyjnych. Produkcyjne usługi poziomu usług (SLA) nie będą dostępne, dopóki integracji z usługą Azure AD dla usługi Azure Storage jest zadeklarowany jest ogólnie dostępna. Jeśli integracji z usługą Azure AD nie jest jeszcze obsługiwana dla danego scenariusza, należy nadal używać klucza wspólnego autoryzacji lub tokenów SAS w swoich aplikacjach. Aby uzyskać dodatkowe informacje na temat korzystania z wersji zapoznawczej, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
>
> W trakcie okresu zapoznawczego przypisania ról RBAC może potrwać do pięciu minut na propagację.
>
> Integracja usługi Azure AD z usługą Azure Storage wymaga używania protokołu HTTPS dla operacji usługi Azure Storage.

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

Aby zalogować się przy użyciu tożsamości usługi Azure AD za pomocą programu Azure PowerShell:

1. Upewnij się, że masz najnowszą wersję zainstalowanego modułu PowerShellGet. Uruchom następujące polecenie, aby zainstalować najnowszy:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Odinstaluj wszystkie poprzednie instalacje programu Azure PowerShell.
3. Zainstaluj usługi AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Instalowanie modułu (wersja zapoznawcza):

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. Wywołaj [poleceniem New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) polecenia cmdlet, aby utworzyć kontekst i obejmują `-UseConnectedAccount` parametru. 
6. Aby wywoływać polecenia cmdlet przy użyciu tożsamości usługi Azure AD, należy przekazać kontekst do polecenia cmdlet.

Poniższy przykład przedstawia listę obiektów blob w kontenerze za pomocą programu Azure PowerShell za pomocą tożsamości usługi Azure AD: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat ról RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).
- Aby zapoznać się z usługą Azure Storage przy użyciu tożsamości usługi zarządzanej, zobacz [uwierzytelnianie w usłudze Azure AD z usługi Azure tożsamości usługi zarządzanej (wersja zapoznawcza)](storage-auth-aad-msi.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z magazynu aplikacji](storage-auth-aad-app.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob platformy Azure, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
