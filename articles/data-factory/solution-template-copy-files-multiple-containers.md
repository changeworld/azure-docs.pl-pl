---
title: Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania plików z wielu kontenerów za pomocą Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: 004a623f0dfe251da9d452b53c2541e53339d965
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684263"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory

W tym artykule opisano szablon rozwiązania, którego można użyć do kopiowania plików z wielu kontenerów między magazynami plików. Na przykład możesz użyć jej do migrowania usługi Data Lake z AWS S3 do Azure Data Lake Store. Można też użyć szablonu, aby replikować wszystko z jednego konta usługi Azure Blob Storage do innego.

> [!NOTE]
> Jeśli chcesz skopiować pliki z jednego kontenera, bardziej wydajne jest użycie [narzędzia kopiowanie danych](copy-data-tool.md) , aby utworzyć potok z jednym działaniem kopiowania. Szablon w tym artykule jest bardziej niezbędny dla tego prostego scenariusza.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon wylicza kontenery ze źródłowego magazynu magazynu. Następnie kopiuje te kontenery do magazynu docelowego.

Szablon zawiera trzy działania:
- **GetMetadata** skanuje źródłowy magazyn magazynu i pobiera listę kontenerów.
- Instrukcja **foreach** pobiera listę kontenerów z działania **GetMetadata** , a następnie wykonuje iterację na liście i przekazuje poszczególne kontenery do działania kopiowania.
- **Kopiuj** kopiuje każdy kontener ze źródłowego magazynu magazynu do magazynu docelowego.

Szablon definiuje dwa parametry:
- *Sourcefilepath* to ścieżka magazynu źródła danych, w którym można uzyskać listę kontenerów. W większości przypadków ścieżka jest katalogiem głównym, który zawiera wiele folderów kontenerów. Wartość domyślna tego parametru to `/`.
- *DestinationFilePath* to ścieżka, w której pliki zostaną skopiowane do magazynu docelowego. Wartość domyślna tego parametru to `/`.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **kopiowanie wielu plików między szablonem magazyny plików** . Utwórz **nowe** połączenie ze źródłowym magazynem magazynu. Źródłowy magazyn magazynów to miejsce, w którym chcesz skopiować pliki z wielu kontenerów.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Utwórz **nowe** połączenie z docelowym magazynem magazynu.

    ![Utwórz nowe połączenie z miejscem docelowym](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Zostanie wyświetlony potok, jak w poniższym przykładzie:

    ![Pokaż potok](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Uruchamianie potoku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Przejrzyj wynik.

    ![Przejrzyj wynik](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie masowe z bazy danych przy użyciu tabeli formantów z Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)