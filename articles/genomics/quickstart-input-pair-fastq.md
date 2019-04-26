---
title: Przesyłanie przepływu pracy przy użyciu plików wejściowych FASTQ — Microsoft Genomics
titleSuffix: Azure
description: W tym artykule przyjęto założenie, mieć zainstalowanego klienta msgen i pomyślnie uruchomił przykładowe dane za pośrednictwem usługi.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 2662a8f52c58a39916e5789fa9ed7fadd91216c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60333676"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Przesyłanie przepływu pracy przy użyciu plików wejściowych FASTQ w usłudze Microsoft Genomics

W tym artykule przedstawiono sposób przesyłania przepływu pracy do usługi Microsoft Genomics, jeśli pliki wejściowe to jedna para plików FASTQ. W tym temacie założono, że użytkownik zainstalował i uruchomił klienta `msgen` oraz że zna sposób korzystania z usługi Azure Storage. Jeśli zostało pomyślnie przesłane przepływu pracy przy użyciu podanych przykładowych danych, można przystąpić do kontynuować z tego artykułu. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Konfiguracja: Przekazywanie plików FASTQ do usługi Azure storage
Załóżmy, że mamy dwa pliki, *reads_1.fq.gz* i *reads_2.fq.gz*, oraz że zostały one przekazane do konta magazynu *myaccount* na platformie Azure jako obiekty **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** oraz **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**. Masz adres URL interfejsu API i klucz dostępu. Chcesz uzyskać dane wyjściowe w lokalizacji **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


## <a name="submit-your-job-to-the-msgen-client"></a>Przesyłanie zadania do klienta `msgen` 

Oto minimalny zestaw argumentów, jakie należy podać do klienta `msgen`; podziały wierszy zostały dodane z myślą o przejrzystości:

W przypadku systemu Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

W przypadku systemu Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Jeśli wolisz używać pliku konfiguracji, oto jego zawartość:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Prześlij plik `config.txt` przy użyciu tego wywołania: `msgen submit -f config.txt`

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przekazano parę plików FASTQ do usługi Azure Storage oraz przesłano przepływ pracy do usługi Microsoft Genomics za pośrednictwem klienta `msgen` Python. Aby dowiedzieć się więcej o przesyłaniu przepływów pracy i innych poleceniach, za pomocą usługi Microsoft Genomics, zobacz nasze [— często zadawane pytania](frequently-asked-questions-genomics.md). 
