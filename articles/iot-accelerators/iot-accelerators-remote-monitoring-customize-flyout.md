---
title: Dodawanie wysuwu do interfejsu użytkownika rozwiązania do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak dodać nowy wysuwany na stronie w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447118"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie niestandardowego wysuwu do interfejsu użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania

W tym artykule pokazano, jak dodać nowy wysuwany plik wysuwany do strony w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania. W artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nowe wysuwany wysuwany do strony w interfejsie użytkownika sieci Web.

Przykładowy komunikat wysuwany w tym artykule jest wyświetlany na stronie z [siatką, że Dodaj siatkę niestandardową do interfejsu użytkownika internetowego akceleratora rozwiązania zdalnego monitorowania sieci Web](iot-accelerators-remote-monitoring-customize-grid.md) pokazuje, jak dodać.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, na lokalnym komputerze deweloperskim należy zainstalować następujące oprogramowanie:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać kroki opisane w następujących artykułach:

- Dodaj stronę niestandardową do interfejsu użytkownika [internetowego akceleratora rozwiązań do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-page.md).
- [Dodawanie usługi niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązań do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-service.md)
- [Dodawanie siatki niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązania do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Dodawanie okna wysuwanego

Aby dodać wysuwany element do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe definiujące wysuwany plik wysuwany i zmodyfikować niektóre istniejące pliki, aby uwrażliwić internetowy interfejs użytkownika o nowym składniku.

### <a name="add-the-new-files-that-define-the-flyout"></a>Dodawanie nowych plików definiujących wysuwany

Aby rozpocząć, **folder src/instruktażowy/składniki/strony/pageWithFlyout/wysuwy/exampleFlyout** zawiera pliki definiujące wysuwy:

**przykładFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**przykładFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Skopiuj folder **src/instruktażowy/składniki/strony/pageWithFlyout/wysuwek** do folderu **src/components/pages/example.**

### <a name="add-the-flyout-to-the-page"></a>Dodawanie wysuń do strony

Zmodyfikuj **src/components/pages/example/basicPage.js,** aby dodać wysuwany.

Dodaj **Btn** do importu ze **składników/udostępnionych** i dodaj importy dla **svgs** i **ExampleFlyoutContainer:**

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

Dodaj **const** definicji **closedFlyoutState** i dodać go do stanu w konstruktorze:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Dodaj następujące funkcje do klasy **BasicPage:**

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Dodaj następujące definicje **const** do funkcji **renderowania:**

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Dodaj przycisk, aby otworzyć wysuwany wysuwany do menu kontekstowego:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Dodaj tekst i kontener wysuwany do zawartości strony:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testowanie wysuwu wysuwu

Jeśli interfejs użytkownika sieci Web nie jest jeszcze uruchomiony lokalnie, uruchom następujące polecenie w katalogu głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika [http://localhost:3000/dashboard](http://localhost:3000/dashboard)lokalnie w pliku . Przejdź do **przykładowej** strony i kliknij pozycję **Otwórz wysuń .**

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostępnych zasobach ułatwiające dodawanie lub dostosowywanie stron w interfejsie użytkownika sieci Web w akceleratorze rozwiązań do zdalnego monitorowania.

Teraz zdefiniowano wysuwany na stronie, następnym krokiem jest [dodanie panelu do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-panel.md).

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
