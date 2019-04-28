---
title: Przenoszenie danych do magazynu obiektów Blob za pomocą Eksploratora usługi Azure Storage — zespołu danych dla celów naukowych
description: Przenoszenie danych do oraz z usługi Azure Blob Storage za pomocą programu Azure Storage Explorer
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
ms.openlocfilehash: 8ec4289c641b3f140950575ac2f8016f894f2c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61430823"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Przenoszenie danych do i z usługi Azure Blob Storage za pomocą Eksploratora usługi Azure Storage
Eksplorator usługi Azure Storage to bezpłatne narzędzie firmy Microsoft, która umożliwia pracę z danymi usługi Azure Storage w Windows, macOS i Linux. W tym temacie opisano, jak z niej korzystać, aby przekazywać i pobierać dane z magazynu obiektów blob platformy Azure. Narzędzie można pobrać z [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Jeśli używasz maszyny Wirtualnej, który został skonfigurowany za pomocą skryptów, dostarczone przez [maszyny wirtualne do analizy danych na platformie Azure](virtual-machines.md), a następnie Eksploratora usługi Azure Storage jest już zainstalowana na maszynie Wirtualnej.
> 
> [!NOTE]
> Pełne wprowadzenie do usługi Azure blob storage, zapoznaj się [podstawowe informacje o usłudze Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed Trwa przekazywanie/pobieranie danych, musisz wiedzieć, usługa Azure storage konta nazwy i klucza konta usługi. 

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz w celu uzyskania konta i informacje o kluczu, zobacz [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md). Zanotuj klucz dostępu konta magazynu według potrzeb tego klucza, aby połączyć się z kontem za pomocą narzędzia Eksplorator usługi Azure Storage.
* Narzędzie Eksplorator usługi Azure Storage można pobrać z [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Zaakceptuj wartości domyślne podczas instalacji.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Za pomocą Eksploratora usługi Azure Storage
Poniższe kroki udokumentować sposób przekazywania/pobierania danych przy użyciu Eksploratora usługi Azure Storage. 

1. Uruchom Eksploratora usługi Storage platformy Microsoft Azure.
2. Aby wyświetlić **Zaloguj się do swojego konta...**  kreatora wybierz **ustawienia konta platformy Azure** ikony, następnie **Dodaj konto** i wprowadź poświadczenia. ![Dodaj konto usługi Azure storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Aby wyświetlić **łączenie z usługą Azure Storage** kreatora wybierz **łączenie z usługą Azure storage** ikony. ![Kliknij przycisk "Połącz z usługi Azure storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Wprowadź klucz dostępu z poziomu konta usługi Azure storage na **łączenie z usługą Azure Storage** kreatora i następnie **dalej**. ![Wprowadź klucz dostępu do konta usługi Azure storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Wprowadź nazwę konta magazynu w **nazwa konta** pole, a następnie wybierz pozycję **dalej**. ![Dołącz magazyn zewnętrzny](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Teraz powinny być wymienione na konto magazynu, dodane. Aby utworzyć kontener obiektów blob na koncie magazynu, kliknij prawym przyciskiem myszy **kontenery obiektów Blob** węzła w ramach tego konta, wybierz opcję **Utwórz kontener obiektów Blob**, a następnie wprowadź nazwę.
7. Aby przekazać dane do kontenera, wybierz kontener docelowy, a następnie kliknij przycisk **przekazywanie** przycisku.![ Konta magazynu](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Kliknij pozycję **...**  po prawej stronie **pliki** wybierz jednego lub wielu plików do przekazania z systemu plików, a następnie kliknij przycisk **przekazywanie** Rozpocznij przekazywanie plików.![ Przekazywanie plików](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Aby pobrać dane, wybierając obiekt blob w kontenerze odpowiednie do pobrania, a następnie kliknij przycisk **Pobierz**. ![Pobieranie plików](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

