---
title: Wyświetlanie magazynu wiedzy (wersja zapoznawcza) za pomocą Eksploratora magazynu
titleSuffix: Azure Cognitive Search
description: Wyświetlanie i analizowanie magazynu wiedzy usługi Azure Cognitive Search za pomocą Eksploratora magazynu usługi Azure portal. magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754064"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Wyświetlanie magazynu wiedzy za pomocą Eksploratora magazynu

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Interfejs [API REST w wersji 2019-05-06-Preview](search-api-preview.md) udostępnia funkcje w wersji zapoznawczej. Obecnie istnieje ograniczona obsługa portalu i nie ma obsługi sdk .NET.

W tym artykule dowiesz się na przykładzie, jak połączyć się z magazynem wiedzy i eksplorować go przy użyciu Eksploratora magazynu w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj kroki opisane w [obszarze Tworzenie magazynu wiedzy w witrynie Azure portal,](knowledge-store-create-portal.md) aby utworzyć przykładowy magazyn wiedzy używany w tym instruktażu.

+ Będzie również potrzebna nazwa konta magazynu platformy Azure, który został użyty do utworzenia magazynu wiedzy, wraz z jego klucz dostępu z witryny Azure portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Wyświetlanie, edytowanie i wykonywanie zapytań o magazyn wiedzy w Eksploratorze magazynu

1. W witrynie Azure portal [otwórz konto usługi Storage](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) użyte do utworzenia magazynu wiedzy.

1. W lewym okienku nawigacji konta magazynu kliknij pozycję **Eksplorator magazynu**.

1. Rozwiń listę **TABEL,** aby wyświetlić listę projekcji tabel platformy Azure, które zostały utworzone po uruchomieniu **Kreatora importu danych** na przykładowych danych opinii hoteli.

Wybierz dowolną tabelę, aby wyświetlić wzbogacone dane, w tym kluczowe frazy i wyniki tonacji.

   ![Wyświetlanie tabel w Eksploratorze magazynu](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Wyświetlanie tabel w Eksploratorze magazynu")

Aby zmienić typ danych dla dowolnej wartości tabeli lub zmienić poszczególne wartości w tabeli, kliknij przycisk **Edytuj**. Po zmianie typu danych dla dowolnej kolumny w jednym wierszu tabeli zostanie on zastosowany do wszystkich wierszy.

   ![Edytowanie tabeli w Eksploratorze magazynu](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Edytowanie tabeli w Eksploratorze magazynu")

Aby uruchamiać kwerendy, kliknij pozycję **Zapytanie** na pasku poleceń i wprowadź warunki.  

   ![Tabela kwerend w Eksploratorze magazynu](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabela kwerend w Eksploratorze magazynu")

## <a name="clean-up"></a>Czyszczenie

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu.

## <a name="next-steps"></a>Następne kroki

Połącz ten magazyn wiedzy z usługą Power BI w celu głębszej analizy lub przejdź do przodu za pomocą kodu, używając interfejsu API REST i listonosza do utworzenia innego magazynu wiedzy.

> [!div class="nextstepaction"]
> [Łączenie się za pomocą usługi Power BI](knowledge-store-connect-power-bi.md)
> [Tworzenie magazynu wiedzy w rest](knowledge-store-create-rest.md)
