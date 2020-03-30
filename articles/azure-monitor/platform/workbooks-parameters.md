---
title: Skoroszyty monitora Platformy Azure tworzące parametry
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658221"
---
# <a name="workbook-parameters"></a>Parametry skoroszytu

Parametry umożliwiają autorom skoroszytu zbieranie danych wejściowych od konsumentów i odwoływanie się do niego w innych częściach skoroszytu — zwykle w celu zakresu zestawu wyników lub ustawienia właściwej wizualizacji. Jest to kluczowa funkcja, która umożliwia autorom tworzenie interaktywnych raportów i środowisk. 

Skoroszyty umożliwiają kontrolowanie sposobu prezentowania formantów parametrów konsumentom — pole tekstowe a rozwijane, pojedyncze i wielokrotne wybieranie, wartości z tekstu, JSON, KQL lub Azure Resource Graph itp.  

Obsługiwane typy parametrów obejmują:
* [Time](workbooks-time.md) - pozwala użytkownikowi wybrać z wstępnie wypełnionych zakresów czasu lub wybrać niestandardowy zakres
* [Rozwijana](workbooks-dropdowns.md) - umożliwia użytkownikowi wybranie wartości lub zestawu wartości
* [Tekst](workbooks-text.md) - umożliwia użytkownikowi wprowadzanie dowolnego tekstu
* [Zasób](workbooks-resources.md) — umożliwia użytkownikowi wybranie co najmniej jednego zasobu platformy Azure
* [Subskrypcja](workbooks-resources.md) — umożliwia użytkownikowi wybranie co najmniej jednego zasobu subskrypcji platformy Azure
* Typ zasobu — umożliwia użytkownikowi wybranie co najmniej jednej wartości typu zasobu platformy Azure
* Lokalizacja — umożliwia użytkownikowi wybranie jednej lub więcej wartości lokalizacji platformy Azure

Te wartości parametrów można odwoływać się w innych częściach skoroszytów za pośrednictwem powiązań lub rozszerzeń wartości.

## <a name="creating-a-parameter"></a>Tworzenie parametru
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru: `TimeRange` *(należy pamiętać, że nazwy __parametrów__ nie **mogą** zawierać spacji ani znaków specjalnych)*
    2. Nazwa wyświetlana: `Time Range` *(jednak nazwy __wyświetlane__ mogą zawierać spacje, znaki specjalne, emotikony itp.)*  
    2. Typ parametru:`Time range picker`
    3. Wymagane:`checked`
    4. Dostępne zakresy czasu: Ostatnia godzina, Ostatnie 12 godzin, Ostatnie 24 godziny, Ostatnie 48 godzin, Ostatnie 3 dni, Ostatnie 7 dni i Zezwalaj na wybór niestandardowego zakresu czasu
5. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.

   ![Obraz przedstawiający tworzenie parametru zakresu czasu](./media/workbooks-parameters/time-settings.png)

Tak będzie wyglądał skoroszyt w trybie odczytu, w stylu "Pigułki".

   ![Obraz przedstawiający parametr zakresu czasu w trybie odczytu](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Odwoływanie się do parametru
### <a name="via-bindings"></a>Za pośrednictwem wiązań
1. Dodaj formant kwerendy do skoroszytu i wybierz zasób usługi Application Insights.
2. Otwórz rozwijanie _Zakresu czasu_ `Time Range` i wybierz opcję z sekcji Parametry u dołu.
3. Wiąże to parametr zakresu czasu z zakresem czasu wykresu. Zakres czasu przykładowej kwerendy jest teraz Ostatnie 24 godziny.
4. Uruchom kwerendę, aby zobaczyć wyniki

    ![Obraz przedstawiający parametr zakresu czasu, do którego odwołuje się wiązania](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>w KQL
1. Dodaj formant kwerendy do skoroszytu i wybierz zasób usługi Application Insights.
2. W KQL wprowadź filtr zakresu czasu przy użyciu parametru:`| where timestamp {TimeRange}`
3. Spowoduje to rozszerzenie czasu `| where timestamp > ago(1d)`oceny kwerendy do , który jest wartością zakresu czasu parametru.
4. Uruchom kwerendę, aby zobaczyć wyniki

    ![Obraz przedstawiający zakres czasu, do którego odwołuje się KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>W tekście 
1. Dodawanie formantu tekstowego do skoroszytu.
2. W znacznikach wprowadź`The chosen time range is {TimeRange:label}`
3. Wybierz _gotowe edytowanie_
4. Formant tekstu wyświetli tekst: _Wybrany zakres czasu to Ostatnie 24 godziny_

## <a name="parameter-options"></a>Opcje parametrów
W sekcji W `label` _tekście_ użyto parametru zamiast jego wartości. Parametry ujawniają różne takie opcje w zależności od jego typu - np.

Użyj `Previews` sekcji okienka _Edytuj parametr,_ aby wyświetlić opcje rozszerzenia parametru:

![Obraz przedstawiający opcje parametrów zakresu czasu](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
