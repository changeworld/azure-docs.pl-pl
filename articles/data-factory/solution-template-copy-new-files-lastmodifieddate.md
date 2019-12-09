---
title: Kopiuj nowe i zmienione pliki według LastModifiedDate
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania nowych i zmienionych plików przez LastModifiedDate przy użyciu Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: bf095fbdab0e7368149eac560d3bdb20134e3e3e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927777"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopiuj nowe i zmienione pliki przez LastModifiedDate z Azure Data Factory

W tym artykule opisano szablon rozwiązania, którego można użyć do kopiowania nowych i zmienionych plików tylko przez LastModifiedDate z magazynu opartego na plikach do magazynu docelowego. 

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon najpierw wybiera nowe i zmienione pliki tylko według ich atrybutów **LastModifiedDate**, a następnie kopiuje te wybrane pliki ze źródła danych do magazynu docelowego danych.

Szablon zawiera jedno działanie:
- **Kopiuj** , aby skopiować nowe i zmienione pliki tylko przez LastModifiedDate z magazynu plików do magazynu docelowego.

Szablon definiuje cztery parametry:
-  *FolderPath_Source* to ścieżka folderu, w której można odczytać pliki z magazynu źródłowego. Należy zastąpić wartość domyślną własną ścieżką folderu.
-  *FolderPath_Destination* to ścieżka folderu, w której chcesz skopiować pliki do magazynu docelowego. Należy zastąpić wartość domyślną własną ścieżką folderu.
-  *LastModified_From* jest używany do wybierania plików, których atrybut LastModifiedDate ma wartość lub równą tej wartości daty/godziny.  W celu wybrania tylko nowych plików, które nie zostały ostatnio skopiowane, ta wartość daty i godziny może być czasem, w którym potok został wyzwolony jako ostatni. Wartość domyślną "2019-02-01T00:00:00Z" można zastąpić oczekiwaną LastModifiedDate w strefie czasowej UTC. 
-  *LastModified_To* służy do wybierania plików, których atrybut LastModifiedDate jest wcześniejszy niż ta wartość daty i godziny. W celu wybrania tylko nowych plików, które nie zostały ostatnio skopiowane, ta wartość daty i godziny może być obecna.  Wartość domyślną "2019-02-01T00:00:00Z" można zastąpić oczekiwaną LastModifiedDate w strefie czasowej UTC. 

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **Kopiuj nowe pliki tylko przez LastModifiedDate**. Utwórz **nowe** połączenie ze źródłowym magazynem magazynu. Źródłowy magazyn magazynów to miejsce, z którego mają zostać skopiowane pliki.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Najpierw wybierz **Typ**magazynu. Po wprowadzeniu **nazwy konta** magazynu i **klucza konta**. Na koniec wybierz pozycję **Zakończ**.

    ![Wprowadź parametry połączenia](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Utwórz **nowe** połączenie z magazynem docelowym. Magazyn docelowy to miejsce, do którego chcesz skopiować pliki. Potrzebne są również informacje o połączeniu z magazynem miejsc docelowych danych, podobnie jak w kroku 2.

    ![Utwórz nowe połączenie z miejscem docelowym](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Wybierz przycisk **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Na panelu zostanie wyświetlony potok, jak pokazano w następującym przykładzie:

    ![Pokaż potok](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Wybierz pozycję **Debuguj**, Zapisz wartość **parametrów** i wybierz pozycję **Zakończ**.  Na poniższej ilustracji ustawimy parametry w następujący sposób.
   - **FolderPath_Source** =  **/source/**
   - **FolderPath_Destination** =  **/Destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     Przykład wskazuje, że pliki, które zostały ostatnio zmodyfikowane w przedziale od *2019-02-01T00:00:00Z* i *2019-03-01T00:00:00Z* zostanie skopiowane z folderu */Source/* do folderu */Destination/* .  Można je zastąpić własnymi parametrami.
    
     ![Uruchamianie potoku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Przejrzyj wynik. Zobaczysz tylko te pliki, które ostatnio zmodyfikowano w skonfigurowanym przedziale czasu, zostały skopiowane do magazynu docelowego.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Teraz można dodać wyzwalacz wirowania systemu Windows w celu zautomatyzowania tego potoku, aby potok mógł zawsze kopiować nowe i zmienione pliki tylko przez LastModifiedDate okresowo.  Wybierz pozycję **Dodaj wyzwalacz**, a następnie wybierz pozycję **Nowy/Edytuj**.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. W oknie **Dodawanie wyzwalaczy** wybierz pozycję **+ Nowy**.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Wybierz **okno wirowania** dla typu wyzwalacza, ustaw **co 15 minut** jako cykl (możesz zmienić na dowolny czas), a następnie wybierz przycisk **dalej**.

    ![Tworzenie wyzwalacza](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Zapisz wartość **parametrów uruchamiania wyzwalacza** w następujący sposób, a następnie wybierz pozycję **Zakończ**.
    - **FolderPath_Source** =  **/Source/** .  Możesz zastąpić folder w źródłowym magazynie danych.
    - **FolderPath_Destination** =  **/Destination/** .  Możesz zamienić folder na docelowy magazyn danych.
    - **LastModified_From** =   **\@wyzwalacza (). Output. windowStartTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas ostatniego uruchomienia potoku.
    - **LastModified_To** =  **\@wyzwalacza (). Output. windowEndTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas, w którym potok jest wyzwalany w tym czasie.
    
    ![Parametry wejściowe](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Wybierz pozycję **Opublikuj wszystkie**.
    
    ![Publikuj wszystko](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Utwórz nowe pliki w folderze źródłowym magazynu źródła danych.  Trwa oczekiwanie na automatyczne wyzwolenie potoku, a tylko nowe pliki zostaną skopiowane do magazynu docelowego.

14. Wybierz kartę **monitorowanie** w lewym panelu nawigacyjnym i odczekaj około 15 minut, jeśli cykl wyzwalacza został ustawiony na co 15 minut. 

    ![Wybierz monitorowanie](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Przejrzyj wynik. Zobaczysz, że potok zostanie wyzwolony automatycznie co 15 minut, a tylko nowe lub zmienione pliki z magazynu źródłowego zostaną skopiowane do magazynu docelowego w każdym uruchomieniu potoku.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
