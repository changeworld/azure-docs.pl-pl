---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 44823ce888e97b308f29403612f598c0eb585ae5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673358"
---
Kolejkę można wyświetlić w [witrynie Azure portal](../articles/storage/queues/storage-quickstart-queues-portal.md) lub w [Eksploratorze magazynu platformy Microsoft Azure](https://storageexplorer.com/). Kolejkę można również wyświetlić w wierszu polecenia platformy Azure, zgodnie z opisem w następujących krokach:

1. Otwórz plik *local.setting.json* projektu funkcji i skopiuj wartość ciągu połączenia. W oknie terminala lub polecenia uruchom następujące polecenie, `AZURE_STORAGE_CONNECTION_STRING`aby utworzyć zmienną środowiskową `<MY_CONNECTION_STRING>`o nazwie , wklejając określony ciąg połączenia zamiast pliku . (Ta zmienna środowiskowa oznacza, że nie trzeba podawać ciągu `--connection-string` połączenia do każdego kolejnego polecenia przy użyciu argumentu).

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Opcjonalnie) Użyj [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na koncie. Dane wyjściowe z tego polecenia `outqueue`powinny zawierać kolejkę o nazwie , która została utworzona, gdy funkcja napisała swój pierwszy komunikat do tej kolejki.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Użyj [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) polecenia, aby odczytać wiadomość z tej kolejki, która powinna być pierwszą nazwą używaną podczas testowania funkcji wcześniej. Polecenie odczytuje i usuwa pierwszą wiadomość z kolejki. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ten skrypt używa certutil do dekodowania kolekcji wiadomości zakodowanych base64 z lokalnego pliku tymczasowego. Jeśli nie ma żadnych danych `> NUL` wyjściowych, spróbuj usunąć ze skryptu, aby zatrzymać pomijanie danych wyjściowych certutil, w przypadku wystąpienia błędu. 
    
    ---
    
    Ponieważ treść wiadomości jest przechowywana [base64 zakodowana,](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)wiadomość musi zostać zdekodowana przed jej wyświetleniem. Po wykonaniu `az storage message get`komunikat zostanie usunięty z kolejki. Jeśli w `outqueue`pliku znajdowała się tylko jedna wiadomość, nie zostanie pobrana wiadomość po uruchomieniu tego polecenia po raz drugi i zamiast tego pojawi się błąd.