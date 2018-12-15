---
title: Przechowywanie usługi Batch AI zadania wejściowe i wyjściowe przy użyciu usługi Azure Storage | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure Storage za pomocą usługi Batch AI dla magazynu w chmurze jest łatwe i szybkie plików wejściowych i wyjściowych
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1e9a4c6355c60b18bb78aae362c1e2f142e2d864
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408004"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Store dane wejściowe zadania usługi Batch AI i danych wyjściowych za pomocą usługi Azure Storage

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ten przewodnik opisuje sposób używania usługi Azure Storage do przechowywania plików wejściowych i wyjściowych, podczas uruchamiania zadania. Usługa Azure Storage jest jednym z kilku opcji magazynowania obsługiwane przez usługę Batch AI. Usługa Batch AI integruje się z usługą Azure Storage przez zainstalowanie systemy usługi Azure Storage do usługi Batch AI zadania lub klastra systemu plików, dzięki czemu bezproblemowy dostęp do plików przechowywanych w chmurze. 

## <a name="introduction-to-azure-storage"></a>Wprowadzenie do usługi Azure Storage

Usługa Azure Storage to rozwiązanie do magazynowania w chmurze firmy Microsoft. Usługa Batch AI obsługuje instalowanie kontenerów obiektów Blob platformy Azure i udziałów plików platformy Azure do zadania usługi Batch AI lub klastrów, co pliki były dostępne z zadania tak, jakby były natywne systemu plików. Usługa Batch AI instaluje kontenerów obiektów Blob platformy Azure przy użyciu [blobfuse](https://github.com/Azure/azure-storage-fuse)i udziałów za pośrednictwem protokołu SMB plików platformy Azure. Aby uzyskać więcej informacji na temat usługi Azure Storage, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Store zestawy danych i wprowadzania skryptów w usłudze Azure Storage

W przypadku wybrania usługi Azure Storage dla środowiska usługi Batch AI, zaleca się przechowywanie plików wejściowych (takich jak zestawy danych) w kontenerze obiektów Blob, który ma wyższą przepływność, i szkolenia dane wyjściowe są przechowywane w udziale plików, który obsługuje przesyłania strumieniowego (umożliwiając Odczytywanie danych wyjściowych dzienników po uruchomieniu zadania jednocześnie). 

Zanim użyjesz usługi Azure Storage, należy najpierw [Tworzenie konta usługi Azure Storage](../storage/common/storage-quickstart-create-account.md). Usługa Batch AI obsługuje instalowanie woluminów z obu ogólnego przeznaczenia w wersji 1 (GPv1) i ogólnego przeznaczenia w wersji 2 (GPv2) usługi Azure Storage kont. Konto usługi Azure Storage można przechowywać wiele kontenerów obiektów Blob lub wystąpienia udziału plików. Wybierając typ konta magazynu do utworzenia, weź pod uwagę wymagania dotyczące kosztów i wydajności. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md). 

Aby utworzyć kontener obiektów Blob i Przekaż swój zestaw danych do kontenera obiektów Blob platformy Azure, wybierz jedną z następujących metod:
- [Witryna Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) przekazywania z graficznym interfejsem użytkownika sieci web. Aby przekazać małą liczbę plików, witryna Azure portal udostępnia najprostszy operacji.
- [Interfejs wiersza polecenia Azure Storage](../storage/blobs/storage-quickstart-blobs-cli.md) przekazywania za pomocą wiersza polecenia (obsługuje przekazywanie katalogu). Aby przekazać katalogi plików, należy użyć `az storage blob upload-batch`.
- [innych technik](../storage/common/storage-moving-data.md), w tym za pomocą zestawów SDK aplikacji.

Podobnie Aby utworzyć udział plików platformy Azure, wybierz jedną z następujących metod:
- [Azure Portal](../storage/files/storage-how-to-use-files-portal.md)
- [Interfejs wiersza polecenia usługi Azure Storage](../storage/files/storage-how-to-use-files-cli.md)
- [inne techniki](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Auto-storage przy użyciu usługi Batch AI

Alternatywnie można utworzyć konta usługi Azure Storage za pomocą udziału plików platformy Azure i kontener obiektów Blob (i automatycznie instalować te woluminy w klastrze usługi Batch AI) przy użyciu `--use-auto-storage` parametrem `az batchai cluster create`. Aby uzyskać więcej informacji, zobacz [tutaj](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Instalowanie usługi Azure Storage 

### <a name="mount-volumes-to-a-job"></a>Woluminy instalacji do zadania

Instalowanie wolumin magazynu platformy Azure umożliwia mu są dostępne za pośrednictwem systemu plików utworzonych dla każdego zadania. W związku z tym zadanie może odczytywać i zapisywać pliki, które bezproblemowo do magazynu w chmurze tak, jakby były one plików lokalnych.

Aby zainstalować wolumin usługi Azure Storage, aby zadania utworzone przy użyciu wiersza polecenia platformy Azure, należy użyć `mountVolumes` właściwości w Twojej `job.json` pliku podczas uruchamiania `az batchai job create`. Aby uzyskać przykład, zobacz [przepisu rozproszonych GPU Tensorflow](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json). Schemat dla `mountVolumes` jest:
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` i `azureBlobFileSystems` obydwiema tablicami obiekty reprezentujące woluminy do zainstalowania. Opisy symbole zastępcze:

- < RELATIVE_MOUNT_PATH > - wolumin zostanie zainstalowany na tej ścieżce. Na przykład, jeśli `relativeMountPath` jest `jobs`, wolumin zostanie umieszczona w `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`)
- < nazwa_konta_magazynu > - Nazwa konta usługi Azure Storage, które zawiera udział plików lub kontenera obiektów Blob
- < FILE_SHARE_NAME > - nazwę udziału plików
- < BLOB_CONTAINER_NAME > - Nazwa kontenera obiektów Blob

Aby zainstalować woluminów magazynu platformy Azure za pomocą zestawów SDK usługi Azure Batch AI, należy ustawić `mount_volumes` (Python) lub `MountVolumes` (C# i Java) właściwość `JobCreateParameters`. W przypadku instalowania woluminów przy użyciu zestawów SDK usługi Azure Batch AI, musisz podać poświadczenia konta magazynu. Przeglądać schematy montażu woluminy [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet), i [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>Woluminy instalacji klastra

Usługa Batch AI obsługuje także instalowanie woluminów magazynu platformy Azure w klastrze usługi Batch AI. Gdy wolumin jest instalowany w klastrze, wszelkie zadania uruchomione w tym klastrze może użyć woluminów zainstalowanych na tym klastrze. Instalowanie poziomu zadania zapewnia największą elastyczność (umożliwiając, każde zadanie ma inną zainstalowanych woluminów), jednocześnie w prostych sytuacjach może wystarczyć instalowanie poziomu klastra.

Aby zainstalować wolumin magazynu Azure do klastra utworzone przy użyciu wiersza polecenia platformy Azure, należy użyć `mountVolumes` właściwości w swojej `cluster.json` pliku podczas uruchamiania `az batchai cluster create`. Schemat dla `mountVolumes` podczas instalowania klastra jest taka sama jak w przypadku instalowania do zadania. 

Podobnie, można użyć `mount_volumes` (Python) lub `MountVolumes` (C# i Java) właściwość `ClusterCreateParameters` w przypadku instalowania za pomocą zestawów SDK usługi Azure Batch AI. 

## <a name="access-mounted-filesystem-in-a-job"></a>Dostęp do zainstalowanego systemu plików, w ramach zadania

### <a name="azbatchaijobmountroot-environment-variable"></a>Zmienna środowiskowa AZ_BATCHAI_JOB_MOUNT_ROOT $

Wewnątrz środowiska wykonania zadania, możliwy do katalogu zawierającego systemów magazynowania zainstalowanych za pomocą `$AZ_BATCHAI_JOB_MOUNT_ROOT` zmienna środowiskowa (Jeśli używasz instalowanie poziomu zadania). Jeśli używasz instalowanie poziomu klastra, ta zmienna środowiskowa została `$AZ_BATCHAI_MOUNT_ROOT`. W poniższych przykładach założono, że używasz instalowanie poziomu zadania.

Aby zapewnić ścieżki danych w woluminie, należy użyć zmiennej środowiskowej `$AZ_BATCHAI_JOB_MOUNT_ROOT` wraz z zainstalowanym ścieżki. Na przykład jeśli skrypt szkoleniowy `train.py` został przekazany do plików platformy Azure zainstalowany udział jako ścieżka względna instalacji `scripts`, plik będzie dostępne pod adresem `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Jeśli skrypt szkolenia wymaga znajomości ścieżki, należy przekazać go jako argument wiersza polecenia. Na przykład, jeśli dane są przechowywane w folderze o nazwie `train_data` w kontenerze obiektów Blob platformy Azure zainstalowany w ścieżce `data`, mogą przekazywać `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` jako argument wiersza polecenia do skryptu. W związku z tym należy zmodyfikować skrypt, aby przyjmowały argumenty wiersza polecenia.

### <a name="abbreviate-input-paths"></a>Skrócić ścieżek wejściowych

Aby skrócić ścieżek wejściowych jako zmienną środowiskową, należy użyć `inputDirectories` właściwości usługi `job.json` pliku (lub `models.JobCreateParameters.input_directories` korzystania z zestawu SDK sztucznej Inteligencji usługi Batch). Schemat `inputDirectories` jest:

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Każda ścieżka określona zostaną umieszczone w zmiennej środowiskowej o nazwie `$AZ_BATCHAI_INPUT_<ID>`. Za pomocą tej metody można uprościć ścieżki jako danych wejściowych plików lub katalogów. Na przykład, aby skrócić ścieżkę do skryptu szkolenia: Jeśli `"id"` jest `"SCRIPT"` i `"path"` jest `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, a następnie tej ścieżce znajduje się w temacie `$AZ_BATCHAI_INPUT_SCRIPT` w środowisku wykonawczym zadania.

Aby uzyskać więcej informacji, zobacz [tutaj](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Skrócić ścieżek danych wyjściowych

Aby skrócić zmiennej środowiskowej ścieżki w danych wyjściowych, należy użyć `outputDirectories` właściwości usługi `job.json` pliku (lub `models.JobCreateParameters.output_directories` korzystania z zestawu SDK sztucznej Inteligencji usługi Batch). Przy użyciu tej metody można uproszczenie ścieżki dla plików wyjściowych. Schemat `outputDirectories` jest:

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Każda ścieżka określona zostaną umieszczone w zmiennej środowiskowej o nazwie `$AZ_BATCHAI_OUTPUT_<ID>`. `pathPrefix` Określa zainstalowany wolumin do przechowywania danych wyjściowych (na przykład `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`). `pathSuffix` Określa nazwę folderu wyjściowego (na przykład `"logs"`, `"checkpoints"`). Ścieżka rzeczywistych danych wyjściowych jest składa się z `pathPrefix`, `jobOutputDirectoryPathSegment` (automatycznie wygenerowany dla każdego zadania) i `pathSuffix`.

Aby uzyskać więcej informacji, zobacz [tutaj](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Pobieranie danych wyjściowych zadania z usługi Azure Storage

### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Azure portal to wygodny sposób, aby wyświetlić dane wyjściowe zadania za pomocą Eksploratora plików graficznego interfejsu użytkownika. Jednakże jeśli chcesz wyświetlić dane wyjściowe Stdout i Stderr lub ścieżki w `outputDirectories`, pliki są umieszczane w ścieżce automatycznie wygenerowany w woluminie magazynu platformy Azure. Więcej informacji można znaleźć poniżej.

### <a name="access-stdout-and-stderr-output"></a>Dostęp do strumienia wyjściowego Stdout i Stderr danych wyjściowych

Użyj `stdOutErrPathPrefix` właściwość `job.json` stwierdzić zadania, gdzie umieścić dzienniki wykonywania zadania i dane wyjściowe Stdout i Stderr. Na przykład, jeśli zainstalowany udział plików w ścieżce względnej instalacji `outputs`, i określeniu `stdOutErrPathPrefix` jako `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`, Stdout i Stderr dane wyjściowe zadania nie będą dostępne pod adresem `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` w tym wolumin. Ta ścieżka wygenerowany automatycznie jest używana, aby uniemożliwić wyjście kolizji między zadaniami o takiej samej nazwie.

Aby uzyskać więcej informacji, zobacz [tutaj](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>Wyświetlić pliki wyjściowe

Można użyć wiersza polecenia platformy Azure, aby wyświetlić listę plików dostępnych danych wyjściowych zadania przy użyciu `az batchai job file list` polecenia. Na przykład, aby wyświetlić listę plików w katalogu standardowe dane wyjściowe zadania, należy użyć `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

Aby uzyskać więcej informacji i przykładów, zobacz [tutaj](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Pliki wyjściowe Stream

Można użyć wiersza polecenia platformy Azure do strumienia plików za pomocą `az batchai job file stream` polecenia. Na przykład, aby wyświetlić:
- STDOUT: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Strumienia wyjściowego stderr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

Aby uzyskać więcej informacji i przykładów, zobacz [tutaj](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się więcej na temat interfejsu wiersza polecenia można współpracować z usługą Azure Storage, należy wyświetlić [dokumentacji interfejsu wiersza polecenia platformy Azure Batch AI](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Aby uzyskać więcej przykładów użycia usługi Batch AI, w tym zainstalowanie pamięci masowej i odczytywania plików wyjściowych, zobacz [rozwiązania Jupyter Notebook na potrzeby usługi Batch AI](https://github.com/Azure/BatchAI).
- Poznaj inne opcje dotyczące instalowania magazynu, w tym [instalowania serwera systemu plików NFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) i [zainstalowanie własnego klastra systemu plików NFS, cifs lub GlusterFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)