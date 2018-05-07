---
title: Przenoszenie danych do i z magazynu obiektów Blob z Eksploratora usługi Storage platformy Azure | Dokumentacja firmy Microsoft
description: Przenoszenie danych do oraz z usługi Azure Blob Storage za pomocą programu Azure Storage Explorer
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: c20bf045b156400e38e111337908d76b3016c0ea
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Przenoszenie danych do i z magazynu obiektów Blob Azure za pomocą Eksploratora usługi Storage platformy Azure
Eksplorator usługi Storage platformy Azure to bezpłatne narzędzie firmy Microsoft, który umożliwia pracę z danymi usługi Azure Storage w systemie Windows, macOS i Linux. W tym temacie opisano, jak używać go do przekazywania i pobierania danych z magazynu obiektów blob platformy Azure. Narzędzie można pobrać z [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Jeśli używasz maszyny Wirtualnej, który został skonfigurowany za pomocą skryptów dostarczonych przez [maszyn wirtualnych nauki danych na platformie Azure](virtual-machines.md), następnie Eksploratora usługi Storage platformy Azure jest już zainstalowana na maszynie Wirtualnej.
> 
> [!NOTE]
> Pełne wprowadzenie do magazynu obiektów blob platformy Azure, można znaleźć w temacie [podstawy obiektów Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi obiektów Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed przekazaniem/pobieranie danych, musisz znać Azure klucz konta magazynu nazwy i konta. 

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz uzyskiwanie konta i informacje o kluczu, zobacz [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md). Zanotuj klucz dostępu dla konta magazynu zgodnie z potrzebami ten klucz, aby połączyć konto za pomocą narzędzia Eksplorator magazynu Azure.
* Narzędzie Eksploratora usługi Storage platformy Azure można pobrać z [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/). Zaakceptuj ustawienia domyślne podczas instalacji.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Użyj Eksploratora usługi Storage platformy Azure
Poniższe kroki dokumentu sposobu wysyłania/pobierania danych przy użyciu Eksploratora usługi Storage platformy Azure. 

1. Uruchom Eksploratora usługi Storage platformy Microsoft Azure.
2. Aby wyświetlić **Zaloguj się do swojego konta...**  kreatora wybierz **ustawienia konta Azure** ikony, następnie **Dodaj konto** i podać swoje poświadczenia. ![Dodawanie konta magazynu platformy Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Aby wyświetlić **nawiązywanie połączenia z usługą Azure Storage** kreatora wybierz pozycję **nawiązywanie połączenia z usługą Azure storage** ikony. ![Łączenie się z magazynem Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Wprowadź klucz dostępu z kontem magazynu platformy Azure **nawiązywanie połączenia z usługą Azure Storage** kreatora, a następnie **dalej**. ![Łączenie się z magazynem Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Wprowadź nazwę konta magazynu w **nazwa konta** polu, a następnie wybierz **dalej**. ![Dołącz magazynu zewnętrznego](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Teraz dodane konto magazynu powinien być wyświetlany. Aby utworzyć kontener obiektów blob na koncie magazynu, kliknij prawym przyciskiem myszy **kontenerów obiektów Blob** węzłów na tym koncie, wybierz opcję **Tworzenie kontenera obiektów Blob**i wprowadź nazwę.
7. Aby przekazać dane do kontenera, wybierz kontener docelowego, a następnie kliknij przycisk **przekazać** przycisku.![ Konta magazynu](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Polecenie **...**  z prawej strony **pliki** wybierz jednego lub wielu plików, aby przekazać z systemu plików, a następnie kliknij przycisk **przekazać** można rozpocząć przekazywania plików.![ Przekazywanie plików](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Podczas pobierania danych, wybierając obiektu blob w kontenerze odpowiednie do pobrania i kliknij przycisk **Pobierz**. ![Pobierz pliki](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

