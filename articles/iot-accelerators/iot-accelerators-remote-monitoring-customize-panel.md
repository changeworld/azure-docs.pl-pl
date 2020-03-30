---
title: Dodawanie panelu do interfejsu użytkownika rozwiązania do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak dodać nowy panel do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447067"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie panelu niestandardowego do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania

W tym artykule pokazano, jak dodać nowy panel do strony pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania. W artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nowy panel do strony pulpitu nawigacyjnego w interfejsie użytkownika sieci Web.

Przykładowy panel w tym artykule jest wyświetlany na istniejącej stronie pulpitu nawigacyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, na lokalnym komputerze deweloperskim należy zainstalować następujące oprogramowanie:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać kroki opisane na stronie Dodawanie niestandardowego do artykułu [interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania.](iot-accelerators-remote-monitoring-customize-page.md)

## <a name="add-a-panel"></a>Dodawanie panelu

Aby dodać panel do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe definiujące panel, a następnie zmodyfikować pulpit nawigacyjny, aby wyświetlić panel.

### <a name="add-the-new-files-that-define-the-panel"></a>Dodawanie nowych plików definiujących panel

Aby rozpocząć, **src /instruktaż/składniki/strony/deska rozdzielcza/panele/przykładPanel** folder zawiera pliki definiujące panel, w tym:

**przykładPanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Skopiuj folder **src/walkthrough/components/pages/dashboard/panels/examplePanel** do folderu **src/components/pages/dashboard/panels** folder.

Dodaj następujący eksport do pliku **src/instruktażowy/składniki/strony/dashboard/panele/index.js:**

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Dodawanie panelu do pulpitu nawigacyjnego

Zmodyfikuj **src/components/pages/dashboard/dashboard.js,** aby dodać panel.

Dodaj przykładowy panel do listy importów z paneli:

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

## <a name="test-the-flyout"></a>Testowanie wysuwu wysuwu

Jeśli interfejs użytkownika sieci Web nie jest jeszcze uruchomiony lokalnie, uruchom następujące polecenie w katalogu głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika [http://localhost:3000/dashboard](http://localhost:3000/dashboard)lokalnie w pliku . Przejdź do strony **pulpitu nawigacyjnego,** aby wyświetlić nowy panel.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostępnych zasobach ułatwiające dodawanie lub dostosowywanie pulpitów nawigacyjnych w interfejsie użytkownika sieci Web w akceleratorze rozwiązań do zdalnego monitorowania.

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
