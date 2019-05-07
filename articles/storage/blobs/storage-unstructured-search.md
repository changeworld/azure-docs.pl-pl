---
title: 'Samouczek: przeszukiwanie danych bez struktury w usłudze Azure Blob Storage'
description: 'Samouczek: przeszukiwanie danych bez struktury w usłudze Blob Storage za pomocą usługi Azure Search.'
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 8c11d2e6d9a902707d3fd98f09d3474a5d2a5f64
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150794"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Samouczek: Przeszukiwanie danych bez struktury w magazynie w chmurze

Z tego samouczka dowiesz się, jak przeszukiwać dane bez struktury za pomocą usługi [Azure Search](../../search/search-what-is-azure-search.md) z wykorzystaniem danych przechowywanych w usłudze Azure Blob Storage. Dane bez struktury to dane, które nie są zorganizowane w sposób wstępnie zdefiniowany lub nie mają modelu danych. Przykładem jest plik tekstowy (txt).

Ten samouczek wymaga subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów
> * Tworzenie konta magazynu
> * Tworzenie kontenera
> * Przekazywanie danych do kontenera
> * Tworzenie usługi wyszukiwania w portalu
> * Łączenie usługi wyszukiwania z kontem magazynu
> * Tworzenie źródła danych
> * Konfigurowanie indeksu
> * Tworzenie indeksatora
> * Używanie usługi wyszukiwania na potrzeby przeszukiwania kontenera

## <a name="prerequisites"></a>Wymagania wstępne

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym samouczku utworzymy nową grupę zasobów.

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

Przygotowaliśmy przykładowy zestaw danych, który możesz wykorzystać na potrzeby tego samouczka. Pobierz plik [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) i rozpakuj go do własnego folderu.

Przykład składa się z plików tekstowych uzyskanych z witryny [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). W tym samouczku są one używane jako przykładowe pliki tekstowe, które będą przeszukiwane przy użyciu usług Azure Search.

## <a name="create-a-container"></a>Tworzenie kontenera

Kontenery są podobne do folderów i służą do przechowywania obiektów blob.

W tym samouczku użyjesz pojedynczego kontenera do przechowywania plików tekstowych uzyskanych z witryny clinicaltrials.gov.

1. Przejdź do swojego konta magazynu w witrynie Azure Portal.

2. Wybierz polecenie **Przeglądaj obiekty blob** w obszarze **Blob Service**.

3. Dodaj nowy kontener.

4. Nadaj kontenerowi nazwę **dane** i wybierz pozycję **Kontener** jako poziom dostępu publicznego.

5. Wybierz przycisk **OK**, aby utworzyć kontener.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Przekazywanie przykładowych danych

Teraz, po utworzeniu kontenera, możesz przekazać do niego przykładowe dane.

1. Zaznacz kontener i wybierz polecenie **Przekaż**.

2. Wybierz niebieską ikonę folderu obok pola **Pliki** i przejdź do folderu lokalnego, do którego rozpakowano przykładowe dane.

3. Zaznacz wszystkie wyodrębnione pliki i wybierz polecenie **Otwórz**.

4. Wybierz pozycję **Przekaż**, aby rozpocząć proces przekazywania.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/upload.png)

Proces przekazywania może chwilę potrwać.

Po jego zakończeniu wróć do swojego kontenera danych, aby upewnić się, że pliki tekstowe zostały przekazane.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Tworzenie usługi wyszukiwania

Azure Search jest opartym na chmurze rozwiązaniem typu „wyszukiwanie jako usługa”, które udostępnia deweloperom interfejsy API oraz narzędzia umożliwiające dodawanie funkcji wyszukiwania danych.

W tym samouczku użyjesz usługi wyszukiwania do przeszukiwania plików tekstowych uzyskanych z witryny clinicaltrials.gov.

1. Przejdź do swojego konta magazynu w witrynie Azure Portal.

2. Przewiń w dół i wybierz pozycję **Dodaj usługę Azure Search** w obszarze **BLOB SERVICE**.

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

3. Wybierz pozycję **Search Service** w obszarze **Importuj dane**, a następnie kliknij usługę wyszukiwania utworzoną w poprzedniej sekcji. Spowoduje to otwarcie okna **Nowe źródło danych**.

### <a name="create-a-data-source"></a>Tworzenie źródła danych

  Źródło danych określa, które dane mają być indeksowane oraz w jaki sposób można uzyskać dostęp do danych. Usługa wyszukiwania może wielokrotnie korzystać z tego samego źródła danych.

1. Wprowadź nazwę źródła danych. W obszarze **Dane do wyodrębnienia** wybierz pozycję **Zawartość i metadane**. Źródło danych określa, które części obiektu blob są indeksowane.

2. Ponieważ używane obiekty blob to pliki tekstowe, dla pozycji **Tryb analizy** ustaw wartość **Tekst**.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/datasources.png)

3. Wybierz pozycję **Kontener magazynu**, aby wyświetlić listę dostępnych kont magazynu.

4. Wybierz swoje konto magazynu, a następnie zaznacz wcześniej utworzony kontener.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/datacontainer.png)

5. Kliknij pozycję **Wybierz**, aby powrócić do okna **Nowe źródło danych**, a następnie wybierz przycisk **OK** w celu kontynuowania.

### <a name="configure-the-index"></a>Konfigurowanie indeksu

  Indeks to kolekcja pól ze źródła danych, które mogą być przeszukiwane. Dla tych pól należy ustawić i skonfigurować parametry, aby usługa wyszukiwania wiedziała, w jaki sposób ma przeszukiwać dane.

1. W obszarze **Importuj dane** wybierz pozycję **Dostosuj indeks docelowy**.

2. W polu **Nazwa indeksu** wprowadź nazwę indeksu.

3. Zaznacz pole wyboru atrybutu **Z możliwością pobierania**  w obszarze **metadata_storage_name**.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/valuestoselect.png)

4. Wybierz przycisk **OK**, aby otworzyć okno **Tworzenie indeksatora**.

Parametry indeksu i atrybuty nadawane tym parametrom są istotne. Parametry określają, *jakie* dane mają być przechowywane, a atrybuty określają, *jak* mają one być przechowywane.

Parametry znajdują się w kolumnie **NAZWA POLA**. Poniższa tabela zawiera listę dostępnych atrybutów i ich opisy.

#### <a name="field-attributes"></a>Atrybuty pól

| Atrybut | Opis |
| --- | --- |
| *Klucz* |Ciąg udostępniający unikatowy identyfikator każdego dokumentu, który jest używany do wyszukiwania dokumentu. Każdy indeks musi mieć jeden klucz. Tylko jedno pole może być kluczem i musi ono mieć typ Edm.String. |
| *Pobieranie* |Określa, czy pole może być zwracane w wynikach wyszukiwania. |
| *Filtrowanie* |Umożliwia używanie pola w zapytaniach filtrów. |
| *Sortowanie* |Umożliwia zapytaniom sortowanie wyników wyszukiwania za pomocą tego pola. |
| *Tworzenie aspektów* |Umożliwia używanie pola w strukturze nawigacji aspektowej podczas samodzielnego filtrowania przez użytkownika. Zwykle jako aspekty najlepiej sprawdzają się pola zawierające powtarzające się wartości, które umożliwiają grupowanie dokumentów (na przykład wiele dokumentów podlegających pod jedną markę lub kategorię usługi). |
| *Wyszukiwanie* |Oznacza pole jako podlegające wyszukiwaniu pełnotekstowemu. |

### <a name="create-an-indexer"></a>Tworzenie indeksatora

  Indeksator łączy źródło danych z indeksem wyszukiwania i udostępnia harmonogram ponownego indeksowania danych.

1. Wprowadź nazwę w polu **Nazwa** i wybierz przycisk **OK**.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/exindexer.png)

2. Zostanie ponownie wyświetlone okno **Importowanie danych**. Wybierz przycisk **OK**, aby zakończyć proces łączenia.

Obiekt blob został teraz pomyślnie połączony z usługą wyszukiwania. Po kilku minutach w portalu będzie widać, że indeks został wypełniony. Jednak usługa wyszukiwania rozpoczyna indeksowanie natychmiast, dzięki czemu od razu można rozpocząć wyszukiwanie.

## <a name="search-your-text-files"></a>Wyszukiwanie plików tekstowych

Aby wyszukać pliki, otwórz Eksploratora wyszukiwania wewnątrz indeksu nowo utworzonej usługi wyszukiwania.

Poniżej opisano, gdzie można znaleźć Eksploratora wyszukiwania, oraz przedstawiono kilka przykładowych zapytań:

1. Przejdź do wszystkich zasobów i znajdź nowo utworzoną usługę wyszukiwania.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/exampleurl.png)

2. Wybierz indeks, aby go otworzyć.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/overview.png)

3. Wybierz pozycję **Eksplorator wyszukiwania**, aby otworzyć to narzędzie umożliwiające tworzenie aktywnych zapytań względem danych.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/indexespane.png)

4. Wybierz pozycję **Wyszukaj**, nie wpisując nic w polu ciągu zapytania. Puste zapytanie zwraca *wszystkie* dane z obiektów blob.

   ![Wyszukiwanie bez struktury](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>Wykonywanie wyszukiwania pełnotekstowego

Wprowadź ciąg **Myopia** w polu **Ciąg zapytania** i wybierz przycisk **Wyszukaj**. Ten krok uruchamia przeszukiwanie zawartości plików i zwraca podzestaw plików zawierających wyraz „Myopia”.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>Wykonywanie wyszukiwania właściwości systemu

Oprócz wyszukiwania pełnotekstowego, przy użyciu parametru `$select` można również tworzyć zapytania, które wyszukują według właściwości systemu.

Wprowadź ciąg `$select=metadata_storage_name` w polu ciągu zapytania, a następnie naciśnij klawisz **Enter**. Dzięki temu zostanie zwrócone tylko to określone pole.

Ciąg zapytania bezpośrednio modyfikuje adres URL, dlatego znaki spacji są niedozwolone. Aby wyszukać wiele pól, należy użyć przecinka, na przykład: `$select=metadata_storage_name,metadata_storage_path`

Za pomocą parametru `$select` można wyszukiwać tylko te pola, które zostały oznaczone atrybutem Pobieranie podczas definiowania indeksu.

  ![Wyszukiwanie bez struktury](media/storage-unstructured-search/metadatasearchunstructured.png)

To już koniec tego samouczka. Dysponujesz teraz możliwym do wyszukiwania zestawem danych bez struktury.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najprostszym sposobem usunięcia utworzonych zasobów jest usunięcie grupy zasobów. Usunięcie grupy zasobów spowoduje także usunięcie wszystkich zasobów uwzględnionych w tej grupie. W poniższym przykładzie usunięcie grupy zasobów powoduje usunięcie konta magazynu i samej grupy zasobów.

1. W witrynie Azure Portal przejdź do listy grup zasobów w ramach subskrypcji.
2. Wybierz grupę zasobów, którą chcesz usunąć.
3. Wybierz przycisk **Usuń grupę zasobów**, a następnie wprowadź nazwę grupy zasobów w polu usuwania.
4. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Skorzystaj z tego linku, aby dowiedzieć się więcej o indeksowaniu dokumentów za pomocą usługi Azure Search:

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage with Azure Search](../../search/search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage za pomocą usługi Azure Search)
