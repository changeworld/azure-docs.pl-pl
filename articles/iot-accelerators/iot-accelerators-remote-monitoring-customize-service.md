---
title: Dodać usługę do monitorowania zdalnego interfejsu użytkownika rozwiązania — Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób dodawania nowej usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/02/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447050"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodaj niestandardowe usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika

W tym artykule przedstawiono sposób dodawania nowej usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika. W artykule opisano:

- Jak przygotować lokalne Środowisko deweloperskie.
- Jak dodać nową usługę w internetowym interfejsie użytkownika.

Przykład usługi w tym artykule znajdują się dane dla siatki, [dodać siatkę niestandardowych do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-grid.md) artykule przedstawiono sposób dodawania.

W przypadku aplikacji platformy React usługi zwykle korzysta z usługi zaplecza. W akceleratora rozwiązania monitorowania zdalnego Przykładami usług wchodzących w interakcję z Menedżera Centrum IoT i konfiguracją mikrousług.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, potrzebne są następujące oprogramowanie zainstalowane na lokalnej maszynie do programowania:

- [Usługa Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Należy wykonać czynności opisane w [dodawania niestandardowej strony do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-page.md) artykule przed kontynuowaniem.

## <a name="add-a-service"></a>Dodawanie usługi

Aby dodać usługę do interfejsu użytkownika sieci web, należy dodać pliki źródłowe, które definiują usługi i zmodyfikować niektóre istniejące pliki, aby uświadomić interfejsu użytkownika sieci web nowej usługi.

### <a name="add-the-new-files-that-define-the-service"></a>Dodaj nowe pliki, które definiują usługi

Aby rozpocząć pracę, **wskazówki/src/usług** folder zawiera pliki, które definiują prostą usługę:

**exampleService.js**



Aby dowiedzieć się więcej na temat sposobu implementacji usług, zobacz [wprowadzenie do programowania reaktywnego dotąd istniała Brak](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**



Kopiuj **exampleService.js** do **src/usługi** folder i skopiuj **exampleModels.js** do **src/services/modeli** folderu.

Aktualizacja **index.js** w pliku **src/usługom** folderu do nowej usługi do wyeksportowania:

```js
export * from './exampleService';
```

Aktualizacja **index.js** w pliku **src/services/modeli** folder eksportu nowego modelu:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Konfigurowanie wywołań do usługi w sklepie

Aby rozpocząć pracę, **src/wskazówki/store/reduktorów** folder zawiera reduktor próbki:

**exampleReducer.js**



Kopiuj **exampleReducer.js** do **src/store/reduktorów** folderu.

Aby dowiedzieć się więcej na temat reduktor i **Epiki**, zobacz [redux obserwowalnymi](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurowanie oprogramowania pośredniczącego

Aby skonfigurować oprogramowanie pośredniczące, Dodaj reduktor do **rootReducer.js** w pliku **src/store** folderu:

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Epiki, aby dodać **rootEpics.js** w pliku **src/store** folderu:

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano zasoby, które można dodać lub dostosować usług w internetowym interfejsie użytkownika w akceleratora rozwiązania monitorowania zdalnego.

Po zdefiniowaniu usługi, następnym krokiem jest [dodać siatkę niestandardowych do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-grid.md) wyświetlającą dane zwracane przez usługę.

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
