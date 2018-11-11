---
title: Przykładowe dane w usłudze Azure blob storage | Dokumentacja firmy Microsoft
description: Przykładowe dane w usłudze Azure Blob Storage
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
ms.openlocfilehash: 9f987daf8b0f111f8d527b2f18dc5e3428df282a
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344930"
---
# <a name="heading"></a>Przykładowe dane w usłudze Azure blob storage

W tym artykule opisano próbkowania — dane przechowywane w usłudze Azure blob storage, programowo ją pobrać, a następnie próbkowanie go za pomocą procedury napisane w języku Python.

**Dlaczego przykładowe dane?**
Jeśli zestaw danych, która ma zostać analizowanie jest duża, zazwyczaj przyczyną jest dobrym pomysłem jest obniżenie częstotliwości próbkowania danych, aby zmniejszyć jego rozmiar mniejszy, ale reprezentatywny i łatwiejsze w zarządzaniu. To ułatwia zrozumienie danych, poznawanie i technicznego opracowywania funkcji. Jego rolę w procesie Cortana Analytics jest umożliwienie szybkiego tworzenia prototypów funkcji do przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krok [Team Data Science naukowych](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Pobierz i obniżenie częstotliwości próbkowania danych
1. Pobierz dane z usługi Azure blob storage przy użyciu usługi obiektów blob z następującego przykładowego kodu Python: 
   
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

2. Odczytywanie danych do Pandas-ramki danych z pliku pobranego powyżej.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Obniżenie częstotliwości próbkowania danych przy użyciu `numpy`firmy `random.choice` w następujący sposób:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Teraz możesz pracować z powyższych ramki danych z przykładu 1 procent potrzeby dalszych badań i generowania funkcji.

## <a name="heading"></a>Przekazywanie danych, a wczytywany usługi Azure Machine Learning
Następujący przykładowy kod umożliwia obniżenie częstotliwości próbkowania danych i używać go bezpośrednio w usłudze Azure Machine Learning:

1. Zapis ramki danych do pliku lokalnego
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Przekaż plik lokalny do obiektu blob platformy Azure przy użyciu następującego przykładowego kodu:
   
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

3. Odczytywanie danych z obiektów blob platformy Azure przy użyciu usługi Azure Machine Learning [importu danych](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) jak pokazano na poniższej ilustracji:

![czytnika obiektów blob](./media/sample-data-blob/reader_blob.png)

