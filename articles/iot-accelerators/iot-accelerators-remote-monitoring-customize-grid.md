---
title: Dodawanie siatki do interfejsu użytkownika rozwiązania do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak dodać nowy gid na stronie w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447101"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie siatki niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązania do zdalnego monitorowania

W tym artykule pokazano, jak dodać nową siatkę do strony w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania. W artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nową siatkę do strony w interfejsie użytkownika sieci Web.

Przykładowa siatka w tym artykule wyświetla dane z usługi, które [Dodaj usługę niestandardową do interfejsu użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania sieci Web](iot-accelerators-remote-monitoring-customize-service.md) pokazuje, jak dodać.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, na lokalnym komputerze deweloperskim należy zainstalować następujące oprogramowanie:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać kroki opisane w następujących artykułach:

- Dodaj stronę niestandardową do interfejsu użytkownika [internetowego akceleratora rozwiązań do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-page.md).
- [Dodawanie usługi niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązań do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Dodawanie siatki

Aby dodać siatkę do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe definiujące siatkę i zmodyfikować niektóre istniejące pliki, aby uwrażliwić internetowy interfejs użytkownika o nowym składniku.

### <a name="add-the-new-files-that-define-the-grid"></a>Dodawanie nowych plików definiujących siatkę

Aby rozpocząć, **src/instruktaż/składniki/strony/pageWithGrid/exampleGrid** folder zawiera pliki definiujące siatkę:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Skopiuj folder **src/instruktażowy/składniki/strony/pageWithGrid/exampleGrid** do folderu **src/components/pages/example.**

### <a name="add-the-grid-to-the-page"></a>Dodawanie siatki do strony

Zmodyfikuj **src/components/pages/example/basicPage.container.js** w następujący sposób, aby zaimportować definicje usług:

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

Zmodyfikuj **src/components/pages/example/basicPage.js** w następujący sposób, aby dodać siatkę:

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

Zmodyfikuj **src/components/pages/example/basicPage.test.js** w następujący sposób, aby zaktualizować testy:

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

Jeśli interfejs użytkownika sieci Web nie jest jeszcze uruchomiony lokalnie, uruchom następujące polecenie w katalogu głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika [http://localhost:3000/dashboard](http://localhost:3000/dashboard)lokalnie w pliku . Przejdź do **przykładowej** strony, aby wyświetlić dane siatki wyświetlane z usługi.

## <a name="select-rows"></a>Zaznaczanie wierszy

Istnieją dwie opcje umożliwiające użytkownikowi zaznaczanie wierszy w siatce:

### <a name="hard-select-rows"></a>Wiersze z wyborem twardym

Jeśli użytkownik musi działać w wielu wierszach jednocześnie, użyj pól wyboru w wierszach:

1. Włącz twardy wybór wierszy, dodając **checkboxColumn** do **columnDefs** dostarczone do siatki. **checkboxColumn** jest zdefiniowany w **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Aby uzyskać dostęp do wybranych elementów, otrzymasz odwołanie do wewnętrznego interfejsu API siatki:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Podaj przyciski kontekstu do strony, gdy wiersz w siatce jest zaznaczony na twardo:

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

1. Po kliknięciu przycisku kontekstu pobierz starannie wybrane elementy, nad którymi chcesz wykonać pracę:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Wiersze z miękkim zaznaczeniem

Jeśli użytkownik musi działać tylko w jednym wierszu, skonfiguruj łącze wyboru nietrwego losowego dla jednej lub kilku kolumn w **kolumnieDefs**.

1. W **przykładzieGridConfig.js**, dodaj **SoftSelectLinkRenderer** jako **cellRendererFramework** dla **columnDef**.

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

1. Po kliknięciu łącza wyboru nietrwałego wyzwala zdarzenie **onSoftSelectChange.** Wykonaj dowolną czynność wymaganą dla tego wiersza, na przykład otwierając wysuń szczegółów. W tym przykładzie po prostu zapisuje do konsoli:

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

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostępnych zasobach ułatwiające dodawanie lub dostosowywanie stron w interfejsie użytkownika sieci Web w akceleratorze rozwiązań do zdalnego monitorowania.

Teraz zdefiniowano siatkę, następnym krokiem jest [dodanie niestandardowego wysuń do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania,](iot-accelerators-remote-monitoring-customize-flyout.md) który jest wyświetlany na przykładowej stronie.

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
