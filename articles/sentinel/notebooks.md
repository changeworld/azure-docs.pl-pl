---
title: Możliwości łowiectwa przy użyciu notesów na platformie Azure — wskaźnikowa | Microsoft Docs
description: W tym artykule opisano, jak używać notesów z możliwościami polowania na platformie Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5b90ecc1db686b698668b07bd839304b425445ca
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240526"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Korzystanie z notesów Jupyter do wyszukiwania zagrożeń bezpieczeństwa

Podstawą platformy Azure jest magazyn danych; łączy ona wysoką wydajność zapytań, schematu dynamicznego i skaluje się do dużych ilości danych. Aby uzyskać dostęp do tego magazynu danych, należy użyć wspólnego interfejsu API w portalu Azure — wskaźniku i wszystkich narzędziach usługi Azure wskaźnikowych. Ten sam interfejs API jest również dostępny dla narzędzi zewnętrznych, takich jak notesy [Jupyter](https://jupyter.org/) i Python. Chociaż wiele typowych zadań można wykonać w portalu, Jupyter rozszerza zakres tego, co można zrobić za pomocą tych danych. Łączy ona pełen programowalność z ogromną kolekcją bibliotek na potrzeby uczenia maszynowego, wizualizacji i analizy danych. Te atrybuty sprawiają, że Jupyter narzędzi do dochodzenia do zabezpieczeń i polowania.

![Przykładowy Notes](./media/notebooks/sentinel-notebooks-map.png)

Środowisko Jupyter zostało zintegrowane z portalem usługi Azure wskaźnikiem, co ułatwia tworzenie i wykonywanie notesów w celu analizowania danych. Biblioteka *Kqlmagic* zapewnia klej, który umożliwia wykonywanie zapytań z platformy Azure, i uruchamianie ich bezpośrednio w notesie. Zapytania używają [języka zapytań Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Kilka notesów opracowanych przez niektórych analityków zabezpieczeń firmy Microsoft jest pakowanych z platformą Azure — wskaźnikiem. Niektóre z tych notesów zostały skompilowane w konkretnym scenariuszu i mogą być używane w taki sam sposób. Inne zawarto jako przykłady ilustrujące techniki i funkcje, które można skopiować lub dostosować do użycia we własnych notesach. Inne notesy mogą być również importowane z witryny Azure

Zintegrowane środowisko Jupyter korzysta z [Azure Notebooks](https://notebooks.azure.com/) do przechowywania, udostępniania i wykonywania notesów. Możesz również uruchamiać te notesy lokalnie (Jeśli masz środowisko Python i Jupyter na komputerze) lub w innych środowiskach JupterHub, takich jak Azure Databricks.

Notesy mają dwa składniki:

- Interfejs oparty na przeglądarce, w którym można wprowadzać i uruchamiać zapytania i kod oraz miejsce wyświetlania wyników wykonania.
- *jądro* odpowiedzialne za analizowanie i wykonywanie kodu. 

W Azure Notebooks to jądro jest domyślnie uruchamiane w *bezpłatnej usłudze Azure Storage i magazyn w chmurze* . Jeśli notesy zawierają złożone modele uczenia maszynowego lub wizualizacje, należy rozważyć użycie bardziej zaawansowanych, dedykowanych zasobów obliczeniowych, takich jak [Virtual Machines analizy danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Notesy na Twoim koncie są przechowywane jako prywatne, chyba że zdecydujesz się je udostępnić.

W notesach usługi Azure Pandas są używane wiele popularnych bibliotek języka Python, takich jak, matplotlib, bokeh i inne. Istnieje ogromna liczba innych pakietów języka Python, które można wybrać, obejmujących obszary takie jak:

- wizualizacje i grafika
- przetwarzanie i analiza danych
- Statystyka i obliczenia liczbowe
- Uczenie maszynowe i uczenie głębokie

Wydano również niektóre narzędzia zabezpieczeń Jupyter typu open source w pakiecie o nazwie [msticpy](https://github.com/Microsoft/msticpy/). Ten pakiet jest używany w wielu z dołączonych notesów. Narzędzia Msticpy zostały zaprojektowane specjalnie w celu ułatwienia tworzenia notesów dla łowiectwa i badania, a my pracujemy nad nowymi funkcjami i ulepszeniami.

Notesy początkowe obejmują:

- **Badanie z przewodnikiem — alerty procesów**: Umożliwia szybkie Klasyfikacja alertów przez analizowanie aktywności na odpowiednich hostach.
- **Łowiectwo z przewodnikiem — Eksplorator hosta systemu Windows**: Umożliwia Eksplorowanie aktywności konta, wykonywania procesów, aktywności sieci i innych zdarzeń na hoście.  
- **Łowiectwo z przewodnikiem — usługi Office 365**: W przypadku podejrzanych działań związanych z pakietem Office 365 w wielu zestawach danych usługi O365.

[Repozytorium Azure wskaźnikowego społeczności GitHub](https://github.com/Azure/Azure-Sentinel) jest lokalizacją dla każdego przyszłego notesu usługi Azure wskaźnikowego, utworzonego przez firmę Microsoft lub pochodzącego od społeczności.

## <a name="run-a-notebook"></a>Uruchamianie notesu

W poniższym przykładzie utworzysz projekt Azure Notebooks z portalu Azure wskaźnikowego, wypełniania projektu notesami. Przed rozpoczęciem korzystania z tych notesów dobrze jest utworzyć kopię notesu i rozpocząć korzystanie z kopii. Praca nad kopiami pozwala bezpiecznie aktualizować do przyszłych wersji notesów bez zastępowania żadnych danych.

1. W portalu usługi Azure wskaźnikowym kliknij pozycję **notesy** w menu nawigacji. Aby utworzyć nowy projekt Azure Notebooks, kliknij pozycję **Klonuj notesy usługi Azure wskaźnikowej** lub Otwórz istniejące projekty notesów, a następnie kliknij pozycję **Przejdź do notesów**.
  
   ![Wybierz notesy](./media/notebooks/sentinel-azure-notebooks-home.png)

2. W przypadku wybrania opcji **klonowania notesów Azure wskaźnikowego** w poprzednim kroku zostanie wyświetlone następujące okno dialogowe. Kliknij przycisk **Importuj** , aby sklonować repozytorium GitHub do projektu Azure Notebooks. Jeśli nie masz istniejącego konta Azure Notebooks, zostanie wyświetlony monit o utworzenie i zalogowanie się.

   ![Importuj Notes](./media/notebooks/sentinel-notebooks-clone.png)

3. Podczas tworzenia nowego projektu należy nazwać projekt — Użyj nazwy domyślnej lub typu w nowym. Nie sprawdzaj **cyklicznie opcji klonowania** — ta opcja odnosi się do połączonych repozytoriów GitHub. Kliknięcie przycisku **Importuj** spowoduje odprowadzenie klonowania zawartości usługi GitHub, co może potrwać kilka minut.

   ![Importuj Notes](./media/notebooks/sentinel-create-project.png)

4. Otwórz folder **notesy** , aby wyświetlić notesy. Każdy Notes przeprowadzi Cię przez kroki w celu przeprowadzenia wyszukiwania lub badania. Biblioteki i inne zależności wymagające tego notesu można zainstalować z notesu lub za pomocą prostej procedury konfiguracji. Konfiguracja, która wiąże Twój projekt notesu z powrotem z subskrypcją usługi Azure wskaźnikowego, jest automatycznie obsługiwana w poprzednich krokach. Twoje notesy są gotowe do uruchomienia w obszarze roboczym Log Analytics wskaźnikowej platformy Azure.

   ![Importuj repozytorium](./media/notebooks/sentinel-open-notebook1.png)

5. Otwórz Notes. Bezpłatne obliczanie jest domyślnie zaznaczone, aby uruchomić notesy (wyróżnione). Jeśli skonfigurowano DSVM do użycia (patrz powyżej), wybierz DSVM i Uwierzytelnij przed otwarciem pierwszego notesu. Kliknij Notes, aby go otworzyć.

   ![Wybieranie notesu](./media/notebooks/sentinel-open-notebook2.png)

6. Wybieranie wersji języka Python. Po pierwszym otwarciu notesu może pojawić się monit o wybranie wersji jądra. W przeciwnym razie wybierz jądro, które ma być używane w następujący sposób. Środowisko Python 3,6 lub nowszy powinno być wybranym jądrem (w prawym górnym rogu okna Notes).

   ![Wybieranie notesu](./media/notebooks/sentinel-select-kernel.png)

Aby zapoznać się z szybkim wprowadzeniem do wykonywania zapytań dotyczących danych na platformie Azure, zapoznaj się z notesem [getstarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) w folderze głównym notesów. Dodatkowe przykładowe notesy znajdują się w podfolderze **Samples-Notess** . Przykładowe notesy zostały zapisane z danymi, dzięki czemu łatwiej jest zobaczyć zamierzone dane wyjściowe (zalecamy wyświetlanie ich w [nbviewer](https://nbviewer.jupyter.org/)). Folder **HOWTOs** zawiera notesy opisujące, na przykład: ustawienie domyślnej wersji języka Python, skonfigurowanie DSVM, tworzenie zakładek wskaźnikowych platformy Azure z notesu i innych tematów.

Te notesy są przeznaczone zarówno do przydatnych narzędzi, jak i przykładów kodu, których można używać do tworzenia własnych notesów.

Zapraszamy do przekazywania opinii, sugestii, żądań dotyczących funkcji, notesów programu, raportów błędów lub ulepszeń oraz dodatków do istniejących notesów. Przejdź do witryny [Azure wskaźnikowej społeczności GitHub](https://github.com/Azure/Azure-Sentinel) , aby utworzyć problem lub rozwidlenie i przekazać wkład.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rozpoczynania pracy z notesami Jupyter na platformie Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- [Proaktywne wyszukiwanie zagrożeń](hunting.md)
- [Korzystanie z zakładek do zapisywania interesujących informacji podczas polowania](bookmarks.md)
