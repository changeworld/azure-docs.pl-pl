---
title: Możliwości myślistwo przy użyciu notesów w usłudze Azure przez wartownika w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: W tym artykule opisano korzystanie z notesów możliwości myślistwo przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ae9d52e4a26825e4318a6afb8aadc86ac29fa2b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403116"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Umożliwia aplikacji Jupyter Notebooks możliwe pod kątem zagrożeń zabezpieczeń

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podstawą Azure przez wartownika to magazyn danych; łączy ona wysoką wydajność zapytań, schemat dynamiczny i możliwość skalowania do woluminów duża ilość danych. Portal Azure przez wartownika i wszystkie narzędzia platformy Azure przez wartownika Użyj wspólny interfejs API dostępu do tego magazynu danych. Tego samego interfejsu API jest również dostępna dla zewnętrznych narzędzi takich jak [Jupyter](https://jupyter.org/) notesów i Python. Chociaż wiele typowych zadań mogą odbywać się w portalu, Jupyter rozszerza zakres działania z tymi danymi. Łączy ona pełną programowania ogromny zbiór bibliotek, machine learning, wizualizacji i przeprowadzać analizę danych. Te atrybuty należy Jupyter atrakcyjnym narzędziem do badania zabezpieczeń i myślistwo.

![Przykład notesu](./media/notebooks/sentinel-nb-mapandtimeline.png)

Zintegrowaliśmy środowisko aplikacji Jupyter do portalu Azure przez wartownika, co ułatwia tworzenie i wykonywanie notesów do analizowania danych. *Kqlmagic* Biblioteka zapewnia spoiwo łączące umożliwia wybranie zapytania z platformy Azure przez wartownika i uruchamiaj je bezpośrednio w notesie. Użyj zapytania [język zapytania Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Kilka notesów, opracowane przez analityków zabezpieczeń firmy Microsoft, są dostarczane za pomocą platformy Azure przez wartownika. Niektóre z tych notesów są tworzone dla konkretnego scenariusza i mogą być używane jako-to. Inne osoby mają jako przykłady przedstawiające różne techniki i funkcje, które można skopiować lub dostosować do użytku w notesach własne. Inne notesów mogą być importowane z społeczności platformy Azure przez wartownika usługi GitHub.

Zintegrowane rozwiązanie Jupyter używa [notesów usługi Azure](https://notebooks.azure.com/) do przechowywania, udostępnianie i wykonać notesy. Można również uruchomić te notesy lokalnie (Jeśli masz środowisko Python i Jupyter na komputerze) lub w innych środowiskach JupterHub, takich jak usługi Azure Databricks.

Notesy ma dwa składniki:

- interfejsu opartego na przeglądarce, gdzie wprowadź i uruchamianie zapytań i kodu, i której wyniki wykonywania są wyświetlane.
- *jądra* jest odpowiedzialny za analizowaniu i wykonywaniu sam kod. 

W notesach Azure to jądra działa na platformie Azure *bezpłatnej chmury zasobów obliczeniowych i magazynowych* domyślnie. Czy notesy obejmują modele uczenia maszynowego złożonych wizualizacji należy rozważyć użycie bardziej wydajne, dedykowanej zasoby obliczeniowe, takie jak [maszyn wirtualnych do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Notesy na Twoim koncie są przechowywane prywatnych, chyba że użytkownik chce udostępnić je.

Notesy platformy Azure przez wartownika użyć wielu popularnych bibliotek języka Python, takich jak pandas, matplotlib, bokeh i inne. Istnieje ogromna liczba innych pakietów języka Python można wybrać z opisem zagadnień, takich jak:

- wizualizacje i grafiki
- przetwarzanie danych i analiza
- statystyki i obliczeń numerycznych
- uczenie maszynowe i uczenia głębokiego

Wydaliśmy również niektóre narzędzia typu open-source Jupyter zabezpieczeń w pakiecie o nazwie [msticpy](https://github.com/Microsoft/msticpy/). Ten pakiet jest używany w wielu notesów uwzględnione. Narzędzia Msticpy są przeznaczone specjalnie do pomocy w tworzeniu notatników myślistwo i badania aktywnie współpracujemy na nowe funkcje i ulepszenia.

Początkowa notesów obejmują:

- **Z przewodnikiem badania — alerty procesu**: Pozwala na szybkie klasyfikowania alertów, analizując działania na hostach, których to dotyczy.
- **Z przewodnikiem myślistwo - explorer hosta Windows**: Umożliwia Eksplorowanie aktywności konta, wykonania procesu, działania sieciowe i inne zdarzenia, na hoście.  
- **Z przewodnikiem myślistwo — poznawanie usługi Office 365**: Hunt dla podejrzanych działań usługi Office 365 w wielu zestawach danych usługi Office 365.

[Repozytorium usługi GitHub społeczności platformy Azure przez wartownika](https://github.com/Azure/Azure-Sentinel) jest lokalizacja platformy Azure przez wartownika notesów, wszystkie przyszłe utworzone przez firmę Microsoft lub współtworzonych przez społeczność.

## <a name="run-a-notebook"></a>Uruchamianie notesu

W poniższym przykładzie utworzymy projektu notesy platformy Azure z poziomu portalu Azure przez wartownika wypełnianie projektu z notesami. Przed rozpoczęciem korzystania z tych notesów jest dobry pomysł, aby utworzyć kopię Notes i pracować nad kopii. Praca z kopii pozwala bezpiecznie aktualizacji do przyszłych wersji notesów bez zastępowania żadnych danych.

1. W portalu Azure przez wartownika kliknij **notesów** w menu nawigacji. Aby utworzyć nowy projekt notesy platformy Azure, kliknij przycisk **klonowania Azure przez wartownika notesów** lub otworzyć istniejących notesów projektów kliknij **przejdź do notesów programu**.
  
   ![Wybierz notesów](./media/notebooks/sentinel-az-notebooks-home.png)

2. Jeśli została wybrana opcja **klonowania Azure przez wartownika notesów** w poprzednim kroku, pojawi się następujące okno dialogowe. Kliknij przycisk **importu** do sklonowania repozytorium GitHub w projekcie notesy platformy Azure. Jeśli nie masz istniejącego konta notesów usługi Azure zostanie wyświetlony monit ją utworzyć i zaloguj się.

   ![Importuj notesu](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Podczas tworzenia nowego projektu, musisz nadaj projektowi nazwę - Użyj domyślnej nazwy lub typu nowego. Nie zaznaczaj **rekursywnie Klonuj** opcja — ta opcja odnosi się do połączonej repozytoriów GitHub. Kliknięcie **importu** rozpoczyna się klonowanie zawartości usługi GitHub, co może zająć kilka minut.

   ![Importuj notesu](./media/notebooks/sentinel-create-nb-project.png)

4. Otwórz **notesów** folderze, aby zobaczyć notesów. Każdy notesu przeprowadzi Cię przez kroki przeprowadzania hunt lub badania. Biblioteki i inne zależności wymagane przez notesu można zainstalować z notesu, sama lub za pomocą procedury prostej konfiguracji. Konfiguracja, która projektu Notes do Twojej subskrypcji platformy Azure przez wartownika zostanie automatycznie aprowizowana w poprzednich krokach. Notesy są gotowe do uruchomienia obszaru roboczego usługi Azure przez wartownika Log Analytics.

   ![Importowanie repozytorium](./media/notebooks/sentinel-open-notebook1.png)

5. Otwieranie notesu. Bezpłatnych zasobów obliczeniowych jest domyślnie zaznaczona, aby uruchomić notesów (wyróżnione). Jeśli skonfigurowano nauki do użycia (zobacz powyżej), wybierz maszyny DSVM i Uwierzytelnij się przed otwarciem pierwszego notesu. Kliknij notes, aby go otworzyć.

   ![Wybierz notes](./media/notebooks/sentinel-open-notebook2.png)

6. Wybór wersji języka Python. Przy pierwszym otwarciu Notes, jego może monitują o wybranie wersji jądra. W przeciwnym razie wybierz jądra do użycia w następujący sposób. Python 3.6 lub nowszej powinna być wybrane jądra (w prawym górnym rogu okna notesu).

   ![Wybierz notes](./media/notebooks/sentinel-select-kernel.png)

Szybkie wprowadzenie do wykonywania zapytań danych platformy Azure przez wartownika, Przyjrzyj się [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) Notes w głównym folderze Notesy. Notesy dodatkowe przykładowy można znaleźć w **notesów przykładowe** podfolderu. Notesy próbki zostały zapisane z danymi, tak, aby było widać zamierzonych danych wyjściowych (Firma Microsoft zaleca, wyświetlając je w [nbviewer](https://nbviewer.jupyter.org/)). **HowTos** folder zawiera notesy opisującego, na przykład: ustawienie domyślnej wersji języka Python, konfigurowanie maszyny wirtualnej DSVM, tworzenie przez wartownika Azure zakładki z notesu, a inne podmioty.

Te notesy służą jako zarówno przydatnych narzędzi i ilustracje i przykłady kodu, które służą do tworzenia własnych notesów.

Chętnie poznamy opinię, czy sugestie, żądania dotyczące funkcji przyczynić się notesów, raportów o błędach lub ulepszenia i dodatki do istniejących notesów. Przejdź do [społeczności przez wartownika Azure w witrynie GitHub](https://github.com/Azure/Azure-Sentinel) Utwórz problem lub rozwidlenia i Przekaż wkład.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób rozpocząć korzystanie z notesów programu Jupyter w przez wartownika platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:

- [Proaktywnie możliwe pod kątem zagrożeń](hunting.md)
- [Używanie zakładek w celu zapisania interesujące informacje podczas myślistwo](bookmarks.md)