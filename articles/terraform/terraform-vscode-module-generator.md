---
title: Generator modułu narzędzia Azure Terraform w programie VS Code
description: Dowiedz się, jak utworzyć szablon podstawowy Terraform za pomocą narzędzia Yeoman.
services: terraform
ms.service: terraform
keywords: terraform, devops, virtual machine, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 09/12/2018
ms.openlocfilehash: 513b123c44cf2cd37cf81a91e0d2da9599eb1fcd
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396358"
---
# <a name="create-a-terraform-base-template-using-yeoman"></a>Tworzenie szablonu podstawowego Terraform przy użyciu narzędzia Yeoman

Narzędzie [Terraform](https://docs.microsoft.com/azure/terraform/
) umożliwia łatwe tworzenie infrastruktury na platformie Azure. Narzędzie [Yeoman](http://yeoman.io/) znacznie upraszcza zadanie dewelopera modułu podczas tworzenia modułów Terraform, zapewniając doskonałą platformę *najlepszych rozwiązań*.

W tym artykule dowiesz się, jak używać generatora modułu narzędzia Yeoman do tworzenia podstawowego szablonu Terraform.

## <a name="prerequisites"></a>Wymagania wstępne

- Komputer z systemem Windows 10, Linux lub macOS 10.10 +.
- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- **Visual Studio Code**: będziemy używali programu [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) do badania plików utworzonych przez generator narzędzia Yeoman. Jednak możesz użyć dowolnego wybranego przez siebie edytora kodu.
- **Terraform**: musisz zainstalować narzędzie [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ), aby uruchomić moduł utworzony przez narzędzie Yeoman.
- **Docker**: użyjemy platformy [Docker](https://www.docker.com/get-started), aby uruchomić moduł utworzony przez generator narzędzia Yeoman. (Jeśli wolisz, możesz użyć języka Ruby zamiast platformy Docker do uruchomienia przykładowego modułu).
- **Język programowania Go**: musisz zainstalować język [Go](https://golang.org/), ponieważ przypadki testowe wygenerowane przez narzędzie Yeoman są napisane w języku Go.

>[!NOTE]
>Większość procedur opisanych w tym samouczku obejmuje wpisy wiersza polecenia. Opisane tutaj kroki dotyczą wszystkich systemów operacyjnych i narzędzi wiersza polecenia. W naszych przykładach wybraliśmy używanie programu PowerShell. Jednak możesz użyć dowolnego spośród kilku rozwiązań alternatywnych, takich jak Git Bash, wiersz polecenia systemu Windows lub polecenia wiersza polecenia systemu Linux lub macOS.

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

    >[!NOTE]
    >Pozycję <new-directory-name> zastąp nazwą swojego nowego katalogu. W tym przykładzie nadaliśmy nowemu katalogowi nazwę `GeneratorDocSample`.

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

        ![Nazwa projektu](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >W tym przykładzie wprowadziliśmy `doc-sample-module`.

    - *Czy chcesz dołączyć plik obrazu platformy Docker?*

        ![Dołączyć plik obrazu platformy Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Wprowadź `y`. Jeśli wybierzesz **n**, kod wygenerowanego modułu będzie obsługiwał uruchamianie tylko w trybie natywnym.

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
- **Gopkg.lock** i **Gopkg.toml**: definiują Twoje zależności. 

## <a name="test-the-module-using-docker"></a>Testowanie modułu przy użyciu platformy Docker

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

1. Wprowadź polecenie `bundle install`.

    Poczekaj na komunikat **Bundle complete** (Ukończono pakiet), po czym przejdź do następnego kroku.

1. Wprowadź polecenie `rake build`.

    ![Kompilacja szczegółowa](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="perform-the-end-to-end-test"></a>Wykonaj test całościowy

1. Wprowadź polecenie `rake e2e`.

1. Po kilku chwilach zostanie wyświetlony komunikat **PASS** (POWODZENIE).

    ![PASS (POWODZENIE)](media/terraform-vscode-module-generator/ymg-pass.png)

1. Wprowadź polecenie `exit`, aby zakończyć test całościowy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instalowanie i używanie rozszerzenia narzędzia Azure Terraform dla programu Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
