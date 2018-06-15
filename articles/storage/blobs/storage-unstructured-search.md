---
title: Wyszukiwanie danych bez struktury w magazynie w chmurze Azure
description: Wyszukiwanie danych niestrukturalnych przy użyciu usługi Azure search.
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2017
ms.locfileid: "23930204"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Przeszukiwanie danych bez struktury w magazynie w chmurze

W tym samouczku opisano sposób wyszukiwania danych niestrukturalnych przy użyciu [usługi Azure Search](../../search/search-what-is-azure-search.md) z danych przechowywanych w obiektach blob Azure. Dane bez struktury są dane, które nie są zorganizowane w sposób wstępnie zdefiniowanych lub nie ma modelu danych. Przykładem może być pliku txt.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów
> * Tworzenie konta magazynu
> * Tworzenie kontenera
> * Przekazywanie danych do Twojej kontenera
> * Utwórz usługę wyszukiwania, za pośrednictwem portalu
> * Umożliwia wyszukiwanie z kontenera usługi wyszukiwania

## <a name="download-the-sample"></a>Pobierz przykład

Przykładowy zestaw danych został przygotowany dla Ciebie. **Pobierz [klinicznych trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)**  i Rozpakuj go do własnego folderu.

Próbka składa się z plików tekstowych uzyskane z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Można używać ich jako pliki tekstowe przykład wyszukiwania za pomocą usługi Azure.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny [Azure Portal](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu zapewnia unikatową lokalizację do przechowywania i udostępniania obiektów danych usługi Azure Storage.

Obecnie istnieją dwa typy kont magazynu **obiektu Blob** i **ogólnego przeznaczenia**. W tym samouczku utworzysz **ogólnego przeznaczenia** konta magazynu.

Jeśli nie masz doświadczenia z procesu tworzenia konta magazynu ogólnego przeznaczenia, Oto jak utworzyć:

1. Wybierz z menu po lewej stronie **kont magazynu**, a następnie wybierz pozycję **Dodaj**.

2. Wprowadź unikatową nazwę konta magazynu. 

3. Wybierz **Resource Manager** dla Twojego **modelu wdrażania** i wybierz **ogólnego przeznaczenia** z **konta rodzaju** listy rozwijanej.

4. Wybierz **magazyn lokalnie nadmiarowy (LRS)** z **replikacji** listy rozwijanej.

5. W obszarze **grupy zasobów**, wybierz pozycję **Utwórz nowy** , a następnie wprowadź unikatową nazwę.

6. Wybierz odpowiednią subskrypcję.

7. Wybierz lokalizację i wybierz **Utwórz.**

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Tworzenie kontenera

Kontenery są podobne do folderów i są używane do przechowywania obiektów blob.

W tym samouczku służy do przechowywania plików tekstowych uzyskane z clinicaltrials.gov jeden kontener.

1. Przejdź do konta magazynu w portalu Azure.

2. Wybierz **Przeglądaj obiekty BLOB** w obszarze **usługi Blob.**

3. Dodaj nowy kontener.

4. Nazwa kontenera "dane" i wybierz **kontenera** poziomu dostępu publicznego.

5. Wybierz **OK** można utworzyć kontenera. 

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Przekaż przykładowe dane

Teraz, gdy masz kontenera, możesz przekazać dane przykładowe do niego.

1. Wybierz z kontenera i wybierz **przekazać**.

2. Wybierz ikonę niebieski folderu przedstawianych obok pola plików i przejdź do folderu lokalnego, w którym została rozpakowana przykładowych danych.

3. Wybierz wszystkie wyodrębnione pliki i wybierz **Otwórz**

4. Wybierz **przekazać** do rozpoczęcia procesu przekazywania.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/upload.png)

Proces przekazywania może chwilę potrwać.

Po jej zakończeniu, przejdź wstecz do kontenerem danych o potwierdzenie przekazać pliki tekstowe.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Utwórz usługę wyszukiwania

Usługa Azure Search jest rozwiązaniem chmury wyszukiwanie jako usługa, która zapewnia deweloperom interfejsów API i narzędzia do dodawania obsługi wyszukiwania sformatowanego za pośrednictwem danych w aplikacjach sieci web, mobilnych i enterprise.

Jeśli nie masz doświadczenia z procesu tworzenia usługi wyszukiwania, Oto jak utworzyć:

1. Przejdź do konta magazynu w portalu Azure.

2. Przewiń w dół i kliknij przycisk **Dodaj usługi Azure Search** w obszarze **usługa BLOB**.

3. W obszarze **i zaimportuj dane**, wybierz pozycję **wybierz usługi**.

4. Wybierz **kliknij tutaj, aby utworzyć nową usługę wyszukiwania**.

5. Wewnątrz **nową usługę wyszukiwania** wprowadź unikatową nazwę dla usługi wyszukiwania w **adres URL** pola.

6. W obszarze **grupy zasobów**, wybierz pozycję **Użyj istniejącego** i wybierz wcześniej utworzoną grupę zasobów.

7. Dla **warstwa cenowa**, wybierz pozycję **wolne** warstwy, a następnie kliknij przycisk **wybierz**.

8. Wybierz **Utwórz** można utworzyć usługi wyszukiwania.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Nawiązać połączenie z kontenera usługi wyszukiwania

Teraz, gdy masz usługi wyszukiwania, aby podłączyć go do magazynu obiektów blob. Ta sekcja przeprowadzi Cię przez proces wybierania źródła danych, tworzenie indeksu i tworzenie indeksatora.

1. Przejdź do swojego konta magazynu.

2. Wybierz **Dodaj wyszukiwanie Azure** w obszarze **usługi BLOB.**

3. Wybierz **usługi wyszukiwania** wewnątrz **i zaimportuj dane** a następnie kliknij usługę wyszukiwania został utworzony w poprzedniej sekcji. Spowoduje to otwarcie **nowe źródło danych**.

### <a name="new-data-source"></a>Nowe źródło danych

  Źródło danych określa, które dane do indeksu oraz sposobu uzyskiwania dostępu do danych. Źródło danych można wielokrotnie tej samej usługi wyszukiwania.

1. Wprowadź nazwę dla źródła danych. W obszarze **danych, aby wyodrębnić**, wybierz pozycję **zawartości i metadanych**. Źródło danych określa, które części obiektu blob są indeksowane.
    
    a. W własne scenariusz w przyszłości, można również wybrać **tylko metadane magazynu**. Czy spraw Aby zaznaczenie, jeśli chcesz ograniczyć dane, które jest indeksowana właściwości standardowych obiektów blob lub właściwości zdefiniowane przez użytkownika.
    
    b. Można również wybrać **wszystkich metadanych** uzyskanie obie właściwości standardowych obiektów blob i *wszystkie* metadane specyficzne dla typu zawartości. 

2. Ponieważ używasz obiekty BLOB są plikami tekstowymi, ustaw **podczas analizowania trybu** do **tekstu**.
    
    a. W własnych scenariuszy przyszłych, możesz wybrać [innych trybów analizy](../../search/search-howto-indexing-azure-blob-storage.md) w zależności od zawartości obiektów blob.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/datasources.png)

3. Wybierz **kontenera magazynu** do listy kont dostępny magazyn.

4. Wybierz konto magazynu, a następnie wybierz kontener, który został utworzony wcześniej.

5. Kliknij przycisk **wybierz** aby powrócić do **nowe źródło danych** i wybierz **OK** aby kontynuować.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Skonfiguruj indeksu

  Indeks jest kolekcją pól ze źródła danych, które mogą być wyszukiwane. Indeks jest jak usługi wyszukiwania wie, w jaki sposób można przeszukać danych.

1. W **importowania danych** wybierz **Dostosuj indeks docelowy**.
 
2. Wprowadź nazwę indeksu w **nazwę indeksu** pola.

3. Wybierz **pobieranie** wyboru atrybutu **metadata_storage_name**.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/valuestoselect.png)

4. Kliknij przycisk **OK**, co spowoduje uruchomienie **, Utwórz indeksator**.

Parametry indeksu i atrybuty zapewniają te parametry są ważne. Określ parametry *co* dane mają być przechowywane, określ atrybuty *jak* do przechowywania danych.

**Nazwa pola** kolumna zawiera parametry. Poniższa tabela zawiera listę dostępnych atrybutów i ich opisy.

### <a name="field-attributes"></a>Atrybuty pól
| Atrybut | Opis |
| --- | --- |
| *Klucz* |Ciąg, który zawiera unikatowy identyfikator każdego dokumentu, używany do wyszukiwania dokumentu. Każdy indeks musi mieć jeden klucz. Tylko jedno pole może być kluczem i musi ono mieć typ Edm.String. |
| *Pobieranie* |Określa, czy pole może być zwracane w wynikach wyszukiwania. |
| *Filtrowanie* |Umożliwia używanie pola w zapytaniach filtrów. |
| *Sortowanie* |Umożliwia zapytaniom sortowanie wyników wyszukiwania za pomocą tego pola. |
| *Tworzenie aspektów* |Umożliwia pola do użycia w strukturze nawigacji aspektowej samodzielnego filtrowania użytkownika. Zwykle jako aspekty najlepiej sprawdzają się pola zawierające powtarzające się wartości, które umożliwiają grupowanie wielu dokumentów (na przykład wiele dokumentów podlegających pod jedną markę lub kategorię usługi). |
| *Wyszukiwanie* |Oznacza pole jako podlegające wyszukiwaniu pełnotekstowemu. |


### <a name="create-an-indexer"></a>Utwórz indeksator
    
  Indeksator łączy źródła danych z indeksem wyszukiwania i udostępnia harmonogram indeksowania danych.

1. Wprowadź nazwę w **nazwa** i wybierz pozycję **OK**.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/exindexer.png)

2. Możesz zostaną zwrócone do **i zaimportuj dane**, wybierz pozycję **OK** do ukończenia procesu połączenia.

Jesteś teraz pomyślnie połączony z obiektu blob usługi wyszukiwania. Trwa kilka minut, aż portalu, aby pokazać, że indeks jest wypełnione. Jednak usługa wyszukiwania rozpoczyna indeksowania natychmiast, aby rozpocząć wyszukiwanie od razu.

## <a name="search-your-text-files"></a>Wyszukiwanie plików tekstowych

Aby przeprowadzić wyszukiwanie plików, otwórz Eksploratora wyszukiwania wewnątrz indeks usługi wyszukiwania nowo utworzony.

Poniższe kroki pokazują, gdzie można znaleźć Eksploratora wyszukiwania i zapewnia kilka przykładowych zapytań:

1. Przejdź do wszystkich zasobów i Znajdź usługi wyszukiwania nowo utworzony.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/exampleurl.png)

3. Wybierz indeks otwarcia go. 

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/overview.png)

4. Wybierz **Eksplorator wyszukiwania** otwarcie Eksploratora wyszukiwania, w którym można dokonać na żywo zapytania na danych.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/indexespane.png)

5. Wybierz **wyszukiwania** podczas pola ciągu zapytania jest pusta. Pusty zapytanie zwraca *wszystkie* danych z obiektów blob.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Wyszukiwanie pełnotekstowe 

Wprowadź "Myopia" w **ciągu zapytania** i wybierz pozycję **wyszukiwania**. Inicjowanie wyszukiwania zawartości plików i zwracanie ich podzbiór, zawierający słowo "Myopia."

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Wyszukiwanie właściwości systemu

Można również utworzyć kwerendy, które wyszukiwania za pomocą właściwości systemu `$select` parametru. Wprowadź `$select=metadata_storage_name` w ciągu zapytania i naciśnij klawisz wprowadzać, zwracanie tylko tego pola.
    
Ciąg zapytania jest bezpośrednie modyfikowanie adresu URL, więc spacje są niedozwolone. Aby przeprowadzić wyszukiwanie wielu pól, należy użyć przecinka, takich jak:`$select=metadata_storage_name,metadata_storage_path`
    
`$select` Parametru można tylko z polami, które są oznaczone pobieranie podczas definiowania indeksu.

  ![Bez struktury wyszukiwania](media/storage-unstructured-search/metadatasearchunstructured.png) 

Ukończono ten samouczek i można wyszukiwać zestaw danych bez struktury.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono dotyczące wyszukiwania danych niestrukturalnych przy użyciu usługi Azure Search, np.:

> [!div class="checklist"]
> * Tworzenie grupy zasobów
> * Tworzenie konta magazynu
> * Tworzenie kontenera
> * Przekazywanie danych do Twojej kontenera
> * Utwórz usługę wyszukiwania
> * Korzystanie z usługi wyszukiwania do wyszukiwania z kontenera

Wykonaj to łącze, aby dowiedzieć się więcej o indeksowania dokumentów za pomocą usługi Azure Search.

> [!div class="nextstepaction"]
> [Indeksowanie dokumentów w magazynie obiektów Blob Azure o usłudze Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)