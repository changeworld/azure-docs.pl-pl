---
title: Przenoszenie danych do magazynu obiektów Blob za pomocą Eksploratora usługi Azure Storage — zespołu danych dla celów naukowych
description: Dowiedz się, jak za pomocą Eksplorator usługi Azure Storage przekazywać i pobierać dane z usługi Azure Blob Storage.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bfc63c6f5aca92fb7fda9e3ecf63ce4c332b12ef
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720915"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Przenoszenie danych do i z usługi Azure Blob Storage za pomocą Eksploratora usługi Azure Storage
Eksplorator usługi Azure Storage to bezpłatne narzędzie firmy Microsoft, która umożliwia pracę z danymi usługi Azure Storage w Windows, macOS i Linux. W tym temacie opisano, jak z niej korzystać, aby przekazywać i pobierać dane z magazynu obiektów blob platformy Azure. Narzędzie można pobrać z [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> W przypadku korzystania z maszyny wirtualnej, która została skonfigurowana przy użyciu skryptów dostarczonych przez [maszyny wirtualne do analizy danych na platformie Azure](virtual-machines.md), Eksplorator usługi Azure Storage jest już zainstalowana na maszynie wirtualnej.
> 
> [!NOTE]
> Aby uzyskać pełne wprowadzenie do usługi Azure Blob Storage, zobacz [podstawowe informacje](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) o usłudze Azure BLOB i [usłudze Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed przekazaniem/pobraniem danych należy znać nazwę konta i klucz konta usługi Azure Storage. 

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Instrukcje dotyczące tworzenia konta magazynu i uzyskiwania informacji o kontach i kluczach znajdują się w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md). Zanotuj klucz dostępu konta magazynu według potrzeb tego klucza, aby połączyć się z kontem za pomocą narzędzia Eksplorator usługi Azure Storage.
* Narzędzie Eksplorator usługi Azure Storage można pobrać z [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/). Zaakceptuj wartości domyślne podczas instalacji.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Za pomocą Eksploratora usługi Azure Storage
Poniższe kroki udokumentować sposób przekazywania/pobierania danych przy użyciu Eksploratora usługi Azure Storage. 

1. Uruchom Eksploratora usługi Storage platformy Microsoft Azure.
2. Aby wyświetlić kreatora **logowania do konta...** , wybierz ikonę **Ustawienia konta platformy Azure** , a następnie **Dodaj konto** i wprowadź poświadczenia. 
![dodać konta usługi Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Aby wyświetlić Kreatora łączenia z usługą **Azure Storage** , wybierz ikonę **Połącz z usługą Azure Storage** . ![kliknij pozycję "Połącz z usługą Azure Storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Wprowadź klucz dostępu z konta usługi Azure Storage w kreatorze **łączenia z usługą Azure Storage** , a następnie kliknij przycisk **dalej**. ![wprowadzić klucza dostępu z konta usługi Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Wprowadź nazwę konta magazynu w polu **nazwa konta** , a następnie wybierz przycisk **dalej**. ![dołączyć magazyn zewnętrzny](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Dodane konto magazynu powinno być teraz wyświetlane. Aby utworzyć kontener obiektów BLOB na koncie magazynu, kliknij prawym przyciskiem myszy węzeł **kontenery obiektów BLOB** w ramach tego konta, wybierz pozycję **Utwórz kontener obiektów BLOB**, a następnie wprowadź nazwę.
7. Aby przekazać dane do kontenera, wybierz kontener docelowy, a następnie kliknij przycisk **Przekaż** .
![Konta magazynu](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Kliknij przycisk **...** z prawej strony pola **pliki** , wybierz co najmniej jeden plik do przekazania z systemu plików, a następnie kliknij przycisk **Przekaż** , aby rozpocząć przekazywanie plików.![przekazywanie plików](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Aby pobrać dane, wybierz obiekt BLOB w odpowiednim kontenerze do pobrania, a następnie kliknij pozycję **Pobierz**. pliki do pobrania ![](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

