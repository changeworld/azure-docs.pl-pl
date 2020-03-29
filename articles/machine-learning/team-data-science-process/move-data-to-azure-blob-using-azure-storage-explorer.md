---
title: Przenoszenie danych magazynu obiektów Blob za pomocą Eksploratora usługi Azure Storage — proces nauki o danych zespołowych
description: Dowiedz się, jak używać Eksploratora usługi Azure Storage do przekazywania i pobierania danych z magazynu obiektów blob platformy Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720915"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Przenoszenie danych do i z usługi Azure Blob Storage przy użyciu Eksploratora usługi Azure Storage
Usługa Azure Storage Explorer to bezpłatne narzędzie firmy Microsoft, które umożliwia pracę z danymi usługi Azure Storage w systemach Windows, macOS i Linux. W tym temacie opisano sposób używania go do przekazywania i pobierania danych z magazynu obiektów blob platformy Azure. Narzędzie można pobrać z [Eksploratora magazynu Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Jeśli używasz maszyny Wirtualnej, która została skonfigurowana przy użyciu skryptów dostarczonych przez maszyny wirtualne do nauki o danych na [platformie Azure,](virtual-machines.md)eksplorator usługi Azure Storage jest już zainstalowany na maszynie Wirtualnej.
> 
> [!NOTE]
> Aby uzyskać pełne wprowadzenie do magazynu obiektów blob platformy Azure, zobacz [Podstawowe usługi Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob.](https://msdn.microsoft.com/library/azure/dd179376.aspx)   
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konto magazynu i odpowiedni klucz magazynu dla tego konta. Przed przekazaniem/pobraniem danych musisz znać nazwę konta usługi Azure Storage i klucz konta. 

* Aby skonfigurować subskrypcję platformy Azure, zobacz [Bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz uzyskiwania informacji o koncie i kluczach, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md). Zanotuj klucz dostępu dla konta magazynu, ponieważ potrzebujesz tego klucza do połączenia się z kontem za pomocą narzędzia Azure Storage Explorer.
* Narzędzie Eksploratora usługi Azure Storage można pobrać z [Eksploratora magazynu platformy Microsoft Azure](https://storageexplorer.com/). Zaakceptuj ustawienia domyślne podczas instalacji.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Korzystanie z Eksploratora usługi Azure Storage
Poniższe kroki dokumentują sposób przekazywania/pobierania danych przy użyciu Eksploratora usługi Azure Storage. 

1. Uruchom Eksploratora magazynu platformy Microsoft Azure.
2. Aby **przywołać kreatora Logowania się na swoje konto...** wybierz ikonę **ustawień konta platformy Azure,** a następnie **dodaj konto** i wprowadź poświadczenia. 
![Dodawanie konta usługi Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Aby **przywołać kreatora Połącz z usługą Azure Storage,** wybierz ikonę **Połącz z usługą Azure Storage.** ![Kliknij "Połącz się z usługą Azure Storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Wprowadź klucz dostępu z konta usługi Azure Storage w kreatorze **Połącz z usługą Azure Storage,** a następnie **dalej**. ![Wprowadzanie klucza dostępu z konta usługi Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Wprowadź nazwę konta magazynu w polu **Nazwa konta,** a następnie wybierz pozycję **Dalej**. ![Dołączanie pamięci zewnętrznej](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Dodane konto magazynu powinno być teraz wyświetlane. Aby utworzyć kontener obiektów blob na koncie magazynu, kliknij prawym przyciskiem myszy węzeł **Kontenery obiektów blob** na tym koncie, wybierz pozycję **Utwórz kontener obiektów Blob**i wprowadź nazwę.
7. Aby przekazać dane do kontenera, wybierz kontener docelowy i kliknij przycisk **Przekaż.**
![Konta magazynu](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Kliknij **na ...** po prawej stronie pola **Pliki,** wybierz jeden lub wiele plików do przesłania z systemu plików i kliknij przycisk **Przekaż,** aby rozpocząć przesyłanie plików. ![Przekazywanie plików](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Aby pobrać dane, wybierz obiekt blob w odpowiednim kontenerze, aby pobrać, a następnie kliknij przycisk **Pobierz**. ![Pobieranie plików](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

