---
title: Dodawanie panelu z rozwiązaniem monitorowania zdalnego interfejsu użytkownika — Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób dodawania nowego panelu do pulpitu nawigacyjnego w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 656a2375b8b1033e9b698d5d8e1e2bbc32b32a79
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094585"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie panelu niestandardowe do pulpitu nawigacyjnego w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika

W tym artykule pokazano, jak dodać nowy panel na stronie pulpitu nawigacyjnego w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika. W artykule opisano:

- Jak przygotować lokalne Środowisko deweloperskie.
- Jak dodać nowy panel do strony pulpitu nawigacyjnego w internetowym interfejsie użytkownika.

Panel przykład, w tym artykule, wyświetla się na istniejącej strony pulpitu nawigacyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, potrzebne są następujące oprogramowanie zainstalowane na lokalnej maszynie do programowania:

- [Usługa Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Należy wykonać czynności opisane w [dodawania niestandardowej strony do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-page.md) artykuł, aby kontynuować.

## <a name="add-a-panel"></a>Dodawanie panelu

Aby dodać panel do interfejsu użytkownika sieci web, należy dodać pliki źródłowe, które definiują panelu, a następnie zmodyfikuj pulpitu nawigacyjnego, aby wyświetlić panel.

### <a name="add-the-new-files-that-define-the-panel"></a>Dodaj nowe pliki, które definiują panelu

Ułatwią Ci rozpoczęcie pracy, **src/wskazówki/składniki/stron/pulpit nawigacyjny/panele/examplePanel** folder zawiera pliki, które definiują panelu, w tym:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopiowanie **src/wskazówki/składniki/stron/pulpit nawigacyjny/panele/examplePanel** folder do **src/składniki/stron/pulpit nawigacyjny/paneli** folderu.

Dodawanie następującej operacji eksportowania do **src/walkthrough/components/pages/dashboard/panels/index.js** pliku:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Dodawanie panelu do pulpitu nawigacyjnego

Modyfikowanie **src/components/pages/dashboard/dashboard.js** dodać panel.

Dodaj panel przykład do listy importów z paneli:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Dodaj następującą definicję komórki do siatki w zawartości strony:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-fly-out"></a>Testowanie staną się

Jeśli internetowy interfejs użytkownika nie jest jeszcze uruchomiona lokalnie, uruchom następujące polecenie w folderze głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejsu użytkownika lokalnie [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Przejdź do **pulpit nawigacyjny** strony, aby wyświetlić nowy panel.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano dostępnych zasobów umożliwiających można dodawać lub dostosowywać pulpity nawigacyjne w internetowym interfejsie użytkownika w akceleratora rozwiązania monitorowania zdalnego.

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
