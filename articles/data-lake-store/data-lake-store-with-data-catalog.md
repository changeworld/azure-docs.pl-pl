---
title: Zarejestruj dane z Data Lake Store w usłudze Azure Data Catalog | Dokumentacja firmy Microsoft
description: Zarejestruj dane z Data Lake Store w usłudze Azure Data Catalog
services: data-lake-store,data-catalog
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 8da9f0f8aeb36d9ff2f87511c902dd719bc755b9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441604"
---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Zarejestruj dane z Data Lake Store w usłudze Azure Data Catalog
W tym artykule dowiesz się, jak zintegrować usługi Azure Data Lake Store za pomocą usługi Azure Data Catalog w celu uzyskania danych stała się wykrywalna dla organizacji, integrując je z usługą Data Catalog. Aby uzyskać więcej informacji na temat katalogowanie danych, zobacz [usługi Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Aby zrozumieć scenariusze, w których możesz użyć wykazu danych, zobacz [typowych scenariuszy usługi Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Włączanie subskrypcji platformy Azure** w publicznej wersji zapoznawczej Data Lake Store. Zobacz [instrukcje](data-lake-store-get-started-portal.md).
* **Konto usługi Azure Data Lake Store**. Postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md). W tym samouczku należy utworzyć konta Data Lake Store, o nazwie **datacatalogstore**.

    Po utworzeniu konta, a następnie Przekaż przykładowy zestaw danych do niego. W tym samouczku, Daj nam przekazać wszystkie pliki CSV w **AmbulanceData** folderu w [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Można użyć różnych klientów, takie jak [Eksploratora usługi Azure Storage](http://storageexplorer.com/), aby przekazać dane do kontenera obiektów blob.
* **Usługa Azure Data Catalog**. Twoja organizacja musi już mieć utworzone dla Twojej organizacji usługi Azure Data Catalog. Tylko jeden wykaz jest dozwolony dla każdej organizacji.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Register Data Lake Store jako źródło dla usługi Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Przejdź do `https://azure.microsoft.com/services/data-catalog`i kliknij przycisk **wprowadzenie**.
1. Zaloguj się do portalu usługi Azure Data Catalog, a następnie kliknij przycisk **publikowania danych**.

    ![Rejestrowanie źródła danych](./media/data-lake-store-with-data-catalog/register-data-source.png "rejestrowania źródła danych")
1. Na następnej stronie kliknij pozycję **Uruchom aplikację**. Spowoduje to pobranie pliku manifestu aplikacji na komputerze. Kliknij dwukrotnie plik manifestu, aby uruchomić aplikację.
1. Na stronie powitalnej kliknij **Zaloguj**, a następnie wprowadź swoje poświadczenia.

    ![Ekran powitalny](./media/data-lake-store-with-data-catalog/welcome.screen.png "ekran powitalny")
1. Wybierz stronę źródła danych wybierz **usługi Azure Data Lake**, a następnie kliknij przycisk **dalej**.

    ![Wybierz źródło danych](./media/data-lake-store-with-data-catalog/select-source.png "wybierz źródło danych")
1. Na następnej stronie podaj nazwę konta Data Lake Store, które chcesz zarejestrować w usłudze Data Catalog. Dla pozostałych opcji zostaw jako domyślne, a następnie kliknij przycisk **Connect**.

    ![Łączenie ze źródłem danych](./media/data-lake-store-with-data-catalog/connect-to-source.png "łączenie ze źródłem danych")
1. Następnej strony można podzielić na następujące segmenty.

    a. **Hierarchii serwerów** pole reprezentuje strukturę folderów konta Data Lake Store. **$Root** reprezentuje katalog główny konta Data Lake Store i **AmbulanceData** reprezentuje folder utworzony w katalogu głównym konta Data Lake Store.

    b. **Dostępne obiekty** pole zawiera listę plików i folderów w obszarze **AmbulanceData** folderu.

    c. **Obiekty do zarejestrowania** pole zawiera listę plików i folderów, które chcesz zarejestrować w usłudze Azure Data Catalog.

    ![Wyświetlanie struktury danych](./media/data-lake-store-with-data-catalog/view-data-structure.png "wyświetlić strukturę danych")
1. W tym samouczku należy zarejestrować wszystkie pliki w katalogu. W tym kliknij (![przenoszenie obiektów](./media/data-lake-store-with-data-catalog/move-objects.png "przenoszenie obiektów")) przycisk, aby przenieść wszystkie pliki do **obiekty do zarejestrowania** pole.

    Ponieważ dane zostaną zarejestrowane w wykazie danych całej organizacji, jest zalecane podejście, aby dodać niektóre metadane, który mógł później użyć odnajdywanie danych. Na przykład można dodać adres e-mail właściciela danych (na przykład jeden, który jest przekazywania danych), lub dodać tag, aby zidentyfikować dane. Przechwytywanie ekranu poniżej przedstawiono tag sytuacja: dodajesz do danych.

    ![Wyświetlanie struktury danych](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "wyświetlić strukturę danych")

    Kliknij przycisk **zarejestrować**.
1. Poniższy zrzut ekranu wskazuje, że dane po pomyślnym zarejestrowaniu w usłudze Data Catalog.

    ![Rejestracja zakończona](./media/data-lake-store-with-data-catalog/registration-complete.png "wyświetlić strukturę danych")
1. Kliknij przycisk **Wyświetl Portal** wróć do portalu usługi Data Catalog i sprawdź, czy możesz teraz uzyskiwać dostęp danych zarejestrowanych w portalu. Możesz wyszukiwać dane, można użyć znaczników, które było używane podczas rejestrowania danych.

     ![Wyszukiwanie danych w wykazie](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "wyszukiwanie danych w katalogu")
1. Można teraz wykonywać operacje, takie jak dodawanie adnotacji i dokumentacji do danych. Aby uzyskać więcej informacji zobacz poniższe linki.

    * [Dodawać adnotacje do źródeł danych w usłudze Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentowanie źródeł danych w usłudze Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Zobacz także
* [Dodawać adnotacje do źródeł danych w usłudze Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentowanie źródeł danych w usłudze Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integracja Data Lake Store z innymi usługami platformy Azure](data-lake-store-integrate-with-other-services.md)
