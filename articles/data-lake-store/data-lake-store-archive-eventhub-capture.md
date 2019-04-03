---
title: Przechwytywanie danych z usługi Event Hubs do usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Przechwyć dane z usługi Event Hubs za pomocą usługi Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884514"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Przechwyć dane z usługi Event Hubs za pomocą usługi Azure Data Lake Storage Gen1

Dowiedz się, jak używać usługi Azure Data Lake Storage Gen1 do przechwytywania danych otrzymywanych przez usługi Azure Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Przestrzeń nazw usługi Event Hubs**. Aby uzyskać instrukcje, zobacz [tworzenie przestrzeni nazw usługi Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Upewnij się, że konta Data Lake Storage Gen1 i przestrzeni nazw usługi Event Hubs znajdują się w tej samej subskrypcji platformy Azure.


## <a name="assign-permissions-to-event-hubs"></a>Przypisywanie uprawnień do usługi Event Hubs

W tej sekcji utworzysz folder w ramach konta, w którym chcesz przechwytywać dane z usługi Event Hubs. Uprawnienia można także przypisać do usługi Event Hubs, tak aby mogła ona zapisywać dane do konta Data Lake Storage Gen1. 

1. Otwieranie konta Data Lake Storage Gen1, którym chcesz przechwytywać dane z usługi Event Hubs, a następnie kliknij polecenie **Eksplorator danych**.

    ![Eksplorator danych w data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 Eksplorator danych")

1.  Kliknij przycisk **nowy Folder** a następnie wprowadź nazwę folderu, w którym chcesz przechwytywać dane.

    ![Utwórz nowy folder w Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Utwórz nowy folder w Data Lake Storage Gen1")

1. Przypisz uprawnienia w katalogu głównym Data Lake Storage Gen1. 

    a. Kliknij przycisk **Eksplorator danych**wybierz katalog główny konta Data Lake Storage Gen1, a następnie kliknij przycisk **dostępu**.

    ![Przypisywanie uprawnień do katalogu głównego Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "przypisywanie uprawnień do katalogu głównego Data Lake Storage Gen1")

    b. W obszarze **dostępu**, kliknij przycisk **Dodaj**, kliknij przycisk **Wybieranie użytkownika lub grupy**, a następnie wyszukaj `Microsoft.EventHubs`. 

    ![Przypisywanie uprawnień do katalogu głównego Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "przypisywanie uprawnień do katalogu głównego Data Lake Storage Gen1")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**, kliknij przycisk **wybierz uprawnienia**. Ustaw **uprawnienia** do **wykonania**. Ustaw **dodać do** do **ten folder i wszystkie obiekty podrzędne**. Ustaw **Dodaj jako** do **wpis uprawnień dostępu i domyślny wpis uprawnień**.

    > [!IMPORTANT]
    > Podczas tworzenia nowej hierarchii folderów do przechwytywania danych otrzymywanych przez usługi Azure Event Hubs, jest to prosty sposób, aby zapewnić dostęp do folderu docelowego.  Jednakże Dodawanie uprawnień do wszystkich elementów podrzędnych folderu najwyższego poziomu za pomocą wielu podrzędnych plików i folderów może zająć dużo czasu.  Jeśli folder główny zawiera dużą liczbę plików i folderów, może to być szybsze do dodania **Execute** uprawnienia dla `Microsoft.EventHubs` pojedynczo, aby każdy folder w ścieżce do miejsca docelowego folderu. 

    ![Przypisywanie uprawnień do katalogu głównego Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "przypisywanie uprawnień do katalogu głównego Data Lake Storage Gen1")

    Kliknij przycisk **OK**.

1. Przypisz uprawnienia do folderu w ramach konta Data Lake Storage Gen1, którym chcesz przechwytywać dane.

    a. Kliknij przycisk **Eksplorator danych**, wybierz folder, w ramach konta Data Lake Storage Gen1, a następnie kliknij **dostępu**.

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "przypisać uprawnienia do folderu Data Lake Storage Gen1")

    b. W obszarze **dostępu**, kliknij przycisk **Dodaj**, kliknij przycisk **Wybieranie użytkownika lub grupy**, a następnie wyszukaj `Microsoft.EventHubs`. 

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "przypisać uprawnienia do folderu Data Lake Storage Gen1")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**, kliknij przycisk **wybierz uprawnienia**. Ustaw **uprawnienia** do **Odczyt, zapis,** i **wykonania**. Ustaw **dodać do** do **ten folder i wszystkie obiekty podrzędne**. Wreszcie, ustaw **Dodaj jako** do **wpis uprawnień dostępu i domyślny wpis uprawnień**.

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "przypisać uprawnienia do folderu Data Lake Storage Gen1")
    
    Kliknij przycisk **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Skonfiguruj usługę Event Hubs, aby przechwycić dane do programu Data Lake Storage Gen1

W tej sekcji utworzysz Centrum zdarzeń w przestrzeni nazw usługi Event Hubs. Możesz również skonfigurować Centrum zdarzeń, aby przechwytywać dane na konto usługi Azure Data Lake Storage Gen1. W tej sekcji założono, że utworzono już przestrzeni nazw usługi Event Hubs.

1. Z **Przegląd** okienku przestrzeni nazw usługi Event Hubs, kliknij polecenie **+ Centrum zdarzeń**.

    ![Tworzenie Centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "utworzyć Centrum zdarzeń")

1. Podaj następujące wartości w celu skonfigurowania usługi Event Hubs, aby przechwycić dane do programu Data Lake Storage Gen1.

    ![Tworzenie Centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "utworzyć Centrum zdarzeń")

    a. Podaj nazwę Centrum zdarzeń.
    
    b. W tym samouczku ustaw **liczba partycji** i **okres przechowywania wiadomości** do wartości domyślnych.
    
    c. Ustaw **przechwytywania** do **na**. Ustaw **przedział czasu** (jak często przechwycić) i **rozmiar okna** (rozmiar danych do przechwytywania). 
    
    d. Dla **dostawca przechwytywania**, wybierz opcję **usługi Azure Data Lake Store** a następnie wybierz pozycję konta Data Lake Storage Gen1 została utworzona wcześniej. Aby uzyskać **Data Lake ścieżki**, wprowadź nazwę folderu utworzonego w ramach konta Data Lake Storage Gen1. Wystarczy podać ścieżkę względną do folderu.

    e. Pozostaw **przykładowe formaty nazwy pliku dla przechwytywania** do wartości domyślnej. Ta opcja określa strukturę folderów, która jest tworzony w folderze przechwytywania.

    f. Kliknij pozycję **Utwórz**.

## <a name="test-the-setup"></a>Testowanie ustawień

Teraz możesz przetestować rozwiązanie za wysyłanie danych do usługi Azure Event Hub. Postępuj zgodnie z instrukcjami w artykule [wysyłanie zdarzeń do usługi Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Po rozpoczęciu wysyłania danych, możesz zobaczyć dane zostaną uwzględnione w Data Lake Storage Gen1 za pomocą folderu struktury podana. Na przykład zostanie wyświetlony strukturę folderów, jak pokazano na poniższym zrzucie ekranu na koncie usługi Data Lake Storage Gen1.

![Przykładowe dane Centrum EventHub w Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "EventHub przykładowe dane w Data Lake Storage Gen1")

> [!NOTE]
> Nawet jeśli nie masz wiadomości przychodzących do usługi Event Hubs, usługa Event Hubs zapisuje pustych plików przy użyciu tylko nagłówków na konto usługi Data Lake Storage Gen1. Pliki są zapisywane w tym samym przedziale czasowym, podane podczas tworzenia usługi Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analizowanie danych w Data Lake Storage Gen1

Gdy dane znajdują się w Data Lake Storage Gen1, można uruchomić zadania analityczne do przetworzenia i Przetwarzaj dane. Zobacz [USQL Avro przykład](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) o tym, jak to zrobić za pomocą usługi Azure Data Lake Analytics.
  

## <a name="see-also"></a>Zobacz także
* [Zabezpieczanie danych w Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
