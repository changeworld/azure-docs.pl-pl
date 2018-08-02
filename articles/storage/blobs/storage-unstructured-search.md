---
title: Przeszukiwanie danych bez struktury w magazynie w chmurze platformy Azure
description: Przeszukiwanie danych bez struktury przy użyciu usługi Azure Search.
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: eba2ef280e60693cfd4402348fe61b122cdccdf6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399860"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Przeszukiwanie danych bez struktury w magazynie w chmurze

W tym samouczku dowiesz się, jak przeszukiwać dane bez struktury za pomocą usługi [Azure Search](../../search/search-what-is-azure-search.md) z wykorzystaniem danych przechowywanych w obiektach blob platformy Azure. Dane bez struktury to dane, które nie są zorganizowane w sposób wstępnie zdefiniowany lub nie mają modelu danych. Przykładem może być plik z rozszerzeniem txt.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów
> * Tworzenie konta magazynu
> * Tworzenie kontenera
> * Przekazywanie danych do kontenera
> * Tworzenie usługi wyszukiwania w portalu
> * Używanie usługi wyszukiwania na potrzeby przeszukiwania kontenera

## <a name="download-the-sample"></a>Pobierz przykład

Przygotowaliśmy dla Ciebie przykładowy zestaw danych. **Pobierz plik [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** i rozpakuj go do własnego folderu.

Przykład składa się z plików tekstowych uzyskanych z witryny [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Możesz ich użyć jako przykładowych plików tekstowych do wyszukiwania przy użyciu platformy Azure.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny [Azure Portal](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu zapewnia unikatową lokalizację do przechowywania i umożliwiania dostępu do obiektów danych usługi Azure Storage.

Obecnie istnieją dwa typy kont magazynu: **Blob** i **Ogólnego przeznaczenia**. W tym samouczku utworzysz konto magazynu **Ogólnego przeznaczenia**.

Jeśli jeszcze nie znasz procesu tworzenia konta magazynu ogólnego przeznaczenia, poniżej wyjaśniono, jak to zrobić:

1. Z menu po lewej stronie wybierz pozycję **Konta magazynu**, a następnie wybierz polecenie **Dodaj**.

2. Wprowadź unikatową nazwę konta magazynu. 

3. Wybierz pozycję **Menedżer zasobów** jako **Model wdrażania** i pozycję **Ogólnego przeznaczenia** z listy rozwijanej **Rodzaj konta**.

4. Wybierz pozycję **Magazyn lokalnie nadmiarowy (LRS)** z listy rozwijanej **Replikacja**.

5. W obszarze **Grupa zasobów** wybierz plecenie **Utwórz nową** i wprowadź unikatową nazwę.

6. Wybierz odpowiednią subskrypcję.

7. Zaznacz lokalizację i wybierz pozycję **Utwórz**.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Tworzenie kontenera

Kontenery są podobne do folderów i służą do przechowywania obiektów blob.

W tym samouczku użyjesz pojedynczego kontenera do przechowywania plików tekstowych uzyskanych z witryny clinicaltrials.gov.

1. W witrynie Azure Portal przejdź do swojego konta magazynu.

2. Wybierz polecenie **Przeglądaj obiekty blob** w obszarze **Blob Service**.

3. Dodaj nowy kontener.

4. Nadaj kontenerowi nazwę „dane” i wybierz pozycję **Kontener** jako poziom dostępu publicznego.

5. Wybierz przycisk **OK**, aby utworzyć kontener. 

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Przekazywanie przykładowych danych

Teraz, po utworzeniu kontenera, możesz przekazać do niego przykładowe dane.

1. Zaznacz kontener i wybierz polecenie **Przekaż**.

2. Wybierz niebieską ikonę folderu wyświetlaną obok pola Pliki i przejdź do folderu lokalnego, do którego rozpakowano przykładowe dane.

3. Zaznacz wszystkie wyodrębnione pliki i wybierz polecenie **Otwórz**.

4. Wybierz pozycję **Przekaż**, aby rozpocząć proces przekazywania.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/upload.png)

Proces przekazywania może chwilę potrwać.

Po jego zakończeniu wróć do swojego kontenera danych, aby się upewnić, że pliki tekstowe zostały przekazane.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Tworzenie usługi wyszukiwania

Azure Search jest opartym na chmurze rozwiązaniem typu „wyszukiwanie jako usługa”, które udostępnia deweloperom interfejsy API oraz narzędzia umożliwiające dodawanie zaawansowanych funkcji wyszukiwania danych w aplikacjach internetowych, mobilnych i firmowych.

Jeśli jeszcze nie znasz procesu tworzenia usługi wyszukiwania, poniżej wyjaśniono, jak to zrobić:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.

2. Przewiń w dół i kliknij pozycję **Dodaj usługę Azure Search** w obszarze **BLOB SERVICE**.

3. W obszarze **Importuj dane** wybierz polecenie **Wybierz usługę**.

4. Wybierz pozycję **Kliknij tutaj, aby utworzyć nową usługę wyszukiwania**.

5. W obszarze **Nowa usługa wyszukiwania** wprowadź unikatową nazwę dla Twojej usługi wyszukiwania w polu **Adres URL**.

6. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej** i zaznacz wcześniej utworzoną grupę zasobów.

7. W obszarze **Warstwa cenowa** wybierz warstwę **Bezpłatna** i kliknij pozycję **Wybierz**.

8. Wybierz polecenie **Utwórz**, aby utworzyć usługę wyszukiwania.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Łączenie usługi wyszukiwania z kontenerem

Teraz, po utworzeniu usługi wyszukiwania, można ją dołączyć do usługi Blob Storage. W tej sekcji znajduje się opis procesu wybierania źródła danych, tworzenia indeksu i tworzenia indeksatora.

1. Przejdź do swojego konta magazynu.

2. Wybierz pozycję **Dodaj usługę Azure Search** w obszarze **BLOB SERVICE**.

3. Wybierz pozycję **Usługa wyszukiwania** w obszarze **Importuj dane**, a następnie kliknij usługę wyszukiwania utworzoną w poprzedniej sekcji. Spowoduje to otwarcie okna **Nowe źródło danych**.

### <a name="new-data-source"></a>Nowe źródło danych

  Źródło danych określa, które dane mają być indeksowane oraz w jaki sposób można uzyskać dostęp do danych. Usługa wyszukiwania może wielokrotnie korzystać z tego samego źródła danych.

1. Wprowadź nazwę źródła danych. W obszarze **Dane do wyodrębnienia** wybierz pozycję **Zawartość i metadane**. Źródło danych określa, które części obiektu blob są indeksowane.
    
    a. Później, we własnych scenariuszach, możesz również wybrać pozycję **Tylko metadane magazynu**. Tę pozycję należy wybrać, aby ograniczyć indeksowane dane do właściwości standardowych obiektów blob lub właściwości zdefiniowanych przez użytkownika.
    
    b. Można również wybrać pozycję **Wszystkie metadane**, aby uzyskać zarówno właściwości standardowych obiektów blob, jak i *wszystkie* metadane specyficzne dla typu zawartości. 

2. Ponieważ używane obiekty blob to pliki tekstowe, ustaw **Tryb analizy** na wartość **Tekst**.
    
    a. Później, we własnych scenariuszach, możesz wybrać [inne tryby analizy](../../search/search-howto-indexing-azure-blob-storage.md) w zależności od zawartości obiektów blob.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/datasources.png)

3. Wybierz pozycję **Kontener magazynu**, aby wyświetlić listę dostępnych kont magazynu.

4. Wybierz konto magazynu, a następnie zaznacz wcześniej utworzony kontener.

5. Kliknij pozycję **Wybierz**, aby powrócić do okna **Nowe źródło danych**, a następnie wybierz przycisk **OK** w celu kontynuowania.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Konfigurowanie indeksu

  Indeks to kolekcja pól ze źródła danych, które mogą być przeszukiwane. Indeks wskazuje usłudze wyszukiwania, w jaki sposób mają być wyszukiwane dane.

1. W obszarze **Importuj dane** wybierz pozycję **Dostosuj indeks docelowy**.
 
2. W polu **Nazwa indeksu** wprowadź nazwę indeksu.

3. Zaznacz pole wyboru atrybutu **Pobieranie** w obszarze **metadata_storage_name**.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/valuestoselect.png)

4. Kliknij przycisk **OK**, aby otworzyć okno **Tworzenie indeksatora**.

Parametry indeksu i atrybuty nadawane tym parametrom są istotne. Parametry określają *jakie* dane mają być przechowywane, a atrybuty określają *jak* mają one być przechowywane.

Parametry znajdują się w kolumnie **NAZWA POLA**. Poniższa tabela zawiera listę dostępnych atrybutów i ich opisy.

### <a name="field-attributes"></a>Atrybuty pól
| Atrybut | Opis |
| --- | --- |
| *Klucz* |Ciąg udostępniający unikatowy identyfikator każdego dokumentu, który jest używany do wyszukiwania dokumentu. Każdy indeks musi mieć jeden klucz. Tylko jedno pole może być kluczem i musi ono mieć typ Edm.String. |
| *Pobieranie* |Określa, czy pole może być zwracane w wynikach wyszukiwania. |
| *Filtrowanie* |Umożliwia używanie pola w zapytaniach filtrów. |
| *Sortowanie* |Umożliwia zapytaniom sortowanie wyników wyszukiwania za pomocą tego pola. |
| *Tworzenie aspektów* |Umożliwia używanie pola w strukturze nawigacji aspektowej podczas samodzielnego filtrowania przez użytkownika. Zwykle jako aspekty najlepiej sprawdzają się pola zawierające powtarzające się wartości, które umożliwiają grupowanie wielu dokumentów (na przykład wiele dokumentów podlegających pod jedną markę lub kategorię usługi). |
| *Wyszukiwanie* |Oznacza pole jako podlegające wyszukiwaniu pełnotekstowemu. |


### <a name="create-an-indexer"></a>Tworzenie indeksatora
    
  Indeksator łączy źródło danych z indeksem wyszukiwania i udostępnia harmonogram ponownego indeksowania danych.

1. Wprowadź nazwę w polu **Nazwa** i wybierz przycisk **OK**.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/exindexer.png)

2. Po powrocie do obszaru **Importuj dane** wybierz przycisk **OK**, aby zakończyć proces łączenia.

Obiekt blob został teraz pomyślnie połączony z usługą wyszukiwania. Po kilku minutach w portalu będzie widać, że indeks został wypełniony. Jednak usługa wyszukiwania rozpoczyna indeksowanie natychmiast, dzięki czemu od razu można rozpocząć wyszukiwanie.

## <a name="search-your-text-files"></a>Wyszukiwanie plików tekstowych

Aby wyszukać pliki, otwórz Eksploratora wyszukiwania wewnątrz indeksu nowo utworzonej usługi wyszukiwania.

Poniżej opisano, gdzie można znaleźć Eksploratora wyszukiwania, oraz przedstawiono kilka przykładowych zapytań:

1. Przejdź do wszystkich zasobów i znajdź nowo utworzoną usługę wyszukiwania.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/exampleurl.png)

3. Wybierz indeks, aby go otworzyć. 

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/overview.png)

4. Wybierz pozycję **Eksplorator wyszukiwania**, aby otworzyć to narzędzie umożliwiające tworzenie aktywnych zapytań względem danych.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/indexespane.png)

5. Wybierz pozycję **Wyszukaj**, nie wpisując nic w polu ciągu zapytania. Puste zapytanie zwraca *wszystkie* dane z obiektów blob.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Wyszukiwanie pełnotekstowe 

W polu **Ciąg zapytania** wprowadź ciąg „Myopia” i wybierz przycisk **Wyszukaj**. Zostanie zainicjowane wyszukiwanie zawartości plików i zwrócony zostanie podzestaw zawierający te z nich, które zawierają wyraz „Myopia”.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Wyszukiwanie właściwości systemu

Przy użyciu parametru `$select` można również tworzyć zapytania, które wyszukują według właściwości systemu. Wprowadź ciąg `$select=metadata_storage_name` w polu zapytania i naciśnij klawisz Enter, aby zwrócić tylko pole o tej konkretnej nazwie.
    
Ciąg zapytania bezpośrednio modyfikuje adres URL, dlatego znaki spacji są niedozwolone. Aby wyszukać wiele pól, należy użyć przecinka, na przykład: `$select=metadata_storage_name,metadata_storage_path`
    
Za pomocą parametru `$select` można wyszukiwać tylko te pola, które zostały oznaczone atrybutem Pobieranie podczas definiowania indeksu.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/metadatasearchunstructured.png) 

To już koniec tego samouczka. Dysponujesz teraz możliwym do wyszukiwania zestawem danych bez struktury.

## <a name="next-steps"></a>Następne kroki

Dzięki temu samouczkowi wiesz już, na czym polega wyszukiwanie danych bez struktury za pomocą usługi Azure Search. Opisano w nim następujące czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów
> * Tworzenie konta magazynu
> * Tworzenie kontenera
> * Przekazywanie danych do kontenera
> * Tworzenie usługi wyszukiwania
> * Używanie usługi wyszukiwania na potrzeby przeszukiwania kontenera

Skorzystaj z tego linku, aby dowiedzieć się więcej o indeksowaniu dokumentów za pomocą usługi Azure Search.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage with Azure Search](../../search/search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage za pomocą usługi Azure Search)