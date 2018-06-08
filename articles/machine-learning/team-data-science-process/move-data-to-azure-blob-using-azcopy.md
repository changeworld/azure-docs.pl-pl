---
title: Przenoszenie danych do i z magazynu obiektów Blob platformy Azure przy użyciu narzędzia AzCopy | Dokumentacja firmy Microsoft
description: Przenoszenie danych do oraz z usługi Azure Blob Storage za pomocą usługi AzCopy
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: a25a35bc05781c6a52e21d697233ba1187ebeccf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838419"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Przenoszenie danych do i z magazynu obiektów Blob platformy Azure przy użyciu narzędzia AzCopy
Narzędzie AzCopy to narzędzie wiersza polecenia przeznaczone dla przekazywanie, pobieranie i kopiowanie danych do i z obiektów blob Microsoft Azure, plików i magazynowania tabeli.

Aby uzyskać instrukcje na temat instalowania programów AzCopy i dodatkowe informacje na temat używania go z platformą Azure, zobacz [wprowadzenie do korzystania z wiersza polecenia Azcopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Jeśli używasz maszyny Wirtualnej, który został skonfigurowany za pomocą skryptów dostarczonych przez [maszyn wirtualnych nauki danych na platformie Azure](virtual-machines.md), następnie AzCopy jest już zainstalowana na maszynie Wirtualnej.
> 
> [!NOTE]
> Pełne wprowadzenie do magazynu obiektów blob platformy Azure, można znaleźć w temacie [podstawy obiektów Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi obiektów Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed przekazaniem/pobieranie danych, musisz znać Azure klucz konta magazynu nazwy i konta.

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz uzyskiwanie konta i informacje o kluczu, zobacz [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Uruchom polecenia AzCopy
Aby uruchomić polecenia AzCopy, Otwórz okno polecenia i przejdź do katalog instalacyjny programu AzCopy na komputerze, w którym znajduje się AzCopy.exe pliku wykonywalnego. 

Podstawowa składnia polecenia AzCopy jest:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Możesz dodać lokalizacji instalacji programu AzCopy do ścieżki systemowej i Uruchom polecenia z dowolnego katalogu. Domyślnie program AzCopy jest instalowana na *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* lub *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Przekazać pliki do obiektów blob platformy Azure
Aby przekazać plik, użyj następującego polecenia:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Pobierz pliki z obiektów blob platformy Azure
Aby pobrać plik z obiektów blob platformy Azure, użyj następującego polecenia:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transferowania obiektów blob Azure kontenerów
Do przesyłania obiektów blob między Azure kontenery, użyj następującego polecenia:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Porady dotyczące używania narzędzia AzCopy
> [!TIP]
> 1. Gdy **przekazywania** pliki, */S* przekazuje rekursywnie plików. Bez tego parametru nie są przekazywane pliki w podkatalogach.  
> 2. Gdy **pobieranie** pliku */S* wyszukuje rekursywnie kontenera do momentu usunięcia wszystkich plików w określonym katalogu i jego podkatalogach lub wszystkie pliki, które jest zgodny z określonym wzorcem w podanym katalogu i jego podkatalogach zostaną pobrane.  
> 3. Nie można określić **pliku konkretnego obiektu blob** do pobrania przy użyciu */Source* parametru. Aby pobrać określonego pliku, określ nazwę pliku obiektu blob można pobrać przy użyciu */wzorca* parametru. **/S** parametru można mieć AzCopy Wyszukaj rekursywnie wzorzec nazwy pliku. Bez parametru wzorzec AzCopy pobiera wszystkie pliki w tym katalogu.
> 
> 

