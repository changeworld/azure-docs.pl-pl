---
title: Kopiuj pliki z wielu kontenerów przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować pliki z wielu kontenerów przy użyciu usługi Azure Data Factory za pomocą szablonu rozwiązania.
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
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635184"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiuj pliki z wielu kontenerów przy użyciu usługi Azure Data Factory

W tym artykule opisano szablon rozwiązania, który służy do kopiowania plików z wielu kontenerów między magazynami plików. Na przykład służy do migracji z usługi data lake z AWS S3 do usługi Azure Data Lake Store. Ewentualnie można użyć szablonu, do replikowania wszystko z jednego konta magazynu obiektów Blob platformy Azure do innego.

> [!NOTE]
> Jeśli chcesz skopiować pliki z jednego kontenera jest bardziej wydajne, aby użyć [narzędzia do kopiowania danych](copy-data-tool.md) umożliwia tworzenie potoku z działaniem pojedynczej kopii. Szablon w tym artykule jest większa niż wymaga tego prostego scenariusza.

## <a name="about-this-solution-template"></a>Temat ten szablon rozwiązania

Ten szablon wylicza kontenery z magazynu magazynu źródłowego. Następnie kopiuje te kontenery do docelowego magazynu.

Szablon zawiera trzy czynności:
- **GetMetadata** skanuje sklepu magazynu źródłowego i pobiera listę kontenerów.
- **Instrukcja ForEach** pobiera listę kontenerów z **GetMetadata** działanie wykonuje iterację na liście i przekazuje każdego kontenera do działania kopiowania.
- **Kopiuj** kopiuje każdy kontener z magazynu magazynu źródłowego do docelowego magazynu.

Szablon definiuje dwa parametry:
- *SourceFilePath* to ścieżka usługi magazynu danych źródłowych, gdzie można uzyskać listę kontenerów. W większości przypadków ścieżka jest katalog główny, który zawiera wiele folderów kontenera. Wartość domyślna tego parametru to `/`.
- *DestinationFilePath* jest ścieżką, w którym pliki zostaną skopiowane do w magazynie docelowym. Wartość domyślna tego parametru to `/`.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do **kopiowanie wielu kontenerów plików między magazynami plików** szablonu. Tworzenie **New** połączenia ze swoim magazynem magazynu źródłowego. Źródłowy magazyn magazynu jest, które chcesz skopiować pliki z wielu kontenerów z.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Tworzenie **New** połączenia ze swoim magazynem magazynu docelowego.

    ![Utwórz nowe połączenie do miejsca docelowego](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Wybierz **za pomocą tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Zostanie wyświetlony potok, jak w poniższym przykładzie:

    ![Pokaż potoku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Wybierz **debugowania**, wprowadź **parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Uruchamianie potoku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Przejrzyj wyniki.

    ![Przejrzyj wynik](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Kolejne kroki

- [Zbiorcze kopiowanie danych z bazy danych przy użyciu tabeli kontroli przy użyciu usługi Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiuj pliki z wielu kontenerów przy użyciu usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)