---
title: Skopiuj nowe i zmienione pliki, Data ostatniej modyfikacji przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować nowych i zmienionych plików, Data ostatniej modyfikacji przy użyciu usługi Azure Data Factory za pomocą szablonu rozwiązania.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312817"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Skopiuj nowe i zmienione pliki, Data ostatniej modyfikacji przy użyciu usługi Azure Data Factory

W tym artykule opisano szablon rozwiązania, który służy do kopiowania nowych i zmienionych plików tylko przez Data ostatniej modyfikacji ze magazynu oparte na plikach do docelowego magazynu. 

## <a name="about-this-solution-template"></a>Temat ten szablon rozwiązania

Ten szablon wybiera pierwszy nowych i zmienionych plików tylko przez ich atrybutów **Data ostatniej modyfikacji**, a następnie kopiuje te wybrane pliki z magazynu danych źródłowych do docelowego magazynu danych.

Szablon zawiera jedno działanie:
- **Kopiuj** do skopiowania plików nowe i zmienione tylko przez Data ostatniej modyfikacji magazynu plików do docelowego magazynu.

Szablon definiuje cztery parametry:
-  *FolderPath_Source* jest ścieżka folderu, w którym mogą odczytywać pliki z magazynu źródłowego. Należy zastąpić wartość domyślna ścieżka folderu.
-  *FolderPath_Destination* jest ścieżka folderu, w którym chcesz skopiować pliki do magazynu docelowego. Należy zastąpić wartość domyślna ścieżka folderu.
-  *LastModified_From* jest używane, aby wybrać pliki, których atrybut Data ostatniej modyfikacji jest po lub równych tej wartości daty/godziny.  Aby wybrać nowe pliki, które nie został skopiowany ostatniego, ta wartość daty/godziny można czas, kiedy potoku zostało wyzwolone ostatniego. Można zastąpić wartością domyślną "2019-02-01T00:00:00Z" na oczekiwany Data ostatniej modyfikacji w strefie czasowej UTC. 
-  *LastModified_To* służy do wybierania plików, których atrybut Data ostatniej modyfikacji jest wcześniejsza niż ta wartość daty/godziny. Aby wybrać nowe pliki, które nie został skopiowany ostatniego, ta wartość daty i godziny może być obecny czas.  Można zastąpić wartością domyślną "2019-02-01T00:00:00Z" na oczekiwany Data ostatniej modyfikacji w strefie czasowej UTC. 

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **kopiować nowe pliki tylko przez Data ostatniej modyfikacji**. Tworzenie **New** połączenia ze swoim magazynem magazynu źródłowego. Źródłowy magazyn magazynu jest, których chcesz kopiować pliki.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Najpierw wybierz magazyn **typu**. Po, dane wejściowe magazynu **nazwa konta** i **klucz konta**. Na koniec wybierz pozycję **Zakończ**.

    ![Wprowadź parametry połączenia](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Tworzenie **New** połączenia ze swoim magazynem docelowego. Magazyn docelowy jest, które chcesz skopiować pliki. Informacje o połączeniu docelowego magazynu danych podobne należy również wprowadzić w jako użytkownik krok 2.

    ![Utwórz nowe połączenie do miejsca docelowego](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Wybierz **za pomocą tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Zostanie wyświetlony potok, które są dostępne w panelu, jak pokazano w poniższym przykładzie:

    ![Pokaż potoku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Wybierz **debugowania**, zapisać wartości dla **parametry** i wybierz **Zakończ**.  Na ilustracji poniżej, ustawimy parametry, jako pokazano poniżej.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     Przykład wskazuje pliki, które zostały ostatnio zmodyfikowane w ramach przedziału czasu między *2019 r-02-01T00:00:00Z* i *2019-03-01T00:00:00Z* zostaną skopiowane z folderu */source/*  do folderu */destination/*.  Możesz zastąpić je własnymi parametrami.
    
     ![Uruchamianie potoku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Przejrzyj wyniki. Zostanie wyświetlony tylko te pliki, które zostały ostatnio zmodyfikowane w ciągu skonfigurowanego czasu został skopiowany do magazynu docelowego.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Teraz możesz dodać wyzwalacza wirowania systemu windows do automatyzacji tego potoku, tak aby potok zawsze było skopiować pliki nowe i zmienione tylko przez Data ostatniej modyfikacji okresowo.  Wybierz **Dodaj wyzwalacz**i wybierz **nowy/Edytuj**.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. W **Dodawanie wyzwalaczy** wybierz **+ nowy**.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Wybierz **okno wirowania** dla typu wyzwalacza ustaw **co 15 min** jako cyklu (można zmienić na każdym interwale), a następnie wybierz pozycję **dalej**.

    ![Tworzenie wyzwalacza](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Zapisz wartość dla **Parametry uruchamiania wyzwalacza** jako poniżej, a następnie wybierz **Zakończ**.
    - **FolderPath_Source** = **/source/**.  Można zastąpić folderu w magazynie danych źródłowych.
    - **FolderPath_Destination** = **/destination/**.  Można zastąpić folderu w docelowego magazynu danych.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas, kiedy potoku zostało wyzwolone ostatniego.
    - **LastModified_To** = **@trigger(). outputs.windowEndTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas, kiedy potoku jest wyzwalany w tej chwili.
    
    ![Parametry wejściowe](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Wybierz pozycję **Opublikuj wszystkie**.
    
    ![Publikuj wszystko](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Utwórz nowe pliki w folderze źródłowego magazynu źródła danych.  Możesz teraz czeka na potok automatyczne wyzwolenie i nowe pliki zostaną skopiowane do docelowego magazynu.

14. Wybierz **monitorowanie** w panelu nawigacyjnym po lewej stronie, a następnie poczekaj około 15 minut, jeśli ustawiono cyklu wyzwalacza co 15 minut. 

    ![Wybierz opcję monitorowania](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Przejrzyj wyniki. Zostanie wyświetlony potok będą wyzwalane automatycznie co 15 minut, a tylko nowe lub zostały zmienione pliki z magazynu źródłowego zostaną skopiowane do docelowego magazynu w każdym uruchomieniem potoku.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)