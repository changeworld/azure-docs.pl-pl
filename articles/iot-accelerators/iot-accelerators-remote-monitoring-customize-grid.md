---
title: Dodać siatkę z rozwiązaniem monitorowania zdalnego interfejsu użytkownika — Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób dodawania nowych gid w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika strony.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447101"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodaj niestandardowe siatki do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika

W tym artykule pokazano, jak dodać nową siatkę na stronę w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika. W artykule opisano:

- Jak przygotować lokalne Środowisko deweloperskie.
- Jak dodać nową siatkę do strony w internetowym interfejsie użytkownika.

W siatce przykładu w tym artykule są wyświetlane dane z usługi, [dodawania niestandardowych usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-service.md) artykule przedstawiono sposób dodawania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, potrzebne są następujące oprogramowanie zainstalowane na lokalnej maszynie do programowania:

- [Usługa Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać czynności opisane w następujących artykułach:

- [Dodawanie niestandardowej strony do monitorowania zdalnego rozwiązania akceleratora internetowy interfejs użytkownika](iot-accelerators-remote-monitoring-customize-page.md).
- [Dodaj niestandardowe usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Dodawanie siatki

Aby dodać siatkę do interfejsu użytkownika sieci web, należy dodać pliki źródłowe, które definiują siatki i zmodyfikować niektóre istniejące pliki, aby uświadomić interfejsu użytkownika sieci web nowego składnika.

### <a name="add-the-new-files-that-define-the-grid"></a>Dodawaj nowe pliki, które definiują siatki

Aby rozpocząć pracę, **src/wskazówki/składniki/stron/pageWithGrid/exampleGrid** folder zawiera pliki, które definiują siatki:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Kopiuj **src/wskazówki/składniki/stron/pageWithGrid/exampleGrid** folder do **src/składniki/stron/przykład** folderu.

### <a name="add-the-grid-to-the-page"></a>Na stronie Dodaj siatki

Modyfikowanie **src/components/pages/example/basicPage.container.js** w następujący sposób, aby zaimportować definicje usług:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Modyfikowanie **src/components/pages/example/basicPage.js** w następujący sposób, aby dodać siatki:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Modyfikowanie **src/components/pages/example/basicPage.test.js** w następujący sposób w celu zaktualizowania testów:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Testowanie siatki

Jeśli internetowy interfejs użytkownika nie jest jeszcze uruchomiona lokalnie, uruchom następujące polecenie w folderze głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejsu użytkownika lokalnie [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Przejdź do **przykład** stronę, aby wyświetlić siatkę wyświetlenie danych z usługi.

## <a name="select-rows"></a>Wybierz wiersze

Istnieją dwie opcje umożliwiające użytkownika do wybrania wierszy do siatki:

### <a name="hard-select-rows"></a>Twarde wybierz wiersze

Jeśli użytkownik musi działać w wielu wierszach, w tym samym czasie, użyj pól wyboru w wierszach:

1. Włącz twardych zaznaczenia wierszy, dodając **checkboxColumn** do **columnDefs** dostarczane do siatki. **checkboxColumn** jest zdefiniowany w **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Dostęp do wybranych elementów, możesz uzyskać odwołanie do interfejsu API siatki wewnętrzny:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Podaj przyciski kontekstu do strony po wiersza w siatce twardych wybrane:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Po kliknięciu przycisku kontekstu, Pobierz twardych wybrane elementy do pracy na:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Wybierz elastyczne wierszy

Jeśli użytkownik musi jedynie do działania w jednym wierszu, skonfiguruj soft wybierz link do co najmniej jedną kolumnę w **columnDefs**.

1. W **exampleGridConfig.js**, Dodaj **SoftSelectLinkRenderer** jako **cellRendererFramework** dla **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Po kliknięciu łącza soft-select, wyzwala **onSoftSelectChange** zdarzeń. Wykonaj dowolną akcję jest pożądane dla tego wiersza, np. otwierając menu wysuwanego szczegółowe informacje. W tym przykładzie po prostu zapisuje do konsoli:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano dostępnych zasobów umożliwiających można dodawać lub dostosowywanie stron w internetowym interfejsie użytkownika w akceleratora rozwiązania monitorowania zdalnego.

Po zdefiniowaniu siatki, następnym krokiem jest [dodać niestandardowe okno wysuwane do monitorowania zdalnego rozwiązania akceleratora internetowy interfejs użytkownika](iot-accelerators-remote-monitoring-customize-flyout.md) wyświetlającą na przykładowej stronie.

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
