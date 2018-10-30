---
title: Testowanie modułów programu Terraform na platformie Azure za pomocą struktury Terratest
description: Dowiedz się, jak używać struktury Terratest do testowania modułów programu Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, konto magazynu, azure, terratest, test jednostkowy, test integracyjny
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638715"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Testowanie modułów programu Terraform na platformie Azure za pomocą struktury Terratest

Moduły programu Terraform umożliwiają tworzenie składników wielokrotnego użytku, które można łączyć i testować. Implementują one hermetyzację w świecie „infrastruktury jako kodu”.

Podobnie jak w przypadku innych składników oprogramowania, kontrola jakości odgrywa ważną rolę w przypadku modułów programu Terraform. Niestety, mało jest dostępnej dokumentacji wyjaśniającej, jak tworzyć testy jednostkowe i testy integracji w modułach programu Terraform. W tym samouczku przedstawiono infrastrukturę testowania i najlepsze rozwiązania, które zaadaptowaliśmy podczas tworzenia [modułów programu Terraform na platformie Azure](https://registry.terraform.io/browse?provider=azurerm).

Po rozważeniu wszystkich najpopularniejszych infrastruktur testowania wybraliśmy do użycia strukturę [Terratest](https://github.com/gruntwork-io/terratest). Struktura Terratest jest zaimplementowana jako biblioteka języka Go. Udostępnia ona kolekcję funkcji pomocniczych i wzorców dla typowych zadań testowania infrastruktury, takich jak wykonywanie żądań HTTP i logowanie za pomocą protokołu SSH do konkretnej maszyny wirtualnej. Niektóre najważniejsze zalety struktury Terratest:

- **Udostępnia wygodne procedury pomocnicze do sprawdzania infrastruktury.** Ta funkcjonalność jest przydatna, gdy chcesz zweryfikować rzeczywistą infrastrukturę w rzeczywistym środowisku.
- **Struktura folderów jest przejrzyście zorganizowana.** Przypadki testowe będą przejrzyście zorganizowane i będą zgodne ze [standardową strukturą folderów modułu programu Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Wszystkie przypadki testowe są pisane w języku Go.** Ponieważ większość deweloperów programu Terraform jest już deweloperami języka Go, użycie struktury Terratest eliminuje potrzebę nauki kolejnego języka programowania. Ponadto jedyne zależności niezbędne do uruchamiania przypadków testowych struktury Terratest to język Go i program Terraform.
- **Ta infrastruktura jest wysoce rozszerzalna.** Nie jest trudne rozszerzenie o dodatkowe funkcje ponad strukturą Terratest, takie jak funkcje specyficzne dla platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Ten praktyczny przewodnik jest niezależny od platformy — można z niego korzystać w systemie Windows, Linux lub MacOS. Przed kontynuowaniem należy zainstalować następujące oprogramowanie:

- **Język programowania Go**: przypadki testowe programu Terraform pisze się w języku [Go](https://golang.org/dl/).
- **DEP**: [dep](https://github.com/golang/dep#installation) to narzędzie do zarządzania zależnościami dla języka Go.
- **Interfejs wiersza polecenia platformy Azure**: [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) to narzędzie wiersza polecenia do zarządzania zasobami platformy Azure. (Program Terraform obsługuje uwierzytelnianie na platformie Azure za pomocą jednostki usługi lub [interfejsu wiersza polecenia platformy Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)).
- **mage**: użyjemy [pliku wykonywalnego mage](https://github.com/magefile/mage/releases), aby dowiedzieć się, jak uprościć uruchamianie przypadków struktury Terratest. 

## <a name="create-a-static-webpage-module"></a>Tworzenie modułu statycznej strony internetowej

W tym samouczku utworzysz moduł programu Terraform, który aprowizuje statyczną stronę internetową, przekazując pojedynczy plik HTML do obiektu blob w magazynie usługi Azure. Moduł umożliwi użytkownikom na całym świecie dostęp do tej strony internetowej za pomocą adresu URL zwróconego przez moduł.

> [!NOTE]
> Wszystkie pliki opisane w tej sekcji należy utworzyć w ścieżce [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Najpierw utwórz nowy folder o nazwie `staticwebpage` w folderze `src` na ścieżce GoPath. Ogólna struktura folderów w tym samouczku jest przedstawiona poniżej. (Ta sekcja koncentruje się głównie na plikach oznaczonych gwiazdką `(*)`).

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Moduł statycznej strony internetowej przyjmuje trzy elementy wejściowe, które są zadeklarowane w pliku `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Jak wspomniano wcześniej, ten moduł wyprowadza także adres URL zadeklarowany w pliku `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

W ten sposób dotarliśmy do głównej logiki tego modułu. Łącznie aprowizuje on cztery zasoby:
- Grupę zasobów o nazwie określonej przez element wejściowy `website_name` z sufiksem `-staging-rg`.
- Konto magazynu o nazwie określonej przez element wejściowy `website_name` z sufiksem `data001`. Jednak aby dostosować się do ograniczeń nazwy konta magazynu, moduł usunie wszystkie znaki specjalne z nazwy i zamieni wszystkie litery na małe.
- Kontener o stałej nazwie `wwwroot` utworzony w ramach konta magazynu powyżej.
- Pojedynczy plik HTML odczytany z elementu wejściowego `html_path` i przekazany do pliku `wwwroot/index.html`.

Logika modułu statycznej strony internetowej jest zaimplementowana w pliku `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Test jednostkowy

Struktura Terratest to tradycyjnie narzędzie przeznaczone do testów integracji — co oznacza, że aprowizuje rzeczywiste zasoby w rzeczywistym środowisku. Czasami takie zadania mogą stać się wyjątkowo duże, szczególnie jeśli trzeba aprowizować dużą liczbę zasobów. Logika konwersji nazw kont magazynu opisana w poprzedniej sekcji jest dobrym przykładem: tak naprawdę nie potrzebujemy aprowizować żadnych zasobów — a tylko upewnić się, że logika konwersji nazw jest poprawna.

Dzięki elastyczności struktury Terratest łatwo to osiągnąć przy użyciu testów jednostkowych. Testy jednostkowe to przypadki testowe uruchamiane lokalnie (choć nadal jest wymagany dostęp do Internetu) po prostu przez wykonanie poleceń `terraform init` i `terraform plan`. Przypadki testowe testów jednostkowych analizują dane wyjściowe polecenia `terraform plan` i wyszukują wartości atrybutów do porównania.

W pozostałej części tej sekcji opiszemy, jak używamy struktury Terratest do implementowania testów jednostkowych w taki sposób, aby zapewnić, że logika konwersji nazw kont magazynu jest poprawna. Tylko pliki oznaczone gwiazdką `(*)` będą interesujące.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Po pierwsze, pusty plik HTML `./test/fixtures/storage-account-name/empty.html` to tylko plik zastępczy.

Plik `./test/fixtures/storage-account-name/main.tf` to szkielet przypadku testowego. Przyjmuje jeden element wejściowy `website_name`, który jest także elementem wejściowym testów jednostkowych. Jego logikę przedstawiono tutaj:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

I w końcu głównym składnikiem jest implementacja testów jednostkowych: `./test/storage_account_name_unit_test.go`

Jeśli jesteś deweloperem języka Go, rozpoznasz, że pasuje ona do sygnatury klasycznej funkcji testu języka Go, przyjmując argument typu `*testing.T`.

W treści testu jednostkowego mamy łącznie 5 przypadków zdefiniowanych w zmiennej `testCases` (klucz jako dane wejściowe, a wartość jako oczekiwane dane wyjściowe). Dla każdego przypadku testu jednostkowego najpierw uruchomimy polecenie `terraform init` dla folderu warunków początkowych testu (`./test/fixtures/storage-account-name/`). 

Potem polecenie `terraform plan` z konkretnym przypadkiem testowym określonym jako dane wejściowe (zobacz definicję `website_name` w elemencie `tfOptions`) zapisze wynik do pliku `./test/fixtures/storage-account-name/terraform.tfplan` (którego nie ma w ogólnej strukturze folderów).

Następnie plik wyników zostanie przeanalizowany i przekształcony w strukturę możliwą do odczytania przez kod przy użyciu oficjalnego analizatora planów programu Terraform.

Teraz spojrzymy na atrybuty, którymi jesteśmy zainteresowani (w tym przypadku to atrybut `name` elementu `azurerm_storage_account`) i porównamy je z oczekiwanymi danymi wyjściowymi.

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Aby uruchomić testy jednostkowe, należy wykonać poniższe kroki w wierszu polecenia.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Po około minucie zobaczysz wynik tradycyjnego testu języka Go.

### <a name="integration-test"></a>Test integracji

W przeciwieństwie do testów jednostkowych, testy integracji muszą aprowizować zasoby do rzeczywistego środowiska z kompleksowej perspektywy. Struktura Terratest dobrze sprawdza się w takich przypadkach. Ponieważ najlepsze rozwiązanie modułu programu Terraform zaleca także folder `examples` zawierający kilka kompleksowych przykładów, dlaczego nie przetestować tych przykładów w ramach testów integracji? W tej sekcji skoncentrujemy się na trzech plikach oznaczonych gwiazdką `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Najpierw zacznijmy od przykładów. Nowy przykładowy folder o nazwie `hello-world/` został utworzony w folderze `./examples/`. W tym miejscu udostępniamy prostą stronę HTML do przekazania `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

Przykład programu Terraform `./examples/hello-world/main.tf` jest podobny do przedstawionego w testach jednostkowych z jedną dużą różnicą: wyświetla także adres URL przekazanego kodu HTML jako element `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Funkcja testu struktury Terratest i klasyczna funkcja testu języka Go pojawiają się ponownie w pliku testu integracji `./test/hello_world_example_test.go`.

W przeciwieństwie do testów jednostkowych, testy integracji tworzą rzeczywiste zasoby na platformie Azure i dlatego należy uważać, aby uniknąć konfliktów nazw. (Należy zwrócić szczególną uwagę na niektóre globalnie unikatowe nazwy, takie jak nazwa konta magazynu). W związku z tym pierwszym krokiem logiki testowania jest wygenerowanie losowej wartości `websiteName` przy użyciu funkcji `UniqueId()` udostępnionej przez strukturę TerraTest. Ta funkcja spowoduje wygenerowanie losowej nazwy zawierającej małe litery, wielkie litery lub cyfry. Element `tfOptions` sprawia, że wszystkie funkcje programu Terraform są wykonywane dla folderu `./examples/hello-world/` i zapewnia także, że zmienna `website_name` jest ustawiona na losową wartość `websiteName`.

Następnie po kolei są wykonywane funkcje `terraform init`, `terraform apply` i `terraform output`. Użyliśmy innej funkcji pomocniczej `HttpGetWithCustomValidation()` udostępnionej przez strukturę Terratest, aby upewnić się, że kod HTML został przekazany do wyjściowego adresu URL `homepage` zwróconego przez polecenie `terraform output`, przez porównanie kodu stanu HTTP operacji Get z wartością `200` i wyszukanie pewnych słów kluczowych w zawartości HTML. Na koniec wykonanie funkcji `terraform destroy` jest zapewnione dzięki wykorzystaniu instrukcji `defer` języka Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Do uruchomienia testów jednostkowych będzie konieczne wykonanie poniższych kroków w wierszu polecenia.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Po dwóch minutach zobaczysz wynik tradycyjnego testu języka Go. Oczywiście możesz także uruchomić oba testy jednostkowe oraz testy integracji, wykonując polecenia:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Jak widzisz, testy integracji trwają znacznie dłużej niż testy jednostkowe (dwie minuty dla jednego przypadku testowego integracji i jedna minuta dla pięciu przypadków testowych testów jednostkowych). Jednak decyzja o tym, kiedy użyć testów jednostkowych, a kiedy testów integracji, należy do Ciebie. Zazwyczaj preferujemy użycie testów jednostkowych za pomocą funkcji HCL programu Terraform dla złożonej logiki, a testów integracji dla kompleksowej perspektywy użytkownika.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Użycie narzędzia mage do uproszczenia uruchamiania przypadków struktury Terratest 

Jak przedstawiono, uruchamianie przypadków testowych w powłoce nie jest łatwym zadaniem, ponieważ należy przechodzić do różnych katalogów i wykonywać różne polecenia. To dlatego wprowadzamy system kompilacji w projekcie. W tej sekcji użyjemy narzędzia mage systemu kompilacji języka Go, aby wykonać zadanie.

Jedyną rzeczą wymaganą przez narzędzie mage jest umieszczenie pliku `magefile.go` (oznaczonego znakiem `(+)` na poniższej ilustracji) w katalogu głównym projektu.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Oto przykładowy plik `./magefile.go`. W tym skrypcie kompilacji napisanym w języku Go zaimplementowaliśmy pięć kroków kompilacji:
- `Clean`: ten krok usunie wszystkie pliki wygenerowane i tymczasowe powstałe podczas wykonywania testu.
- `Format`: ten krok uruchomi polecenia `terraform fmt` i `go fmt` w celu sformatowania bazy kodu.
- `Unit`: ten krok uruchomi wszystkie testy jednostkowe (przy użyciu konwencji nazw `TestUT_*`) w folderze `./test/`.
- `Integration`: podobnie jak `Unit`, ale zamiast testów jednostkowych zostaną wykonane testy integracji (`TestIT_*`).
- `Full`: ten krok wykonuje kolejno kroki `Clean`, `Format`, `Unit', and ` i Integration.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build/test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Podobnie jak w przypadku uruchamiania kroków wcześniej, możesz użyć następujących poleceń do wykonania pełnego zestawu testu:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

Ostatni wiersz polecenia możesz zastąpić dowolnymi krokami narzędzia mage, na przykład `mage unit` lub `mage clean`. Teraz możesz pomyśleć, że wciąż jest tu za dużo wierszy polecenia i dobrym pomysłem będzie osadzenie poleceń `dep` i polecenia `az login` w pliku magefile. Jednak nie przedstawimy kodu w tym miejscu. Kolejnym krokiem korzystania z narzędzia mage jest udostępnienie kroków przy użyciu systemu pakietów języka Go. Dzięki temu pliki magefile we wszystkich modułach można będzie uprościć przez przywoływanie wspólnej implementacji i deklarowanie zależności (`mg.Deps()`).

> [!NOTE]
> **Opcjonalnie: można ustawić zmienne środowiskowe jednostki usługi pod kątem uruchamiania testów akceptacyjnych**
> 
> Zamiast wykonywania polecenia `az login` przed testami, możesz przeprowadzić uwierzytelnianie platformy Azure, ustawiając zmienne środowiskowe jednostki usługi. Program Terraform publikuje [listę nazw zmiennych środowiskowych](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Tylko pierwsze cztery zmienne środowiskowe są wymagane). Program Terraform publikuje również szczegółowe instrukcje objaśniające sposób [uzyskiwania wartości tych zmiennych środowiskowych.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat struktury Terratest, zobacz [jej stronę w witrynie GitHub](https://github.com/gruntwork-io/terratest). Pewne przydatne informacje dotyczące narzędzia mage możesz znaleźć [na jego stronie w witrynie GitHub](https://github.com/magefile/mage) i [jego stronie głównej](https://magefile.org/).
