---
title: Rejestrowanie danych z usługi Azure Data Lake Storage Gen1 w wykaz wykaz cerze | Dokumenty firmy Microsoft
description: Rejestrowanie danych z usługi Azure Data Lake Storage Gen1 w wykazie danych platformy Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd887560c0011fb1ec2141e33f02f7e3d8a39c81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60196616"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Rejestrowanie danych z usługi Azure Data Lake Storage Gen1 w wykazie danych platformy Azure
W tym artykule dowiesz się, jak zintegrować usługę Azure Data Lake Storage Gen1 z usługą Azure Data Catalog, aby dane były wykrywalne w organizacji, integrując je z wykazem danych. Aby uzyskać więcej informacji na temat katalogowania danych, zobacz [Usługa Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Aby zrozumieć scenariusze, w których można użyć wykazu danych, zobacz [Typowy wykaz danych platformy Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Włącz subskrypcję platformy Azure** dla usługi Data Lake Storage Gen1. Zobacz [instrukcje](data-lake-store-get-started-portal.md).
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w [usłudze Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal.](data-lake-store-get-started-portal.md) W tym samouczku utwórz konto Data Lake Storage Gen1 o nazwie **datacatalogstore**.

    Po utworzeniu konta prześlij do niego przykładowy zestaw danych. W tym samouczku przekażmy wszystkie pliki csv w folderze **AmbulanceData** w [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Można użyć różnych klientów, takich jak [Azure Storage Explorer](https://storageexplorer.com/), aby przekazać dane do kontenera obiektów blob.
* **Wykaz danych platformy Azure**. Twoja organizacja musi mieć już utworzony wykaz danych platformy Azure dla Twojej organizacji. Tylko jeden katalog jest dozwolony dla każdej organizacji.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Rejestrowanie usługi Data Lake Storage Gen1 jako źródła wykazu danych

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Przejdź `https://azure.microsoft.com/services/data-catalog`do , i kliknij przycisk **Rozpocznij**.
1. Zaloguj się do portalu usługi Azure Data Catalog, a następnie kliknij pozycję **Publikuj dane**.

    ![Rejestrowanie źródła danych](./media/data-lake-store-with-data-catalog/register-data-source.png "Rejestrowanie źródła danych")
1. Na następnej stronie kliknij pozycję **Uruchom aplikację**. Spowoduje to pobranie pliku manifestu aplikacji na komputer. Kliknij dwukrotnie plik manifestu, aby uruchomić aplikację.
1. Na stronie Powitalnej kliknij pozycję **Zaloguj się**i wprowadź poświadczenia.

    ![Ekran powitalny](./media/data-lake-store-with-data-catalog/welcome.screen.png "Ekran powitalny")
1. Na stronie Wybierz źródło danych wybierz pozycję **Azure Data Lake Store**, a następnie kliknij przycisk **Dalej**.

    ![Wybieranie źródła danych](./media/data-lake-store-with-data-catalog/select-source.png "Wybieranie źródła danych")
1. Na następnej stronie podaj nazwę konta Usługi Data Lake Storage Gen1, które chcesz zarejestrować w wykazie danych. Pozostaw pozostałe opcje jako domyślne, a następnie kliknij przycisk **Połącz**.

    ![Łączenie ze źródłem danych](./media/data-lake-store-with-data-catalog/connect-to-source.png "Łączenie ze źródłem danych")
1. Następną stronę można podzielić na następujące segmenty.

    a. Pole **Hierarchia serwera** reprezentuje strukturę folderów kont Data Lake Storage Gen1. **$Root** reprezentuje katalog główny konta Data Lake Storage Gen1, a **AmbulanceData** reprezentuje folder utworzony w katalogu głównym konta Data Lake Storage Gen1.

    b. Pole **Dostępne obiekty** zawiera listę plików i folderów w folderze **AmbulanceData.**

    d. Pole Obiekty, które mają **być zarejestrowane,** zawiera listę plików i folderów, które chcesz zarejestrować w usłudze Azure Data Catalog.

    ![Wyświetlanie struktury danych](./media/data-lake-store-with-data-catalog/view-data-structure.png "Wyświetlanie struktury danych")
1. W tym samouczku należy zarejestrować wszystkie pliki w katalogu. W tym celu kliknij przycisk![(Przenieś obiekty),](./media/data-lake-store-with-data-catalog/move-objects.png "Przenoszenie obiektów")aby przenieść wszystkie pliki do **pola Obiekty, które mają zostać zarejestrowane.**

    Ponieważ dane zostaną zarejestrowane w wykazie danych dla całej organizacji, zaleca się dodanie niektórych metadanych, których można później użyć do szybkiego zlokalizowania danych. Na przykład można dodać adres e-mail właściciela danych (na przykład ten, który przekazuje dane) lub dodać tag do identyfikacji danych. Zrzut ekranu poniżej pokazuje znacznik, który można dodać do danych.

    ![Wyświetlanie struktury danych](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Wyświetlanie struktury danych")

    Kliknij **pozycję Zarejestruj**.
1. Następujące przechwytywanie ekranu oznacza, że dane zostały pomyślnie zarejestrowane w wykazie danych.

    ![Rejestracja zakończona](./media/data-lake-store-with-data-catalog/registration-complete.png "Wyświetlanie struktury danych")
1. Kliknij **pozycję Wyświetl portal,** aby wrócić do portalu wykazu danych i sprawdzić, czy można teraz uzyskać dostęp do zarejestrowanych danych z portalu. Aby przeszukać dane, można użyć tagu użytego podczas rejestrowania danych.

     ![Wyszukiwanie danych w katalogu](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Wyszukiwanie danych w katalogu")
1. Teraz można wykonywać operacje, takie jak dodawanie adnotacji i dokumentacji do danych. Aby uzyskać więcej informacji, zobacz poniższe łącza.

    * [Dodawanie adnotacji do źródeł danych w wykazie danych](../data-catalog/data-catalog-how-to-annotate.md)
    * [Źródła danych dokumentu w wykazie danych](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Zobacz też
* [Dodawanie adnotacji do źródeł danych w wykazie danych](../data-catalog/data-catalog-how-to-annotate.md)
* [Źródła danych dokumentu w wykazie danych](../data-catalog/data-catalog-how-to-documentation.md)
* [Integracja usługi Data Lake Storage Gen1 z innymi usługami platformy Azure](data-lake-store-integrate-with-other-services.md)
