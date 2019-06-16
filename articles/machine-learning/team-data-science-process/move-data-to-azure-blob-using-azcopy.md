---
title: Kopiowanie danych do magazynu obiektów Blob za pomocą narzędzia AzCopy - zespołu danych dla celów naukowych
description: Kopiowanie danych do i z usługi Azure Blob Storage przy użyciu narzędzia AzCopy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6c0951eb6ad3b7651da97e1a49c5edf5ab55a199
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044352"
---
# <a name="copy-data-to-and-from-azure-blob-storage-using-azcopy"></a>Kopiowanie danych do i z usługi Azure Blob Storage przy użyciu narzędzia AzCopy
AzCopy to narzędzie wiersza polecenia, zaprojektowana pod kątem przekazywanie, pobieranie i kopiowanie danych do i z obiektów blob, plików i usługi table storage Microsoft Azure.

Aby uzyskać instrukcje na temat instalowania narzędzia AzCopy oraz dodatkowe informacje na temat używania go z platformą Azure, zobacz [rozpoczęcie korzystania z wiersza polecenia Azcopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Jeśli używasz maszyny Wirtualnej, który został skonfigurowany za pomocą skryptów, dostarczone przez [maszyny wirtualne do analizy danych na platformie Azure](virtual-machines.md), a następnie narzędzie AzCopy jest już zainstalowane na maszynie Wirtualnej.
> 
> [!NOTE]
> Pełne wprowadzenie do usługi Azure blob storage, zapoznaj się [podstawowe informacje o usłudze Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed Trwa przekazywanie/pobieranie danych, musisz wiedzieć, usługa Azure storage konta nazwy i klucza konta usługi.

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz w celu uzyskania konta i informacje o kluczu, zobacz [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Uruchom polecenia narzędzia AzCopy
Aby uruchomić polecenia narzędzia AzCopy, Otwórz okno polecenia i przejdź do katalogu instalacyjnego programu AzCopy na komputerze, w którym znajduje się AzCopy.exe pliku wykonywalnego. 

Podstawowa składnia polecenia narzędzia AzCopy jest:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Można dodać lokalizacji instalacji narzędzia AzCopy do ścieżki systemowej i następnie uruchom polecenia z dowolnego katalogu. Domyślnie narzędzie AzCopy jest zainstalowane na *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* lub *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Przekazywanie plików do obiektu blob platformy Azure
Aby przekazać plik, użyj następującego polecenia:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Pobierz pliki z obiektu blob platformy Azure
Aby pobrać plik z obiektu blob platformy Azure, użyj następującego polecenia:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="copy-blobs-between-azure-containers"></a>Kopiowanie obiektów blob między kontenerów platformy Azure
Aby skopiować obiektów blob między kontenerów platformy Azure, użyj następującego polecenia:

    # Copying blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be copied. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Porady dotyczące korzystania z narzędzia AzCopy
> [!TIP]
> 1. Gdy **przekazywania** plików */S* przekazuje plikach cyklicznie. Bez tego parametru nie są ładowane pliki w podkatalogach.  
> 2. Gdy **pobieranie** pliku */S* wyszukuje rekursywnie kontenera, dopóki wszystkie pliki w określonym katalogu i jego podkatalogach lub wszystkie pliki, które spełniają określony wzorzec w podanym katalogu i jego podkatalogach są pobierane.  
> 3. Nie można określić **konkretny obiekt blob, plik** można pobrać przy użyciu */Source* parametru. Aby pobrać określony plik, określ nazwę pliku obiektu blob do pobrania przy użyciu */wzorca* parametru. **/S** parametru można mieć narzędzia AzCopy, poszukaj rekursywnie wzorzec nazwy pliku. Bez parametru wzorzec AzCopy pobiera wszystkie pliki w tym katalogu.
> 
> 

