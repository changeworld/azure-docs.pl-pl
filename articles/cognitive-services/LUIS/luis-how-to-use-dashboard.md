---
title: Pulpit nawigacyjny aplikacji dla aplikacji LUIS | Dokumentacja firmy Microsoft
description: Informacje o pulpicie nawigacyjnym aplikacji wizualizowanego narzędzie raportowania, który umożliwia monitorowanie aplikacji jeden rzut oka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: c7ef38e2f2edaf795d3d76706afd4aa09b3b6959
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110052"
---
# <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji
Pulpit nawigacyjny aplikacji umożliwia monitorowanie aplikacji jeden rzut oka. **Pulpitu nawigacyjnego** są wyświetlane po otwarciu aplikacji, klikając nazwę aplikacji **Moje aplikacje** następnie wybierz opcję **pulpitu nawigacyjnego** z górnym panelu. 

> [!CAUTION]
> Jeśli chcesz aktualną metryki dla LUIS należy:
> * Użyj LUIS [klucz punktu końcowego](luis-how-to-azure-subscription.md) utworzona na platformie Azure
> * Klucz punktu końcowego LUIS użycia dla wszystkich żądań punktu końcowego, w tym LUIS [interfejsu API](https://aka.ms/luis-endpoint-apis) i bot
> * Dla każdej aplikacji LUIS za pomocą klawisza innym punktem końcowym. Nie należy używać klucza jeden punkt końcowy dla wszystkich aplikacji. Klucz punktu końcowego są śledzone na poziomie klucza, nie na poziomie aplikacji.  

**Pulpitu nawigacyjnego** strony zawiera przegląd aplikacji LUIS bieżącego modelu w tym stanie, a także [punktu końcowego](luis-glossary.md#endpoint) użycia wraz z upływem czasu. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>Stan aplikacji
Pulpit nawigacyjny Wyświetla szkolenia aplikacji i stan, w tym datę i godzinę, kiedy aplikacja była ostatnim publikowania uczenia i opublikowane.  

![Pulpit nawigacyjny — stanu aplikacji](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Statystyki modelu danych
Pulpit nawigacyjny Wyświetla łącznej liczby lokalizacji docelowych, jednostki i etykietą zniesławiających istniejących w aplikacji. 

![Statystyki dane aplikacji](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Trafienia punktu końcowego
Pulpit nawigacyjny Wyświetla trafień całkowita punktu końcowego, LUIS aplikacja odbiera i określanie, do wyświetlenia: trafienia w terminie który umożliwia. Całkowita liczba trafień, wyświetlane jest sumą trafień punktu końcowego, które używają [klucz punktu końcowego](./luis-concept-keys.md#endpoint-key) i punktu końcowego trafienia używające [Tworzenie klucza](./luis-concept-keys.md#authoring-key).

<!-- TBD: this image is old but I don't have a new one based on usage -->
![Trafienia punktu końcowego](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Liczba trafień najbardziej bieżący punkt końcowy znajduje się w portalu Azure na LUIS Omówienie usługi. 
 
### <a name="total-endpoint-hits"></a>Liczba całkowita punktu końcowego
Całkowita liczba trafień punktu końcowego odebranych aplikacji od czasu utworzenia aplikacji poprzedzającym bieżącą datę.

### <a name="endpoint-hits-per-period"></a>Trafień punktu końcowego w okresie
Liczba trafień otrzymanych w ciągu ostatnich wyświetlanych na dzień. Punkty od daty rozpoczęcia i zakończenia reprezentują dni, w tym okresie. Umieść wskaźnik myszy nad każdego punktu, aby zobaczyć liczba trafień każdego dnia w okresie. 

Aby wybrać okres, aby wyświetlić na wykresie:
 
1. Kliknij przycisk **dodatkowe ustawienia** ![przycisk dodatkowe ustawienia](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) dostęp do listy okresów. Możesz wybrać okresy od jednego tygodnia się do jednego roku. 

    ![Trafień punktu końcowego w okresie](./media/luis-how-to-use-dashboard/timerange.png)

2. Z listy wybierz okres, a następnie kliknij strzałkę Wstecz ![Strzałka wstecz](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Aby wyświetlić na wykresie.

### <a name="key-usage"></a>Użycie klucza
Liczba trafień używane z klucza punkt końcowy aplikacji. Aby uzyskać więcej informacji o kluczach punktu końcowego, zobacz [kluczy w LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Podział konwersji
**Podział zamiar** przedstawia podział na podstawie zniesławiających etykietą lub trafień punktu końcowego lokalizacji docelowych. Ten wykres podsumowania przedstawia względnego każdego zamiar w aplikacji. Po umieszczeniu wskaźnika myszy na wycinek Zobacz konwersji nazwy i które reprezentuje procent całkowitej liczby trafień etykietą zniesławiających/punktu końcowego. 

![Podział konwersji](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Do kontrolowania, czy podziału jest oparta na zniesławiających etykietą lub trafień punktu końcowego:

1. Kliknij przycisk **dodatkowe ustawienia** ![przycisk dodatkowe ustawienia](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) dostęp do listy, tak jak na poniższej ilustracji:

    ![Lista konwersji podziału](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Wybierz wartość z listy, a następnie kliknij strzałkę Wstecz ![Strzałka wstecz](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Aby wyświetlić na wykresie.

## <a name="entity-breakdown"></a>Podział jednostki
Pulpitu nawigacyjnego przedstawia podział jednostek na podstawie zniesławiających etykietą lub trafień punktu końcowego. Ten wykres podsumowania przedstawia względnego każdej jednostki w aplikacji. Po umieszczeniu wskaźnika myszy na wycinek Zobacz nazwa podmiotu i wartość procentowa w trafień etykietą zniesławiających/punktu końcowego. 

![Podział jednostki](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Do kontrolowania, czy podziału jest oparta na zniesławiających etykietą lub trafień punktu końcowego:

1. Kliknij przycisk **dodatkowe ustawienia** ![przycisk dodatkowe ustawienia](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) dostęp do listy, tak jak na poniższej ilustracji:

    ![Lista podział jednostki](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Wybierz wartość z listy, a następnie kliknij strzałkę Wstecz ![Strzałka wstecz](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Aby wyświetlić odpowiednio wykresu.
