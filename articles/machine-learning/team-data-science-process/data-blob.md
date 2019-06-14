---
title: Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanych analiz — zespołu danych dla celów naukowych
description: Eksplorowanie danych i generowania funkcji z danych przechowywanych w usłudze Azure Blob storage za pomocą zaawansowanych analiz.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a91c4d9f5dcdcee436f2dbf012eb5485b7a92192
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60398561"
---
# <a name="heading"></a>Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanej analizy
W tym dokumencie opisano analizowania danych i generowania funkcji z danych przechowywanych w usłudze Azure Blob storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Załaduj dane do ramki danych Pandas
Aby eksplorować i manipulowania zestawu danych, jej musi zostać pobrany z źródła obiektów blob do pliku lokalnego, która może zostać załadowana w ramce danych Pandas. Poniżej przedstawiono kroki do wykonania tej procedury:

1. Pobieranie danych z usługi Azure blob przy użyciu następującego przykładowego kodu Python przy użyciu usługi blob. Zastąp swoje określone wartości zmiennej w poniższym kodzie: 
   
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
2. Odczytać dane w ramce Pandas danych — z pobranego pliku.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Teraz wszystko jest gotowe do eksplorowania danych i wygenerować funkcje, w tym zestawie danych.

## <a name="blob-dataexploration"></a>Eksploracja danych
Poniżej przedstawiono kilka przykładów sposobów eksploracji danych przy użyciu Pandas:

1. Sprawdź liczbę wierszy i kolumn 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Zbadaj pierwszy lub ostatni kilka wierszy w zestawie danych, tak jak pokazano poniżej:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Sprawdź typ danych, które każdej kolumny został zaimportowany, używając następującego przykładowego kodu
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Sprawdź następujące podstawowe statystyki dla kolumn w zestawie danych
   
        dataframe_blobdata.describe()
5. Spójrz na liczbę wpisów dla każdej wartości kolumny w następujący sposób
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Liczba brakujących wartości, a rzeczywista liczba wpisów w poszczególnych kolumnach, za pomocą następującego przykładowego kodu
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Jeśli masz brakujących wartości dla określonej kolumny w danych, można upuścić je w następujący sposób:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Innym sposobem, aby zastąpić wartości Brak jest funkcją trybu:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Utwórz wykres histogram, do wykreślenia dystrybucji zmiennej za pomocą zmiennej liczba pojemników    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Przyjrzyj się korelacji między zmienne przy użyciu wykres punktowy lub przy użyciu funkcji wbudowanych korelacji
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Generowanie funkcji
Firma Microsoft może wygenerować funkcji przy użyciu języka Python w następujący sposób:

### <a name="blob-countfeature"></a>Wartość wskaźnika na podstawie funkcji generowania
Funkcje podzielonych na kategorie można utworzyć w następujący sposób:

1. Należy sprawdzić rozkład podzielonych na kategorie kolumny:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generowanie wartości wskaźnika dla każdej wartości w kolumnach
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Dołącz kolumny wskaźnika z pierwotnej ramce danych 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Usuń oryginalny sama zmienna:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Kwantowanie generacji funkcji
Podczas generowania funkcji kwanty, możemy wykonać następujące czynności:

1. Dodaj sekwencję kolumn do pojemnika kolumny liczbowej
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konwertuj pakowania na sekwencję zmiennych logicznych
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Na koniec dołączyć zmienne fikcyjnego do pierwotnej ramce danych
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Zapisywanie danych z powrotem w obiektów blob platformy Azure i używania w usłudze Azure Machine Learning
Po mają eksplorować dane i utworzyć wymagane funkcje, możesz przekazać dane (próbkowane lub neural) do usługi Azure blob i stosować go w usłudze Azure Machine Learning wykonując następujące czynności: Należy pamiętać, że można tworzyć dodatkowe funkcje w usłudze Azure Machine Learning Studio również. 

1. Zapis ramki danych do pliku lokalnego
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Przekaż dane do obiektów blob platformy Azure w następujący sposób:
   
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
3. Teraz dane mogą być odczytywane z magazynu obiektów blob za pomocą usługi Azure Machine Learning [importu danych] [ import-data] modułu, jak pokazano na poniższym ekranie:

![czytnika obiektów blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

