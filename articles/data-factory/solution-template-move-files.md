---
title: Przenoszenie plików między magazynem opartym na plikach
description: Informacje o sposobie przenoszenia plików między magazynem opartym na plikach przy użyciu szablonu rozwiązania Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: e0f9179918016b9eaec04b0d559e25ed7973f18a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896199"
---
# <a name="move-files-with-azure-data-factory"></a>Przenoszenie plików z Azure Data Factory

W tym artykule opisano szablon rozwiązania, którego można użyć do przenoszenia plików z jednego folderu do innego między magazynami opartymi na plikach. Jednym z typowych scenariuszy korzystania z tego szablonu: pliki są ciągle opuszczane do folderu wyładunkowego magazynu źródłowego. Tworząc wyzwalacz harmonogramu, potok ADF może okresowo przenosić te pliki ze źródła do magazynu docelowego.  Sposób, w jaki potok ADF osiąga "przeniesienie plików", pobiera pliki z folderu wyładunkowego, kopiując każdy z nich do innego folderu w magazynie docelowym, a następnie usuwając te same pliki z folderu wyładunkowego w magazynie źródłowym.

> [!NOTE]
> Należy pamiętać, że ten szablon jest przeznaczony do przenoszenia plików, a nie do przenoszenia folderów.  Jeśli chcesz przenieść folder, zmieniając zestaw danych tak, aby zawierał tylko ścieżkę do folderu, a następnie za pomocą działania kopiowania i usuwania, aby odwołać się do tego samego zestawu danych reprezentującego folder, musisz zachować ostrożność. Jest to spowodowane tym, że trzeba upewnić się, że nie będzie nowych plików dołączanych do folderu między operacją kopiowania a operacją usuwania. Jeśli w folderze znajdują się nowe pliki docierające do folderu, gdy działanie kopiowania po prostu zostało ukończone, ale działanie usuwania nie zostało jaśniejsze, istnieje możliwość usunięcia tego nowego pliku, który nie został skopiowany do destinati jeszcze przez usunięcie całego folderu.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon pobiera pliki ze źródłowego magazynu opartego na plikach. Następnie każdy z nich przenosi do magazynu docelowego.

Szablon zawiera pięć działań:
- **GetMetadata** pobiera listę obiektów, w tym pliki i podfoldery z folderu w magazynie źródłowym. Obiekty nie zostaną cyklicznie pobrane. 
- **Filtr Filtruj** listę obiektów z działania **GetMetadata** , aby wybrać tylko pliki. 
- Instrukcja **foreach** pobiera listę plików z działania **filtru** , a następnie wykonuje iterację na liście i przekazuje każdy plik do działania kopiowania i usuwania działania.
- **Kopiuj** kopiuje jeden plik ze źródła do magazynu docelowego.
- **Delete** usuwa ten sam plik z magazynu źródłowego.

Szablon definiuje dwa parametry:
- *FolderPath_SourceStore* to ścieżka folderu magazynu źródłowego, z której chcesz przenieść pliki. 
- *FolderPath_DestinationStore* to ścieżka folderu magazynu docelowego, do której chcesz przenieść pliki. 

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **przenoszenie plików** . Wybierz istniejące połączenie lub Utwórz **nowe** połączenie ze źródłowym magazynem plików, z którego chcesz przenieść pliki. Należy pamiętać, że **DataSource_Folder** i **DataSource_File** odnoszą się do tego samego połączenia ze źródłowym magazynem plików.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-move-files/move-files1.png)

2. Wybierz istniejące połączenie lub Utwórz **nowe** połączenie z docelowym magazynem plików, do którego chcesz przenieść pliki.

    ![Utwórz nowe połączenie z miejscem docelowym](media/solution-template-move-files/move-files2.png)

3. Wybierz przycisk **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-move-files/move-files3.png)
    
4. Zostanie wyświetlony potok, jak w poniższym przykładzie:

    ![Pokaż potok](media/solution-template-move-files/move-files4.png)

5. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.   Parametry są ścieżka folderu, z którego chcesz przenieść pliki, oraz ścieżka folderu, do której chcesz przenieść pliki. 

    ![Uruchamianie potoku](media/solution-template-move-files/move-files5.png)

6. Przejrzyj wynik.

    ![Przejrzyj wynik](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Następne kroki

- [Kopiuj nowe i zmienione pliki przez LastModifiedDate z Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)