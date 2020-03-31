---
title: Dodawanie strony do interfejsu użytkownika rozwiązania do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak dodać nową stronę do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 0228f317e2d3380f2387dd557a27203eb3abc4ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240260"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie strony niestandardowej do interfejsu użytkownika internetowego akceleratora rozwiązania zdalnego monitorowania

W tym artykule pokazano, jak dodać nową stronę do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania. W artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nową stronę do interfejsu użytkownika sieci Web.

Inne przewodniki insceny rozszerzają ten scenariusz, aby dodać więcej funkcji do strony, którą dodajesz.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, na lokalnym komputerze deweloperskim należy zainstalować następujące oprogramowanie:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotowanie lokalnego środowiska programistycznego dla interfejsu użytkownika

Kod interfejsu użytkownika akceleratora rozwiązania zdalnego monitorowania jest implementowany przy użyciu struktury [JavaScript React.](https://reactjs.org/) Kod źródłowy można znaleźć w repozytorium Usługi WebUI GitHub [do zdalnego monitorowania.](https://github.com/Azure/pcs-remote-monitoring-webui)

Aby wprowadzić i przetestować zmiany w interfejsie użytkownika, można go uruchomić na komputerze deweloperskim lokalnym. Opcjonalnie kopia lokalna może połączyć się z wdrożonym wystąpieniem akceleratora rozwiązań, aby umożliwić mu interakcję z rzeczywistymi lub symulowanych urządzeniami.

Aby przygotować lokalne środowisko programistyczne, użyj git, aby sklonować repozytorium [WebUI zdalnego monitorowania](https://github.com/Azure/pcs-remote-monitoring-webui) na komputerze lokalnym:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Dodawanie strony

Aby dodać stronę do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe definiujące stronę i zmodyfikować niektóre istniejące pliki, aby uwrażliwić internetowy interfejs użytkownika na nową stronę.

### <a name="add-the-new-files-that-define-the-page"></a>Dodawanie nowych plików definiujących stronę

Aby rozpocząć, folder **src/walkthrough/components/pages/basicPage** zawiera cztery pliki definiujące prostą stronę:

**strona podstawowaPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Utwórz nowy folder **src / components / pages / przykład** i skopiować te cztery pliki do niego.

### <a name="add-the-new-page-to-the-web-ui"></a>Dodawanie nowej strony do interfejsu użytkownika sieci Web

Aby dodać nową stronę do interfejsu użytkownika sieci Web, należy wprowadzić następujące zmiany w istniejących plikach:

1. Dodaj nowy kontener strony do pliku **src/components/pages/index.js:**

    ```js
    export * from './example/basicPage.container';
    ```

1. (Opcjonalnie)  Dodaj ikonę SVG dla nowej strony. Aby uzyskać więcej informacji, zobacz [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Można użyć istniejącego pliku SVG.

1. Dodaj nazwę strony do pliku tłumaczeń, **public/locales/en/translations.json**. Interfejs użytkownika sieci Web używa [i18następny](https://www.i18next.com/) do internacjonalizacji.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Otwórz plik **src/components/app.js** definiujący stronę aplikacji najwyższego poziomu. Dodaj nową stronę do listy importów:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. W tym samym pliku dodaj nową `pagesConfig` stronę do tablicy. Ustaw `to` adres trasy, odwoł się do ikony SVG i `component` tłumaczeń dodanych wcześniej i ustaw kontener strony:

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

1. Dodaj dowolną nową bułkę `crumbsConfig` tartą do tablicy:

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

    Ta przykładowa strona ma tylko jedną kolejka na boks, ale niektóre strony mogą mieć więcej.

Zapisz wszystkie zmiany. Możesz uruchomić internetowy interfejs użytkownika z nową stroną dodaną.

### <a name="test-the-new-page"></a>Testowanie nowej strony

W wierszu polecenia przejdź do katalogu głównego lokalnej kopii repozytorium i uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić internetowy interfejs użytkownika lokalnie:

```cmd/sh
npm install
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika [http://localhost:3000/dashboard](http://localhost:3000/dashboard)lokalnie w pliku .

Bez łączenia lokalnego wystąpienia interfejsu użytkownika sieci web z wdrożonym wystąpieniem akceleratora rozwiązań na pulpicie nawigacyjnym są widoczne błędy. Te błędy nie wpływają na możliwość przetestowania nowej strony.

Teraz można edytować kod, gdy witryna jest uruchomiona lokalnie i dynamicznie wyświetlać aktualizację interfejsu użytkownika sieci Web.

## <a name="optional-connect-to-deployed-instance"></a>[Opcjonalnie] Łączenie się z wdrożonym wystąpieniem

Opcjonalnie można podłączyć lokalną uruchomię kopię interfejsu użytkownika sieci web do akceleratora rozwiązań zdalnego monitorowania w chmurze:

1. Wdrażanie **podstawowego** wystąpienia akceleratora rozwiązań przy użyciu **interfejsu wiersza** polecenia komputera. Zanotuj nazwę wdrożenia i poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Użyj witryny Azure portal lub [az CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby włączyć dostęp SSH do maszyny wirtualnej, która obsługuje mikrousług w rozwiązaniu. Przykład:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Dostęp SSH należy włączyć tylko podczas testowania i programowania. Jeśli włączysz SSH, [należy wyłączyć go ponownie tak szybko, jak to możliwe](../security/fundamentals/network-best-practices.md).

1. Użyj witryny Azure portal lub [interfejsu wiersza polecenia az,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby znaleźć nazwę i publiczny adres IP maszyny wirtualnej. Przykład:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Użyj protokołu SSH, aby połączyć się z maszyną wirtualną przy użyciu adresu IP z poprzedniego kroku i poświadczeń podanych podczas pracy **na komputerach w** celu wdrożenia rozwiązania.

1. Aby zezwolić lokalnemu środowisku użytkownika na łączenie się, uruchom następujące polecenia w powłoce bash na maszynie wirtualnej:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po zakończeniu polecenia i uruchomieniu witryny sieci Web można odłączyć się od maszyny wirtualnej.

1. W lokalnej kopii repozytorium [WebUI zdalnego monitorowania](https://github.com/Azure/pcs-remote-monitoring-webui) edytuj plik **.env,** aby dodać adres URL wdrożonego rozwiązania:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostępnych zasobach ułatwiające dostosowanie interfejsu użytkownika sieci Web w akceleratorze rozwiązań do zdalnego monitorowania.

Teraz zdefiniowano stronę, następnym krokiem jest [dodanie usługi niestandardowej do interfejsu użytkownika sieci web akceleratora rozwiązania zdalnego monitorowania,](iot-accelerators-remote-monitoring-customize-service.md) który pobiera dane do wyświetlenia w interfejsie użytkownika.

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
