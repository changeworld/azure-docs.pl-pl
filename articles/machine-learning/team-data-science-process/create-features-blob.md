---
title: Tworzenie funkcji dla danych magazynu obiektów blob platformy Azure przy użyciu Panda | Dokumentacja firmy Microsoft
description: Jak utworzyć funkcje dla danych przechowywanych w kontenerze obiektów blob platformy Azure z pakietem Panda Python.
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev
ms.openlocfilehash: a5a4b92f04fcb4683f792263f3477ecea4d7239e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Tworzenie funkcji dla danych usługi Azure Blob Storage za pomocą usługi Panda
Ten dokument przedstawia sposób tworzenia funkcji dla danych przechowywanych w użyciu kontenera obiektów blob platformy Azure [Pandas](http://pandas.pydata.org/) pakiet języka Python. Po zwijania jak załadować dane do ramki danych Panda, widoczny jest sposób generowania podzielone na kategorie funkcji za pomocą skryptów języka Python z wartościami wskaźnik i binning funkcji.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **menu** linki do tematów opisujących sposób tworzenia funkcji dla danych w różnych środowiskach. To zadanie jest krokiem w [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że utworzono konto magazynu obiektów blob platformy Azure i przechowywanych danych istnieje. Aby uzyskać instrukcje, aby skonfigurować konto, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>Ładowanie danych do ramki danych Pandas
Aby eksplorować i manipulowania zestawu danych, pobrana z źródłowego obiektu blob do pliku lokalnego. Następnie załadować do Pandas ramki danych. Poniżej przedstawiono kroki do wykonania w przypadku tej procedury:

1. Pobieranie danych z usługi Azure blob z następujący przykładowy kod języka Python za pomocą usługi blob. Zastąp zmienną w poniższym kodzie z określonymi wartościami:
   
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
2. Dane do odczytu do Pandas danych ramki z pobranego pliku.
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Teraz można przystąpić do eksplorowania danych oraz do generowania funkcji dla tego zestawu danych.

## <a name="blob-featuregen"></a>Funkcja generowania
W dwóch następnych sekcjach pokazano, jak Generowanie podzielone na kategorie funkcje wartościami wskaźnik i binning za pomocą skryptów języka Python.

### <a name="blob-countfeature"></a>Generowanie funkcji na podstawie wartości wskaźnika
Podzielone na kategorie funkcji można utworzyć w następujący sposób:

1. Należy sprawdzić rozkład kolumny podzielone na kategorie:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generowanie wartości wskaźnika dla każdej wartości w kolumnie
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Dołącz kolumny wskaźnika z oryginalną ramką danych
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Usuń oryginalny samej zmiennej:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Funkcja generowania binning
Podczas generowania binned funkcji, wykonać następujące czynności:

1. Dodanie sekwencji kolumn do bin kolumny liczbowej
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konwertuj binning sekwencję zmienne typu boolean
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Na koniec Join fikcyjny zmienne wstecz do oryginalnego ramki danych
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Zapisywania danych do obiektów blob platformy Azure, aby pobrać go w usłudze Azure Machine Learning
Do pracy z danymi w usłudze Azure Machine Learning, która ma zostały przedstawione, próbkowany lub featurized przekazać dane obiektów blob platformy Azure. Dodatkowe funkcje mogą być tworzone w usłudze Azure Machine Learning Studio również. W poniższej procedurze pokazano, jak przekazywać dane:

1. Zapis ramki danych do pliku lokalnego
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Przekazywanie danych do obiektów blob platformy Azure w następujący sposób:
   
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
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Teraz można odczytać danych z obiektu blob przy użyciu usługi Azure Machine Learning [i zaimportuj dane](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modułu, jak pokazano na poniższym zrzucie ekranu:

![Czytnik obiektów blob](./media/data-blob/reader_blob.png)

