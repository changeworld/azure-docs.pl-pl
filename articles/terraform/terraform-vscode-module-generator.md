---
title: Samouczek — Tworzenie podstawowego szablonu Terraform na platformie Azure przy użyciu narzędzia Yeoman
description: Dowiedz się, jak utworzyć szablon podstawowy Terraform na platformie Azure przy użyciu narzędzia Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba81d0ee797fd879fdadc3a6b25ca8f310383f61
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159167"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Samouczek: Tworzenie podstawowego szablonu Terraform na platformie Azure przy użyciu narzędzia Yeoman

W tym samouczku dowiesz się, jak używać kombinacji [Terraform](/azure/terraform/) i [Narzędzia Yeoman](https://yeoman.io/). Terraform to narzędzie do tworzenia infrastruktury na platformie Azure. Narzędzia Yeoman ułatwia tworzenie modułów Terraform.

W tym artykule dowiesz się, jak wykonywać następujące zadania:
> [!div class="checklist"]
> * Utwórz podstawowy szablon Terraform przy użyciu generatora modułu narzędzia Yeoman.
> * Przetestuj szablon Terraform przy użyciu dwóch różnych metod.
> * Uruchom moduł Terraform przy użyciu pliku platformy Docker.
> * Uruchom moduł Terraform w sposób natywny w Azure Cloud Shell.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- **Visual Studio Code**: [Pobierz Visual Studio Code](https://code.visualstudio.com/download) dla Twojej platformy.
- **Terraform**: [Zainstaluj Terraform](/azure/virtual-machines/linux/terraform-install-configure ) , aby uruchomić moduł utworzony przez narzędzia Yeoman.
- **Docker**: [Zainstaluj platformę Docker](https://www.docker.com/get-started) , aby uruchomić moduł utworzony przez generator narzędzia Yeoman.
- **Język programowania języka go**: [Zainstaluj go](https://golang.org/) jako narzędzia Yeoman — generowane przypadki testowe to kod przy użyciu języka go.

>[!NOTE]
>Większość procedur opisanych w tym samouczku obejmuje interfejs wiersza polecenia. Opisane kroki dotyczą wszystkich systemów operacyjnych i narzędzi wiersza polecenia. Przykładowo dla środowiska Cloud Shell została wybrana opcja PowerShell dla środowiska lokalnego i narzędzia Git bash.

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

Aby sprawdzić, czy narzędzia Yeoman jest zainstalowana, uruchom następujące polecenie:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Utwórz katalog dla modułu wygenerowanego przez narzędzia Yeoman

Szablon narzędzia Yeoman generuje pliki w bieżącym katalogu. Z tego powodu musisz utworzyć katalog.

Ten pusty katalog należy umieścić na ścieżce $GOPATH/src. Aby uzyskać więcej informacji na temat tej ścieżki, zobacz [ustawienie artykułu zmienną gopath](https://github.com/golang/go/wiki/SettingGOPATH).

1. Przejdź do katalogu nadrzędnego, z którego ma zostać utworzony nowy katalog.

1. Uruchom następujące polecenie, zastępując symbol zastępczy. W tym przykładzie jest używana nazwa katalogu `GeneratorDocSample`.

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

    - **Nazwa projektu modułu Terraform** — dla przykładu jest używana wartość `doc-sample-module`.

        ![Project name (Nazwa projektu)](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Czy chcesz dołączyć plik obrazu platformy Docker?** -Wprowadź `y`. W przypadku wybrania `n`wygenerowany kod modułu będzie obsługiwał uruchamianie tylko w trybie macierzystym.

        ![Dołączyć plik obrazu platformy Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Wyświetl listę zawartości katalogu, aby wyświetlić utworzone pliki powstałe w programie:

    ```bash
    ls
    ```

    ![Wyświetlanie utworzonych plików](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Przeglądanie kodu wygenerowanego modułu

1. Uruchom program Visual Studio Code.

1. Na pasku menu wybierz pozycję **File > Open Folder** (Plik > Otwórz folder), a następnie przejdź do utworzonego folderu.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Następujące pliki zostały utworzone przez generator modułu narzędzia Yeoman. Aby uzyskać więcej informacji na temat tych plików i ich użycia, zobacz [Terratest in Terraform modules.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

- `main.tf` — definiuje moduł o nazwie `random-shuffle`. Dane wejściowe to `string_list`. Dane wyjściowe to liczba permutacji.
- `variables.tf` — definiuje zmienne wejściowe i wyjściowe używane przez moduł.
- `outputs.tf` — definiuje dane wyjściowe modułu. W tym miejscu jest wartością zwracaną przez `random_shuffle`, która jest wbudowanym modułem Terraform.
- `Rakefile` — definiuje kroki kompilacji. Kroki te obejmują:
    - `build` — sprawdza poprawność formatowania pliku main.tf.
    - `unit` — wygenerowany szkielet modułu nie zawiera kodu dla testu jednostkowego. Jeśli chcesz określić scenariusz testu jednostkowego, musisz dodać kod w tym miejscu.
    - `e2e` — wykonuje test kompleksowy modułu.
- `test`
    - Przypadki testowe są pisane w języku Go.
    - Wszystkie kody w teście są testami całościowymi.
    - Testy typu end-to-end umożliwiają dostarczenie wszystkich elementów zdefiniowanych w obszarze `fixture`. Wyniki w pliku `template_output.go` są porównywane ze wstępnie zdefiniowanymi oczekiwanymi wartościami.
    - `Gopkg.lock` i `Gopkg.toml`: definiuje zależności. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testowanie modułu Terraform przy użyciu pliku Docker

W tej sekcji przedstawiono sposób testowania modułu Terraform przy użyciu pliku platformy Docker.

>[!NOTE]
>W tym przykładzie moduł jest uruchamiany lokalnie; nie na platformie Azure.

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

    ![Komunikat informujący o pomyślnej kompilacji](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. W wierszu polecenia wprowadź `docker image ls`, aby wyświetlić utworzony `terra-mod-example` modułu.

    ![Lista zawierająca nowy moduł](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Wprowadź polecenie `docker run -it terra-mod-example /bin/sh`. Po uruchomieniu polecenia `docker run` Jesteś w środowisku Docker. W tym momencie można odnaleźć plik za pomocą polecenia `ls`.

    ![Lista plików w Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

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

1. Wprowadź `exit`, aby zakończyć test i wyjść ze środowiska Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Tworzenie i testowanie modułu w usłudze Cloud Shell przy użyciu generatora Yeoman

W tej sekcji Generator narzędzia Yeoman służy do tworzenia i testowania modułu w Cloud Shell. Użycie usługi Cloud Shell zamiast pliku Docker znacznie upraszcza ten proces. Korzystając z Cloud Shell, wszystkie następujące produkty są wstępnie zainstalowane:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Uruchamianie sesji usługi Cloud Shell

1. Uruchom sesję usługi Azure Cloud Shell za pośrednictwem witryny [Azure Portal](https://portal.azure.com/), witryny [shell.azure.com](https://shell.azure.com) lub [aplikacji mobilnej platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Zostanie otwarta strona **Witamy w usłudze Azure Cloud Shell**. Wybierz pozycję **Bash (Linux)** .

    ![Witamy w usłudze Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Jeśli nie jest jeszcze skonfigurowane konto magazynu platformy Azure, zostanie wyświetlony poniższy ekran. Wybierz pozycję **Utwórz magazyn**.

    ![Nie masz zainstalowanego magazynu](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Usługa Azure Cloud Shell zostanie uruchomiona w wybranej wcześniej powłoce i wyświetli informacje dotyczące dysku w chmurze, który właśnie został utworzony dla Ciebie.

    ![Dysk w chmurze został utworzony](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Przygotuj katalog do przechowywania modułu Terraform

1. W tym momencie usługa Cloud Shell skonfigurowała już wartość GOPATH w zmiennych środowiskowych. Aby wyświetlić tę ścieżkę, wprowadź polecenie `go env`.

1. Utwórz katalog $GOPATH, jeśli jeszcze nie istnieje: wprowadź `mkdir ~/go`.

1. Utwórz katalog w katalogu $GOPATH. Ten katalog jest używany do przechowywania różnych katalogów projektu utworzonych w tym przykładzie. 

    ```bash
    mkdir ~/go/src
    ```

1. Utwórz katalog do przechowywania modułu Terraform, zastępując symbol zastępczy. W tym przykładzie jest używana nazwa katalogu `my-module-name`.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Przejdź do katalogu modułu: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Tworzenie i testowanie modułu Terraform

1. Uruchom następujące polecenie i postępuj zgodnie z instrukcjami. Gdy zostanie wyświetlony monit o utworzenie plików platformy Docker, należy wprowadzić `N`.

    ```bash
    yo az-terra-module
    ```

1. Uruchom następujące polecenie, aby zainstalować zależności:

    ```bash
    bundle install
    ```

1. Uruchom następujące polecenie, aby skompilować moduł:

    ```bash
    rake build
    ```

    ![Kompilacja szczegółowa](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Uruchom następujące polecenie, aby uruchomić tekst:

    ```bash
    rake e2e
    ```

    ![Wyniki przebiegu testowego](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj rozszerzenie Visual Studio Code Azure Terraform i użyj go](/azure/terraform/terraform-vscode-extension).