---
title: Uruchom polecenia wiersza polecenia platformy Azure lub programu PowerShell z tożsamością usługi Azure AD, aby uzyskać dostępu do usługi Azure Storage (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Azure CLI i programu PowerShell obsługuje logowanie przy użyciu tożsamości usługi Azure AD do uruchamiania poleceń na kontenery magazynu Azure, kolejek i ich dane. Token dostępu ma być dostarczana w sesji i używane do wywoływania operacji autoryzacji. Uprawnienia są uzależnione od roli przypisanej do tożsamości usługi Azure AD.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 27397d490902625d10e54ea17a9e534fbfbca8cd
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757506"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Użyj tożsamości usługi Azure AD dostęp do magazynu Azure z interfejsu wiersza polecenia lub środowiska PowerShell (wersja zapoznawcza)

Usługa Azure Storage udostępnia rozszerzenia podglądu dla wiersza polecenia platformy Azure i programu PowerShell, które umożliwiają zalogować i uruchomić polecenia skryptu z tożsamością usługi Azure Active Directory (Azure AD). Tożsamość usługi Azure AD może być użytkownika, grupy lub nazwy głównej usługi aplikacji lub może być [tożsamość usługi zarządzanej](../../active-directory/managed-service-identity/overview.md). Można przypisać uprawnienia dostępu do zasobów magazynu tożsamości usługi Azure AD za pomocą kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji o rolach RBAC w usłudze Azure Storage, zobacz [Zarządzaj praw dostępu do danych usługi Azure Storage za pomocą RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

Po zalogowaniu się do wiersza polecenia platformy Azure lub programu PowerShell przy użyciu tożsamości usługi Azure AD, token dostępu jest zwracana do uzyskiwania dostępu do magazynu Azure w ramach tej tożsamości. Token jest następnie automatycznie używany przez interfejs wiersza polecenia lub programu PowerShell do autoryzacji operacji w usłudze Azure Storage. Dla obsługiwanych operacji nie trzeba przekazać klucz konta lub tokenu sygnatury dostępu Współdzielonego przy użyciu polecenia.

> [!IMPORTANT]
> Ta wersja zapoznawcza jest przeznaczony tylko do użytku z systemem innym niż produkcji. Produkcyjnej umowy poziomu usług (SLA) nie będą dostępne, dopóki integracji z usługą Azure AD dla usługi Azure Storage jest zadeklarowany jako ogólnie dostępna. Jeśli dla danego scenariusza integracji z usługą Azure AD nie jest jeszcze obsługiwana, nadal używać klucza wspólnego autoryzacji lub tokeny sygnatury dostępu Współdzielonego w aplikacji. Aby uzyskać dodatkowe informacje o wersji zapoznawczej, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
>
> W wersji zapoznawczej przypisań ról RBAC może potrwać maksymalnie pięć minut propagacji.
>
> Integracja usługi Azure AD z usługą Azure Storage wymaga używania protokołu HTTPS dla operacji usługi Azure Storage.

## <a name="supported-operations"></a>Obsługiwane operacje

Rozszerzenia podglądu są obsługiwane dla operacji na kontenery i kolejek. Operacje, które może wywołać zależy od uprawnień tożsamości usługi Azure AD, z którym logowania do wiersza polecenia platformy Azure lub programu PowerShell. Uprawnienia do kontenerów usługi Azure Storage lub kolejek są przypisywane przy użyciu kontroli dostępu opartej na rolach (RBAC). Na przykład jeśli rola czytnik danych jest przypisany do tożsamości, następnie polecenia można wykonać skryptu odczytujących dane z kontenera lub kolejki. Jeśli przypisano rolę współautora danych tożsamości, można uruchomić poleceń skryptu, które odczytu, zapisu lub usuwania kontenera lub kolejki lub danych, które zawierają. 

Aby uzyskać więcej informacji dotyczących uprawnień wymaganych dla każdej operacji usługi Azure Storage w kontenerze lub kolejki, zobacz [uprawnienia do wywoływania operacji REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Wywołanie polecenia interfejsu wiersza polecenia przy użyciu tożsamości usługi Azure AD

Aby zainstalować rozszerzenie wersji zapoznawczej dla wiersza polecenia platformy Azure:

1. Upewnij się, że zainstalowano wersję interfejsu wiersza polecenia Azure 2.0.32 lub nowszy. Uruchom `az --version` do sprawdzenia zainstalowanej wersji.
2. Uruchom następujące polecenie, aby zainstalować rozszerzenie wersji zapoznawczej: 

    ```azurecli
    az extension add -n storage-preview
    ```

Rozszerzenie podglądu dodaje nową `--auth-mode` parametr obsługiwane polecenia:

- Ustaw `--auth-mode` parametr `login` zalogowanie się przy użyciu tożsamości usługi Azure AD.
- Ustaw `--auth-mode` parametr starszego `key` podano wartość, aby próba wykonania zapytania do klucza konta, gdy nie parametry uwierzytelniania dla konta. 

Na przykład, aby pobrać obiektu blob w wiersza polecenia platformy Azure przy użyciu tożsamości usługi Azure AD, najpierw uruchom `az login`, następnie wywołać polecenie z `--auth-mode` ustawioną `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

Zmienna środowiskowa skojarzone z `--auth-mode` parametr jest `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Wywołania poleceń programu PowerShell przy użyciu tożsamości usługi Azure AD

Aby zalogować się przy użyciu tożsamości usługi Azure AD przy użyciu programu Azure PowerShell:

1. Upewnij się, że masz najnowszą wersję PowerShellGet zainstalowane. Uruchom następujące polecenie, aby zainstalować najnowsze:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Odinstaluj wszelkie poprzednie instalacje programu Azure PowerShell.
3. Zainstaluj AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Zainstaluj moduł podglądu:

    ```powershell
    Install-Module-Name Azure.Storage-RequiredVersion 4.4.0-AllowPrerelease –AllowClobber -Repository PSGallery -Force 
    ```

5. Wywołanie [AzureStorageContext nowy](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) polecenia cmdlet, aby utworzyć kontekstu i uwzględnić `-UseConnectedAccount` parametru. 
6. Wywoływanie polecenia cmdlet przy użyciu tożsamości usługi Azure AD, należy przekazać do polecenia cmdlet kontekstu.

Poniższy przykład pokazuje, jak wyświetlić listę obiektów blob w kontenerze z programu Azure PowerShell przy użyciu tożsamości usługi Azure AD: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o rolach RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych z RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).
- Aby zapoznać się z usługą Azure Storage za pomocą zarządzanych tożsamość usługi, zobacz [uwierzytelniony przez usługi Azure AD z tożsamości usługi zarządzane Azure (wersja zapoznawcza)](storage-auth-aad-msi.md).
- Aby uzyskać informacje o autoryzacji dostępu do kontenerów i kolejki ze w aplikacjach magazynu, zobacz [użycia usługi Azure AD z aplikacjami magazynu](storage-auth-aad-app.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob Azure, zobacz w usłudze Azure Storage team na blogu, [Announcing uwierzytelniania Podgląd Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
