---
title: Samouczek — tworzenie szablonu podstawowego Terraform na platformie Azure przy użyciu programu Yeoman
description: Dowiedz się, jak utworzyć szablon podstawowy Terraform na platformie Azure przy użyciu narzędzia Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472149"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Samouczek: Tworzenie szablonu podstawowego Terraform na platformie Azure przy użyciu programu Yeoman

W tym samouczku dowiesz się, jak korzystać z kombinacji [Terraform](/azure/terraform/) i [Yeoman](https://yeoman.io/). Terraform to narzędzie do tworzenia infrastruktury na platformie Azure. Yeoman ułatwia tworzenie modułów Terraform.

W tym artykule dowiesz się, jak wykonać następujące zadania:
> [!div class="checklist"]
> * Utwórz podstawowy szablon Terraform za pomocą generatora modułów Yeoman.
> * Przetestuj szablon Terraform przy użyciu dwóch różnych metod.
> * Uruchom moduł Terraform przy użyciu pliku docker.
> * Uruchom moduł Terraform natywnie w usłudze Azure Cloud Shell.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- **Kod programu Visual Studio:** [Pobierz kod programu Visual Studio](https://code.visualstudio.com/download) dla swojej platformy.
- **Terraform**: [Zainstaluj Terraform,](terraform-install-configure.md) aby uruchomić moduł stworzony przez Yeomana.
- **Docker**: [Zainstaluj program Docker,](https://www.docker.com/get-started) aby uruchomić moduł utworzony przez generator Yeoman.
- **Idź język programowania:** [Zainstaluj Go](https://golang.org/) jako Yeoman generowane przypadków testowych są kod przy użyciu języka Go.

>[!NOTE]
>Większość procedur w tym samouczku obejmuje interfejs wiersza polecenia. Opisane kroki dotyczą wszystkich systemów operacyjnych i narzędzi wiersza polecenia. Przykłady programu PowerShell został wybrany dla środowiska lokalnego i Git Bash dla środowiska powłoki chmury.

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

### <a name="install-nodejs"></a>Instalowanie środowiska Node.js

Aby używać narzędzia Terraform w usłudze Cloud Shell, musisz [zainstalować środowisko Node.js](https://nodejs.org/en/download/) w wersji 6.0 lub nowszej.

>[!NOTE]
>Aby sprawdzić, czy środowisko Node.js jest zainstalowane, otwórz okno terminalu i wprowadź polecenie `node --version`.

### <a name="install-yeoman"></a>Instalowanie narzędzia Yeoman

Uruchom następujące polecenie:

```bash
npm install -g yo
```

![Instalowanie narzędzia Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Zainstaluj szablon narzędzia Yeoman dla modułu narzędzia Terraform

Uruchom następujące polecenie:

```bash
npm install -g generator-az-terra-module
```

![Zainstaluj element generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Aby sprawdzić, czy program Yeoman jest zainstalowany, uruchom następujące polecenie:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Tworzenie katalogu dla modułu generowanego przez Yeomana

Szablon narzędzia Yeoman generuje pliki w bieżącym katalogu. Z tego powodu musisz utworzyć katalog.

Ten pusty katalog należy umieścić na ścieżce $GOPATH/src. Aby uzyskać więcej informacji na temat tej ścieżki, zobacz artykuł [Ustawianie GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Przejdź do katalogu nadrzędnego, z którego chcesz utworzyć nowy katalog.

1. Uruchom następujące polecenie zastępując symbol zastępczy. W tym przykładzie używana `GeneratorDocSample` jest nazwa katalogu.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Przejdź do nowego katalogu:

    ```bash
    cd <new-directory-name>
    ```

    ![Przejdź do swojego nowego katalogu](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Utwórz szablon podstawowego modułu

1. Uruchom następujące polecenie:

    ```bash
    yo az-terra-module
    ```

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby podać następujące informacje:

    - **Terraform moduł projektu Nazwa** `doc-sample-module` — wartość jest używana dla przykładu.

        ![Project name (Nazwa projektu)](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Czy chcesz dołączyć plik obrazu platformy Docker?** - `y`Wprowadź . Jeśli wybierzesz `n`, wygenerowany kod modułu będzie obsługiwać działa tylko w trybie macierzystym.

        ![Dołączyć plik obrazu platformy Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Wyświetl listę zawartości katalogu, aby wyświetlić utworzone pliki wynikowe:

    ```bash
    ls
    ```

    ![Wyświetlanie utworzonych plików](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Przeglądanie kodu wygenerowanego modułu

1. Uruchom program Visual Studio Code.

1. Na pasku menu wybierz pozycję **File > Open Folder** (Plik > Otwórz folder), a następnie przejdź do utworzonego folderu.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Następujące pliki zostały utworzone przez generator modułu Yeoman. Aby uzyskać więcej informacji na temat tych plików i ich użycia, zobacz [Terratest w Moduły Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf`- Definiuje moduł `random-shuffle`o nazwie . Dane wejściowe `string_list`to . Dane wyjściowe to liczba permutacji.
- `variables.tf`- Definiuje zmienne wejściowe i wyjściowe używane przez moduł.
- `outputs.tf`- Określa, co moduł wyprowadza. W tym miejscu jest to `random_shuffle`wartość zwracana przez , który jest wbudowany, Moduł Terraform.
- `Rakefile`- Definiuje kroki kompilacji. Kroki te obejmują:
    - `build`- Sprawdza poprawność formatowania pliku main.tf.
    - `unit`- Szkielet wygenerowanego modułu nie zawiera kodu do testu jednostkowego. Jeśli chcesz określić scenariusz testu jednostkowego, musisz dodać kod w tym miejscu.
    - `e2e`- Uruchamia kompleksowy test modułu.
- `test`
    - Przypadki testowe są pisane w języku Go.
    - Wszystkie kody w teście są testami całościowymi.
    - Testy end-to-end próbują aprowizować `fixture`wszystkie elementy zdefiniowane w obszarze . Wyniki w `template_output.go` pliku są porównywane ze wstępnie zdefiniowanymi wartościami oczekiwanymi.
    - `Gopkg.lock`i `Gopkg.toml`: Definiuje zależności. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testowanie modułu Terraform przy użyciu pliku Docker

W tej sekcji pokazano, jak przetestować moduł Terraform przy użyciu pliku Docker.

>[!NOTE]
>W tym przykładzie uruchamia moduł lokalnie; nie na platformie Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Upewnij się, że platforma Docker jest zainstalowana i uruchomiona

W wierszu polecenia wprowadź polecenie `docker version`.

![Wersja platformy Docker](media/terraform-vscode-module-generator/ymg-docker-version.png)

Wynikowe dane wyjściowe potwierdzają, że platforma Docker jest zainstalowana.

Aby potwierdzić, że platforma Docker faktycznie działa, wprowadź polecenie `docker info`.

![Informacje o platformie Docker](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Konfigurowanie kontenera platformy Docker

1. W wierszu polecenia wprowadź polecenie

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Zostanie wyświetlony komunikat **Successfully built** (Pomyślnie skompilowane).

    ![Komunikat wskazujący pomyślną kompilację](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. W wierszu polecenia `docker image ls` wprowadź, aby `terra-mod-example` wyświetlić utworzony moduł na liście.

    ![Lista zawierająca nowy moduł](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Wprowadź polecenie `docker run -it terra-mod-example /bin/sh`. Po uruchomieniu `docker run` polecenia, jesteś w środowisku platformy Docker. W tym momencie można odnajdować plik za pomocą `ls` polecenia.

    ![Lista plików w cker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Konstruowanie modułu

1. Uruchom następujące polecenie:

    ```bash
    bundle install
    ```

1. Uruchom następujące polecenie:

    ```bash
    rake build
    ```

    ![Kompilacja szczegółowa](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Uruchamianie testu całościowego

1. Uruchom następujące polecenie:

    ```bash
    rake e2e
    ```

1. Po kilku chwilach zostanie wyświetlony komunikat **PASS** (POWODZENIE).

    ![PASS (POWODZENIE)](media/terraform-vscode-module-generator/ymg-pass.png)

1. Wprowadź, `exit` aby zakończyć test i wyjść ze środowiska platformy Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Tworzenie i testowanie modułu w usłudze Cloud Shell przy użyciu generatora Yeoman

W tej sekcji generator Yeoman służy do tworzenia i testowania modułu w Cloud Shell. Użycie usługi Cloud Shell zamiast pliku Docker znacznie upraszcza ten proces. Korzystając z usługi Cloud Shell, wszystkie wszystkie produkty są wstępnie zainstalowane:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Uruchamianie sesji usługi Cloud Shell

1. Uruchom sesję usługi Azure Cloud Shell za pośrednictwem witryny [Azure Portal](https://portal.azure.com/), witryny [shell.azure.com](https://shell.azure.com) lub [aplikacji mobilnej platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Zostanie otwarta strona **Witamy w usłudze Azure Cloud Shell**. Wybierz pozycję **Bash (Linux)**.

    ![Witamy w usłudze Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Jeśli nie jest jeszcze skonfigurowane konto magazynu platformy Azure, zostanie wyświetlony poniższy ekran. Wybierz pozycję **Utwórz magazyn**.

    ![Nie masz zainstalowanego magazynu](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Usługa Azure Cloud Shell zostanie uruchomiona w wybranej wcześniej powłoce i wyświetli informacje dotyczące dysku w chmurze, który właśnie został utworzony dla Ciebie.

    ![Dysk w chmurze został utworzony](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Przygotowanie katalogu do przechowywania modułu Terraform

1. W tym momencie usługa Cloud Shell skonfigurowała już wartość GOPATH w zmiennych środowiskowych. Aby wyświetlić tę ścieżkę, wprowadź polecenie `go env`.

1. Utwórz katalog $GOPATH, jeśli jeszcze go nie istnieje: Enter `mkdir ~/go`.

1. Tworzenie katalogu w katalogu $GOPATH. Ten katalog jest używany do przechowywania różnych katalogów projektu utworzonych w tym przykładzie. 

    ```bash
    mkdir ~/go/src
    ```

1. Utwórz katalog, aby przytrzymać moduł Terraform zastępujący symbol zastępczy. W tym przykładzie używana `my-module-name` jest nazwa katalogu.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Przejdź do katalogu modułów: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Tworzenie i testowanie modułu Terraform

1. Uruchom następujące polecenie i postępuj zgodnie z instrukcjami. Na pytanie, czy chcesz utworzyć pliki platformy `N`Docker, należy wprowadzić .

    ```bash
    yo az-terra-module
    ```

1. Uruchom następujące polecenie, aby zainstalować zależności:

    ```bash
    bundle install
    ```

1. Uruchom następujące polecenie, aby utworzyć moduł:

    ```bash
    rake build
    ```

    ![Kompilacja szczegółowa](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Uruchom następujące polecenie, aby uruchomić tekst:

    ```bash
    rake e2e
    ```

    ![Wyniki testów](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj i użyj rozszerzenia kodu programu Azure Terraform Visual Studio](/azure/terraform/terraform-vscode-extension).