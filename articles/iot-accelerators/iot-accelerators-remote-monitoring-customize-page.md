---
title: Dodawanie strony do rozwiązania do zdalnego monitorowania interfejsu użytkownika — Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób dodawania nowej strony do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447084"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie niestandardowej strony do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika

W tym artykule przedstawiono sposób dodawania nowej strony do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika. W artykule opisano:

- Jak przygotować lokalne Środowisko deweloperskie.
- Jak dodać nową stronę do interfejsu użytkownika sieci web.

Inne przewodniki z instrukcjami rozszerzyć ten scenariusz, aby dodać więcej funkcji do strony, dodawanych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, potrzebne są następujące oprogramowanie zainstalowane na lokalnej maszynie do programowania:

- [Usługa Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotowywanie lokalnego środowiska deweloperskiego dla interfejsu użytkownika

Zdalne monitorowanie akceleratora rozwiązań kodu interfejsu użytkownika jest implementowany przy użyciu [React](https://reactjs.org/) platformy JavaScript. Można znaleźć kodu źródłowego w [zdalnego monitorowania interfejsie sieci Web odbędzie](https://github.com/Azure/pcs-remote-monitoring-webui) repozytorium GitHub.

Aby zrobić i testować zmiany w interfejsie użytkownika, można go uruchomić na lokalnej maszynie do programowania. Opcjonalnie lokalna kopia można nawiązać wdrożone wystąpienie akcelerator rozwiązań, aby umożliwić interakcję z rzeczywistych lub symulowanych urządzeń.

W celu przygotowania swojego lokalnego środowiska deweloperskiego, użyj narzędzia Git, aby sklonować [zdalnego monitorowania interfejsie sieci Web odbędzie](https://github.com/Azure/pcs-remote-monitoring-webui) repozytorium na komputerze lokalnym:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Dodawanie strony

Aby dodać strony interfejsu użytkownika sieci web, należy dodać pliki źródłowe, które definiują strony i zmodyfikować niektóre istniejące pliki, aby uświadomić nowej strony interfejsu użytkownika sieci web.

### <a name="add-the-new-files-that-define-the-page"></a>Dodaj nowe pliki, które definiują strony

Aby rozpocząć pracę, **src/wskazówki/składniki/stron/basicPage** folder zawiera cztery pliki, które definiują prostą stronę:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Utwórz nowy folder **src/składniki/stron/przykład** i skopiuj następujące cztery pliki do niego.

### <a name="add-the-new-page-to-the-web-ui"></a>Dodaj nową stronę do interfejsu użytkownika sieci web

Dodawanie nowej strony do interfejsu użytkownika sieci web, należy wprowadzić następujące zmiany do istniejących plików:

1. Dodaj nowy kontener strony do **src/components/pages/index.js** pliku:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Opcjonalnie)  Dodaj ikonę SVG nowej strony. Aby uzyskać więcej informacji, zobacz [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Można użyć istniejącego pliku SVG.

1. Dodaj nazwę strony do pliku tłumaczenia **public/locales/en/translations.json**. Korzysta z interfejsu użytkownika sieci web [i18next](https://www.i18next.com/) dla internacjonalizacji.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Otwórz **src/components/app.js** pliku, który definiuje stronę aplikacji najwyższego poziomu. Dodaj nową stronę do listy importów:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. W tym samym pliku Dodaj nową stronę do `pagesConfig` tablicy. Ustaw `to` adresów dla trasy, odwołać ikonę SVG i tłumaczenia dodane wcześniej i ustawić `component` do strony kontenera:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Dodaj wszystkie nowe linki do stron nadrzędnych do `crumbsConfig` tablicy:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Ta przykładowa strona ma tylko jedną łączy do stron nadrzędnych, ale niektóre strony mogą mieć więcej.

Zapisz wszystkie zmiany. Jesteś gotowy do uruchomienia interfejsu użytkownika sieci web przy użyciu nowej strony dodane.

### <a name="test-the-new-page"></a>Testowanie nowej strony

Polecenie w wierszu polecenia przejdź do katalogu głównego lokalnej kopii repozytorium i uruchom następujące polecenia, aby instalowanie wymaganych bibliotek i uruchom lokalnie interfejsu użytkownika sieci web:

```cmd/sh
npm install
npm start
```

Poprzednie polecenie uruchamia interfejsu użytkownika lokalnie [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard).

Na pulpicie nawigacyjnym, bez łączenia lokalnym wystąpieniem interfejsu użytkownika sieci web z wdrożonego wystąpienia akcelerator rozwiązań, zobacz błędy. Te błędy nie mają wpływu na możliwość testowania Twoja nowa strona.

Można teraz edytować kod lokacji jest uruchomiona lokalnie i znajduje się w Internecie interfejsu użytkownika są aktualizowane dynamicznie.

## <a name="optional-connect-to-deployed-instance"></a>[Opcjonalnie] Połącz się z wystąpieniem wdrożone

Opcjonalnie można połączyć uruchomionej lokalną kopię interfejsu użytkownika sieci web do akceleratora rozwiązania monitorowania zdalnego, w chmurze:

1. Wdrażanie **podstawowe** wystąpienie za pomocą akceleratora rozwiązań **komputerów** interfejsu wiersza polecenia. Zanotuj nazwę wdrożenia i poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Za pomocą witryny Azure portal lub [interfejsu wiersza polecenia az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby umożliwić dostęp SSH do maszyny wirtualnej, która obsługuje mikrousługi w rozwiązaniu. Na przykład:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Dostęp SSH należy włączyć tylko podczas projektowania i testowania. Po włączeniu protokołu SSH, [należy wyłączyć je ponownie tak szybko jak to możliwe](../security/azure-security-network-security-best-practices.md).

1. Za pomocą witryny Azure portal lub [interfejsu wiersza polecenia az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) można znaleźć nazwę i publiczny adres IP swojej maszyny wirtualnej. Na przykład:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Za pomocą protokołu SSH Połącz się z maszyną wirtualną przy użyciu adresu IP z poprzedniego kroku i poświadczenia podane po uruchomieniu **komputerów** do wdrożenia rozwiązania.

1. Aby umożliwić lokalnego środowiska użytkownika do łączenia z, uruchom następujące polecenia w powłoce bash na maszynie wirtualnej:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po zakończeniu działania polecenia i rozpoczyna się w witrynie sieci web, możesz odłączyć od maszyny wirtualnej.

1. W lokalnej kopii [zdalnego monitorowania interfejsie sieci Web odbędzie](https://github.com/Azure/pcs-remote-monitoring-webui) repozytorium, Edytuj **ENV** plik, aby dodać adres URL wdrożonego rozwiązania:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano zasobów dostępnych do dostosowywania interfejsu użytkownika sieci web w akceleratora rozwiązania monitorowania zdalnego.

Po zdefiniowaniu stroną, następnym krokiem jest [dodawania niestandardowych usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-service.md) , który pobiera dane do wyświetlenia w interfejsie użytkownika.

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
