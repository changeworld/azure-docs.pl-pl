---
title: Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure z usługą Azure Storage | Microsoft Docs
description: Dowiedz się, jak używać klasycznego interfejsu wiersza polecenia platformy Azure z usługą Azure Storage, aby tworzyć konta magazynu i zarządzać nimi oraz korzystać z obiektów blob i plików platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 6554385a879b054153dcb808c3dff4b60c136458
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120859"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure z usługą Azure Storage

## <a name="overview"></a>Omówienie

Klasyczny interfejs wiersza polecenia platformy Azure udostępnia zestaw wieloplatformowych poleceń typu "open source" do pracy z platformą Azure. Zapewnia to wiele funkcji dostępnych w [Azure Portal](https://portal.azure.com) , a także bogate funkcje dostępu do danych.

W tym przewodniku dowiesz się, jak używać [klasycznego interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md) do wykonywania różnych zadań deweloperskich i administracyjnych w usłudze Azure Storage. Zalecamy pobranie i zainstalowanie lub uaktualnienie do najnowszego klasycznego interfejsu wiersza polecenia przed rozpoczęciem korzystania z tego przewodnika.

W tym przewodniku założono, że rozumiesz podstawowe pojęcia związane z usługą Azure Storage. Przewodnik zawiera wiele skryptów demonstrujących użycie klasycznego interfejsu wiersza polecenia w usłudze Azure Storage. Przed uruchomieniem każdego skryptu należy zaktualizować zmienne skryptów na podstawie konfiguracji.

> [!NOTE]
> Przewodnik zawiera klasyczne polecenie interfejsu wiersza polecenia platformy Azure i przykłady skryptów dla klasycznych kont magazynu. Zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows za pomocą usługi Azure Resource Management](../../virtual-machines/azure-cli-arm-commands.md#storage-objects) dla klasycznych poleceń interfejsu CLI platformy azure dla Menedżer zasobów kont magazynu.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Rozpoczynanie pracy z usługą Azure Storage i klasycznym interfejsem wiersza polecenia platformy Azure w ciągu 5 minut
W tym przewodniku wykorzystano Ubuntu na przykład, ale inne platformy systemu operacyjnego powinny działać podobnie.

**Nowość na platformie Azure:** Pobierz subskrypcję Microsoft Azure i konto Microsoft skojarzoną z tą subskrypcją. Aby uzyskać informacje na temat opcji zakupu platformy Azure, zobacz [bezpłatny okres próbny](https://azure.microsoft.com/pricing/free-trial/), [Opcje zakupu](https://azure.microsoft.com/pricing/purchase-options/)i [oferty członków](https://azure.microsoft.com/pricing/member-offers/) (w przypadku elementów członkowskich MSDN, Microsoft Partner Network i BizSpark oraz innych programów firmy Microsoft).

Aby uzyskać więcej informacji na temat subskrypcji platformy Azure, zobacz [Przypisywanie ról administratorów w Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) .

**Po utworzeniu subskrypcji Microsoft Azure i konta:**

1. Pobierz i zainstaluj klasyczny interfejs wiersza polecenia platformy Azure, postępując zgodnie z instrukcjami podanymi w temacie [Instalowanie klasycznego interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md).
2. Po zainstalowaniu klasycznego interfejsu wiersza polecenia można użyć polecenia platformy Azure z poziomu interfejsu wiersza poleceń (bash, terminalu, wiersza polecenia), aby uzyskać dostęp do klasycznych poleceń interfejsu Wpisz polecenie _platformy Azure_ i zobacz następujące dane wyjściowe.

    ![Dane wyjściowe polecenia platformy Azure](./media/storage-azure-cli/azure_command.png)   
3. W interfejsie wiersza polecenia wpisz `azure storage`, aby wyświetlić listę wszystkich poleceń usługi Azure Storage i uzyskać pierwsze wrażenie funkcji dostępnych w klasycznym interfejsie CLI. Można wpisać nazwę polecenia z parametrem **-h** (na przykład `azure storage share create -h`), aby wyświetlić szczegóły składni polecenia.
4. Teraz udostępnimy prosty skrypt, który pokazuje podstawowe, klasyczne polecenia CLI, aby uzyskać dostęp do usługi Azure Storage. Skrypt najpierw poprosił Cię o ustawienie dwóch zmiennych dla konta magazynu i klucza. Następnie skrypt utworzy nowy kontener na nowym koncie magazynu i przekaże istniejący plik obrazu (BLOB) do tego kontenera. Po wyświetleniu przez skrypt wszystkich obiektów BLOB w tym kontenerze plik ten zostanie pobrany do katalogu docelowego, który istnieje na komputerze lokalnym.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. Na komputerze lokalnym Otwórz swój preferowany Edytor tekstu (na przykład vim). Wpisz powyżej skrypt w edytorze tekstu.
6. Teraz należy zaktualizować zmienne skryptów na podstawie ustawień konfiguracji.

   * **< storage_account_name >** Użyj podaną nazwę w skrypcie lub wprowadź nową nazwę konta magazynu. **Ważne:** Nazwa konta magazynu musi być unikatowa na platformie Azure. Musi być zbyt małe litery.
   * **< storage_account_key >** Klucz dostępu konta magazynu.
   * **< container_name >** Użyj podaną nazwę w skrypcie lub wprowadź nową nazwę dla swojego kontenera.
   * **< image_to_upload >** Wprowadź ścieżkę do obrazu na komputerze lokalnym, na przykład: "~/images/HelloWorld.png".
   * **< destination_folder >** Wprowadź ścieżkę do katalogu lokalnego, aby przechowywać pliki pobrane z usługi Azure Storage, np. "~/downloadImages".
7. Po zaktualizowaniu wymaganych zmiennych w usłudze vim naciśnij kombinację klawiszy `ESC`, `:``wq!`, aby zapisać skrypt.
8. Aby uruchomić ten skrypt, wystarczy wpisać nazwę pliku skryptu w konsoli bash. Po uruchomieniu tego skryptu należy mieć lokalny folder docelowy zawierający pobrany plik obrazu. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe:

Po uruchomieniu skryptu należy mieć lokalny folder docelowy zawierający pobrany plik obrazu.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Zarządzanie kontami magazynu przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
### <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure
Chociaż większość poleceń magazynu będzie działała bez subskrypcji platformy Azure, zalecamy nawiązanie połączenia z subskrypcją za pomocą klasycznego interfejsu wiersza polecenia.

### <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu
Do korzystania z usługi Azure Storage wymagane jest konto magazynu. Nowe konto usługi Azure Storage można utworzyć po skonfigurowaniu komputera do nawiązania połączenia z subskrypcją.

```azurecli
azure storage account create <account_name>
```

Nazwa konta magazynu musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Ustaw domyślne konto usługi Azure Storage w zmiennych środowiskowych
W ramach subskrypcji można mieć wiele kont magazynu. Można wybrać jedną z nich i ustawić ją w zmiennych środowiskowych dla wszystkich poleceń magazynu w ramach tej samej sesji. Dzięki temu można uruchamiać klasyczne polecenia magazynu CLI bez określania konta magazynu i klucza jawnie.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Innym sposobem ustawienia domyślnego konta magazynu jest użycie parametrów połączenia. Po pierwsze Pobierz parametry połączenia za pomocą polecenia:

```azurecli
azure storage account connectionstring show <account_name>
```

Następnie skopiuj ciąg połączenia danych wyjściowych i ustaw go na zmienną środowiskową:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Tworzenie obiektów blob i zarządzanie nimi
Azure Blob Storage to usługa służąca do przechowywania dużych ilości danych bez struktury, takich jak dane tekstowe lub binarne, do których można uzyskiwać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. W tej sekcji założono, że znasz już pojęcia związane z usługą Azure Blob Storage. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z usługą Azure Blob Storage za pomocą platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md) i [obiektów BLOB](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt BLOB w usłudze Azure Storage musi znajdować się w kontenerze. Kontener prywatny można utworzyć przy użyciu polecenia `azure storage container create`:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Istnieją trzy poziomy anonimowego dostępu do odczytu: **off**, **BLOB**i **Container**. Aby uniemożliwić anonimowy dostęp do obiektów blob, ustaw dla parametru uprawnienie wartość **off**. Domyślnie nowy kontener jest prywatny i dostęp do niego jest możliwy tylko przez właściciela konta. Aby zezwolić na anonimowy publiczny dostęp do odczytu do zasobów obiektów blob, ale nie do metadanych kontenera lub do listy obiektów BLOB w kontenerze, ustaw parametr uprawnienia na **obiekt BLOB**. Aby zezwolić na pełny publiczny dostęp do odczytu do zasobów obiektów blob, metadanych kontenera i listy obiektów BLOB w kontenerze, ustaw parametr uprawnień na **kontener**. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera
Azure Blob Storage obsługuje blokowe i stronicowe obiekty blob. Aby uzyskać więcej informacji, zobacz [Omówienie blokowych obiektów blob, dołączanie obiektów blob i stronicowych obiektów BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Aby przekazać obiekty blob do kontenera, można użyć `azure storage blob upload`. Domyślnie to polecenie przekazuje pliki lokalne do blokowego obiektu BLOB. Aby określić typ obiektu BLOB, można użyć parametru `--blobtype`.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Pobieranie obiektów blob z kontenera
Poniższy przykład ilustruje sposób pobierania obiektów blob z kontenera.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopiuj obiekty blob
Można asynchronicznie kopiować obiekty blob w obrębie konta magazynu i regionu lub między różnymi kontami magazynu i regionami.

W poniższym przykładzie pokazano sposób kopiowania obiektów blob z jednego konta magazynu do innego. W tym przykładzie utworzysz kontener, w którym obiekty blob są publicznie dostępne anonimowo.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Ten przykład wykonuje kopię asynchroniczną. Stan każdej operacji kopiowania można monitorować, uruchamiając `azure storage blob copy show` operację.

Należy pamiętać, że źródłowy adres URL podany dla operacji kopiowania musi być dostępny publicznie lub zawierać token SAS (Sygnatura dostępu współdzielonego).

### <a name="delete-a-blob"></a>Usuwanie obiektu blob
Aby usunąć obiekt BLOB, użyj poniższego polecenia:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Tworzenie udziałów plików i zarządzanie nimi
Azure Files oferuje udostępniony magazyn dla aplikacji przy użyciu standardowego protokołu SMB. Microsoft Azure maszyny wirtualne i usługi w chmurze, a także aplikacje lokalne, mogą udostępniać dane plików za pośrednictwem zainstalowanych udziałów. Udziałami plików i danymi plików można zarządzać za pomocą klasycznego interfejsu wiersza polecenia. Aby uzyskać więcej informacji na temat Azure Files, zobacz [wprowadzenie do Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Udział plików platformy Azure to udział plików SMB na platformie Azure. Wszystkie katalogi i pliki muszą zostać utworzone w udziale plików. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików — do limitów pojemności konta magazynu. Poniższy przykład tworzy udział plików o nazwie Moje **udostępnianie**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Tworzenie katalogu
Katalog zawiera opcjonalną strukturę hierarchiczną dla udziału plików platformy Azure. Poniższy przykład tworzy katalog o nazwie **myDir** w udziale plików.

```azurecli
azure storage directory create myshare myDir
```

Należy zauważyć, że ścieżka katalogu może zawierać wiele poziomów, *np.* , **a/b**. Należy jednak upewnić się, że wszystkie katalogi nadrzędne istnieją. Na przykład dla ścieżki **a/b**należy najpierw utworzyć katalog **a** , a następnie utworzyć katalog **b**.

### <a name="upload-a-local-file-to-directory"></a>Przekaż plik lokalny do katalogu
Poniższy przykład przekazuje plik z **~/temp/SampleFile.txt** do katalogu **myDir** . Edytuj ścieżkę pliku tak, aby wskazywała prawidłowy plik na komputerze lokalnym:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Należy pamiętać, że rozmiar pliku w udziale może wynosić do 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Wyświetl listę plików w folderze głównym lub katalogu udziału
Można wyświetlić listę plików i podkatalogów w katalogu głównym udziału lub katalogu przy użyciu następującego polecenia:

```azurecli
azure storage file list myshare myDir
```

Należy pamiętać, że nazwa katalogu jest opcjonalna dla operacji tworzenia listy. W przypadku pominięcia polecenie wyświetla zawartość katalogu głównego udziału.

### <a name="copy-files"></a>Kopiowanie plików
Począwszy od wersji 0.9.8 klasycznego interfejsu wiersza polecenia, można skopiować plik do innego pliku, pliku do obiektu BLOB lub obiektu BLOB do pliku. Poniżej przedstawiono sposób wykonywania tych operacji kopiowania przy użyciu poleceń interfejsu wiersza polecenia. Aby skopiować plik do nowego katalogu:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Aby skopiować obiekt BLOB do katalogu plików:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Następne kroki

Informacje o interfejsie wiersza polecenia klasycznej platformy Azure umożliwiające pracę z zasobami magazynu można znaleźć tutaj:

* [Klasyczne polecenie interfejsu wiersza polecenia platformy Azure w trybie Menedżer zasobów](../../virtual-machines/azure-cli-arm-commands.md#storage-objects)
* [Klasyczne polecenie interfejsu wiersza polecenia platformy Azure w trybie zarządzania usługami platformy Azure](../../cli-install-nodejs.md)

Możesz również wypróbować najnowszą wersję [interfejsu wiersza polecenia platformy Azure](../storage-azure-cli.md), która będzie używana z modelem wdrażania Menedżer zasobów.
