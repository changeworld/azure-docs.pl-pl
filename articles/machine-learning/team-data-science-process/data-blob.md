---
title: Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowana analityka | Dokumentacja firmy Microsoft
description: Przetwarzania danych w magazynie obiektów Blob Azure.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d8a59078-91d3-4440-b85c-430363c3f4d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 7abb547692bf7cc8e78caabe7c4c0f47edc1a0d2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="heading"></a>Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowana analityka
W tym dokumencie opisano masowej danych i funkcji generowania z danych przechowywanych w magazynie obiektów Blob Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Ładowanie danych do ramki danych Pandas
Aby eksplorować i manipulowania zestawu danych, musi zostać pobrana od źródła obiektu blob do pliku lokalnego, który może zostać załadowana w ramce Pandas danych. Poniżej przedstawiono kroki do wykonania w przypadku tej procedury:

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

## <a name="blob-dataexploration"></a>Eksploracja danych
Oto kilka przykładów sposobów, aby eksplorować dane przy użyciu Pandas:

1. Sprawdź liczbę wierszy i kolumn 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Sprawdź pierwszego lub ostatniego kilka wierszy w zestawie danych, jak pokazano poniżej:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Sprawdź typ danych, każda kolumna zostało zaimportowane jako przy użyciu następujący przykładowy kod
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Sprawdź następujące podstawowe statystyki dla kolumn w zestawie danych
   
        dataframe_blobdata.describe()
5. Przyjrzyj się liczba wpisów dla każdej wartości w kolumnie w następujący sposób
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Liczba brakujących wartości w zależności od rzeczywistej liczby wpisów w każdej kolumnie, za pomocą następujący przykładowy kod
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Jeśli brakuje wartości dla określonej kolumny danych, musisz porzucić je w następujący sposób:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Zastąp brakujące wartości w inny sposób jest funkcją trybu:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Utwórz wykres histogramu przy użyciu zmiennej liczby bins do wykreślenia dystrybucji zmiennej    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Przyjrzyj się korelacji między zmienne przy użyciu scatterplot lub funkcja wbudowana korelacji
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Funkcja generowania
Firma Microsoft może wygenerować funkcji za pomocą języka Python w następujący sposób:

### <a name="blob-countfeature"></a>Funkcja generowania na podstawie wartości wskaźnika
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
Podczas generowania binned funkcje, możemy wykonać następujące czynności:

1. Dodanie sekwencji kolumn do bin kolumny liczbowej
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konwertuj binning sekwencję zmienne typu boolean
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Na koniec Join fikcyjny zmienne wstecz do oryginalnego ramki danych
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Zapisywanie danych ponownie do obiektów blob platformy Azure i korzystanie w usłudze Azure Machine Learning
Po zostały przedstawione danych i utworzyć niezbędne funkcje, możesz przekazać dane (próbkowany lub featurized) do platformy Azure blob i używać go w usłudze Azure Machine Learning, wykonując następujące kroki: należy pamiętać, że w usłudze Azure Machine Learning Studio również można tworzyć dodatkowe funkcje. 

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
3. Teraz można odczytać danych z obiektu blob przy użyciu usługi Azure Machine Learning [i zaimportuj dane] [ import-data] modułu, jak pokazano na poniższym zrzucie ekranu:

![Czytnik obiektów blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

