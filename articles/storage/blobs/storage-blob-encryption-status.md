---
title: Sprawdzanie stanu szyfrowania obiektu blob — usługa Azure Storage
description: Dowiedz się, jak używać witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy dany obiekt blob jest zaszyfrowany. Jeśli obiekt blob nie jest zaszyfrowany, dowiedz się, jak użyć AzCopy wymusić szyfrowanie, pobierając i ponownie przekazując obiekt blob.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707594"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Sprawdzanie stanu szyfrowania obiektu blob

Każdy blokowy obiekt blob, dołączanie obiektu blob lub stronicowego obiektu blob, który został napisany w usłudze Azure Storage po 20 października 2017 r. jest szyfrowany za pomocą szyfrowania usługi Azure Storage. Obiekty BLOB utworzone przed tą datą są nadal szyfrowane przez proces w tle.

W tym artykule pokazano, jak ustalić, czy dany obiekt blob został zaszyfrowany.

## <a name="check-a-blobs-encryption-status"></a>Sprawdzanie stanu szyfrowania obiektu blob

Użyj witryny Azure portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, aby ustalić, czy obiekt blob jest szyfrowany bez kodu.

### <a name="azure-portal"></a>[Portal Azure](#tab/portal)

Aby użyć witryny Azure portal, aby sprawdzić, czy obiekt blob został zaszyfrowany, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz **kontenery,** aby przejść do listy kontenerów na koncie.
1. Znajdź obiekt blob i wyświetl jego kartę **Przegląd.**
1. Wyświetl właściwość **Zaszyfrowana serwer.** Jeśli **True**, jak pokazano na poniższej ilustracji, a następnie obiekt blob jest szyfrowany. Należy zauważyć, że właściwości obiektu blob obejmują również datę i godzinę utworzenia obiektu blob.

    ![Zrzut ekranu przedstawiający sposób sprawdzania właściwości zaszyfrowanej serwera w witrynie Azure portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Powershell](#tab/powershell)

Aby użyć programu PowerShell, aby sprawdzić, czy obiekt blob został zaszyfrowany, sprawdź właściwość **IsServerEncrypted** obiektu blob. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Aby ustalić, kiedy obiekt blob został utworzony, sprawdź wartość **Created** właściwości:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby użyć interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy obiekt blob został zaszyfrowany, sprawdź właściwość **IsServerEncrypted** obiektu blob. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Aby ustalić, kiedy obiekt blob został utworzony, sprawdź wartość **utworzonej** właściwości.

---

## <a name="force-encryption-of-a-blob"></a>Wymuszanie szyfrowania obiektu blob

Jeśli obiekt blob, który został utworzony przed 20 października 2017 nie został jeszcze zaszyfrowany przez proces w tle, można wymusić szyfrowanie natychmiast, pobierając i ponownie przekazując obiekt blob. Prosty sposób, aby to zrobić, jest z AzCopy.

Aby pobrać obiekt blob do lokalnego systemu plików za pomocą programu AzCopy, należy użyć następującej składni:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Aby ponownie przekazać obiekt blob do usługi Azure Storage za pomocą usługi AzCopy, należy użyć następującej składni:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Aby uzyskać więcej informacji na temat kopiowania danych obiektów blob za pomocą programu AzCopy, zobacz [Przenoszenie danych za pomocą magazynu obiektów AzCopy i Blob](../common/storage-use-azcopy-blobs.md).

## <a name="next-steps"></a>Następne kroki

[Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](../common/storage-service-encryption.md)
