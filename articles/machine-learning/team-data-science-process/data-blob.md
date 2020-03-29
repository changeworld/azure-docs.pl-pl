---
title: Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanej analizy — proces nauki o danych zespołowych
description: Eksploruj dane i generuj funkcje na podstawie danych przechowywanych w magazynie obiektów Blob platformy Azure przy użyciu zaawansowanej analizy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721102"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanej analizy
Ten dokument obejmuje eksplorowanie danych i generowanie funkcji z danych przechowywanych w magazynie obiektów Blob platformy Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Ładowanie danych do ramki danych Pandas
Aby eksplorować i manipulować zestawem danych, należy go pobrać ze źródła obiektu blob do pliku lokalnego, który można następnie załadować w ramce danych Pandas. Oto kroki, które należy wykonać w przypadku tej procedury:

1. Pobierz dane z obiektu blob platformy Azure z następującym przykładowym kodem języka Python przy użyciu usługi obiektów Blob. Zastąp zmienną w poniższym kodzie określonymi wartościami: 
   
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
2. Odczyt danych w ramce danych Pandas z pobranego pliku.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Teraz możesz przystąpić do eksplorowania danych i generowania funkcji tego zestawu danych.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Eksploracja danych
Oto kilka przykładów sposobów eksplorowania danych przy użyciu pand:

1. Sprawdzanie liczby wierszy i kolumn 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Sprawdź pierwsze lub ostatnie kilka wierszy w zestawie danych, jak poniżej:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Sprawdź typ danych, który każda kolumna została zaimportowana, jako przy użyciu następującego przykładowego kodu
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Sprawdź podstawowe statystyki kolumn w zestawie danych w następujący sposób
   
        dataframe_blobdata.describe()
5. Spójrz na liczbę wpisów dla każdej wartości kolumny w następujący sposób
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Zliczanie brakujących wartości w porównaniu z rzeczywistą liczbą wpisów w każdej kolumnie przy użyciu następującego przykładowego kodu
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Jeśli w danych brakuje wartości dla określonej kolumny, możesz je upuścić w następujący sposób:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Innym sposobem zastąpienia brakujących wartości jest funkcja trybu:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Tworzenie wykresu histogramu przy użyciu zmiennej liczby pojemników w celu wykreślenia rozkładu zmiennej    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Spójrz na korelacje między zmiennymi za pomocą scatterplot lub za pomocą wbudowanej funkcji korelacji
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Generowanie funkcji
Możemy generować funkcje za pomocą Pythona w następujący sposób:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Generowanie funkcji oparte na wartości wskaźnika
Funkcje kategoryczne można tworzyć w następujący sposób:

1. Sprawdź rozkład kolumny kategorycznej:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generowanie wartości wskaźników dla każdej z wartości kolumny
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Łączenie kolumny wskaźnika z oryginalną ramką danych 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Usuń samą oryginalną zmienną:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Generowanie funkcji Binning
Do generowania binned funkcji, postępujemy w następujący sposób:

1. Dodawanie sekwencji kolumn do kosza na kolumnę liczbową
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konwertowanie binningu na sekwencję zmiennych logicznych
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Na koniec dołącz zmienne manekina z powrotem do oryginalnej ramki danych
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Zapisywanie danych z powrotem do obiektu blob platformy Azure i korzystanie z usługi Azure Machine Learning
Po zbadaniu danych i utworzeniu niezbędnych funkcji można przekazać dane (próbkowane lub featurized) do obiektu blob platformy Azure i korzystać z nich w usłudze Azure Machine Learning, wykonując następujące kroki: Dodatkowe funkcje można utworzyć w usłudze Azure Machine Learning Studio (klasyczny) jak również. 

1. Zapisywanie ramki danych w pliku lokalnym
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Przekaż dane do obiektu blob platformy Azure w następujący sposób:
   
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
3. Teraz dane można odczytać z obiektu blob przy użyciu modułu [danych importu][import-data] usługi Azure Machine Learning, jak pokazano na poniższym ekranie:

![obiekt blob czytnika][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

