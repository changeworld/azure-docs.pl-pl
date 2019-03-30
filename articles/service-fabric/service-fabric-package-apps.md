---
title: Pakiet usługi Azure app Service Fabric | Dokumentacja firmy Microsoft
description: Jak pakiet aplikacji usługi Service Fabric, przed wdrożeniem w klastrze.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: d32d593fcc93ec2e27676b1bb174940c12c24193
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667654"
---
# <a name="package-an-application"></a>Tworzenie pakietu aplikacji
W tym artykule opisano, jak pakiet aplikacji usługi Service Fabric i gotowe do wdrożenia.

## <a name="package-layout"></a>Układ pakietu
Manifest aplikacji, co najmniej jeden manifesty usługi i inne pliki potrzebny pakiet musi być zorganizowane w określonego układu dla wdrożenia w klastrze usługi Service Fabric. Manifesty przykład, w tym artykule musi być zorganizowane w następującą strukturę katalogów:

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Foldery noszą do dopasowania **nazwa** atrybuty każdego odpowiadającego elementu. Na przykład, jeśli manifestu usługi zawiera dwa pakiety kodu z nazwami **MyCodeA** i **MyCodeB**, a następnie dwa foldery o takich samych nazwach będzie zawierać pliki binarne niezbędne dla każdego pakietu kodu.

## <a name="use-setupentrypoint"></a>Użyj SetupEntryPoint
Typowe scenariusze użycia **SetupEntryPoint** kiedy należy uruchomić plik wykonywalny przed uruchomieniem usługi lub musisz wykonać operacji z podwyższonym poziomem uprawnień. Na przykład:

* Konfigurowanie i inicjując zmienne środowiskowe, które wymaga pliku wykonywalnego usługi. Nie jest ograniczona do tylko pliki wykonywalne napisane przy użyciu modeli programowania usługi Service Fabric. Na przykład npm.exe musi mieć pewne zmienne środowiskowe skonfigurowane do wdrażania aplikacji node.js.
* Konfigurowanie kontroli dostępu, instalując certyfikatów zabezpieczeń.

Aby uzyskać więcej informacji na temat konfigurowania **SetupEntryPoint**, zobacz [Skonfiguruj zasady dla punktu wejścia usługi Instalatora](service-fabric-application-runas-security.md)

<a id="Package-App"></a>
## <a name="configure"></a>Konfigurowanie
### <a name="build-a-package-by-using-visual-studio"></a>Tworzenie pakietu za pomocą programu Visual Studio
Jeśli używasz programu Visual Studio 2015 do tworzenia aplikacji można użyć polecenia pakietu do automatycznego tworzenia pakietu, który pasuje do układu opisanych powyżej.

Aby utworzyć pakiet, kliknij prawym przyciskiem myszy projekt aplikacji w Eksploratorze rozwiązań, a następnie wybierz polecenie pakietu, jak pokazano poniżej:

![Pakowanie aplikacji przy użyciu programu Visual Studio][vs-package-command]

Po zakończeniu tworzenia pakietów można znaleźć lokalizacji pakietu w **dane wyjściowe** okna. Krok pakowania odbywa się automatycznie podczas wdrażania lub debugowania aplikacji w programie Visual Studio.

### <a name="build-a-package-by-command-line"></a>Tworzenie pakietu przy użyciu wiersza polecenia
Istnieje również możliwość programowego spakowanie swojej aplikacji za pomocą `msbuild.exe`. Kulisy Visual Studio jest uruchomiony, dzięki czemu dane wyjściowe jest taka sama.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testowanie pakietu
Struktura pakietu lokalnie za pomocą programu PowerShell można sprawdzić za pomocą [ServiceFabricApplicationPackage testu](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) polecenia.
To polecenie sprawdza, czy manifest analizowania problemów i sprawdź wszystkie odwołania. To polecenie weryfikuje tylko strukturalnych poprawność katalogów i plików w pakiecie.
Go nie sprawdza wszystkie zawartości pakietu kodu lub danych, poza sprawdzanie, czy wszystkie niezbędne pliki są obecne.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Ten błąd wskazuje, że *MySetup.bat* pliku, do którego odwołuje się manifestu usługi **SetupEntryPoint** brakuje pakietu kodu. Po dodaniu Brak pliku weryfikacji aplikacja przekazuje:

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

Jeśli aplikacja ma [parametry aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) zdefiniowane, możesz przekazać je w [ServiceFabricApplicationPackage testu](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) właściwe weryfikacji.

Jeśli znasz klastra, w którym aplikacja zostanie wdrożona, zalecane jest przekazywane w `ImageStoreConnectionString` parametru. W tym przypadku pakiet również jest weryfikowane względem poprzedniej wersji aplikacji, które zostały już uruchomione w klastrze. Na przykład sprawdzanie poprawności można wykryć, czy pakiet o tej samej wersji, ale różną zawartość została już wdrożona.  

Gdy aplikacja jest poprawnie spakowany i pozytywnie przejdą weryfikację, należy wziąć pod uwagę kompresowanie pakietu dla szybsze operacje wdrażania.

## <a name="compress-a-package"></a>Kompresowanie pakietu
Gdy pakiet jest duży lub ma wiele plików, można skompresować je celu szybszego wdrażania. Kompresja zmniejsza liczbę i rozmiar pakietu.
W przypadku pakietu aplikacji skompresowany [przekazywanie pakietu aplikacji](service-fabric-deploy-remove-applications.md#upload-the-application-package) może trwać dłużej w porównaniu do przekazania pakietu bez kompresji, zwłaszcza, jeśli kompresja jest wykonywane jako część kopiowania. Dzięki kompresji [rejestrowanie](service-fabric-deploy-remove-applications.md#register-the-application-package) i [wyrejestrowanie typu aplikacji](service-fabric-deploy-remove-applications.md#unregister-an-application-type) są realizowane szybciej.

Mechanizm wdrażania to ten sam skompresowanym i nieskompresowanym pakietów. Jeśli pakiet zostanie skompresowany, jest przechowywany w związku z magazynu obrazów klastra, i jest dekompresowane w węźle przed uruchomieniem aplikacji.
Kompresja zastępuje prawidłowy pakiet usługi Service Fabric z nim skompresowanej wersji. Folder muszą zezwalać na uprawnienia do zapisu. Systemem kompresji już skompresowanych pakietów daje żadnych zmian.

Pakiet można skompresować, uruchamiając polecenie programu Powershell [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z `CompressPackage` przełącznika. Można zdekompresować pakietu z takimi samymi polecenia przy użyciu `UncompressPackage` przełącznika.

Następujące polecenie kompresuje pakietu bez kopiowania go do magazynu obrazów. Możesz skopiować skompresowanego pakietu do co najmniej jeden klaster usługi Service Fabric, stosownie do potrzeb, przy użyciu [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) bez `SkipCopy` flagi.
Ten pakiet zawiera teraz pliki zip `code`, `config`, i `data` pakietów. Manifest aplikacji i manifestów usługi są nie zip, ponieważ są one wymagane przez wiele operacji wewnętrznej. Na przykład pakiet do udostępniania, ekstrakcja nazwa i wersja typu aplikacji w przypadku niektórych operacji sprawdzania poprawności wszystkich chcą korzystać manifestów. Kompresowanie manifesty czyniłyby, te operacje nieefektywne.

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Można kompresować i skopiować pakiet z [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) w jednym kroku.
Jeśli pakiet jest duży, podaj wystarczająco wysoka, limitu czasu, aby był czas na kompresję pakietu i Przekaż do klastra.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Wewnętrznie Usługa Service Fabric oblicza sumy kontrolne dla pakietów aplikacji w celu weryfikacji. Przy użyciu kompresji, sum kontrolnych są obliczane na skompresowane wersje każdego pakietu. Generowanie nowego pliku zip z tego samego pakietu aplikacji tworzy różne sumy kontrolne. Aby uniknąć błędów sprawdzania poprawności, należy użyć [aprowizacji diff](service-fabric-application-upgrade-advanced.md). Po wybraniu tej opcji nie dołączaj pakiety bez zmian w nowej wersji. Zamiast tego należy odwoływać się do nich bezpośrednio z nowego manifestu usługi.

Jeśli różnica inicjowania obsługi administracyjnej nie jest opcją i musi zawierać pakiety, wygeneruj nowe wersje dla `code`, `config`, i `data` pakietów, aby uniknąć błąd sumy kontrolnej. Generowanie nowych wersji bez zmian pakietów jest niezbędne w przypadku, gdy używany jest skompresowany pakiet niezależnie od tego, czy poprzednia wersja używa kompresji, czy nie.

Pakiet jest teraz prawidłowo w pakiecie, zweryfikowane i skompresowane (jeśli jest to konieczne), aby była gotowa do [wdrożenia](service-fabric-deploy-remove-applications.md) do co najmniej jeden klaster usługi Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Kompresuj pakiety, w przypadku wdrażania przy użyciu programu Visual Studio
Można poinstruować Visual Studio, aby skompresować pakiety na wdrażanie, dodając `CopyPackageParameters` elementu do Twojego profilu publikowania, a następnie ustaw `CompressPackage` atrybutu `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Utwórz sfpkg
Począwszy od wersji 6.1, Usługa Service Fabric umożliwia inicjowanie obsługi administracyjnej z magazynu zewnętrznego.
Po wybraniu tej opcji pakietu aplikacji nie ma być kopiowane do magazynu obrazów. Zamiast tego można utworzyć `sfpkg` i przekaż go do magazynu zewnętrznego, a następnie zapewnienie identyfikatora URI pobierania usługi Service Fabric podczas inicjowania obsługi administracyjnej. Ten sam pakiet może być przygotowana do wielu klastrów. Inicjowanie obsługi administracyjnej z magazynu zewnętrznego oszczędza czas potrzebny do skopiowania pakietu do każdego klastra.

`sfpkg` Plik jest zip, zawierający pakiet początkowym aplikacji, który ma rozszerzenie "sfpkg".
Wewnątrz pliku zip pakiet aplikacji można skompresować lub bez kompresji. Kompresja wewnątrz pliku zip pakietu aplikacji odbywa się na kod, konfiguracji i poziomy pakiet danych jako [wspomniano wcześniej](service-fabric-package-apps.md#compress-a-package).

Aby utworzyć `sfpkg`, rozpoczyna się w folderze zawierający oryginalny pakiet aplikacji, skompresowany. Następnie za pomocą dowolnego narzędzia zip folder z rozszerzeniem "sfpkg". Na przykład użyć [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

`sfpkg` Muszą być przesłane do magazynu zewnętrznego, poza pasmem, poza usługi Service Fabric. Zewnętrzny magazyn może być dowolnego magazynu, który uwidacznia punkt końcowy http lub https REST. Podczas aprowizowania, Usługa Service Fabric wykonuje operację pobierania, aby pobrać `sfpkg` pakiet aplikacji, dzięki czemu Magazyn musi zezwalać na dostęp do odczytu dla pakietu.

Do aprowizacji pakietu, Użyj zewnętrznego, która wymaga identyfikatora URI pobierania i informacje o typie aplikacji.

>[!NOTE]
> Inicjowanie obsługi administracyjnej na podstawie względnej ścieżki magazynu obrazu nie obsługuje obecnie `sfpkg` plików. W związku z tym `sfpkg` nie powinien zostać skopiowany do magazynu obrazów.

## <a name="next-steps"></a>Kolejne kroki
[Wdrażanie i usuwać aplikacje] [ 10] w tym artykule opisano sposób używania programu PowerShell do zarządzania wystąpieniami aplikacji

[Zarządzanie parametrami aplikacji dla wielu środowisk] [ 11] w tym artykule opisano sposób konfigurowania parametry i zmienne środowiskowe dla optymalizacji różne wystąpienia aplikacji.

[Podczas konfigurowania zasad zabezpieczeń dla aplikacji] [ 12] w tym artykule opisano sposób uruchamiania usług w ramach zasad zabezpieczeń, aby ograniczyć dostęp.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
