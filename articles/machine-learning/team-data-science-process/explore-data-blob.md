---
title: Eksploruj dane w magazynie obiektów blob platformy Azure z Pandas | Dokumentacja firmy Microsoft
description: Jak Eksplorowanie danych przechowywanych w kontenerze obiektów blob platformy Azure przy użyciu Pandas.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 8d769ebba349937448b8eed277d1dfb224f39115
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Eksplorowanie danych usługi Azure Blob Storage za pomocą usług Panda
W tym dokumencie opisano sposób eksplorować dane przechowywane w użyciu kontenera obiektów blob platformy Azure [Pandas](http://pandas.pydata.org/) pakiet języka Python.

Następujące **menu** linki do tematów opisujących sposób użycia narzędzia, aby eksplorować dane w różnych środowiskach magazynu. To zadanie jest krokiem w [proces nauki danych]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Przechowywane dane na koncie magazynu obiektów blob platformy Azure. Aby uzyskać instrukcje, zobacz [przenoszenie danych do i z usługi Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Ładowanie danych do Pandas DataFrame
Aby eksplorować i manipulowania zestawu danych, musi najpierw zostać pobrana od źródła obiektu blob do pliku lokalnego, który następnie będzie można załadować Pandas DataFrame. Poniżej przedstawiono kroki do wykonania w przypadku tej procedury:

1. Pobieranie danych z usługi Azure blob z Poniższy przykładowy kod języka Python za pomocą usługi blob. Zastąp zmienną w poniższym kodzie z określonymi wartościami: 
   
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

## <a name="blob-dataexploration"></a>Przykłady Eksploracja danych przy użyciu Pandas
Oto kilka przykładów sposobów, aby eksplorować dane przy użyciu Pandas:

1. Sprawdź **liczba wierszy i kolumn** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Sprawdź** kilka pierwszego lub ostatniego **wierszy** następujących danych:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Sprawdź **— typ danych** każda kolumna została zaimportowana jako przy użyciu następujący przykładowy kod
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Sprawdź **podstawowe statystyki** kolumn danych, ustaw następujący
   
        dataframe_blobdata.describe()
5. Przyjrzyj się liczba wpisów dla każdej wartości w kolumnie w następujący sposób
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Liczba brakujących wartości** zależności od rzeczywistej liczby wpisów w każdej kolumnie, za pomocą następujący przykładowy kod
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Jeśli masz **brakujące wartości** dla określonej kolumny danych, można umieszczać je w następujący sposób:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Zastąp brakujące wartości w inny sposób jest funkcją trybu:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Utwórz **histogram** wykreślenia przy użyciu zmiennej liczby bins do wykreślenia dystrybucji zmiennej    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Przyjrzyj się **korelacji** między zmienne przy użyciu scatterplot lub funkcja wbudowana korelacji
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

