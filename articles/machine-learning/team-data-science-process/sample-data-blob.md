---
title: Przykładowe dane w usłudze Azure magazynu obiektów blob | Dokumentacja firmy Microsoft
description: Przykładowe dane w magazynie obiektów Blob Azure
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: ffb92df0e0727d02985ec26f61739cdc759d0f93
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837671"
---
# <a name="heading"></a>Magazyn obiektów blob przykładowych danych na platformie Azure
W tym dokumencie opisano próbkowania danych przechowywanych w magazynie obiektów blob platformy Azure programowo ją pobrać, a następnie próbkowania przy użyciu procedury napisane w języku Python.

Następujące **menu** linki do tematów opisujących sposób przykładowe dane z różnych środowiskach magazynu. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Dlaczego przykładowe dane?**
Jeśli zestaw danych, które mają być analizowanie jest duży, zazwyczaj jest dobrym rozwiązaniem w dół przykładowych danych, aby zmniejszyć jego rozmiar mniejsze, ale reprezentatywny i łatwiejsze w zarządzaniu. To ułatwia zrozumienie danych, badanie i inżynieria funkcji. Swoją rolę w procesie Analytics Cortana jest umożliwienie szybkiego prototypy funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Pobieranie i w dół przykładowe dane
1. Pobierz dane z magazynu obiektów blob platformy Azure przy użyciu usługi obiektów blob z następujący przykładowy kod języka Python: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Odczytywanie danych w Pandas danych ramkę z pliku został pobrany powyżej.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Dół przykładowych danych przy użyciu `numpy`w `random.choice` w następujący sposób:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Teraz możesz pracować z powyższych ramki danych próbki 1 procent dalszych badań i generowanie funkcji.

## <a name="heading"></a>Przekazywanie danych i przeczytaj go do usługi Azure Machine Learning
Następujący przykładowy kod służy do dołu przykładowe dane i użyć go bezpośrednio w usłudze Azure Machine Learning:

1. Zapis ramki danych do pliku lokalnego
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Przekazywanie pliku lokalnego do obiektów blob platformy Azure przy użyciu następujący kod:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Odczytywanie danych z obiektów blob platformy Azure przy użyciu usługi Azure Machine Learning [i zaimportuj dane](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) jak pokazano na poniższej ilustracji:

![Czytnik obiektów blob](./media/sample-data-blob/reader_blob.png)

