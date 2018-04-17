---
title: Przechwytywanie danych z usługi Event Hubs do usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Data Lake Store do przechwytywania danych z usługi Event Hubs
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9f91acf8c26fdec0c8d128f598f218cff091c7aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Użyj usługi Azure Data Lake Store do przechwytywania danych z usługi Event Hubs

Dowiedz się, jak używać usługi Azure Data Lake Store do przechwytywania danych otrzymywanych przez usługi Azure Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje dotyczące jego tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Centra zdarzeń w przestrzeni nazw**. Aby uzyskać instrukcje, zobacz [tworzenie przestrzeni nazw usługi Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Upewnij się, że konto usługi Data Lake Store i przestrzeni nazw usługi Event Hubs są w tej samej subskrypcji platformy Azure.


## <a name="assign-permissions-to-event-hubs"></a>Przypisywanie uprawnień do usługi Event Hubs

W tej sekcji utworzysz folder w ramach konta, których chcesz przechwytywać dane z usługi Event Hubs. Można również przypisać uprawnienia do usługi Event Hubs, aby ją zapisać danych do konta usługi Data Lake Store. 

1. Konta Data Lake Store, w której chcesz przechwytywać dane z usługi Event Hubs, a następnie kliknij polecenie Otwórz **Eksploratora danych**.

    ![Eksplorator danych Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Eksploratora danych usługi Data Lake Store")

2.  Kliknij przycisk **nowy Folder** , a następnie wprowadź nazwę folderu, w którym mają być przechwytywane dane.

    ![Utwórz nowy folder w usłudze Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Utwórz nowy folder w usłudze Data Lake Store")

3. Przypisz uprawnienia w katalogu głównym usługi Data Lake Store. 

    a. Kliknij przycisk **Eksploratora danych**wybierz głównego konta usługi Data Lake Store, a następnie kliknij przycisk **dostępu**.

    ![Przypisywanie uprawnień do katalogu głównego usługi Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "przypisać uprawnienia dla elementu głównego usługi Data Lake Store")

    b. W obszarze **dostępu**, kliknij przycisk **Dodaj**, kliknij przycisk **Wybieranie użytkownika lub grupy**, a następnie wyszukaj `Microsoft.EventHubs`. 

    ![Przypisywanie uprawnień do katalogu głównego usługi Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "przypisać uprawnienia dla elementu głównego usługi Data Lake Store")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**, kliknij przycisk **wybierz uprawnienia**. Ustaw **uprawnienia** do **wykonania**. Ustaw **dodać do** do **ten folder i wszystkie obiekty podrzędne**. Ustaw **dodać jako** do **wpisu uprawnienia dostępu i wpis uprawnienia domyślne**.

> [!IMPORTANT]
> Podczas tworzenia nowej hierarchii folderu do przechwytywania danych otrzymywanych przez usługi Azure Event Hubs, jest prosty sposób, aby zapewnić dostęp do folderu docelowego.  Jednak dodanie uprawnień do wszystkich obiektów podrzędnych folderu najwyższego poziomu z wielu podrzędnych plików i folderów może zająć dużo czasu.  Jeśli folder główny zawiera dużą liczbę plików i folderów, może to być szybsze można dodać **Execute** uprawnienia `Microsoft.EventHubs` indywidualnie do każdego folderu w ścieżce do folderu przeznaczenia. 

    ![Assign permissions for Data Lake Store root](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assign permissions for Data Lake Store root")

    Click **OK**.

4. Przypisywanie uprawnień do folderu w ramach konta usługi Data Lake Store, w którym ma do przechwytywania danych.

    a. Kliknij przycisk **Eksploratora danych**, wybierz folder, w ramach konta usługi Data Lake Store, a następnie kliknij przycisk **dostępu**.

    ![Przypisanie uprawnień dla folderu usługi Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "przypisanie uprawnień dla folderu usługi Data Lake Store")

    b. W obszarze **dostępu**, kliknij przycisk **Dodaj**, kliknij przycisk **Wybieranie użytkownika lub grupy**, a następnie wyszukaj `Microsoft.EventHubs`. 

    ![Przypisanie uprawnień dla folderu usługi Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "przypisanie uprawnień dla folderu usługi Data Lake Store")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**, kliknij przycisk **wybierz uprawnienia**. Ustaw **uprawnienia** do **Odczyt, zapis,** i **wykonania**. Ustaw **dodać do** do **ten folder i wszystkie obiekty podrzędne**. Wreszcie, ustaw **dodać jako** do **wpisu uprawnienia dostępu i wpis uprawnienia domyślne**.

    ![Przypisanie uprawnień dla folderu usługi Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "przypisanie uprawnień dla folderu usługi Data Lake Store")
    
    Kliknij przycisk **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Konfigurowanie usługi Event Hubs do przechwytywania danych do usługi Data Lake Store

W tej sekcji utworzysz Centrum zdarzeń w przestrzeni nazw usługi Event Hubs. Możesz również skonfigurować Centrum zdarzeń do przechwytywania danych do konta usługi Azure Data Lake Store. W tej sekcji założono, że utworzono już przestrzeń nazw usługi Event Hubs.

2. Z **omówienie** kliknij w okienku obszaru nazw usługi Event Hubs **+ Centrum zdarzeń**.

    ![Tworzenie Centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "tworzenia Centrum zdarzeń")

3. Podaj następujące wartości, aby skonfigurować usługi Event Hubs do przechwytywania danych do usługi Data Lake Store.

    ![Tworzenie Centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "tworzenia Centrum zdarzeń")

    a. Podaj nazwę Centrum zdarzeń.
    
    b. W tym samouczku, ustaw **liczba partycji** i **przechowywania wiadomości** do wartości domyślnych.
    
    c. Ustaw **przechwytywania** do **na**. Ustaw **przedział czasu** (jak często przechwytywania) i **rozmiar okna** (rozmiar danych do przechwytywania). 
    
    d. Dla **przechwytywania dostawcy**, wybierz pozycję **Azure Data Lake Store** i usługi Data Lake Store wybierz utworzony wcześniej. Dla **Data Lake ścieżka**, wprowadź nazwę folderu utworzonego w ramach konta usługi Data Lake Store. Należy podać ścieżkę względną do folderu.

    e. Pozostaw **formatów nazwy pliku przechwytywania próbki** przy użyciu wartości domyślnej. Ta opcja reguluje struktury folderów, który jest tworzony w folderze przechwytywania.

    f. Kliknij przycisk **Utwórz**.

## <a name="test-the-setup"></a>Testuj ustawienia

Teraz możesz przetestować rozwiązania przez wysyłanie danych do Centrum zdarzeń platformy Azure. Postępuj zgodnie z instrukcjami w [wysyłania zdarzeń do usługi Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Po uruchomieniu wysyłania danych, zostaną wyświetlone dane zostaną uwzględnione w usłudze Data Lake Store za pomocą struktury folderów, które można określić. Na przykład pojawić struktury folderów, jak pokazano na poniższym zrzucie ekranu w usługi Data Lake Store.

![Przykładowe dane EventHub w usłudze Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "EventHub przykładowych danych w usłudze Data Lake Store")

> [!NOTE]
> Nawet jeśli nie masz wiadomości przychodzących do usługi Event Hubs, usługa Event Hubs zapisuje pustych plików z tylko nagłówki do konta usługi Data Lake Store. Pliki są zapisywane w tym samym odstępach czasu podane podczas tworzenia usługi Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analizowanie danych w usłudze Data Lake Store

Po zaimportowaniu danych w usłudze Data Lake Store, można uruchomić zadania analitycznych do przetworzenia oraz wykonywać danych. Zobacz [USQL Avro przykład](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) o tym, jak to zrobić przy użyciu usługi Azure Data Lake Analytics.
  

## <a name="see-also"></a>Zobacz także
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
