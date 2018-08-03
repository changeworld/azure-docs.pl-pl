---
title: Przechwytywanie danych z usługi Event Hubs do usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Data Lake Store umożliwia przechwytywanie danych z usługi Event Hubs
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bda52acc12aad3cad20143c319f557f11d760c42
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435156"
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Użyj usługi Azure Data Lake Store umożliwia przechwytywanie danych z usługi Event Hubs

Dowiedz się, jak używać usługi Azure Data Lake Store umożliwia przechwytywanie danych odebranych przez usługi Azure Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje dotyczące jego tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Przestrzeń nazw usługi Event Hubs**. Aby uzyskać instrukcje, zobacz [tworzenie przestrzeni nazw usługi Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Upewnij się, że konta Data Lake Store i przestrzeni nazw usługi Event Hubs znajdują się w tej samej subskrypcji platformy Azure.


## <a name="assign-permissions-to-event-hubs"></a>Przypisywanie uprawnień do usługi Event Hubs

W tej sekcji utworzysz folder w ramach konta, w którym chcesz przechwytywać dane z usługi Event Hubs. Uprawnienia można także przypisać do usługi Event Hubs, tak aby mogła ona zapisywać dane do konta Data Lake Store. 

1. Otwórz konto Data Lake Store, w którym chcesz przechwytywać dane z usługi Event Hubs, a następnie kliknij polecenie **Eksplorator danych**.

    ![Eksplorator danych Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Eksplorator danych Data Lake Store")

1.  Kliknij przycisk **nowy Folder** a następnie wprowadź nazwę folderu, w którym chcesz przechwytywać dane.

    ![Utwórz nowy folder w Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Utwórz nowy folder w Data Lake Store")

1. Przypisz uprawnienia w katalogu głównym Data Lake Store. 

    a. Kliknij przycisk **Eksplorator danych**wybierz katalog główny konta Data Lake Store, a następnie kliknij przycisk **dostępu**.

    ![Przypisywanie uprawnień do głównych Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "przypisać uprawnienia dla katalogu głównego Data Lake Store")

    b. W obszarze **dostępu**, kliknij przycisk **Dodaj**, kliknij przycisk **Wybieranie użytkownika lub grupy**, a następnie wyszukaj `Microsoft.EventHubs`. 

    ![Przypisywanie uprawnień do głównych Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "przypisać uprawnienia dla katalogu głównego Data Lake Store")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**, kliknij przycisk **wybierz uprawnienia**. Ustaw **uprawnienia** do **wykonania**. Ustaw **dodać do** do **ten folder i wszystkie obiekty podrzędne**. Ustaw **Dodaj jako** do **wpis uprawnień dostępu i domyślny wpis uprawnień**.

    > [!IMPORTANT]
    > Podczas tworzenia nowej hierarchii folderów do przechwytywania danych otrzymywanych przez usługi Azure Event Hubs, jest to prosty sposób, aby zapewnić dostęp do folderu docelowego.  Jednakże Dodawanie uprawnień do wszystkich elementów podrzędnych folderu najwyższego poziomu za pomocą wielu podrzędnych plików i folderów może zająć dużo czasu.  Jeśli folder główny zawiera dużą liczbę plików i folderów, może to być szybsze do dodania **Execute** uprawnienia dla `Microsoft.EventHubs` pojedynczo, aby każdy folder w ścieżce do miejsca docelowego folderu. 

    ![Przypisywanie uprawnień do głównych Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "przypisać uprawnienia dla katalogu głównego Data Lake Store")

    Kliknij przycisk **OK**.

1. Przypisz uprawnienia do folderu w ramach konta Data Lake Store, w którym chcesz przechwytywać dane.

    a. Kliknij przycisk **Eksplorator danych**, wybierz folder, w ramach konta Data Lake Store, a następnie kliknij przycisk **dostępu**.

    ![Przypisywanie uprawnień do folderu Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "przypisać uprawnienia do folderu Data Lake Store")

    b. W obszarze **dostępu**, kliknij przycisk **Dodaj**, kliknij przycisk **Wybieranie użytkownika lub grupy**, a następnie wyszukaj `Microsoft.EventHubs`. 

    ![Przypisywanie uprawnień do folderu Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "przypisać uprawnienia do folderu Data Lake Store")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**, kliknij przycisk **wybierz uprawnienia**. Ustaw **uprawnienia** do **Odczyt, zapis,** i **wykonania**. Ustaw **dodać do** do **ten folder i wszystkie obiekty podrzędne**. Wreszcie, ustaw **Dodaj jako** do **wpis uprawnień dostępu i domyślny wpis uprawnień**.

    ![Przypisywanie uprawnień do folderu Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "przypisać uprawnienia do folderu Data Lake Store")
    
    Kliknij przycisk **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Skonfiguruj usługę Event Hubs, aby przechwycić dane do programu Data Lake Store

W tej sekcji utworzysz Centrum zdarzeń w przestrzeni nazw usługi Event Hubs. Możesz również skonfigurować Centrum zdarzeń, aby przechwytywać dane na konto usługi Azure Data Lake Store. W tej sekcji założono, że utworzono już przestrzeni nazw usługi Event Hubs.

1. Z **Przegląd** okienku przestrzeni nazw usługi Event Hubs, kliknij polecenie **+ Centrum zdarzeń**.

    ![Tworzenie Centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "utworzyć Centrum zdarzeń")

1. Podaj następujące wartości w celu skonfigurowania usługi Event Hubs, aby przechwycić dane do programu Data Lake Store.

    ![Tworzenie Centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "utworzyć Centrum zdarzeń")

    a. Podaj nazwę Centrum zdarzeń.
    
    b. W tym samouczku ustaw **liczba partycji** i **okres przechowywania wiadomości** do wartości domyślnych.
    
    c. Ustaw **przechwytywania** do **na**. Ustaw **przedział czasu** (jak często przechwycić) i **rozmiar okna** (rozmiar danych do przechwytywania). 
    
    d. Aby uzyskać **dostawca przechwytywania**, wybierz opcję **usługi Azure Data Lake Store** i wybierz opcję Data Lake Store utworzone wcześniej. Aby uzyskać **Data Lake ścieżki**, wprowadź nazwę folderu utworzonego w ramach konta Data Lake Store. Wystarczy podać ścieżkę względną do folderu.

    e. Pozostaw **przykładowe formaty nazwy pliku dla przechwytywania** do wartości domyślnej. Ta opcja określa strukturę folderów, która jest tworzony w folderze przechwytywania.

    f. Kliknij pozycję **Utwórz**.

## <a name="test-the-setup"></a>Testowanie ustawień

Teraz możesz przetestować rozwiązanie za wysyłanie danych do usługi Azure Event Hub. Postępuj zgodnie z instrukcjami w artykule [wysyłanie zdarzeń do usługi Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Po rozpoczęciu wysyłania danych, zobaczysz dane zostaną uwzględnione w Data Lake Store przy użyciu struktury folderów, które określono. Na przykład zostanie wyświetlony strukturę folderów, jak pokazano na poniższym zrzucie ekranu w usługi Data Lake Store.

![Przykładowe dane Centrum EventHub w Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "EventHub przykładowe dane w Data Lake Store")

> [!NOTE]
> Nawet jeśli nie masz wiadomości przychodzących do usługi Event Hubs, usługa Event Hubs zapisuje pustych plików przy użyciu tylko nagłówków na konto usługi Data Lake Store. Pliki są zapisywane w tym samym przedziale czasowym, podane podczas tworzenia usługi Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analizowanie danych w Data Lake Store

Gdy dane znajdują się w Data Lake Store, można uruchomić zadania analityczne do przetwarzania i Przetwarzaj dane. Zobacz [USQL Avro przykład](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) o tym, jak to zrobić za pomocą usługi Azure Data Lake Analytics.
  

## <a name="see-also"></a>Zobacz także
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Kopiowanie danych z magazynu obiektów blob usługi Azure Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
