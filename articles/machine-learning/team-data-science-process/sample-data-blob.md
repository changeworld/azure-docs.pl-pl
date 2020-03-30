---
title: Przykładowe dane w magazynie obiektów blob platformy Azure — proces nauki o danych zespołu
description: Próbkowanie danych przechowywanych w magazynie obiektów blob platformy Azure przez pobranie go programowo, a następnie próbkowanie go przy użyciu procedur napisanych w języku Python.
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
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720286"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Przykładowe dane w usłudze Azure Blob Storage

W tym artykule opisano próbkowanie danych przechowywanych w magazynie obiektów blob platformy Azure przez pobranie go programowo, a następnie próbkowanie go przy użyciu procedur napisanych w języku Python.

**Dlaczego warto przykładać swoje dane?**
Jeśli zestaw danych, który planujesz analizować, jest duży, zwykle warto pobrać próbkę w dół danych, aby zmniejszyć je do mniejszego, ale reprezentatywnego i łatwiejszego w zarządzaniu rozmiaru. Próbkowanie ułatwia zrozumienie danych, eksplorację i inżynierię funkcji. Jego rolą w procesie cortana Analytics jest umożliwienie szybkiego prototypowania funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="download-and-down-sample-data"></a>Pobieranie i wprowadzanie próbek danych
1. Pobierz dane z magazynu obiektów blob platformy Azure przy użyciu usługi obiektów Blob z następującego przykładowego kodu języka Python: 
   
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

2. Odczyt danych w ramce danych Pandas z pliku pobranego powyżej.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Próbkowanie w dół `numpy`danych `random.choice` przy użyciu "s w następujący sposób:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Teraz możesz pracować z powyższą ramką danych z próbką jeden Procent do dalszej eksploracji i generowania funkcji.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Przekazywanie danych i odczytywanie ich do usługi Azure Machine Learning
Poniższy przykładowy kod służy do pobierania próbek w dół danych i używania ich bezpośrednio w usłudze Azure Machine Learning:

1. Zapisywanie ramki danych w pliku lokalnym
   
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

3. Przeczytaj dane z obiektu blob platformy Azure przy użyciu [danych importu](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) usługi Azure Machine Learning, jak pokazano na poniższej ilustracji:

![obiekt blob czytnika](./media/sample-data-blob/reader_blob.png)

