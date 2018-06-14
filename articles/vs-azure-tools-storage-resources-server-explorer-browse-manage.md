---
title: Przeglądanie i zarządzanie zasobami magazynu za pomocą Eksploratora serwera | Dokumentacja firmy Microsoft
description: Przeglądanie i zarządzanie zasobami magazynowania za pomocą Eksploratora serwera
services: visual-studio-online
author: ghogen
manager: douge
assetId: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/24/2017
ms.author: ghogen
ms.openlocfilehash: 0beeb8fb7f7e46db97e179f3eacf3c68dd92cff3
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798581"
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Przeglądanie i zarządzanie zasobami magazynu za pomocą Eksploratora serwera

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Przegląd

Jeśli po zainstalowaniu narzędzi Azure dla programu Microsoft Visual Studio można wyświetlić obiektów blob, kolejki i danych z tabeli z kont magazynu dla platformy Azure. Azure **magazynu** węzeł w Eksploratorze serwera zawiera dane konta emulatora magazynu lokalnego i innymi kontami magazynu Azure.

Aby wyświetlić w Eksploratorze serwera w programie Visual Studio na pasku menu, wybierz **widoku** > **Eksploratora serwera**. **Magazynu** węzeł zawiera wszystkich kont magazynu, które istnieją w ramach każdej subskrypcji platformy Azure lub certyfikat, który jest połączony. Jeśli nie ma konta magazynu, można dodać go zgodnie z instrukcjami [dalszej części tego artykułu](#add-storage-accounts-by-using-server-explorer).

Od wersji 2.7 zestawu SDK platformy Azure, również służy Eksplorator chmury do wyświetlania i zarządzania zasobami platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure zarządzanie zasobami za pomocą Eksploratora chmury](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Wyświetlanie i zarządzanie zasobami magazynu w programie Visual Studio

W Eksploratorze serwera automatycznie pokazuje listę tabel, kolejek i obiektów blob na koncie emulatora magazynu. Konto emulatora magazynu znajduje się w Eksploratorze serwera w obszarze **magazynu** węzeł jako **programowanie** węzła.

Aby wyświetlić zasoby konta emulatora magazynu, rozwiń węzeł **programowanie** węzła. Jeśli emulator magazynu nie została uruchomiona po rozwinięciu **programowanie** węzła, rozpocznie automatycznie. Ten proces może potrwać kilka sekund. Można kontynuować pracę w innych obszarach programu Visual Studio podczas uruchamiania emulatora magazynu.

Aby wyświetlić zasoby na koncie magazynu, rozwiń węzeł konta magazynu w Eksploratorze serwera, w której występuje **obiekty BLOB**, **kolejek**, i **tabel** węzłów.

## <a name="work-with-blob-resources"></a>Praca z zasobami obiektów blob

**Obiekty BLOB** węzeł zostanie wyświetlona lista kontenerami dla wybranego konta magazynu. Kontenerów obiektów blob zawierają pliki obiektu blob, a te obiekty BLOB można organizować w folderach i podfolderach. Aby uzyskać więcej informacji, zobacz [jak używać magazynu obiektów Blob z .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>Aby utworzyć kontener obiektów blob

1. Otwórz menu skrótów **obiekty BLOB** węzeł, a następnie wybierz **Tworzenie kontenera obiektów Blob**.
1. W **Tworzenie kontenera obiektów Blob** okna dialogowego wprowadź nazwę nowego kontenera.  
1. Wybierz opcję Wprowadź na klawiaturze, lub można kliknij lub naciśnij polecenie poza pole nazwy, aby zapisać kontenera obiektów blob.

   > [!NOTE]
   > Nazwa kontenera obiektów blob musi rozpoczynać się od liczby (0 – 9) lub małą literę (a-z).

### <a name="to-delete-a-blob-container"></a>Można usunąć kontenera obiektów blob

Otwórz menu skrótów dla kontenera obiektów blob, który chcesz usunąć, a następnie wybierz **usunąć**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Aby wyświetlić listę elementów w kontenerze obiektów blob

Otwórz menu skrótów nazwa kontenera obiektów blob na liście, a następnie wybierz **Otwórz**.

Podczas wyświetlania zawartości kontenera obiektów blob, zostanie wyświetlony na karcie znany jako widok kontenera obiektów blob.

![Widok kontenera obiektów blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

Mogą wykonywać następujące operacje na obiektach blob, za pomocą przycisków w prawym górnym rogu widoku kontenera obiektów blob:

* Wprowadź wartość filtru i zastosować je.
* Odśwież listę obiektów blob w kontenerze.
* Przekaż plik.
* Usuń obiekt blob. (Usuwanie pliku kontenera obiektów blob nie powoduje usunięcia jego pliku. Powoduje tylko usunięcie go z kontenera obiektów blob.)
* Otwórz obiektu blob.
* Zapisywanie obiektu blob na komputerze lokalnym.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Aby utworzyć folder lub podfolder w kontenerze obiektów blob

1. Wybierz kontener obiektów blob w Eksploratorze chmury. W oknie kontenera wybierz **przekazania obiektu Blob** przycisku.

1. W **Przekaż nowy plik** okno dialogowe, wybierz opcję **Przeglądaj** przycisk, aby określić plik, który chcesz przekazać, a następnie wprowadź nazwę folderu w **Folder (opcjonalnie)** pole.

   ![Przekazywanie pliku do folderu obiektów blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Możesz dodać podfoldery w folderach kontenera, wykonując kroki tego samego. Jeśli nie określono nazwy folderu, plik jest przekazywany na najwyższym poziomie kontenera obiektów blob. Plik znajduje się w folderze określonym w kontenerze.

   ![Folderze dodanym do kontenera obiektów blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Kliknij dwukrotnie folder lub wybierz Enter, aby wyświetlić zawartość tego folderu. Podczas pracy w folderze kontenera, możesz wrócić do katalogu głównego kontenera, wybierając **Otwórz katalog nadrzędny** przycisk (strzałka).

### <a name="to-delete-a-container-folder"></a>Aby usunąć folder kontenera

Usuń wszystkie pliki w folderze.

Ponieważ folderów w kontenerów obiektów blob są wirtualnych folderów, nie można utworzyć pusty folder. Również nie można usunąć folderu, aby usunąć jego zawartość pliku, ale zamiast tego należy usunąć całą zawartość folderu można usunąć folderu, do samej siebie.

### <a name="to-filter-blobs-in-a-container"></a>Aby filtrować obiekty BLOB w kontenerze

Można filtrować obiektów blob, które są wyświetlane, określając Wspólny prefiks.

Na przykład, jeśli wprowadzony prefiks **hello** w polu tekstowym filtru, a następnie wybierz **Execute** (**!**) są wyświetlane tylko obiekty BLOB, które zaczynają się od "hello" przycisku.

![Pole tekstowe filtru](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

Pole tekstowe filtru jest rozróżniana wielkość liter i nie obsługuje filtrowania z symbolami wieloznacznymi. Obiekty BLOB można filtrować tylko według prefiksu. Prefiks może zawierać ogranicznika, jeśli używasz ogranicznik do organizowania obiektów blob w hierarchii wirtualnego. Na przykład filtrowanie prefiks "HelloFabric /" zwraca wszystkie obiekty BLOB, które zaczynają się od tego ciągu.

### <a name="to-download-blob-data"></a>Aby pobrać dane obiektów blob

W Eksploratorze chmury użyj jednej z następujących metod:

* Otwórz menu skrótów dla co najmniej jednego obiektu blob, a następnie wybierz **Otwórz**.
* Wybierz nazwę obiektu blob, a następnie wybierz **Otwórz** przycisku.
* Kliknij dwukrotnie nazwę obiektu blob.

Postęp pobierania obiektu blob jest wyświetlana w **dziennika aktywności platformy Azure** okna.

Otwiera obiekt blob w domyślny edytor dla tego typu pliku. Jeśli system operacyjny rozpoznaje typ pliku, plik zostanie otwarty w aplikacji zainstalowanej lokalnie. W przeciwnym razie zostanie wyświetlony monit o wybranie aplikacji, która jest odpowiednia dla danego typu pliku obiektu blob. Pliku lokalnego, który jest tworzony podczas pobierania obiektu blob jest oznaczony jako tylko do odczytu.

Dane obiektu blob jest buforowane lokalnie i sprawdza, czy czas ostatniej modyfikacji obiektu blob w magazynie obiektów Blob Azure. Jeśli od czasu ostatniego pobrania został zaktualizowany obiektu blob, zostanie pobrana ponownie. W przeciwnym razie obiektu blob jest ładowany z dysku.

Domyślnie obiektu blob jest pobierany do katalogu tymczasowego. Aby pobrać obiekty BLOB do określonego katalogu, otwórz menu skrótów dla nazwy wybranego obiektu blob, a następnie wybierz **Zapisz jako**. Po zapisaniu obiektu blob w ten sposób pliku obiektu blob nie jest otwarty i pliku lokalnego jest tworzony z atrybutami odczytu/zapisu.

### <a name="to-upload-blobs"></a>Aby przekazać obiektów blob

Aby przekazać obiektów blob, wybierz **przekazania obiektu Blob** przycisku, gdy kontener jest otwarty w celu wyświetlenia w widoku kontenera obiektów blob.

Można wybrać jeden lub więcej plików do przekazania, a może przekazywać plików dowolnego typu. **Dziennika aktywności platformy Azure** okno będzie wyświetlany postęp przekazywania. Aby uzyskać więcej informacji na temat pracy z danymi obiektów blob, zobacz [jak używać magazynu obiektów Blob platformy Azure w programie .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Aby wyświetlić dzienniki przesyłane do obiektów blob

Jeśli używasz diagnostyki Azure do rejestrowania danych z aplikacji platformy Azure i dzienniki zostały przeniesione do konta magazynu, zostanie wyświetlone kontenery tworzone dla tych dzienników Azure. Wyświetlanie dzienników w Eksploratorze serwera jest łatwo zidentyfikować problemy z aplikacją, zwłaszcza, jeśli jest został wdrożony na platformie Azure.

Aby uzyskać więcej informacji na temat diagnostyki Azure, zobacz [zbierania danych rejestrowania za pomocą diagnostyki Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Aby uzyskać adres URL dla obiektu blob

Otwórz menu skrótów obiektu blob, a następnie wybierz **URL kopii**.

### <a name="to-edit-a-blob"></a>Aby edytować obiektu blob

Wybierz obiekt blob, a następnie wybierz **Otwórz Blob** przycisku.

Plik jest pobierany do tymczasowej lokalizacji i otworzyć na komputerze lokalnym. Po wprowadzeniu zmian ponownie przekazania obiektu blob.

## <a name="work-with-queue-resources"></a>Praca z kolejki zasobów

Magazyn kolejek usługi znajdują się w koncie magazynu platformy Azure. Można używać ich, aby umożliwić chmury usługi ról do komunikowania się ze sobą oraz z innymi usługami przez mechanizm przekazywania komunikatów. Można programowo dostępu do kolejki za pośrednictwem usługi w chmurze, jak i za pośrednictwem usługi sieci web dla klientów zewnętrznych. Kolejkę można także przejść bezpośrednio, za pomocą Eksploratora serwera w programie Visual Studio.

Tworzenie usługi w chmurze, która używa kolejek, można użyć do tworzenia kolejek i pracować z nimi interaktywnie podczas tworzenia i testowania kodu programu Visual Studio.

W Eksploratorze serwera można wyświetlić kolejek na koncie magazynu, tworzenie i usuwanie kolejek, otwórz kolejki, aby wyświetlić jego wiadomości i Dodaj wiadomości do kolejki. Podczas otwierania kolejki w celu wyświetlenia można wyświetlić poszczególne wiadomości, a w kolejce za pomocą przycisków w lewym górnym rogu można wykonać następujące czynności:

* Odśwież widok kolejki.
* Dodawanie komunikatu do kolejki.
* Usuwania z kolejki komunikatów znajdujących się na górze.
* Wyczyść cały kolejki.

Na poniższej ilustracji przedstawiono kolejki, która zawiera dwa komunikaty:

![Wyświetlanie kolejki](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Aby uzyskać więcej informacji na temat magazynu usług kolejek, zobacz [Rozpoczynanie pracy z magazynem kolejek Azure przy użyciu platformy .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Aby uzyskać informacje na temat usługi sieci web uzyskać magazynu usług kolejek, zobacz [pojęcia dotyczące usługi kolejki](http://go.microsoft.com/fwlink/?LinkId=264788). Aby dowiedzieć się, jak wysyłać wiadomości do kolejki usługi magazynu przy użyciu programu Visual Studio, zobacz [wysyłania wiadomości do kolejki usługi magazynu](https://msdn.microsoft.com/library/azure/jj649344.aspx).

> [!NOTE]
> Magazyn kolejek usługi różni się od kolejek usługi Azure Service Bus. Aby uzyskać więcej informacji na temat kolejek usługi Service Bus, zobacz [kolejek usługi Service Bus, tematy i subskrypcje](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>Praca z tabeli zasobów

Usługa Azure Table Storage służy do przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z wewnątrz lub na zewnątrz w chmurze Azure. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

### <a name="to-create-a-table"></a>Aby utworzyć tabelę

1. W Eksploratorze chmury, wybierz **tabel** konta magazynu, a następnie wybierz węzeł **Create Table**.
1. W **Create Table** okna dialogowego wprowadź nazwę tabeli.

### <a name="to-view-table-data"></a>Aby wyświetlić dane w tabeli

1. W Eksploratorze chmury Otwórz **Azure** węzeł, a następnie otwórz **magazynu** węzła.
1. Otwórz węzeł konta magazynu, które są zainteresowani, a następnie otwórz **tabel** węzeł umożliwia wyświetlenie listy tabel dla konta magazynu.
1. Otwórz menu skrótów dla tabeli, a następnie wybierz **widok tabeli**.

    ![Tabeli platformy Azure, w Eksploratorze rozwiązań](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

Tabela jest zorganizowana według jednostek (wyświetlane w wierszach) i właściwości (wyświetlane kolumny). Na przykład dalej ilustracji przedstawiono jednostek projektanta tabel na liście.

### <a name="to-edit-table-data"></a>Aby edytować dane tabeli

Projektanta tabel, otwórz menu skrótów dla jednostki (pojedynczy wiersz) lub właściwości (jedną komórkę), a następnie wybierz **Edytuj**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

Jednostki w jednej tabeli nie są wymagane do mają ten sam zestaw właściwości (kolumny). Mieć na uwadze następujące ograniczenia dotyczące wyświetlania i edytowania danych tabeli:

* Nie można wyświetlić lub edytować dane binarne (`type byte[]`), ale można go zapisać w tabeli.
* Nie można edytować **PartitionKey** lub **RowKey** wartości, ponieważ Magazyn tabel na platformie Azure nie obsługuje tej operacji.
* Nie można utworzyć właściwość o nazwie **sygnatury czasowej**. Usługi magazynu Azure używać właściwości o tej nazwie.
* Po wprowadzeniu **DateTime** wartości, należy wykonać formacie, który jest odpowiedni do ustawienia regionalne i językowe komputera (na przykład MM/DD/RRRR GG [AM | PM] dla Stanów Zjednoczonych w języku angielskim).

### <a name="to-add-entities"></a>Aby dodać jednostek

1. Wybierz projektanta tabel **Dodaj jednostki** przycisku.

    ![Dodawanie przycisku jednostki](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. W **Dodaj jednostki** okna dialogowego wprowadź wartości **PartitionKey** i **RowKey** właściwości.

    ![Dodaj jednostki — okno dialogowe](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Należy dokładnie wprowadzić wartości. Nie można ich zmienić po zamknięciu okna dialogowego, chyba że w przypadku usunięcia jednostki i dodaj go ponownie.

### <a name="to-filter-entities"></a>Aby filtrować jednostek

Można dostosować zestaw jednostek, które są wyświetlane w tabeli, jeśli używasz konstruktora zapytań.

1. Aby otworzyć Konstruktor kwerend, otwórz tabelę do wyświetlenia.
1. Wybierz **konstruktora zapytań** przycisk na pasku narzędzi w widoku tabeli.

    **Konstruktora zapytań** zostanie wyświetlone okno dialogowe. Na poniższej ilustracji przedstawiono kwerendę, która jest tworzona w konstruktora zapytań.

    ![Konstruktor kwerend](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. Po zakończeniu tworzenia zapytania, zamknij okno dialogowe. Tekst w formularzu, zapytania jest wyświetlana w polu tekstowym jako filtr usługi danych WCF.
1. Aby uruchomić zapytanie, wybierz ikonę zielonym trójkątem.

Można również filtrować jednostki dane wyświetlane przez projektanta tabel wpisanie ciąg filtru usługi danych WCF bezpośrednio w polu tekstowym filtru. Tego rodzaju ciągu jest podobna do klauzuli SQL WHERE, ale jest wysyłane do serwera jako żądanie HTTP. Aby uzyskać informacje dotyczące sposobu tworzenia ciągów filtru, zobacz [konstruowania ciągach filtru dla projektanta tabel](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Na poniższej ilustracji przedstawiono przykład ciągu prawidłowym filtrem:

![Ciąg filtru](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Odświeżanie magazynu danych

Eksploratora serwera łączy się lub pobiera dane z konta magazynu, operacji może potrwać kilka minut na zakończenie. Jeśli nie można połączyć z Eksploratora serwera, operacja może upłynął limit czasu. Gdy dane są pobierane, można nadal działają w innych częściach programu Visual Studio. Aby anulować operację, jeśli trwa zbyt długo, wybierz **Zatrzymaj odświeżanie** przycisk na pasku narzędzi Eksploratora serwera.

### <a name="to-refresh-blob-container-data"></a>Aby odświeżyć dane w kontenerze obiektów blob

* Wybierz **obiekty BLOB** węźle poniżej **magazynu**, a następnie wybierz **Odśwież** przycisk na pasku narzędzi Eksploratora serwera.
* Aby odświeżyć listę obiektów blob jest wyświetlana, wybierz **Execute** przycisku.

### <a name="to-refresh-table-data"></a>Aby odświeżyć dane tabeli

* Wybierz **tabel** węźle poniżej **magazynu**, a następnie wybierz **Odśwież** przycisk na pasku narzędzi Eksploratora serwera.
* Aby odświeżyć listę jednostek jest wyświetlany w projektanta tabel, zaznacz **Execute** przycisk przy użyciu projektanta tabeli.

### <a name="to-refresh-queue-data"></a>Aby odświeżyć dane kolejki

Wybierz **kolejek** węźle poniżej **magazynu**, a następnie wybierz **Odśwież** przycisk na pasku narzędzi Eksploratora serwera.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Aby odświeżyć wszystkie elementy na koncie magazynu

Wybierz nazwę konta, a następnie wybierz **Odśwież** przycisk na pasku narzędzi Eksploratora serwera.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Dodawanie konta magazynu za pomocą Eksploratora serwera

Istnieją dwa sposoby dodawania konta magazynu za pomocą Eksploratora serwera. Można utworzyć konta magazynu w ramach subskrypcji platformy Azure, lub można dołączyć istniejącego konta magazynu.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Aby utworzyć konto magazynu przy użyciu Eksploratora serwera

1. W Eksploratorze serwera Otwórz menu skrótów **magazynu** węzeł, a następnie wybierz **Utwórz konto magazynu**.

1. W **Utwórz konto magazynu** oknie dialogowym Wybierz lub wprowadź następujące informacje:

   * Subskrypcja platformy Azure, do której chcesz dodać konto magazynu.
   * Nazwa, która ma być używany dla nowego konta magazynu.
   * Region lub grupę koligacji, (na przykład zachodnie stany USA lub Azja Wschodnia).
   * Typ replikacji, którego chcesz użyć dla konta magazynu, takich jak magazyn lokalnie nadmiarowy.

   ![Tworzenie konta usługi Azure Storage](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Wybierz pozycję **Utwórz**.

Nowe konto magazynu jest wyświetlana w **magazynu** listy w Eksploratorze rozwiązań.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Aby dołączyć istniejącego konta magazynu za pomocą Eksploratora serwera

1. W Eksploratorze serwera Otwórz menu skrótów dla platformy Azure **magazynu** węzeł, a następnie wybierz **Dołącz zewnętrzną usługę Storage**.

    ![Dodawanie istniejącego konta magazynu](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. W **Utwórz konto magazynu** oknie dialogowym Wybierz lub wprowadź następujące informacje:

   * Nazwa istniejącego konta magazynu, który chcesz dołączyć.
   * Klucz dla wybranego konta magazynu. Ta wartość jest zazwyczaj dostarczany po wybraniu konta magazynu. Visual Studio do zapamiętania klucz konta magazynu, wybierz opcję **klucz konta Zapamiętaj** pole wyboru.
   * Protokół używany do nawiązania połączenia konta magazynu, takich jak HTTP, HTTPS lub niestandardowe punktu końcowego. Aby uzyskać więcej informacji na temat niestandardowe punkty końcowe, zobacz [jak skonfigurować parametry połączenia](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>Aby wyświetlić dodatkowej punkty końcowe

Jeśli utworzono konto magazynu przy użyciu **dostęp do odczytu z magazynu geograficznie nadmiarowego** opcji replikacji można wyświetlić jego dodatkowej punkty końcowe, otwierając menu skrótów dla nazwy konta i następnie wybierz **właściwości**.

![Punkty końcowe dodatkowej pamięci masowej](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Aby usunąć konto magazynu z poziomu Eksploratora serwera

W Eksploratorze serwera Otwórz menu skrótów dla nazwy konta, a następnie wybierz **usunąć**. 

Usunięcie konta magazynu, zostaną również usunięte wszystkie zapisane informacje klucza dla tego konta.

Jeśli usuniesz konto magazynu z poziomu Eksploratora serwera nie wpływa na koncie magazynu lub dane, które zawiera. Po prostu usuwa odwołanie z Eksploratora serwera. Aby trwale usunąć konto magazynu, należy użyć [portalu Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat sposobu korzystania z usług Azure storage, zobacz [dostęp do usług magazynu Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).
