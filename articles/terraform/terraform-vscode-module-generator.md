---
title: Tworzenie szablonu podstawowego Terraform na platformie Azure przy użyciu narzędzia Yeoman
description: Dowiedz się, jak utworzyć szablon podstawowy Terraform na platformie Azure przy użyciu narzędzia Yeoman.
services: terraform
ms.service: azure
keywords: terraform, devops, virtual machine, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 7a628eb02170346a826cab19498d6fdf40cebddd
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173380"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Tworzenie szablonu podstawowego Terraform na platformie Azure przy użyciu narzędzia Yeoman

Narzędzie [Terraform](https://docs.microsoft.com/azure/terraform/
) umożliwia łatwe tworzenie infrastruktury na platformie Azure. Narzędzie [Yeoman](https://yeoman.io/) znacznie upraszcza zadanie dewelopera modułu podczas tworzenia modułów Terraform, zapewniając doskonałą platformę *najlepszych rozwiązań*.

W tym artykule dowiesz się, jak używać generatora modułu narzędzia Yeoman do tworzenia podstawowego szablonu Terraform. Dowiesz się, jak przetestować nowy szablon Terraform przy użyciu dwóch różnych metod:

- Uruchamianie modułu Terraform przy użyciu pliku Docker, który zostanie utworzony w tym artykule.
- Uruchamianie modułu Terraform natywnie w usłudze Azure Cloud Shell.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- **Visual Studio Code**: będziemy używali programu [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) do badania plików utworzonych przez generator narzędzia Yeoman. Jednak możesz użyć dowolnego wybranego przez siebie edytora kodu.
- **Terraform**: musisz zainstalować narzędzie [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ), aby uruchomić moduł utworzony przez narzędzie Yeoman.
- **Docker**: użyjemy platformy [Docker](https://www.docker.com/get-started), aby uruchomić moduł utworzony przez generator narzędzia Yeoman. (Jeśli wolisz, możesz użyć języka Ruby zamiast platformy Docker do uruchomienia przykładowego modułu).
- **Język programowania Go**: musisz zainstalować język [Go](https://golang.org/), ponieważ przypadki testowe wygenerowane przez narzędzie Yeoman są napisane w języku Go.

>[!NOTE]
>Większość procedur opisanych w tym samouczku obejmuje wpisy wiersza polecenia. Opisane tutaj kroki dotyczą wszystkich systemów operacyjnych i narzędzi wiersza polecenia. W naszych przykładach wybraliśmy używanie programu PowerShell w środowisku lokalnym oraz programu Git Bash w środowisku powłoki w chmurze.

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

### <a name="install-nodejs"></a>Instalowanie środowiska Node.js

Aby używać narzędzia Terraform w usłudze Cloud Shell, musisz [zainstalować środowisko Node.js](https://nodejs.org/en/download/) w wersji 6.0 lub nowszej.

>[!NOTE]
>Aby sprawdzić, czy środowisko Node.js jest zainstalowane, otwórz okno terminalu i wprowadź polecenie `node --version`.

### <a name="install-yeoman"></a>Instalowanie narzędzia Yeoman

W wierszu polecenia wprowadź polecenie `npm install -g yo`

![Instalowanie narzędzia Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Zainstaluj szablon narzędzia Yeoman dla modułu narzędzia Terraform

W wierszu polecenia wprowadź polecenie `npm install -g generator-az-terra-module`.

![Zainstaluj element generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Aby sprawdzić, czy narzędzie Yeoman jest zainstalowane, w oknie terminalu wprowadź polecenie `yo --version`.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Utwórz pusty folder do przechowywania modułu wygenerowanego przez narzędzia Yeoman

Szablon narzędzia Yeoman generuje pliki w **bieżącym katalogu**. Z tego powodu musisz utworzyć katalog.

>[!Note]
>Ten pusty katalog należy umieścić na ścieżce $GOPATH/src. Instrukcje wykonania tej czynności znajdziesz [tutaj](https://github.com/golang/go/wiki/SettingGOPATH).

W wierszu polecenia:

1. Przejdź do katalogu nadrzędnego, w którym ma zostać utworzony nowy, pusty katalog.
1. Wprowadź polecenie `mkdir <new-directory-name>`.

    > [!NOTE]
    > Zamień `<new-directory-name>` na nazwę nowego katalogu. W tym przykładzie nadaliśmy nowemu katalogowi nazwę `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Przejdź do nowego katalogu, wpisując `cd <new directory's name>`, a następnie naciskając klawisz **enter**.

    ![Przejdź do swojego nowego katalogu](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Aby upewnić się, że ten katalog jest pusty, wprowadź polecenie `ls`. Lista plików wyświetlonych w wynikowych danych wyjściowych tego polecenia powinna być pusta.

## <a name="create-a-base-module-template"></a>Utwórz szablon podstawowego modułu

W wierszu polecenia:

1. Wprowadź polecenie `yo az-terra-module`.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby podać następujące informacje:

    - *Nazwa projektu modułu narzędzia Terraform*

        ![Project name (Nazwa projektu)](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >W tym przykładzie wprowadziliśmy `doc-sample-module`.

    - *Czy chcesz dołączyć plik obrazu platformy Docker?*

        ![Dołączyć plik obrazu platformy Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Wprowadź polecenie `y`. Jeśli wybierzesz **n**, kod wygenerowanego modułu będzie obsługiwał uruchamianie tylko w trybie natywnym.

3. Wprowadź polecenie `ls`, aby wyświetlić utworzone pliki wynikowe.

    ![Wyświetlanie utworzonych plików](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Przeglądanie kodu wygenerowanego modułu

1. Uruchom program Visual Studio Code.

1. Na pasku menu wybierz pozycję **File > Open Folder** (Plik > Otwórz folder), a następnie przejdź do utworzonego folderu.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Przyjrzyjmy się kilku plikom, które zostały utworzone przez generator modułu narzędzia Yeoman.

>[!Note]
>W tym artykule użyjemy plików main.tf, variables.tf i outputs.tf w postaci utworzonej przez generator modułów narzędzia Yeoman. Jednak podczas tworzenia własnych modułów, trzeba będzie edytować te pliki, aby uwzględnić funkcjonalność modułu narzędzia Terraform. Aby zapoznać się z bardziej szczegółowym omówieniem tych plików i ich użycia, zobacz [Terratest w modułach Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

### <a name="maintf"></a>main.tf

Definiuje moduł o nazwie *random-shuffle*. Dane wejściowe to *string_list*. Dane wyjściowe to liczba permutacji.

### <a name="variablestf"></a>variables.tf

Określa zmienne wejściowe i wyjściowe używane przez moduł.

### <a name="outputstf"></a>outputs.tf

Definiuje dane wyjściowe modułu. Tutaj jest to wartość zwrócona przez moduł **random_shuffle**, który jest wbudowanym modułem narzędzia Terraform.

### <a name="rakefile"></a>Rakefile

Definiuje kroki kompilacji. Kroki te obejmują:

- **build**: weryfikuje formatowanie pliku main.tf.
- **unit**: szkielet wygenerowanego modułu nie zawiera kodu dla testu jednostkowego. Jeśli chcesz określić scenariusz testu jednostkowego, musisz dodać kod w tym miejscu.
- **e2e**: uruchamia całościowy test modułu.

### <a name="test"></a>test

- Przypadki testowe są pisane w języku Go.
- Wszystkie kody w teście są testami całościowymi.
- Testy całościowe próbują użyć narzędzia Terraform do aprowizacji wszystkich elementów zdefiniowanych w obszarze **warunków początkowych testu**, a następnie porównać dane wyjściowe w kodzie **template_output.go** za pomocą wstępnie zdefiniowanych oczekiwanych wartości.
- **Gopkg.lock** i **Gopkg.toml**: definiowanie zależności. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testowanie modułu Terraform przy użyciu pliku Docker

>[!NOTE]
>W naszym przykładzie uruchamiamy moduł jako moduł lokalny i w rzeczywistości nie dotykamy platformy Azure.

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

    ![Pomyślna kompilacja](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. W wierszu polecenia wprowadź polecenie `docker image ls`.

    Na liście zostanie wyświetlony nowo utworzony moduł *terra-mod-example*.

    ![Wyniki repozytorium](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >Nazwa modułu, *terra-mod-example*, została określona w poleceniu wprowadzonym w kroku 1 powyżej.

1. Wprowadź polecenie `docker run -it terra-mod-example /bin/sh`.

    Teraz używasz platformy Docker i możesz wyświetlić listę plików, wprowadzając polecenie `ls`.

    ![Listing pliku platformy Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Konstruowanie modułu

1. Wprowadź polecenie `bundle install`.

    Poczekaj na komunikat **Bundle complete** (Ukończono pakiet), po czym przejdź do następnego kroku.

1. Wprowadź polecenie `rake build`.

    ![Kompilacja szczegółowa](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Uruchamianie testu całościowego

1. Wprowadź polecenie `rake e2e`.

1. Po kilku chwilach zostanie wyświetlony komunikat **PASS** (POWODZENIE).

    ![POWODZENIE](media/terraform-vscode-module-generator/ymg-pass.png)

1. Wprowadź polecenie `exit`, aby ukończyć test całościowy i zamknąć środowisko Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Tworzenie i testowanie modułu w usłudze Cloud Shell przy użyciu generatora Yeoman

W poprzedniej sekcji wyjaśniono, jak przetestować moduł Terraform za pomocą pliku Docker. W tej sekcji utworzysz i przetestujesz moduł w usłudze Cloud Shell przy użyciu generatora Yeoman.

Użycie usługi Cloud Shell zamiast pliku Docker znacznie upraszcza ten proces. Korzystanie z usługi Cloud Shell:

- Nie trzeba instalować środowiska Node.js
- Nie trzeba instalować narzędzia Yeoman
- Nie trzeba instalować platformy Terraform

Wszystkie te elementy są preinstalowane w usłudze Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Uruchamianie sesji usługi Cloud Shell

1. Uruchom sesję usługi Azure Cloud Shell za pośrednictwem witryny [Azure Portal](https://portal.azure.com/), witryny [shell.azure.com](https://shell.azure.com) lub [aplikacji mobilnej platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Zostanie otwarta strona **Witamy w usłudze Azure Cloud Shell**. Wybierz pozycję **Bash (Linux)** . (Program PowerShell nie jest obsługiwany).

    ![Witamy w usłudze Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >W tym przykładzie jest wybrana powłoka Bash (Linux).

1. Jeśli nie jest jeszcze skonfigurowane konto magazynu platformy Azure, zostanie wyświetlony poniższy ekran. Wybierz pozycję **Utwórz magazyn**.

    ![Nie masz zainstalowanego magazynu](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Usługa Azure Cloud Shell zostanie uruchomiona w wybranej wcześniej powłoce i wyświetli informacje dotyczące dysku w chmurze, który właśnie został utworzony dla Ciebie.

    ![Dysk w chmurze został utworzony](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Przygotowywanie folderu do przechowywania modułu Terraform

1. W tym momencie usługa Cloud Shell skonfigurowała już wartość GOPATH w zmiennych środowiskowych. Aby wyświetlić tę ścieżkę, wprowadź polecenie `go env`.

1. Utwórz folder $GOPATH, jeśli jeszcze nie istnieje: Wprowadź polecenie `mkdir ~/go`.

1. Utwórz folder w folderze $GOPATH: Wprowadź polecenie `mkdir ~/go/src`. Ten folder będzie używany do przechowywania i organizowania różnych folderów projektu, które można utworzyć, takich jak `<your-module-name>` folder, który zostanie utworzony w następnym kroku.

1. Utwórz folder do przechowywania modułu Terraform: Wprowadź polecenie `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >W tym przykładzie wybraliśmy nazwę folderu: `my-module-name`.

1. Przejdź do folderu modułu: Wprowadź wartość `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Tworzenie i testowanie modułu Terraform

1. Wprowadź polecenie `yo az-terra-module` i postępuj zgodnie z instrukcjami w kreatorze.

    >[!NOTE]
    >Po wyświetleniu monitu z pytaniem, czy chcesz utworzyć pliki Docker, możesz wprowadzić polecenie `N`.

1. Wprowadź polecenie `bundle install`, aby zainstalować zależności.

    Poczekaj na komunikat **Bundle complete** (Ukończono pakiet), po czym przejdź do następnego kroku.

1. Wprowadź polecenie `rake build` w celu utworzenia modułu.

    ![Kompilacja szczegółowa](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Wprowadź polecenie `rake e2e` w celu uruchomienia testu całościowego.

1. Po kilku chwilach zostanie wyświetlony komunikat **PASS** (POWODZENIE).

    ![POWODZENIE](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instalowanie i używanie rozszerzenia Azure Terraform dla programu Visual Studio Code](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
