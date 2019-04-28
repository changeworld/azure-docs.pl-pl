---
title: Za pomocą klasycznego wiersza polecenia platformy Azure z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą platformy Azure klasyczny interfejs wiersza polecenia (CLI) usługi Azure Storage do tworzenia i zarządzania kontami magazynu i pracy z plikami i obiekty BLOB platformy Azure.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 211051254e08d69c06afd4242599c909048e7e17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483749"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Za pomocą klasycznego wiersza polecenia platformy Azure z usługą Azure Storage

## <a name="overview"></a>Omówienie

Klasyczny interfejs wiersza polecenia Azure udostępnia zestaw typu "open source", międzyplatformowych poleceń do pracy z platformą Azure. Zapewnia wiele funkcji w [witryny Azure portal](https://portal.azure.com) również jako zaawansowanych danych dostęp do funkcji.

W tym przewodniku pokażemy, jak używać [klasycznego wiersza polecenia platformy Azure](../../cli-install-nodejs.md) wykonywać różne zadania programowania i administracji wraz z usługą Azure Storage. Firma Microsoft zaleca, aby pobrać i zainstalować lub uaktualnić do najnowszej klasyczny interfejs wiersza polecenia przed rozpoczęciem korzystania z tego przewodnika.

W tym przewodniku założono, że rozumiesz podstawowe pojęcia dotyczące usługi Azure Storage. Przewodnik zawiera szereg skryptów, aby zademonstrować użycie klasyczny interfejs wiersza polecenia za pomocą usługi Azure Storage. Pamiętaj zaktualizować zmienne skryptu na podstawie konfiguracji przed uruchomieniem każdego skryptu.

> [!NOTE]
> Przewodnik przykłady Azure klasycznego interfejsu wiersza polecenia polecenia i skryptu klasycznych kont magazynu. Zobacz [przy użyciu wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows za pomocą usługi Azure Resource Management](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) dla platformy Azure classic interfejsu wiersza polecenia dla kont magazynu usługi Resource Manager.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Rozpoczynanie pracy z usługą Azure Storage i Azure klasyczny interfejs wiersza polecenia w ciągu 5 minut
W tym przewodniku używane Ubuntu przykłady, ale innych platform systemów operacyjnych należy wykonać w podobny sposób.

**Jesteś nowym użytkownikiem platformy Azure:** Skorzystaj z subskrypcji Microsoft Azure i konta Microsoft skojarzonego z posiadaną subskrypcją. Aby uzyskać informacje na temat opcji zakupu platformy Azure, zobacz [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/), [opcjami zakupu](https://azure.microsoft.com/pricing/purchase-options/), i [oferty dla subskrybentów](https://azure.microsoft.com/pricing/member-offers/) (dla uczestników programu MSDN, Microsoft Partner Network czy BizSpark, i inne programy firmy Microsoft).

Zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) Aby uzyskać więcej informacji na temat subskrypcji platformy Azure.

**Po utworzeniu subskrypcji Microsoft Azure i konto:**

1. Pobierz i zainstaluj klasycznego wiersza polecenia platformy Azure zgodnie z instrukcjami, opisane w temacie [zainstalować Azure klasyczny interfejs wiersza polecenia](../../cli-install-nodejs.md).
2. Po zainstalowaniu klasyczny interfejs wiersza polecenia można uzyskać dostęp do klasycznego poleceń interfejsu wiersza polecenia za pomocą polecenia platformy azure, z poziomu interfejsu wiersza polecenia (powłoki Bash, Terminal, wiersza polecenia). Typ _azure_ polecenia i powinien zostać wyświetlony następujące dane wyjściowe.

    ![Dane wyjściowe polecenia platformy Azure](./media/storage-azure-cli/azure_command.png)   
3. W interfejsie wiersza polecenia, wpisz `azure storage` Aby wyświetlić listę wszystkich poleceń usługi azure storage i pierwsze wrażenie funkcji klasyczny interfejs wiersza polecenia zawiera. Możesz wpisać nazwę polecenia za pomocą **-h** parametrów (na przykład `azure storage share create -h`) aby wyświetlić szczegółowe informacje o składni polecenia.
4. Teraz przedstawimy prosty skrypt, który przedstawia podstawowe klasycznego interfejsu wiersza polecenia dostępu do magazynu Azure. Skrypt najpierw zapyta, aby ustawić dwie zmienne dla konta magazynu i klucza. Następnie skrypt tworzeniu nowego kontenera, w tym nowe konto magazynu i przekazać istniejący pliku obrazu (blob) do tego kontenera. Po skrypt zawiera listę wszystkich obiektów blob w kontenerze, pobierze plik obrazu do katalogu docelowego, która istnieje na komputerze lokalnym.

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

5. Na komputerze lokalnym otwórz preferowanym edytorze tekstu (vim, na przykład). Wpisz powyższy skrypt do edytora tekstu.
6. Teraz musisz zaktualizować zmienne skryptu na podstawie własnych ustawień konfiguracji.

   * **< Nazwa_konta_magazynu >** użyć nazwy podanej w skrypcie lub wprowadź nazwę nowego konta magazynu. **Ważne:** Nazwa konta magazynu musi być unikatowa na platformie Azure. Musi być pisana małymi literami i zbyt!
   * **< Klucz_konta_magazynu >** klucz dostępu konta magazynu.
   * **< Container_name >** użyć nazwy podanej w skrypcie lub wprowadź nową nazwę kontenera.
   * **< Image_to_upload >** wprowadź ścieżkę do obrazu na komputerze lokalnym, takich jak: "~ / images/HelloWorld.png".
   * **< Destination_folder >** wprowadź ścieżkę do katalogu lokalnego do przechowywania plików pobranych z usługi Azure Storage, takie jak: "~/downloadImages".
7. Po zaktualizowaniu niezbędne zmiennych w vim, naciśnij klawisz kombinacje klawiszy `ESC`, `:`, `wq!` można zapisać skryptu.
8. Aby uruchomić ten skrypt, należy po prostu wpisz nazwę pliku skryptu, w konsoli programu bash. Po uruchomieniu tego skryptu, powinny mieć folderu lokalne miejsce docelowe, który zawiera plik pobrany obraz. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe:

Po uruchomieniu skryptu, powinny mieć folderu lokalne miejsce docelowe, który zawiera plik pobrany obraz.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Zarządzanie kontami magazynu przy użyciu usługi Azure klasyczny interfejs wiersza polecenia
### <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure
Chociaż większość poleceń magazynu będzie działać bez subskrypcji platformy Azure, firma Microsoft zaleca nawiązywanie połączenia z subskrypcją w klasyczny interfejs wiersza polecenia.

### <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu
Aby użyć usługi Azure storage, konieczne będzie konto magazynu. Można utworzyć nowe konto usługi Azure storage, po skonfigurowaniu komputera, aby nawiązać połączenie z subskrypcją.

```azurecli
azure storage account create <account_name>
```

Nazwa konta magazynu musi mieć długość od 3 do 24 znaków długości i używać wyłącznie cyfry i małe litery.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Ustaw domyślne konto usługi Azure storage w zmiennych środowiskowych
Może mieć wielu kont magazynu w ramach subskrypcji. Można wybrać jedną z nich i ustaw go w zmiennych środowiskowych dla wszystkich poleceń magazynu w tej samej sesji. Dzięki temu można uruchomić polecenia do magazynu klasycznego interfejsu wiersza polecenia bez określania konta magazynu i klucza jawnie.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Innym sposobem, aby ustawić domyślne konto magazynu jest przy użyciu parametrów połączenia. Po pierwsze uzyskać parametry połączenia za pomocą polecenia:

```azurecli
azure storage account connectionstring show <account_name>
```

Następnie skopiuj parametry połączenia danych wyjściowych i ustaw zmienną środowiskową:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Tworzenie i zarządzanie obiektami blob
Usługa Azure Blob storage jest usługą służącą do przechowywania dużych ilości danych niestrukturalnych, takich jak dane tekstowe lub binarne, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. W tej sekcji założono, że znasz już pojęcia magazynu obiektów Blob platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md) i [pojęcia dotyczące usługi Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt blob w usłudze Azure storage musi być w kontenerze. Można utworzyć kontenera prywatnej przy użyciu `azure storage container create` polecenia:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Istnieją trzy poziomy anonimowy dostęp do odczytu: **Wyłącz**, **Blob**, i **kontenera**. Aby uniemożliwić dostęp anonimowy do obiektów blob, należy ustawić parametr uprawnień na **poza**. Domyślnie nowy kontener jest prywatny i są dostępne tylko dla właściciela konta. Aby zezwolić na anonimowy publicznego dostępu do odczytu do zasobów obiektów blob, ale nie metadanych kontenera lub listy obiektów blob w kontenerze, ustawić uprawnienia dla parametru **Blob**. Aby zezwolić na pełne publicznego dostępu do odczytu do obiektów blob, zasobów, metadanych kontenera i listy obiektów blob w kontenerze, należy ustawić parametr uprawnień na **kontenera**. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera
Azure Blob Storage obsługuje blokowe i stronicowe obiekty blob. Aby uzyskać więcej informacji, zobacz [omówienie blokowych obiektów blob, Uzupełnialnych obiektów blob i stronicowe obiekty BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Aby przekazać obiekty BLOB w kontenerze, można użyć `azure storage blob upload`. Domyślnie to polecenie przekazuje pliki lokalne do blokowego obiektu blob. Aby określić typ obiektu blob, można użyć `--blobtype` parametru.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Pobierz obiekty BLOB z kontenera
Poniższy przykład pokazuje, jak pobierać obiekty BLOB z kontenera.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopiowanie obiektów blob
Można asynchronicznie kopiować obiekty blob w obrębie konta magazynu i regionu lub między różnymi kontami magazynu i regionami.

W poniższym przykładzie pokazano sposób kopiowania obiektów blob z jednego konta magazynu do innego. W tym przykładzie utworzymy kontener, w których obiekty BLOB są publicznie, dostępne anonimowo.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

W tym przykładzie tworzona kopia asynchronicznego. Możesz monitorować stan każdej operacji kopiowania, uruchamiając `azure storage blob copy show` operacji.

Należy zauważyć, że podany adres URL źródła dla operacji kopiowania musi być dostępny publicznie lub zawierać token sygnatury dostępu Współdzielonego (sygnatura dostępu współdzielonego).

### <a name="delete-a-blob"></a>Usuwanie obiektu blob
Aby usunąć obiekt blob, użyj poniższego polecenia:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Tworzenie i Zarządzanie udziałami plików
Usługa Azure Files oferuje współużytkowany magazyn dla aplikacji używających standardowego protokołu SMB. Maszyny wirtualne Microsoft Azure i usług w chmurze, jak również aplikacje lokalne mogą współużytkować dane plików, za pośrednictwem zainstalowanych udziałów. Możesz zarządzać udziałami plików i danych plików za pośrednictwem klasyczny interfejs wiersza polecenia. Aby uzyskać więcej informacji na temat usługi Azure Files, zobacz [wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Udział plików platformy Azure jest udziałem plików SMB na platformie Azure. Wszystkie pliki i katalogi, należy utworzyć w udziale plików. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików, nieprzekraczającą limitów pojemności konta magazynu. Poniższy przykład tworzy udział plików o nazwie **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Tworzenie katalogu
Katalogu zapewnia strukturę hierarchiczną opcjonalne dla udziału plików platformy Azure. Poniższy przykład tworzy katalog o nazwie **myDir** w udziale plików.

```azurecli
azure storage directory create myshare myDir
```

Uwaga tej ścieżki katalogu może zawierać wiele poziomów *np.*, **/ b**. Jednak należy upewnić się, że wszystkie katalogi nadrzędne istnieją. Na przykład dla ścieżki **a/b**, należy utworzyć katalog **a** najpierw utwórz katalog **b**.

### <a name="upload-a-local-file-to-directory"></a>Przekazanie pliku lokalnego do katalogu
Następujący kod przykładowy przekazuje plik z **~/temp/samplefile.txt** do **myDir** katalogu. Zmień ścieżkę pliku, które wskazuje prawidłowy plik na komputerze lokalnym:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Należy pamiętać, że plik w udziale można maksymalnie 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Lista plików w katalogu głównym udziału lub
Możesz wyświetlić listę plików i podkatalogów w katalogu głównego udziału lub katalogu, używając następującego polecenia:

```azurecli
azure storage file list myshare myDir
```

Należy pamiętać, że nazwa katalogu jest opcjonalna dla operacji listy. Jeśli argument jest pominięty, polecenie wyświetla zawartość katalogu głównego udziału.

### <a name="copy-files"></a>Kopiowanie plików
Począwszy od wersji 0.9.8 klasyczny interfejs wiersza polecenia, można skopiować pliku do innego pliku, pliki do obiektu blob oraz obiekty blob do pliku. Poniżej przedstawiamy sposób wykonywania tych operacji kopiowania za pomocą interfejsu wiersza polecenia. Aby skopiować plik do nowego katalogu:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Aby skopiować obiekt blob do katalogu plików:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Następne kroki

Dokumentacja poleceń interfejsu wiersza polecenia klasycznej platformy Azure można znaleźć do pracy z zasobami magazynu w tym miejscu:

* [Azure classic interfejsu wiersza polecenia w trybie usługi Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure classic interfejsu wiersza polecenia w trybie Azure Service Management](../../cli-install-nodejs.md)

Może również chcesz ponownie najnowszą wersję [wiersza polecenia platformy Azure](../storage-azure-cli.md), do użycia przy użyciu modelu wdrażania usługi Resource Manager.
