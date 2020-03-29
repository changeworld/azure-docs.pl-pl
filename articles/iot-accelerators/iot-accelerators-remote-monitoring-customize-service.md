---
title: Dodawanie usługi do interfejsu użytkownika rozwiązania do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak dodać nową usługę do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447050"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie usługi niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązań do zdalnego monitorowania

W tym artykule pokazano, jak dodać nową usługę do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania. W artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nową usługę do interfejsu użytkownika sieci Web.

Przykładowa usługa w tym artykule zawiera dane dla siatki, że [Dodaj siatkę niestandardową do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania sieci Web](iot-accelerators-remote-monitoring-customize-grid.md) pokazuje, jak dodać.

W aplikacji React usługa zazwyczaj współdziała z usługą zaplecza. Przykłady w akcelerator rozwiązania zdalnego monitorowania obejmują usługi, które współdziałają z menedżerem centrum IoT i mikrousługami konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, na lokalnym komputerze deweloperskim należy zainstalować następujące oprogramowanie:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać kroki opisane na stronie Dodawanie niestandardowego do artykułu instrukcje dotyczące [interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania.](iot-accelerators-remote-monitoring-customize-page.md)

## <a name="add-a-service"></a>Dodawanie usługi

Aby dodać usługę do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe definiujące usługę i zmodyfikować niektóre istniejące pliki, aby uwrażliwić internetowy interfejs użytkownika o nowej usłudze.

### <a name="add-the-new-files-that-define-the-service"></a>Dodawanie nowych plików definiujących usługę

Aby rozpocząć, folder **src/instruktaż/usługi** zawiera pliki definiujące prostą usługę:

**przykładService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Aby dowiedzieć się więcej o tym, jak usługi są implementowane, zobacz [Wprowadzenie do programowania reaktywnego, którego brakowało.](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

**model/przykładModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Skopiuj **przykładService.js** do folderu **src/services** i skopiuj **exampleModels.js** do folderu **src/services/models.**

Zaktualizuj plik **index.js** w folderze **src/services,** aby wyeksportować nową usługę:

```js
export * from './exampleService';
```

Zaktualizuj plik **index.js** w folderze **src/services/models,** aby wyeksportować nowy model:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Konfigurowanie wywołań do usługi ze sklepu

Aby rozpocząć, folder **src/walkthrough/store/reducers** zawiera reduktor próbek:

**przykładReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

**Kopiuj przykładReducer.js** do folderu **src/store/reducers.**

Aby dowiedzieć się więcej o reduktorze i **epics**, zobacz [redux-observable](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurowanie oprogramowania pośredniczącego

Aby skonfigurować oprogramowanie pośredniczące, dodaj reduktor do pliku **rootReducer.js** w folderze **src/store:**

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

Dodaj etyki do pliku **rootEpics.js** w folderze **src/store:**

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

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostępnych zasobach ułatwiające dodawanie lub dostosowywanie usług w interfejsie użytkownika sieci Web w akceleratorze rozwiązań do zdalnego monitorowania.

Teraz zdefiniowano usługę, następnym krokiem jest [dodanie siatki niestandardowej do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania,](iot-accelerators-remote-monitoring-customize-grid.md) który wyświetla dane zwrócone przez usługę.

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
