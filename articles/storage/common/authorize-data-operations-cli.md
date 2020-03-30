---
title: Autoryzuj dostęp do danych obiektów blob lub kolejek za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Określ sposób autoryzowania operacji danych względem danych obiektów blob lub kolejek za pomocą interfejsu wiersza polecenia platformy Azure. Można autoryzować operacje danych przy użyciu poświadczeń usługi Azure AD, za pomocą klucza dostępu do konta lub tokenu sygnatury dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207695"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Autoryzuj dostęp do danych obiektów blob lub kolejek za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Storage udostępnia rozszerzenia interfejsu wiersza polecenia platformy Azure, które umożliwiają określenie sposobu autoryzowania operacji na danych obiektów blob lub kolejek. Operacje danych można autoryzować w następujący sposób:

- Z podmiotem zabezpieczeń usługi Azure Active Directory (Azure AD). Firma Microsoft zaleca używanie poświadczeń usługi Azure AD w celu uzyskania najwyższego bezpieczeństwa i łatwości użytkowania.
- Z kluczem dostępu do konta lub tokenem sygnatury dostępu współdzielonego (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Określanie sposobu autoryzowania operacji danych

Polecenia interfejsu wiersza polecenia platformy Azure do odczytywania i zapisywania danych obiektów blob i kolejki zawierają parametr opcjonalny. `--auth-mode` Określ ten parametr, aby wskazać sposób autoryzacji operacji danych:

- Ustaw `--auth-mode` parametr, `login` aby zalogować się przy użyciu jednostki zabezpieczeń usługi Azure AD (zalecane).
- Ustaw `--auth-mode` parametr na `key` starszą wartość, aby spróbować pobrać klucz dostępu do konta do użycia do autoryzacji. Jeśli pominięto `--auth-mode` parametr, a następnie interfejsu wiersza polecenia platformy Azure również próbuje pobrać klucz dostępu.

Aby użyć `--auth-mode` tego parametru, upewnij się, że zainstalowano platformę Azure CLI w wersji 2.0.46 lub nowszej. Uruchom, `az --version` aby sprawdzić zainstalowaną wersję.

> [!IMPORTANT]
> Jeśli pominięto `--auth-mode` parametr lub ustaw `key`go na , a następnie interfejsu wiersza polecenia platformy Azure próbuje użyć klucza dostępu do konta do autoryzacji. W takim przypadku firma Microsoft zaleca podanie klucza dostępu w poleceniu lub zmiennej środowiskowej **AZURE_STORAGE_KEY.** Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz sekcję ["Ustawianie zmiennych środowiskowych dla parametrów autoryzacji"](#set-environment-variables-for-authorization-parameters)
>
> Jeśli nie podasz klucz dostępu, następnie interfejsu wiersza polecenia platformy Azure próbuje wywołać dostawcę zasobów usługi Azure Storage, aby pobrać go dla każdej operacji. Wykonywanie wielu operacji danych, które wymagają wywołania dostawcy zasobów może spowodować ograniczanie przepustowości. Aby uzyskać więcej informacji na temat limitów dostawców zasobów, zobacz [Cele skalowalności i wydajności dostawcy zasobów usługi Azure Storage](scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autoryzuj przy użyciu poświadczeń usługi Azure AD

Po zalogowaniu się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD zwracany jest token dostępu OAuth 2.0. Ten token jest automatycznie używany przez narzędzie Azure CLI do autoryzowania kolejnych operacji danych względem magazynu obiektów Blob lub Queue. W przypadku obsługiwanych operacji nie trzeba już przekazywać klucza konta lub tokenu Sygnatury dostępu Współdzielonego za pomocą polecenia.

Można przypisać uprawnienia do danych obiektów blob i kolejek do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą funkcji RBAC](storage-auth-aad-rbac.md).

### <a name="permissions-for-calling-data-operations"></a>Uprawnienia do wywoływania operacji danych

Rozszerzenia usługi Azure Storage są obsługiwane dla operacji na danych obiektów blob i kolejki. Operacje, które można wywołać, zależą od uprawnień przyznanych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do interfejsu wiersza polecenia platformy Azure. Uprawnienia do kontenerów lub kolejek usługi Azure Storage są przypisywane za pośrednictwem funkcji RBAC. Na przykład jeśli przypisano rolę **programu Blob Data Reader,** można uruchomić polecenia skryptów, które odczytują dane z kontenera lub kolejki. Jeśli przypisano rolę **współautora danych obiektów blob,** można uruchomić polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub kolejkę lub dane, które zawierają.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze lub kolejce, zobacz [Wywoływanie operacji magazynu za pomocą tokenów OAuth.](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Przykład: Autoryzowanie operacji w celu utworzenia kontenera z poświadczeniami usługi Azure AD

W poniższym przykładzie pokazano, jak utworzyć kontener z interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby utworzyć kontener, musisz zalogować się do interfejsu wiersza polecenia platformy Azure, a będziesz potrzebować grupy zasobów i konta magazynu. Aby dowiedzieć się, jak utworzyć te zasoby, zobacz [Szybki start: Tworzenie, pobieranie i wystawianie obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-quickstart-blobs-cli.md).

1. Przed utworzeniem kontenera należy przypisać do siebie rolę [współautora danych obiektów blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) magazynu. Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [Udzielanie dostępu do obiektów blob platformy Azure i danych kolejki za pomocą funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Propagowanie przydziałów ról RBAC może potrwać kilka minut.

1. Wywołanie [az storage container](/cli/azure/storage/container#az-storage-container-create) `--auth-mode` utworzyć polecenie `login` z parametrem ustawionym do tworzenia kontenera przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autoryzowanie przy przy tym kluczu dostępu do konta

Jeśli posiadasz klucz konta, możesz wywołać dowolną operację danych usługi Azure Storage. Ogólnie rzecz biorąc przy użyciu klucza konta jest mniej bezpieczne. Jeśli klucz konta zostanie naruszony, wszystkie dane na twoim koncie mogą zostać naruszone.

W poniższym przykładzie pokazano, jak utworzyć kontener przy użyciu klucza dostępu do konta. Określ klucz konta i `--auth-mode` podaj parametr z wartością: `key`

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autoryzowanie za pomocą tokenu sygnatury dostępu Współdzielonego

Jeśli posiadasz token sygnatury dostępu Współdzielonego, można wywołać operacje danych, które są dozwolone przez sygnatury dostępu Współdzielonego. W poniższym przykładzie pokazano, jak utworzyć kontener przy użyciu tokenu sygnatury dostępu Współdzielonego:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ustawianie zmiennych środowiskowych dla parametrów autoryzacji

Można określić parametry autoryzacji w zmiennych środowiskowych, aby uniknąć włączenia ich przy każdym wywołaniu operacji danych usługi Azure Storage. W poniższej tabeli opisano dostępne zmienne środowiskowe.

| Zmienna środowiskowa                  | Opis                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Nazwa konta magazynu. Ta zmienna powinna być używana w połączeniu z kluczem konta magazynu lub tokenem sygnatury dostępu Współdzielonego. Jeśli nie są obecne, interfejsu wiersza polecenia platformy Azure próbuje pobrać klucz dostępu do konta magazynu przy użyciu uwierzytelnionego konta usługi Azure AD. Jeśli duża liczba poleceń są wykonywane w tym samym czasie, może zostać osiągnięty limit ograniczania alokacji dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat limitów dostawców zasobów, zobacz [Cele skalowalności i wydajności dostawcy zasobów usługi Azure Storage](scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Klucz konta magazynu. Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Parametry połączenia, który zawiera klucz konta magazynu lub token sygnatury dostępu Współdzielonego. Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token sygnatury dostępu współdzielonego (SAS). Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Tryb autoryzacji, za pomocą którego ma być uruchamiane polecenie. Dozwolone wartości `login` są (zalecane) lub `key`. Jeśli określisz `login`, interfejsu wiersza polecenia platformy Azure używa poświadczeń usługi Azure AD do autoryzacji operacji danych. Jeśli określisz `key` tryb starszej wersji, interfejsu wiersza polecenia platformy Azure próbuje zbadać klucz dostępu do konta i autoryzować polecenie za pomocą klucza.    |

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli RBAC w celu uzyskania dostępu do danych obiektów blob i kolejek za pomocą interfejsu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [Autoryzowanie dostępu do danych obiektów blob i kolejek z zarządzanymi tożsamościami zasobów platformy Azure](storage-auth-aad-msi.md)
