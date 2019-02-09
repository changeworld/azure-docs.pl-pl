---
title: Kopiuj pliki z wielu kontenerów przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kopiować pliki z wielu kontenerów przy użyciu usługi Azure Data Factory za pomocą szablonu rozwiązania.
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
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967414"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiuj pliki z wielu kontenerów przy użyciu usługi Azure Data Factory

Szablon rozwiązania opisane w tym artykule ułatwiają kopiować pliki z wielu plików, kontenerów lub zasobników między magazynami plików. Na przykład może być użytkownik chce migrować swoje usługi data lake z usługi AWS S3 do usługi Azure Data Lake Store. Lub być może chcesz replikować wszystko z jednego konta usługi Azure Blob Storage do innego konta usługi Azure Blob Storage. Ten szablon jest przeznaczony dla tych przypadków użycia.

Jeśli chcesz skopiować pliki z jednego kontenera lub przedział jest bardziej wydajne, aby użyć **narzędzia do kopiowania danych** umożliwia tworzenie potoku za pomocą jednego działania kopiowania. Ten szablon jest większa niż użytkownik należy uzyskać prosty przypadek użycia.

## <a name="about-this-solution-template"></a>Temat ten szablon rozwiązania

Ten szablon wylicza kontenery z magazynu źródłowego magazynu, a następnie zapisać kopie każdego kontenery z magazynu źródłowego do docelowego magazynu. 

Szablon zawiera trzy czynności:
-   A **GetMetadata** działanie, aby skanować sklepu magazynu źródłowego i Uzyskaj listę kontenerów.
-   A **ForEach** działania, aby uzyskać listę kontenerów z **GetMetadata** działania iteracji po liście a następnie przekaż każdy kontener do działania kopiowania.
-   A **kopiowania** działania do skopiowania każdego kontenera magazynu przechowywania źródłowego do docelowego magazynu.

Szablon definiuje dwa parametry:
-   Parametr *SourceFilePath* to ścieżka usługi magazynu danych źródłowych, gdzie można uzyskać listę kontenerów lub zasobników. W większości przypadków ścieżka jest katalog główny, który zawiera wiele folderów kontenera. Wartość domyślna tego parametru to `/`.
-   Parametr *DestinationFilePath* jest ścieżką, w której będą kopiowane pliki w magazynie docelowym. Wartość domyślna tego parametru to `/`.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **kopiowanie wielu kontenerów plików między magazynami plików**i Utwórz **nowe połączenie** ze swoim magazynem magazynu źródłowego. Źródłowy magazyn magazynu jest to miejsce, w którym chcesz skopiować pliki z wielu kontenerów lub zasobników.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Tworzenie **nowe połączenie** ze swoim magazynem magazynu docelowego.

    ![Utwórz nowe połączenie do miejsca docelowego](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Kliknij przycisk **za pomocą tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Zostanie wyświetlony potok, które są dostępne w panelu, jak pokazano w poniższym przykładzie:

    ![Pokaż potoku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Kliknij przycisk **debugowania**, wprowadź **parametry** i kliknij przycisk **Zakończ**.

    ![Uruchamianie potoku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Przejrzyj wyniki.

    ![Przejrzyj wynik](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
