---
title: Eksplorowanie danych w usłudze Azure blob storage za pomocą usług Panda - zespołu danych dla celów naukowych
description: Jak eksplorować dane, które są przechowywane w kontenerze obiektów blob platformy Azure przy użyciu pakietu pandas języka Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 76cc22f614b7877db54fb5af0e58ff90105a8194
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961775"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Eksplorowanie danych w usłudze Azure blob storage za pomocą usług Panda

W tym artykule opisano, jak eksplorować dane, które są przechowywane w kontenerze obiektów blob platformy Azure za pomocą [pandas](http://pandas.pydata.org/) pakiet języka Python.

To zadanie jest to krok w [zespołu danych dla celów naukowych](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-quickstart-create-account.md)
* Przechowywane dane na koncie magazynu obiektów blob platformy Azure. Aby uzyskać instrukcje, zobacz [przenoszenie danych do i z usługi Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Załaduj dane do pandas DataFrame
Aby eksplorować i modyfikować zestaw danych, jej należy najpierw pobrać ze źródła obiektów blob w lokalnym pliku, który może zostać załadowana w pandas DataFrame. Poniżej przedstawiono kroki do wykonania tej procedury:

1. Pobieranie danych z usługi Azure blob przy użyciu Poniższy przykładowy kod języka Python przy użyciu usługi blob. Zastąp swoje określone wartości zmiennej w poniższym kodzie:

```python
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
```

2. Odczytać dane do pandas DataFrame pobranego pliku.

```python
#LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

Teraz wszystko jest gotowe do eksplorowania danych i wygenerować funkcje, w tym zestawie danych.

## <a name="blob-dataexploration"></a>Przykłady eksplorację danych za pomocą biblioteki pandas
Poniżej przedstawiono kilka przykładów sposobów eksploracji danych przy użyciu pandas:

1. Sprawdzanie **liczby wierszy i kolumn**

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

2. **Sprawdzanie** kilka pierwszej lub ostatniej **wierszy** następujących danych:

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

3. Sprawdź **— typ danych** każdej kolumny został zaimportowany, używając następującego przykładowego kodu

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

4. Sprawdź **podstawowe statystyki** dla kolumn w danych, ustaw następujący

```python
dataframe_blobdata.describe()
```

5. Spójrz na liczbę wpisów dla każdej wartości kolumny w następujący sposób

```python
dataframe_blobdata['<column_name>'].value_counts()
```

6. **Liczba brakujących wartości** a rzeczywista liczba wpisów w poszczególnych kolumnach, za pomocą następującego przykładowego kodu

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

7. Jeśli masz **brakujących wartości** dla określonej kolumny w danych, można upuścić je w następujący sposób:

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

Innym sposobem, aby zastąpić wartości Brak jest funkcją trybu:

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})
```

8. Tworzenie **histogram** wykresu, do wykreślenia dystrybucji zmiennej za pomocą zmiennej liczba pojemników

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

9. Przyjrzyj się **korelacji** między zmiennymi za pomocą wykres punktowy lub przy użyciu funkcji wbudowanych korelacji

```python
#relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

#correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```
