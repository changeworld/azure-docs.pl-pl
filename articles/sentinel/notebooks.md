---
title: Używanie notesów z usługą Azure Sentinel do polowania na zabezpieczenia
description: W tym artykule opisano sposób używania notesów z możliwościami polowania usługi Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581841"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Używanie notebooków Jupyter do polowania na zagrożenia bezpieczeństwa

Podstawą usługi Azure Sentinel jest magazyn danych; łączy w sobie kwerendy o wysokiej wydajności, schemat dynamiczny i skaluje się do ogromnych woluminów danych. Witryna Azure portal i wszystkie narzędzia Azure Sentinel używają wspólnego interfejsu API, aby uzyskać dostęp do tego magazynu danych. Ten sam interfejs API jest również dostępny dla narzędzi zewnętrznych, takich jak notesy [Jupyter](https://jupyter.org/) i Python. Podczas gdy wiele typowych zadań można wykonać w portalu, Jupyter rozszerza zakres tego, co można zrobić z tymi danymi. Łączy pełną programowalność z ogromną kolekcją bibliotek do uczenia maszynowego, wizualizacji i analizy danych. Te atrybuty sprawiają, że Jupyter jest atrakcyjnym narzędziem do badania bezpieczeństwa i polowania.

![przykładowy notes](./media/notebooks/sentinel-notebooks-map.png)

Zintegrowaliśmy środowisko Jupyter z portalem Azure, ułatwiając tworzenie i uruchamianie notesów w celu analizowania danych. Biblioteka *Kqlmagic* zawiera klej, który umożliwia podejmowanie zapytań z usługi Azure Sentinel i uruchamianie ich bezpośrednio w notesie. Kwerendy używają [języka zapytania Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Kilka notesów, opracowanych przez niektórych analityków zabezpieczeń firmy Microsoft, jest dostarczanych z usługą Azure Sentinel. Niektóre z tych notesów są zbudowane dla określonego scenariusza i mogą być używane w stanie takim, w jakim są. Inne są przeznaczone jako przykłady do zilustrowania technik i funkcji, które można skopiować lub dostosować do użycia we własnych notesach. Inne notesy mogą być również importowane ze społeczności Azure Sentinel GitHub.

Zintegrowane środowisko Jupyter używa [notesów platformy Azure](https://notebooks.azure.com/) do przechowywania, udostępniania i wykonywania notesów. Można również uruchomić te notesy lokalnie, jeśli masz środowisko Python i Jupyter na komputerze lub w innych środowiskach JupterHub, takich jak Azure Databricks.

Notebooki mają dwa składniki:

- Interfejs oparty na przeglądarce, w którym można wprowadzać i uruchamiać kwerendy i kod oraz gdzie są wyświetlane wyniki wykonania.
- *Jądro,* które jest odpowiedzialne za analizowanie i wykonywanie samego kodu. 

W notesach platformy Azure domyślnie to jądro działa na platformie Azure *Free Cloud Compute and Storage*. Jeśli notesy zawierają złożone modele uczenia maszynowego lub wizualizacje, należy rozważyć użycie bardziej zaawansowanych, dedykowanych zasobów obliczeniowych, takich jak [maszyny wirtualne do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Notesy na twoim koncie są przechowywane jako prywatne, chyba że zdecydujesz się je udostępnić.

Notesy usługi Azure Sentinel używają wielu popularnych bibliotek języka Python, takich jak pandas, matplotlib, bokeh i innych. Istnieje wiele innych pakietów Pythona do wyboru, obejmujących obszary takie jak:

- Wizualizacje i grafika
- Przetwarzanie i analiza danych
- Statystyki i obliczenia liczbowe
- Uczenie maszynowe i uczenie głębokie

Wydaliśmy również kilka narzędzi zabezpieczających Jupyter open source w pakiecie o nazwie [msticpy](https://github.com/Microsoft/msticpy/). Ten pakiet jest używany w wielu dołączonych notesach. Narzędzia msticpy zostały zaprojektowane specjalnie, aby pomóc w tworzeniu notesów do polowania i badania i aktywnie pracujemy nad nowymi funkcjami i ulepszeniami.

Początkowe notesy obejmują:

- **Badanie z przewodnikiem — alerty procesowe**: Umożliwia szybkie klasyfikowanie alertów przez analizowanie aktywności na serwerze lub hostach, których dotyczy problem.
- **Polowanie z przewodnikiem — Eksplorator hosta systemu Windows**: Umożliwia eksplorowanie aktywności konta, wykonywania procesów, aktywności sieciowej i innych zdarzeń na hoście.
- **Polowanie z przewodnikiem — badanie usługi Office365:** polowanie na podejrzaną aktywność usługi Office 365 w wielu zestawach danych usługi Office 365.

[Repozytorium GitHub społeczności azure sentinel](https://github.com/Azure/Azure-Sentinel) to miejsce dla przyszłych notesów usługi Azure Sentinel utworzonych przez firmę Microsoft lub przekazanych przez społeczność.

Aby korzystać z notesów, musisz mieć konto notesów platformy Azure. Aby uzyskać więcej informacji, zobacz [Szybki start: Zaloguj się i ustaw identyfikator użytkownika](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) z dokumentacji notesów platformy Azure. Aby utworzyć to konto, możesz użyć opcji **Zarejestruj się dla notesów platformy Azure** na pasku poleceń w **usłudze Azure Sentinel — notesy:**

> [!div class="mx-imgBorder"]
>![Zarejestruj się w witrynie Azure Notebooks option](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Notes można uruchomić bezpośrednio z usługi Azure Sentinel lub sklonować wszystkie notesy usługi Azure Sentinel do nowego projektu notesów platformy Azure.

## <a name="run-a-notebook-from-azure-sentinel"></a>Uruchamianie notesu z usługi Azure Sentinel
 
1. W witrynie Azure portal przejdź do**notesów****zarządzania usługą** >  **Azure Sentinel** > Threat , gdzie można wyświetlić notesy, które udostępnia usługa Azure Sentinel. 

2. Wybierz poszczególne notesy, aby odczytać ich opisy, wymagane typy danych i źródła danych. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![uruchamianie notebooka](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Wybierz notes, którego chcesz użyć, a następnie wybierz pozycję **Uruchom notes (Wersja zapoznawcza),** aby sklonować i skonfigurować notes w nowym projekcie notesów platformy Azure, który łączy się z obszarem roboczym usługi Azure Sentinel. Po zakończeniu procesu notes zostanie otwarty w notesach platformy Azure, aby można go uruchomić.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Klonowanie notesów usługi Azure Sentinel do nowego projektu notesów platformy Azure

Ta procedura tworzy projekt notesów platformy Azure dla Ciebie, który zawiera notesy usługi Azure Sentinel. Następnie można uruchomić notesy w stanie bez zmian lub wprowadzić w nich zmiany, a następnie uruchomić je.

1. W witrynie Azure portal przejdź do**notesów** **zarządzania usługą** >  **Azure Sentinel** > Threat, a następnie wybierz pozycję **Klonuj notesy** na pasku poleceń:
  
    > [!div class="mx-imgBorder"]
    >![Opcja Klonuj notesy](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Po wyświetleniu następującego okna dialogowego wybierz pozycję **Importuj,** aby sklonować repozytorium Usługi GitHub do projektu notesów platformy Azure. Jeśli nie masz istniejącego konta notesów platformy Azure, zostanie wyświetlony monit o utworzenie jednego z nich i zalogowanie się.

   ![Importowanie notesu](./media/notebooks/sentinel-notebooks-clone.png)

3. W oknie dialogowym **Przekazywanie repozytorium GitHub** nie **wybieraj cyklicznie opcji Klonuj,** ponieważ ta opcja odnosi się do połączonych repozytoriów GitHub. W przypadku nazwy projektu należy użyć domyślnej nazwy lub typu w nowej. Następnie kliknij przycisk **Importuj,** aby rozpocząć klonowanie zawartości usługi GitHub, co może potrwać kilka minut.

   ![Importowanie notesu](./media/notebooks/sentinel-create-project.png)

4. Otwórz utworzony właśnie projekt, a następnie otwórz folder **Notesy,** aby wyświetlić notesy. Przykład:

   ![Importowanie repo](./media/notebooks/sentinel-open-notebook1.png)

Następnie można uruchomić notesy z notesów platformy Azure. Aby powrócić do tych notesów z usługi Azure Sentinel, wybierz pozycję **Przejdź do notesów** z paska poleceń w **usłudze Azure Sentinel — notesy:**

> [!div class="mx-imgBorder"]
>![Przejdź do opcji Notesy](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Używanie notesów do polowania

Każdy notebook przeprowadzi Cię przez kroki do przeprowadzenia polowania lub dochodzenia. Biblioteki i inne zależności wymagane przez notes można zainstalować z samego notesu lub za pomocą prostej procedury konfiguracji. Konfiguracja, która wiąże projekt notesu z powrotem do subskrypcji usługi Azure Sentinel jest automatycznie aprowizowana w poprzednich krokach.

1. Jeśli nie masz jeszcze w notesach platformy Azure, możesz użyć opcji **Przejdź do notesów** z paska poleceń w **usłudze Azure Sentinel — Notesy:**
    
    > [!div class="mx-imgBorder"]
    >![Przejdź do opcji Notesy](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    W notesach platformy Azure wybierz pozycję **Moje projekty**, a następnie projekt zawierający notesy usługi Azure Sentinel, a na koniec folder **Notesy.**
    
2. Przed otwarciem notesu należy pamiętać, że domyślnie do uruchamiania notesów jest zaznaczona opcja Wolne obliczenia:
    
   ![wybieranie notesu](./media/notebooks/sentinel-open-notebook2.png)
    
    Jeśli skonfigurowano maszyny wirtualne do nauki o danych (DSVM) do użycia zgodnie z instrukcją we wstępie, wybierz dsvm i uwierzytelnić przed otwarciem pierwszego notesu. 

3. Wybierz notes, aby go otworzyć.
    
    Przy pierwszym otwarciu notesu może zostać wyświetlony monit o wybranie wersji jądra. Jeśli nie zostanie wyświetlony monit, możesz wybrać wersję jądra z **jądra** >  **Zmień jądro,** a następnie wybrać wersję o co najmniej 3,6. Wybrana wersja jądra jest wyświetlana w prawym górnym rogu okna notesu:
    
   ![wybieranie notesu](./media/notebooks/sentinel-select-kernel.png)

4. Przed wprowadzeniem jakichkolwiek zmian w pobranym notesie warto wykonać kopię oryginalnego notesu i pracować nad kopią. Aby to zrobić, wybierz **opcję File** > **Make a Copy**. Praca nad kopiami umożliwia bezpieczną aktualizację do przyszłych wersji notesów bez zastępowania żadnych danych.
    
    Teraz możesz uruchomić lub edytować wybrany notes.

Zalecenia:

- Aby uzyskać krótkie wprowadzenie do wykonywania zapytań o dane w usłudze Azure Sentinel, zapoznaj się z notesem [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) w głównym **folderze Notesy.** 

- Dodatkowe przykładowe notesy znajdziesz w podfolderze **Notesy przykładowe.** Te przykładowe notesy zostały zapisane z danymi, dzięki czemu łatwiej jest zobaczyć zamierzone dane wyjściowe. Zalecamy przeglądanie tych notebooków w [nbviewer](https://nbviewer.jupyter.org/). 

- Folder **HowTos** zawiera notesy opisujące na przykład: Ustawianie domyślnej wersji języka Python, konfigurowanie dsvm, tworzenie zakładek usługi Azure Sentinel z notesu i inne tematy.

Dostarczone notesy są przeznaczone zarówno jako przydatne narzędzia, jak i ilustracje i przykłady kodu, których można użyć w tworzeniu własnych notesów.

Z zadowoleniem przyjmujemy opinie, czy sugestie, prośby o funkcje, przekazane notesy, raporty o błędach lub ulepszenia i dodatki do istniejących notesów. Przejdź do [usługi Azure Sentinel Community GitHub,](https://github.com/Azure/Azure-Sentinel) aby utworzyć problem lub rozwidlenie i przekazać wkład.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak rozpocząć korzystanie z notesów Jupyter w usłudze Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:

- [Proaktywne polowanie na zagrożenia](hunting.md)
- [Użyj zakładek, aby zapisać interesujące informacje podczas polowania](bookmarks.md)
