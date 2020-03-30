---
title: Zarządzanie farmami
description: W tym artykule opisano sposób zarządzania farmami
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271722"
---
# <a name="manage-farms"></a>Zarządzanie farmami

Możesz zarządzać swoimi farmami w usłudze Azure FarmBeats. Ten artykuł zawiera informacje o tym, jak tworzyć farmy, instalować urządzenia, czujniki i drony, które pomagają zarządzać farmami.

## <a name="create-farms"></a>Tworzenie gospodarstw rolnych

Wykonaj następujące czynności:

1. Zaloguj się do akceleratora farm, zostanie wyświetlona strona **Farmy.**
    Na stronie **Farmy** jest wyświetlana lista farm w przypadku, gdy zostały one już utworzone w ramach subskrypcji.

    Oto przykładowy obraz:

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Wybierz **pozycję Utwórz farmę** i podaj **nazwę**, **Uprawy** i **Adres**.
3. W polu **Definiuj granicę farmy**(pole obowiązkowe) wybierz opcję **Oznacz na mapie** lub **Wklej kod GeoJSON**.

Oto dwa sposoby definiowania granicy farmy:

1. **Oznacz na mapie:** Użyj narzędzia sterowania mapą, aby narysować i oznaczyć granicę farmy. Aby oznaczyć ![granice, Project](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) Farm Beats i zaznacz dokładne granice.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Wklej Kod GeoJson:** GeoJSON jest formatem kodowania struktur danych geograficznych, przy użyciu notacji obiektu JavaScript (JSON). Ta opcja wyświetla pole tekstowe, w którym można wprowadzić ciąg GeoJSON w celu oznaczenia granic farmy. Można również utworzyć kod GeoJSON z GeoJSON.io.
Użyj etykietek narzędzi, aby wypełnić informacje.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Wybierz **opcję Prześlij,** aby utworzyć farmę. Nowa farma jest tworzona i wyświetlana na stronie **Farmy.**

## <a name="view-farm"></a>Zobacz farmę

Na stronie Lista Farm jest wyświetlana lista utworzonych farm. Wybierz farmę, aby wyświetlić listę:

 - **Liczba urządzeń** — wyświetla liczbę i stan urządzeń wdrożonych w farmie.
 - **Mapa** — mapa gospodarstwa z urządzeniami wdrożonymi w gospodarstwie.
 - **Dane telemetryczne** — wyświetla dane telemetryczne z czujników wdrożonych w farmie.
 - **Najnowsze mapy precyzyjne** — wyświetla najnowszą mapę wskaźników satelitarnych (EVI, NDWI), mapę ciepła wilgotności gleby i mapę rozmieszczenia czujników.

## <a name="edit-farm"></a>Edytuj farmę

Na stronie **Farmy** jest wyświetlana lista utworzonych farm.

1.  Wybierz farmę, aby wyświetlić i edytować farmę.
2.  Wybierz **pozycję Edytuj farmę,** aby edytować informacje o farmie. W oknie **Szczegóły farmy** można edytować **nazwy**, **Uprawy**, **Adres**i zdefiniować pola **Granice gospodarstwa.**

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Wybierz **pozycję Prześlij,** aby zapisać edytowane szczegóły.

## <a name="delete-farm"></a>Usuwanie farmy

Na stronie **Farmy** jest wyświetlana lista utworzonych farm. Aby usunąć farmę, należy wykonać następujące czynności:

1.  Wybierz farmę z listy, aby usunąć szczegóły farmy.
2.  Wybierz **pozycję Usuń farmę,** aby usunąć farmę.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Po usunięciu farmy urządzenia i mapy skojarzone z farmą nie są usuwane. Wszelkie informacje o farmie skojarzone z urządzeniem i mapami nie będą istotne. Możesz nadal wyświetlać urządzenia, dane telemetryczne i mapy z usługi FarmBeats.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy utworzyłeś swoją farmę, dowiedz się, jak [uzyskać dane z czujników](get-sensor-data-from-sensor-partner.md) płynące do twojej farmy.
