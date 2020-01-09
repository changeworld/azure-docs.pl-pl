---
title: Uruchamianie poleceń interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki
titleSuffix: Azure Storage
description: Interfejs wiersza polecenia platformy Azure obsługuje logowanie przy użyciu poświadczeń usługi Azure AD w celu uruchamiania poleceń w usłudze Azure Storage BLOB i kolejkach danych. Token dostępu jest dostarczany dla sesji i używany do autoryzacji operacji wywoływania. Uprawnienia są zależne od roli RBAC przypisanej do podmiotu zabezpieczeń usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfbb466b7679e841c9c0154dbe28de14574d4282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553464"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Uruchamianie poleceń interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki

Usługa Azure Storage udostępnia rozszerzenia interfejsu wiersza polecenia platformy Azure, które umożliwiają logowanie i uruchamianie poleceń skryptów przy użyciu poświadczeń Azure Active Directory (Azure AD). Po zalogowaniu się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD zwracany jest token dostępu OAuth 2,0. Ten token jest automatycznie używany przez interfejs wiersza polecenia platformy Azure do autoryzowania kolejnych operacji na danych w odniesieniu do magazynu obiektów blob lub Queue. W przypadku obsługiwanych operacji nie jest już konieczne przekazywanie klucza konta ani tokenu SAS przy użyciu polecenia.

Można przypisać uprawnienia do obiektów blob i kolejek danych do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Obsługiwane operacje

Rozszerzenia usługi Azure Storage są obsługiwane w przypadku operacji na danych obiektów blob i kolejek. Operacje, które można wywołać, zależą od uprawnień udzielonych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do interfejsu wiersza polecenia platformy Azure. Uprawnienia do kontenerów lub kolejek usługi Azure Storage są przypisywane za pośrednictwem RBAC. Na przykład, jeśli przypisano rolę **czytnika danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują dane z kontenera lub kolejki. Jeśli przypisano rolę **współautor danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub kolejkę lub zawarte w nich dane.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze lub kolejce, zobacz [wywoływanie operacji magazynu przy użyciu tokenów OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Wywoływanie poleceń interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD

Interfejs wiersza polecenia platformy Azure obsługuje parametr `--auth-mode` dla operacji na danych obiektów blob i kolejek:

- Ustaw parametr `--auth-mode` na `login`, aby zalogować się przy użyciu podmiotu zabezpieczeń usługi Azure AD.
- Ustaw parametr `--auth-mode` na starszą wartość `key`, aby podjąć próbę zbadania klucza konta, jeśli nie podano parametrów uwierzytelniania dla konta.

Poniższy przykład pokazuje, jak utworzyć kontener na nowym koncie magazynu z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

1. Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom `az --version`, aby sprawdzić zainstalowaną wersję.

1. Uruchom `az login` i Uwierzytelnij w oknie przeglądarki:

    ```azurecli
    az login
    ```

1. Określ żądaną subskrypcję. Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Utwórz konto magazynu w ramach tej grupy zasobów przy użyciu polecenia [AZ Storage account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Przed utworzeniem kontenera Przypisz do siebie rolę [współautor danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [przyznawanie dostępu do obiektów blob platformy Azure i danych z kolejki RBAC w Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Propagowanie przypisań ról RBAC może potrwać kilka minut.

1. Wywołaj polecenie [AZ Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) z parametrem `--auth-mode` ustawionym na `login`, aby utworzyć kontener przy użyciu poświadczeń usługi Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

Zmienna środowiskowa skojarzona z parametrem `--auth-mode` jest `AZURE_STORAGE_AUTH_MODE`. Możesz określić odpowiednią wartość w zmiennej środowiskowej, aby uniknąć uwzględniania jej przy każdym wywołaniu operacji na danych usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

- [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli RBAC na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-cli.md)
- [Autoryzuj dostęp do danych obiektów blob i kolejek z tożsamościami zarządzanymi dla zasobów platformy Azure](storage-auth-aad-msi.md)
