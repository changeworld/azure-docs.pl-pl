---
title: Omówienie skoroszytów usługi Azure Monitor
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658238"
---
# <a name="azure-monitor-workbooks"></a>Skoroszyty monitora platformy Azure

Skoroszyty zapewniają elastyczną kanwę do analizy danych i tworzenia bogatych raportów wizualnych w witrynie Azure portal. Umożliwiają one korzystanie z wielu źródeł danych z całej platformy Azure i łączenie ich w ujednolicone interaktywne środowisko. 

## <a name="data-sources"></a>Źródła danych

Skoroszyty mogą wysyłać zapytania do danych z wielu źródeł na platformie Azure. Autorzy skoroszytów mogą przekształcać te dane, aby zapewnić wgląd w dostępność, wydajność, użycie i ogólną kondycję podstawowych składników. Na przykład analizowanie dzienników wydajności z maszyn wirtualnych w celu zidentyfikowania wystąpień procesora CPU lub małej ilości pamięci i wyświetlanie wyników jako siatki w raporcie interaktywnym.
  
Ale prawdziwą siłą skoroszytów jest możliwość łączenia danych z różnych źródeł w ramach jednego raportu. Pozwala to na tworzenie widoków zasobów złożonych lub łączy między zasobami, umożliwiając bogatsze dane i szczegółowe informacje, które w przeciwnym razie byłyby niemożliwe.

Skoroszyty są obecnie zgodne z następującymi źródłami danych:

* [Dzienniki](workbooks-data-sources.md#logs)
* [Metryki](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Alerty (wersja zapoznawcza)](workbooks-data-sources.md#alerts-preview)
* [Kondycja obciążenia (wersja zapoznawcza)](workbooks-data-sources.md#workload-health-preview)
* [Kondycja zasobów platformy Azure (wersja zapoznawcza)](workbooks-data-sources.md#azure-resource-health)
* [Eksplorator danych platformy Azure (wersja zapoznawcza)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Wizualizacje

Skoroszyty zapewniają bogaty zestaw funkcji wizualizacji danych. Szczegółowe przykłady każdego typu wizualizacji można znaleźć w poniższych przykładowych łączach:

* [Tekst](workbooks-visualizations.md#text)
* [Wykresy](workbooks-visualizations.md#charts)
* [Siatki](workbooks-visualizations.md#grids)
* [Płytki](workbooks-visualizations.md#tiles)
* [Drzew](workbooks-visualizations.md#trees)
* [Wykresy](workbooks-visualizations.md#graphs)

![Przykładowe wizualizacje skoroszytu](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Wprowadzenie

Aby zapoznać się ze skoroszytami, najpierw przejdź do usługi Azure Monitor. Można to zrobić, wpisując **Monitor** w polu wyszukiwania w witrynie Azure portal.

Następnie wybierz **skoroszyty (podgląd)**.

![Zrzut ekranu przedstawiający przycisk podglądu skoroszytów wyróżniony w czerwonym polu](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galeria

Spowoduje to przejście do galerii skoroszytów:

![Zrzut ekranu przedstawiający widok galerii skoroszytów usługi Azure Monitor](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Skoroszyty i szablony skoroszytów

_Skoroszyt_ jest zielony, a niektóre _szablony skoroszytu_ w kolorze fioletowym. Szablony służą jako wyselekcjonowane raporty, które są przeznaczone do elastycznego ponownego użycia przez wielu użytkowników i zespoły. Otwarcie szablonu powoduje utworzenie przejściowego skoroszytu wypełnionego zawartością szablonu. 

Można dostosować parametry skoroszytu opartego na szablonie i przeprowadzić analizę bez obawy o złamanie przyszłego doświadczenia raportowania dla współpracowników. Jeśli otworzysz szablon, dokonasz pewnych korekt, a następnie wybierz ikonę zapisz, którą zapiszesz jako skoroszyt, który następnie będzie pokazywał się na zielono, pozostawiając oryginalny szablon nietknięty. 

Pod maską szablony różnią się również od zapisanych skoroszytów. Zapisanie skoroszytu tworzy skojarzony zasób usługi Azure Resource Manager, podczas gdy skoroszyt przejściowy utworzony podczas otwierania szablonu nie ma skojarzonego z nim unikatowego zasobu. Aby dowiedzieć się więcej o tym, jak kontrola dostępu jest zarządzana w skoroszytach, zapoznaj się z [artykułem kontroli dostępu do skoroszytów](workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Eksplorowanie szablonu skoroszytu

Wybierz **opcję Analiza awarii aplikacji,** aby wyświetlić jeden z domyślnych szablonów skoroszytu aplikacji.

![Zrzut ekranu przedstawiający szablon analizy awarii aplikacji](./media/workbooks-overview/failure-analysis.png)

Jak wspomniano wcześniej, otwarcie szablonu tworzy tymczasowy skoroszyt, z którego można wchodzić w interakcje. Domyślnie skoroszyt otwiera się w trybie odczytu, w którym są wyświetlane tylko informacje dotyczące zamierzonego środowiska analizy utworzonego przez autora oryginalnego szablonu.

W przypadku tego konkretnego skoroszytu środowisko jest interaktywne. Możesz dostosować subskrypcję, aplikacje docelowe i zakres czasu danych, które chcesz wyświetlić. Po dokonaniu tych wyborów siatka żądań HTTP jest również interaktywna, przy czym wybranie pojedynczego wiersza spowoduje zmianę danych renderowanych na dwóch wykresach u dołu raportu.

### <a name="editing-mode"></a>Tryb edycji

Aby zrozumieć, jak ten szablon skoroszytu jest zebrany, musisz zamienić się w tryb edycji, wybierając pozycję **Edytuj**. 

![Zrzut ekranu przedstawiający szablon analizy awarii aplikacji](./media/workbooks-overview/edit.png)

Po przełączeniu do trybu edycji zauważysz, że po prawej stronie pojawi się kilka pól **edycji** odpowiadających poszczególnym aspektom skoroszytu.

![Zrzut ekranu przedstawiający przycisk Edytuj](./media/workbooks-overview/edit-mode.png)

Jeśli wybierzemy przycisk edycji bezpośrednio pod siatką danych żądania, widzimy, że ta część naszego skoroszytu składa się z zapytania Kusto względem danych z zasobu usługi Application Insights.

![Zrzut ekranu przedstawiający podstawowe zapytanie Kusto](./media/workbooks-overview/kusto.png)

Kliknięcie innych przycisków **Edycji** po prawej stronie spowoduje wyświetlenie szeregu podstawowych składników, które tworzą skoroszyty, takich jak pola tekstowe oparte na [znacznikach,](workbooks-visualizations.md#text)elementy interfejsu użytkownika [wyboru parametrów](workbooks-parameters.md) i inne [typy wykresu/wizualizacji.](workbooks-visualizations.md) 

Eksplorowanie gotowych szablonów w trybie edycji, a następnie modyfikowanie ich zgodnie z potrzebami i zapisywanie własnego skoroszytu niestandardowego to doskonały sposób, aby dowiedzieć się, co jest możliwe w skoroszytach usługi Azure Monitor.

## <a name="pinning-visualizations"></a>Przypinanie wizualizacji

Kroki tekstowe, kwerendy i metryki w skoroszycie można przypiąć za pomocą przycisku pinezki tych elementów, gdy skoroszyt jest w trybie pinezki lub jeśli autor skoroszytu włączył ustawienia dla tego elementu, aby ikona pinezki była widoczna. 

Aby uzyskać dostęp do trybu pinezki, kliknij przycisk **Edytuj,** aby przejść do trybu edycji, a następnie wybierz niebieską ikonę pinezki na górnym pasku. Następnie nad polem *Edycji* każdej odpowiedniej części skoroszytu po prawej stronie ekranu pojawi się pojedyncza ikona pinezki.

![Pin doświadczenie](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Stan skoroszytu jest zapisywany w momencie przypinania, a przypięte skoroszyty na pulpicie nawigacyjnym nie będą aktualizowane, jeśli podstawowy skoroszyt zostanie zmodyfikowany. Aby zaktualizować przypiętą część skoroszytu, należy usunąć i ponownie przypiąć tę część.

## <a name="dashboard-time-ranges"></a>Zakresy czasu pulpitu nawigacyjnego

Przypięte części kwerendy skoroszytu będą respektować zakres czasu pulpitu nawigacyjnego, jeśli przypięty element jest skonfigurowany do używania parametru *Zakres czasu.* Wartość zakresu czasu pulpitu nawigacyjnego będzie używana jako wartość parametru zakresu czasu, a każda zmiana zakresu czasu pulpitu nawigacyjnego spowoduje aktualizację przypiętego elementu. Jeśli przypięta część korzysta z zakresu czasu pulpitu nawigacyjnego, zobaczysz podtytuł przypiętej aktualizacji części, aby wyświetlić zakres czasu pulpitu nawigacyjnego za każdym razem, gdy zmienia się zakres czasu. 

Ponadto przypięte części skoroszytu przy użyciu parametru zakresu czasu będą automatycznie odświeżane z szybkością określoną przez zakres czasu pulpitu nawigacyjnego. Ostatni raz wyszukiwane zapytanie pojawi się w podtytule przypiętej części.

Jeśli przypięty krok ma jawnie ustawiony zakres czasu (nie używa parametru zakresu czasu), ten zakres czasu będzie zawsze używany dla pulpitu nawigacyjnego, niezależnie od ustawień pulpitu nawigacyjnego. Podtytuł przypiętej części nie będzie pokazywał zakresu czasu pulpitu nawigacyjnego, a kwerenda nie będzie automatycznie odświeżana na pulpicie nawigacyjnym. Podtytuł pokaże ostatni raz wykonane zapytanie.

> [!NOTE]
> Kwerendy korzystające ze źródła danych *korespondencji seryjnej* nie są obecnie obsługiwane podczas przypinania do pulpitów nawigacyjnych.

## <a name="sharing-workbook-templates"></a>Udostępnianie szablonów skoroszytów

Po rozpoczęciu tworzenia własnych szablonów skoroszytu możesz udostępnić je szerszej społeczności. Aby dowiedzieć się więcej i zapoznać się z innymi szablonami, które nie są częścią domyślnego widoku galerii usługi Azure Monitor, odwiedź nasze [repozytorium GitHub.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) Aby przeglądać istniejące skoroszyty, odwiedź [bibliotekę skoroszytów](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) w usłudze GitHub.

## <a name="next-step"></a>Następny krok

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
