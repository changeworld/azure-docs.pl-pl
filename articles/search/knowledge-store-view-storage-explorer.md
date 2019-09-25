---
title: Wyświetlanie sklepu z bazami danych za pomocą Eksplorator usługi Storage — Azure Search
description: Wyświetlanie i analizowanie Azure Search sklepu z bazami danych za pomocą Eksplorator usługi Storage Azure Portal.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 9ec93bcd2309bb47b24983260c35726ac24b5e95
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265603"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Wyświetlanie sklepu z bazami danych za pomocą Eksplorator usługi Storage

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie powinien być używany w środowisku produkcyjnym. [Interfejs API REST Azure Search wersja 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>
W tym artykule dowiesz się, jak nawiązać połączenie i eksplorować magazyn wiedzy przy użyciu Eksplorator usługi Storage w Azure Portal. Aby utworzyć przykład sklepu z bazami danych używany w tym przewodniku, zobacz temat [Tworzenie sklepu z bazami danych w Azure Portal](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj kroki opisane w temacie [Tworzenie magazynu wiedzy w Azure Portal](knowledge-store-create-portal.md) , aby utworzyć przykładowy magazyn wiedzy używany w tym instruktażu.

+ Potrzebna będzie również nazwa konta usługi Azure Storage, które zostało użyte do utworzenia magazynu wiedzy, wraz z jego kluczem dostępu z poziomu Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Wyświetlaj, Edytuj i badaj magazyn wiedzy w Eksplorator usługi Storage

1. W Azure Portal Otwórz konto magazynu używane do tworzenia sklepu z bazami danych.

1. W lewym okienku nawigacji konta magazynu kliknij przycisk **Eksplorator usługi Storage**.

1. Rozwiń listę **tabele** , aby wyświetlić listę projekcji tabeli platformy Azure, które zostały utworzone podczas uruchamiania kreatora **importu danych** na przykładowych danych dotyczących usług hotelowych.

Wybierz dowolną tabelę, aby wyświetlić wzbogacone dane, w tym kluczowe frazy tonacji wyniki, dane lokalizacji i szerokości geograficznej i nie tylko.

   ![Wyświetl tabele w Eksplorator usługi Storage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Wyświetl tabele w Eksplorator usługi Storage")

Aby zmienić typ danych dla dowolnej wartości tabeli lub zmienić poszczególne wartości w tabeli, kliknij przycisk **Edytuj**. Zmiana typu danych dla dowolnej kolumny w jednym wierszu tabeli zostanie zastosowana do wszystkich wierszy.

   ![Edytuj tabelę w Eksplorator usługi Storage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Edytuj tabelę w Eksplorator usługi Storage")

Aby uruchomić zapytania, kliknij pozycję **zapytanie** na pasku poleceń, a następnie wprowadź warunki.  

   ![Tabela zapytań w Eksplorator usługi Storage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabela zapytań w Eksplorator usługi Storage")

## <a name="clean-up"></a>Czyszczenie

Gdy pracujesz nad własną subskrypcją, dobrym pomysłem jest zakończenie projektu w celu ustalenia, czy nadal potrzebujesz utworzonych zasobów. Zasoby po lewej stronie mogą być kosztowne. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak połączyć ten magazyn wiedzy z Power BI, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Łączenie z usługą Power BI](knowledge-store-connect-power-bi.md)

Aby dowiedzieć się, jak utworzyć magazyn wiedzy przy użyciu interfejsów API REST i programu Poster, zobacz następujący artykuł.  

> [!div class="nextstepaction"]
> [Tworzenie sklepu merytorycznego w usłudze REST](knowledge-store-howto.md)
