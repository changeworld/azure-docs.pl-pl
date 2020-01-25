---
title: Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanych analiz — zespołu danych dla celów naukowych
description: Eksplorowanie danych i generowania funkcji z danych przechowywanych w usłudze Azure Blob storage za pomocą zaawansowanych analiz.
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
ms.openlocfilehash: 4c47dfb8b221b6cb4b6237669ecd17c1637107a2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721102"
---
# <a name="heading"></a>Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanej analizy
W tym dokumencie opisano analizowania danych i generowania funkcji z danych przechowywanych w usłudze Azure Blob storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Załaduj dane do ramki danych Pandas
Aby eksplorować zestaw danych i manipulować nim, należy go pobrać ze źródła obiektów BLOB do pliku lokalnego, który następnie można załadować w Pandasej ramce danych. Poniżej przedstawiono kroki do wykonania tej procedury:

1. Pobierz dane z obiektu blob platformy Azure przy użyciu następującego przykładowego kodu w języku Python za pomocą Blob service. Zastąp swoje określone wartości zmiennej w poniższym kodzie: 
   
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

### <a name="blob-countfeature"></a>Generowanie funkcji opartej na wartościach wskaźnika
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
Po zbadaniu danych i utworzeniu niezbędnych funkcji można przekazać dane (próbkowane lub featurized) do obiektu blob platformy Azure i użyć go w Azure Machine Learning, wykonując następujące czynności: dodatkowe funkcje można utworzyć w Azure Machine Learning Studio (klasyczny). 

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
3. Teraz dane można odczytać z obiektu BLOB przy użyciu modułu Azure Machine Learning [Import danych][import-data] , jak pokazano na poniższym ekranie:

![czytnika obiektów blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

