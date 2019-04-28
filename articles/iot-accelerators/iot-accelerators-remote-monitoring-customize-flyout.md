---
title: Dodawanie menu wysuwane z rozwiązaniem monitorowania zdalnego interfejsu użytkownika — Azure | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak dodać nowe okno wysuwane w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika strony.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/05/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447118"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodaj niestandardowe okno wysuwane do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika

W tym artykule pokazano, jak dodać nowe okno wysuwane na stronę w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika. W artykule opisano:

- Jak przygotować lokalne Środowisko deweloperskie.
- Jak dodać nowe okno wysuwane do strony w internetowym interfejsie użytkownika.

Okno wysuwane przykład, w tym artykule wyświetla na stronie siatki, [dodać siatkę niestandardowych do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-grid.md) artykule przedstawiono sposób dodawania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, potrzebne są następujące oprogramowanie zainstalowane na lokalnej maszynie do programowania:

- [Usługa Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać czynności opisane w następujących artykułach:

- [Dodawanie niestandardowej strony do monitorowania zdalnego rozwiązania akceleratora internetowy interfejs użytkownika](iot-accelerators-remote-monitoring-customize-page.md).
- [Dodaj niestandardowe usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-service.md)
- [Dodaj niestandardowe siatki do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Dodawanie okna wysuwanego

Aby dodać menu wysuwanego do interfejsu użytkownika sieci web, należy dodać pliki źródłowe, które definiują okno wysuwane i zmodyfikować niektóre istniejące pliki, aby uświadomić interfejsu użytkownika sieci web nowego składnika.

### <a name="add-the-new-files-that-define-the-flyout"></a>Dodawaj nowe pliki, które definiują okno wysuwane

Aby rozpocząć pracę, **src/wskazówki/składniki/stron/pageWithFlyout/menu wysuwanych/exampleFlyout** folder zawiera pliki, które definiują menu wysuwanego:

**exampleFlyout.container.js**



**exampleFlyout.js**



Kopiuj **src/wskazówki/składniki/stron/pageWithFlyout/menu wysuwanych** folder do **src/składniki/stron/przykład** folderu.

### <a name="add-the-flyout-to-the-page"></a>Na stronie Dodaj okno wysuwane

Modyfikowanie **src/components/pages/example/basicPage.js** można dodać menu wysuwane.

Dodaj **Btn** do przywozu z **/udostępnione składniki** i dodać Import dla **svgs** i **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Dodaj **const** definicji **closedFlyoutState** i dodaj go do stanu w Konstruktorze:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Dodaj następujące funkcje do **BasicPage** klasy:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Dodaj następujący kod **const** definicje **renderowania** funkcji:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Dodaj przycisk, aby otworzyć menu wysuwane do menu kontekstowego:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Dodaj tekst i kontener okno wysuwane do zawartości strony:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testowanie okno wysuwane

Jeśli internetowy interfejs użytkownika nie jest jeszcze uruchomiona lokalnie, uruchom następujące polecenie w folderze głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejsu użytkownika lokalnie [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Przejdź do **przykład** strony, a następnie kliknij przycisk **Otwórz okno wysuwane**.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano dostępnych zasobów umożliwiających można dodawać lub dostosowywanie stron w internetowym interfejsie użytkownika w akceleratora rozwiązania monitorowania zdalnego.

Po zdefiniowaniu menu wysuwanego na stronie, następnym krokiem jest [Dodawanie panelu do pulpitu nawigacyjnego w zdalne monitorowanie rozwiązań akceleratora internetowy interfejs użytkownika](iot-accelerators-remote-monitoring-customize-panel.md).

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
