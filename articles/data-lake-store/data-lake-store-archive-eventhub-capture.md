---
title: Przechwytywanie danych z centrów zdarzeń do usługi Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Przechwytywanie danych z centrów zdarzeń za pomocą usługi Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265664"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Przechwytywanie danych z centrów zdarzeń za pomocą usługi Azure Data Lake Storage Gen1

Dowiedz się, jak używać usługi Azure Data Lake Storage Gen1 do przechwytywania danych odebranych przez usługi Azure Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Obszar nazw Centrów zdarzeń**. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru nazw Centrów zdarzeń](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Upewnij się, że konto Data Lake Storage Gen1 i obszar nazw usługi Event Hubs znajdują się w tej samej subskrypcji platformy Azure.


## <a name="assign-permissions-to-event-hubs"></a>Przypisywanie uprawnień do centrów zdarzeń

W tej sekcji należy utworzyć folder na koncie, na którym chcesz przechwycić dane z centrum zdarzeń. Można również przypisać uprawnienia do usługi Event Hubs, dzięki czemu można zapisywać dane na koncie Data Lake Storage Gen1. 

1. Otwórz konto Data Lake Storage Gen1, na którym chcesz przechwycić dane z Centrów zdarzeń, a następnie kliknij **Eksplorator danych**.

    ![Eksplorator danych Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Eksplorator danych Data Lake Storage Gen1")

1.  Kliknij **pozycję Nowy folder,** a następnie wprowadź nazwę folderu, w którym chcesz przechwycić dane.

    ![Tworzenie nowego folderu w umiań magazynu usługi Data Lake](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Tworzenie nowego folderu w umiań magazynu usługi Data Lake")

1. Przypisz uprawnienia w katalogu głównym usługi Data Lake Storage Gen1. 

    a. Kliknij **pozycję Eksplorator danych**, wybierz katalog główny konta Data Lake Storage Gen1, a następnie kliknij pozycję **Dostęp**.

    ![Przypisywanie uprawnień dla katalogu głównego usługi Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Przypisywanie uprawnień dla katalogu głównego usługi Data Lake Storage Gen1")

    b. W obszarze **Dostęp**kliknij pozycję **Dodaj**, kliknij `Microsoft.EventHubs`pozycję Wybierz użytkownika lub **Grupuj**, a następnie wyszukaj . 

    ![Przypisywanie uprawnień dla katalogu głównego usługi Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Przypisywanie uprawnień dla katalogu głównego usługi Data Lake Storage Gen1")
    
    Kliknij **pozycję Wybierz**.

    d. W obszarze **Przypisywanie uprawnień**kliknij pozycję **Wybierz uprawnienia**. Ustaw **uprawnienia** do **wykonania**. Ustaw **dodaj do** tego **folderu i wszystkie dzieci**. Ustaw **pozycję Dodaj jako** wpis uprawnienia dostępu i **domyślny wpis uprawnień**.

    > [!IMPORTANT]
    > Podczas tworzenia nowej hierarchii folderów do przechwytywania danych odebranych przez usługi Azure Event Hubs jest to prosty sposób na zapewnienie dostępu do folderu docelowego.  Jednak dodawanie uprawnień do wszystkich obrażeń folderu najwyższego poziomu z wieloma plikami i folderami podrzędnymi może zająć dużo czasu.  Jeśli folder główny zawiera dużą liczbę plików i folderów, **Execute** może być `Microsoft.EventHubs` szybsze dodanie uprawnień wykonywania indywidualnie do każdego folderu w ścieżce do folderu docelowego. 

    ![Przypisywanie uprawnień dla katalogu głównego usługi Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Przypisywanie uprawnień dla katalogu głównego usługi Data Lake Storage Gen1")

    Kliknij przycisk **OK**.

1. Przypisz uprawnienia do folderu w obszarze konta Data Lake Storage Gen1, na którym chcesz przechwytywać dane.

    a. Kliknij **pozycję Eksplorator danych**, wybierz folder na koncie Data Lake Storage Gen1, a następnie kliknij pozycję **Dostęp**.

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Przypisywanie uprawnień do folderu Data Lake Storage Gen1")

    b. W obszarze **Dostęp**kliknij pozycję **Dodaj**, kliknij `Microsoft.EventHubs`pozycję Wybierz użytkownika lub **Grupuj**, a następnie wyszukaj . 

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Przypisywanie uprawnień do folderu Data Lake Storage Gen1")
    
    Kliknij **pozycję Wybierz**.

    d. W obszarze **Przypisywanie uprawnień**kliknij pozycję **Wybierz uprawnienia**. Ustaw **uprawnienia** do **odczytu, zapisu** i **wykonywania**. Ustaw **dodaj do** tego **folderu i wszystkie dzieci**. Na koniec ustaw **dodaj jako** wpis uprawnienia dostępu **i domyślny wpis uprawnień**.

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Przypisywanie uprawnień do folderu Data Lake Storage Gen1")
    
    Kliknij przycisk **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurowanie centrów zdarzeń do przechwytywania danych do usługi Data Lake Storage Gen1

W tej sekcji utworzysz Centrum zdarzeń w obszarze nazw Centrum zdarzeń. Centrum zdarzeń można również skonfigurować do przechwytywania danych na koncie usługi Azure Data Lake Storage Gen1. W tej sekcji przyjęto założenie, że utworzono już obszar nazw centrów zdarzeń.

1. W okienku **Przegląd** obszaru nazw Centra zdarzeń kliknij pozycję **+ Centrum zdarzeń**.

    ![Tworzenie centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Tworzenie centrum zdarzeń")

1. Podaj następujące wartości, aby skonfigurować usługi Event Hubs do przechwytywania danych do usługi Data Lake Storage Gen1.

    ![Tworzenie centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Tworzenie centrum zdarzeń")

    a. Podaj nazwę Centrum zdarzeń.
    
    b. W tym samouczku ustaw **liczbę partycji** i **przechowywanie wiadomości** na wartości domyślne.
    
    d. Ustaw **przechwytywanie** **na włączone**. Ustaw **okno czasu** (jak często przechwytywać) i **Okno rozmiaru** (rozmiar danych do przechwycenia). 
    
    d. W przypadku **dostawcy przechwytywania**wybierz **pozycję Azure Data Lake Store,** a następnie wybierz konto Data Lake Storage Gen1 utworzone wcześniej. W przypadku **ścieżki usługi Data Lake**wprowadź nazwę folderu utworzonego na koncie Data Lake Storage Gen1. Wystarczy podać ścieżkę względną do folderu.

    e. Pozostaw **formaty nazw plików przechwytywania próbki** do wartości domyślnej. Ta opcja reguluje strukturę folderów utworzoną w folderze przechwytywania.

    f. Kliknij przycisk **Utwórz**.

## <a name="test-the-setup"></a>Testowanie konfiguracji

Teraz można przetestować rozwiązanie, wysyłając dane do usługi Azure Event Hub. Postępuj zgodnie z instrukcjami w [witrynie Wyślij zdarzenia do usługi Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Po rozpoczęciu wysyłania danych zobaczysz dane odzwierciedlone w umiań magazynowania usługi Data Lake przy użyciu określonej struktury folderów. Na przykład na koncie Usługi Data Lake Storage Gen1 zostanie wyświetlona struktura folderów, jak pokazano na poniższym zrzucie ekranu.

![Przykładowe dane usługi EventHub w umiar pamięci masowej usługi Data Lake Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Przykładowe dane usługi EventHub w umiar pamięci masowej usługi Data Lake Gen1")

> [!NOTE]
> Nawet jeśli nie masz wiadomości pochodzących z Centrum zdarzeń, Centra zdarzeń zapisuje puste pliki tylko nagłówki do konta Data Lake Storage Gen1. Pliki są zapisywane w tym samym przedziale czasu, który został podany podczas tworzenia centrum zdarzeń.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analizowanie danych w umiar pamięci masowej usługi Data Lake Gen1

Gdy dane znajdują się w u źródła danych Lake Storage Gen1, można uruchomić zadania analityczne do przetwarzania i crunch danych. Zobacz [przykład programu USQL Avro,](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) jak to zrobić za pomocą usługi Azure Data Lake Analytics.
  

## <a name="see-also"></a>Zobacz też
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
