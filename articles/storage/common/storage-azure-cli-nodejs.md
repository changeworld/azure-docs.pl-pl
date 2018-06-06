---
title: Za pomocą usługi Azure CLI 1.0 z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure (Azure CLI) 1.0, z usługą Azure Storage, aby utworzyć i zarządzać kontami magazynu i pracy z plikami i obiekty BLOB platformy Azure. Azure CLI jest narzędziem do wielu platform
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: b502232a-e8f6-4d6c-befd-3476592e0e35
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.openlocfilehash: a8866385c682e86e889ddf171cc640fe0bb8252d
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737473"
---
# <a name="using-the-azure-cli-10-with-azure-storage"></a>Za pomocą usługi Azure CLI 1.0 z usługą Azure Storage

## <a name="overview"></a>Przegląd

Wiersza polecenia platformy Azure oferuje zestaw typu open source, obsługujący wiele platform polecenia dotyczące pracy z platformą Azure. Zapewnia wiele funkcji w [portalu Azure](https://portal.azure.com) danych oraz jak sformatowanego dostęp do funkcji.

W tym przewodniku, możemy Eksploruj sposób użycia [interfejsu wiersza polecenia platformy Azure (Azure CLI)](../../cli-install-nodejs.md) do wykonywania różnych zadań programowania i administracji z usługą Azure Storage. Zaleca się, że pobierania i instalacji lub uaktualnienia do najnowszej wiersza polecenia platformy Azure przed rozpoczęciem korzystania z tego przewodnika.

W tym przewodniku założono, że rozumiesz podstawowe pojęcia dotyczące środowiska usługi Azure Storage. Przewodnik udostępnia wiele skryptów do zaprezentowania użycia interfejsu wiersza polecenia Azure z usługą Azure Storage. Należy zaktualizować zmienne skryptu na podstawie konfiguracji przed uruchomieniem każdego skryptu.

> [!NOTE]
> Przewodnik przedstawiono przykłady poleceń i skryptów wiersza polecenia platformy Azure klasycznych kont magazynu. Zobacz [przy użyciu wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows z usługą Azure Resource Management](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) dla polecenia wiersza polecenia platformy Azure dla Menedżera zasobów konta magazynu.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Wprowadzenie do usługi Azure Storage i wiersza polecenia platformy Azure w ciągu 5 minut
W tym przewodniku używa Ubuntu przykłady, ale innych platform systemu operacyjnego należy wykonać w podobny sposób.

**Jesteś nowym użytkownikiem usługi Azure:** subskrypcji Microsoft Azure i konta Microsoft skojarzonego z jego subskrypcją. Aby uzyskać informacje na temat opcji zakupu platformy Azure, zobacz [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/), [opcjami zakupu](https://azure.microsoft.com/pricing/purchase-options/), i [oferty Członkowskie](https://azure.microsoft.com/pricing/member-offers/) (dla członków MSDN, Microsoft Partner Network, BizSpark i innych programów firmy Microsoft).

Zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) uzyskać więcej informacji o subskrypcji platformy Azure.

**Po utworzeniu subskrypcji Microsoft Azure i konto:**

1. Pobieranie i instalowanie interfejsu wiersza polecenia Azure, wykonując instrukcje podane w [instalowanie interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md).
2. Po zainstalowaniu interfejsu wiersza polecenia Azure, można uzyskać dostępu do poleceń interfejsu wiersza polecenia Azure za pomocą polecenia azure, z interfejsu wiersza polecenia (Bash, terminali, wiersza polecenia). Typ _azure_ polecenia i powinny być widoczne następujące dane wyjściowe.

    ![Dane wyjściowe polecenia platformy Azure](./media/storage-azure-cli/azure_command.png)   
3. W interfejsie wiersza polecenia, wpisz `azure storage` Aby wyświetlić listę wszystkich poleceń magazynu azure i uzyskać pierwszy wrażenie funkcji interfejsu wiersza polecenia Azure udostępnia. Możesz wpisać nazwę polecenia z **-h** parametru (na przykład `azure storage share create -h`) aby wyświetlić szczegóły składni polecenia.
4. Teraz przedstawimy prosty skrypt, który zawiera podstawowe polecenia interfejsu wiersza polecenia Azure dostęp do magazynu Azure. Skrypt najpierw zapyta, aby ustawić dwie zmienne dla konta magazynu i klucza. Następnie skrypt utworzyć nowy kontener w tym nowe konto magazynu i przekazać istniejący plik obrazu (blob) do tego kontenera. Po skrypt znajduje się lista wszystkich obiektów blob w tym kontenerze, pobierze plik obrazu do katalogu docelowego, która istnieje na komputerze lokalnym.

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

5. W komputerze lokalnym Otwórz w edytorze tekstów preferowanych (vim na przykład). Wpisz powyższy skrypt w edytorze tekstu.
6. Teraz musisz zaktualizować zmienne skryptu na podstawie własnych ustawień konfiguracji.

   * **< Storage_account_name >** użyć nazwy podanej w skrypcie lub wprowadź nową nazwę dla konta magazynu. **Ważne:** nazwę konta magazynu musi być unikatowa na platformie Azure. Musi być pisane małymi literami, zbyt!
   * **< Storage_account_key >** klucz dostępu konta magazynu.
   * **< Container_name >** użyć nazwy podanej w skrypcie lub wprowadź nową nazwę dla Twojego kontenera.
   * **< Image_to_upload >** wprowadź ścieżkę do obrazu na komputerze lokalnym, takich jak: "~ / images/HelloWorld.png".
   * **< Destination_folder >** wprowadź ścieżkę do katalogu lokalnego do przechowywania pliki pobierane z usługi Azure Storage, takich jak: "~/downloadImages".
7. Po zaktualizowaniu niezbędne zmienne vim, naciśnij klawisz kombinacje klawiszy `ESC`, `:`, `wq!` można zapisać skryptu.
8. Aby uruchomić ten skrypt, po prostu wpisz nazwę pliku skryptu w konsoli bash. Po uruchomieniu tego skryptu, ma lokalne miejsce docelowe folder, który zawiera plik pobrany obraz. Poniższy zrzut ekranu przedstawia przykład danych wyjściowych:

Po uruchomieniu skryptu, ma lokalne miejsce docelowe folder, który zawiera plik pobrany obraz.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Zarządzanie kontami magazynu z wiersza polecenia platformy Azure
### <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure
Większość poleceń magazyn będzie działać bez subskrypcji platformy Azure, ale zalecamy nawiązywanie połączenia z subskrypcją z wiersza polecenia platformy Azure. Aby skonfigurować wiersza polecenia platformy Azure do pracy z subskrypcją, postępuj zgodnie z instrukcjami [połączenie z subskrypcją platformy Azure z wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

### <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu
Aby korzystać z magazynu Azure, konieczne będzie konto magazynu. Po skonfigurowaniu komputera do nawiązania połączenia subskrypcji, można utworzyć nowe konto magazynu Azure.

```azurecli
azure storage account create <account_name>
```

Nazwa konta magazynu musi należeć do zakresu od 3 do 24 znaków i korzystać tylko cyfry i małe litery.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Ustaw domyślnego konta magazynu Azure w zmiennych środowiskowych
Może mieć wielu kont magazynu w ramach subskrypcji. Można wybrać jedną z nich i ustaw go w zmiennych środowiskowych dla wszystkich poleceń magazynu w tej samej sesji. Dzięki temu można uruchomić polecenia magazynu Azure CLI bez określania konta magazynu i klucza jawnie.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Innym sposobem ustawienia domyślne konto magazynu jest przy użyciu parametrów połączenia. Po pierwsze można pobrać parametry połączenia za pomocą polecenia:

```azurecli
azure storage account connectionstring show <account_name>
```

Następnie skopiuj parametry połączenia danych wyjściowych i wartości zmiennej środowiskowej:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Tworzenie i zarządzanie nimi obiektów blob
Magazyn obiektów Blob Azure to usługa do przechowywania dużych ilości danych bez struktury, takich jak dane tekstowe lub binarne, którego mogą uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. W tej sekcji założono, że znasz już pojęcia dotyczące magazynu obiektów Blob platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md) i [pojęcia dotyczące usługi Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt blob w magazynie Azure musi być w kontenerze. Można utworzyć kontenera prywatnej przy użyciu `azure storage container create` polecenia:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Istnieją trzy poziomy anonimowy dostęp do odczytu: **poza**, **obiektu Blob**, i **kontenera**. Aby uniemożliwić dostęp anonimowy do obiektów blob, ustaw dla parametru uprawnienia **poza**. Domyślnie nowy kontener jest prywatny i jest możliwy tylko przez właściciela konta. Umożliwia anonimowego publiczny dostęp do odczytu do zasobów obiektów blob, ale nie do metadanych kontenera lub listę obiektów blob w kontenerze, ustaw uprawnienia dla parametru **obiektu Blob**. Umożliwia pełną publiczny dostęp do odczytu do zasobów, metadanych kontenera i listę obiektów blob w kontenerze obiektu blob, ustaw dla parametru uprawnienia **kontenera**. Aby uzyskać więcej informacji, zobacz [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera
Azure Blob Storage obsługuje blokowe i stronicowe obiekty blob. Aby uzyskać więcej informacji, zobacz [opis blokowych obiektów blob, Dołącz obiektów blob i stronicowe obiekty BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Aby przekazać obiektów blob w kontenerze, można użyć `azure storage blob upload`. Domyślnie to polecenie operacji przekazywania plików lokalnych do blokowego obiektu blob. Aby określić typ obiektu blob, można użyć `--blobtype` parametru.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Pobierać obiekty BLOB z kontenera
W poniższym przykładzie pokazano, jak pobrać obiekty BLOB z kontenera.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopiowanie obiektów blob
Można asynchronicznie kopiować obiekty blob w obrębie konta magazynu i regionu lub między różnymi kontami magazynu i regionami.

W poniższym przykładzie pokazano sposób kopiowania obiektów blob z jednego konta magazynu do innego. W tym przykładzie tworzymy kontenera, w których obiekty BLOB są publicznie, anonimowo dostępny.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

W tym przykładzie wykonuje asynchroniczne kopiowania. Możesz monitorować stan każdej operacji kopiowania, uruchamiając `azure storage blob copy show` operacji.

Należy pamiętać, że podany adres URL źródła dla operacji kopiowania musi być dostępny publicznie albo obejmuje tokenu sygnatury dostępu Współdzielonego (sygnatury dostępu współdzielonego).

### <a name="delete-a-blob"></a>Usuwanie obiektu blob
Aby usunąć obiekt blob, użyj poniższych poleceń:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Tworzenie i Zarządzanie udziałami plików
Usługa pliki Azure oferuje współużytkowany magazyn dla aplikacji używających standardowego protokołu SMB. Maszyny wirtualne Microsoft Azure i usługi w chmurze, a także aplikacje lokalne mogą udostępniać dane za pośrednictwem zainstalowanych udziałów. Możesz zarządzać udziałami plików i danych plików za pomocą wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na pliki Azure, zobacz [wprowadzenie do usługi pliki Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Udział plików na platformę Azure jest udziałem plików SMB na platformie Azure. Wszystkie pliki i katalogi, należy utworzyć w udziale plików. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików do osiągnięcia limitu pojemności konta magazynu. Poniższy przykład tworzy udział plików o nazwie **moj_udzial**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Tworzenie katalogu
Katalog udostępnia opcjonalne strukturę hierarchiczną do udziału plików na platformę Azure. Poniższy przykład tworzy katalog o nazwie **myDir** w udziale plików.

```azurecli
azure storage directory create myshare myDir
```

Należy pamiętać, że ścieżki katalogu może zawierać wiele poziomów *np.*, **/ b**. Należy jednak upewnij się, że istnieją wszystkie katalogi nadrzędne. Na przykład dla ścieżki **a/b**, należy utworzyć katalog **a** najpierw utwórz katalog **b**.

### <a name="upload-a-local-file-to-directory"></a>Przekazywanie pliku lokalnego do katalogu
Poniższy przykład przekazuje plik z **~/temp/samplefile.txt** do **myDir** katalogu. Zmień ścieżkę pliku, tak aby wskazywał prawidłowy plik na komputerze lokalnym:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Należy zauważyć, że plik w udziale mogą być o rozmiarze do 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Lista plików w katalogu głównego udziału lub katalogu
Można wyświetlić listę plików i podkatalogów w katalogu głównego udziału lub w katalogu za pomocą następującego polecenia:

```azurecli
azure storage file list myshare myDir
```

Należy pamiętać, że nazwa katalogu jest opcjonalny w przypadku operację wyświetlania listy. Przypadku jego pominięcia polecenie wyświetla zawartość katalogu głównego udziału.

### <a name="copy-files"></a>Kopiowanie plików
Począwszy od wersji 0.9.8 wiersza polecenia platformy Azure, możesz skopiować plik do innego pliku, pliki do obiektu blob, obiekty blob do pliku. Poniżej przedstawiamy sposób wykonywania tych operacji kopiowania za pomocą polecenia interfejsu wiersza polecenia. Aby skopiować plik do nowego katalogu:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Aby skopiować obiektu blob do katalogu plików:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Następne kroki

Dokumentacja poleceń Azure 1.0 interfejsu wiersza polecenia można znaleźć do pracy z zasobami magazynu w tym miejscu:

* [Azure polecenia interfejsu wiersza polecenia w trybie Menedżera zasobów](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure polecenia interfejsu wiersza polecenia w trybie zarządzania usługami Azure](../../cli-install-nodejs.md)

Może również chcesz spróbować [Azure CLI 2.0](../storage-azure-cli.md), naszych CLI generacji napisane w języku Python, do użycia z modelu wdrażania usługi Resource Manager.
