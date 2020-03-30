---
title: Pakowanie aplikacji sieci szkieletowej usług Azure
description: Dowiedz się więcej o pakowaniu aplikacji sieci szkieletowej usługi Azure i o tym, jak przygotować się do wdrożenia w klastrze.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7c99eec28ac06ecf666d6dda1015f889841a5dbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258345"
---
# <a name="package-an-application"></a>Tworzenie pakietu aplikacji

W tym artykule opisano sposób pakowania aplikacji sieci szkieletowej usług i przygotowania jej do wdrożenia.

## <a name="package-layout"></a>Układ pakietu

Manifest aplikacji, co najmniej jeden manifest usługi i inne niezbędne pliki pakietów muszą być zorganizowane w określonym układzie do wdrożenia w klastrze sieci szkieletowej usług. Przykładowe manifesty w tym artykule musiałyby zostać zorganizowane w następującej strukturze katalogów:

```
tree /f .\MyApplicationType
```

```Output
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

Foldery są nazwane, aby dopasować **name** atrybuty każdego odpowiedniego elementu. Na przykład jeśli manifest usługi zawiera dwa pakiety kodu o nazwach **MyCodeA** i **MyCodeB**, następnie dwa foldery o tych samych nazwach będzie zawierać niezbędne pliki binarne dla każdego pakietu kodu.

## <a name="use-setupentrypoint"></a>Korzystanie z programu SetupEntryPoint

Typowe scenariusze korzystania z **Programu SetupEntryPoint** to czas, w którym należy uruchomić plik wykonywalny przed uruchomieniem usługi lub wykonać operację z podwyższonymi uprawnieniami. Przykład:

* Konfigurowanie i inicjowanie zmiennych środowiskowych, których potrzebuje plik wykonywalny usługi. Nie ogranicza się tylko do plików wykonywalnych napisanych za pośrednictwem modeli programowania sieci szkieletowej usług. Na przykład npm.exe potrzebuje niektórych zmiennych środowiskowych skonfigurowanych do wdrażania aplikacji node.js.
* Konfigurowanie kontroli dostępu przez instalowanie certyfikatów zabezpieczeń.

Aby uzyskać więcej informacji na temat **konfigurowania programu SetupEntryPoint**, zobacz [Konfigurowanie zasad dla punktu wejścia konfiguracji usługi](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurowanie

### <a name="build-a-package-by-using-visual-studio"></a>Tworzenie pakietu przy użyciu programu Visual Studio

Jeśli do utworzenia aplikacji użyto programu Visual Studio, można użyć polecenia *Package,* aby automatycznie utworzyć pakiet zgodny z układem opisanym powyżej.

Aby utworzyć pakiet, kliknij prawym przyciskiem myszy projekt aplikacji w *Eksploratorze rozwiązań* i wybierz polecenie **Pakiet:**

![Pakowanie aplikacji za pomocą programu Visual Studio][vs-package-command]

Po zakończeniu pakowania można znaleźć lokalizację pakietu w oknie **Dane wyjściowe.** Krok pakowania występuje automatycznie podczas wdrażania lub debugowania aplikacji w programie Visual Studio.

### <a name="build-a-package-by-command-line"></a>Tworzenie pakietu według wiersza polecenia

Możliwe jest również programowo spakować aplikację `msbuild.exe`za pomocą . Pod maską visual studio jest uruchomiony, więc dane wyjściowe jest taka sama.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Przetestuj pakiet

Strukturę pakietu można zweryfikować lokalnie za pomocą programu PowerShell za pomocą polecenia [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)
To polecenie sprawdza problemy z analizowaniem manifestów i weryfikuje wszystkie odwołania. To polecenie sprawdza tylko poprawność strukturalną katalogów i plików w pakiecie.
Nie weryfikuje żadnej zawartości kodu lub pakietu danych poza sprawdzeniem, czy wszystkie niezbędne pliki są obecne.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Ten błąd pokazuje, że w pakiecie kodu brakuje pliku *MySetup.bat,* do którego odwołuje się manifest usługi **SetupEntryPoint.** Po dodaniu brakującego pliku weryfikacja aplikacji przechodzi:

```
tree /f .\MyApplicationType
```

```Output
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
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Jeśli aplikacja ma [zdefiniowane parametry aplikacji,](service-fabric-manage-multiple-environment-app-configuration.md) można przekazać je w [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) dla prawidłowej weryfikacji.

Jeśli znasz klaster, w którym zostanie wdrożona aplikacja, `ImageStoreConnectionString` zaleca się przekazanie w parametrze. W takim przypadku pakiet jest również sprawdzany względem poprzednich wersji aplikacji, które są już uruchomione w klastrze. Na przykład sprawdzanie poprawności można wykryć, czy pakiet z tej samej wersji, ale inna zawartość została już wdrożona.  

Gdy aplikacja jest poprawnie spakowana i przekazuje sprawdzanie poprawności, należy rozważyć kompresję pakietu dla szybszych operacji wdrażania.

## <a name="compress-a-package"></a>Kompresowanie pakietu

Gdy pakiet jest duży lub ma wiele plików, można go skompresować w celu szybszego wdrożenia. Kompresja zmniejsza liczbę plików i rozmiar pakietu.
W przypadku pakietu skompresowanej aplikacji [przekazanie pakietu aplikacji](service-fabric-deploy-remove-applications.md#upload-the-application-package) może potrwać dłużej w porównaniu do przekazywania nieskompresowanego pakietu, zwłaszcza jeśli kompresja odbywa się jako część kopii. Dzięki kompresji, [rejestracji](service-fabric-deploy-remove-applications.md#register-the-application-package) i [odseiwanie typu aplikacji](service-fabric-deploy-remove-applications.md#unregister-an-application-type) są szybsze.

Mechanizm wdrażania jest taki sam dla pakietów skompresowanych i nieskompresowanych. Jeśli pakiet jest skompresowany, jest przechowywany jako taki w magazynie obrazów klastra i jest nieskompresowany w węźle przed uruchomieniem aplikacji.
Kompresja zastępuje prawidłowy pakiet sieci szkieletowej usług skompresowaną wersją. Folder musi zezwalać na uprawnienia do zapisu. Uruchamianie kompresji na już skompresowanym pakiecie nie daje żadnych zmian.

Pakiet można skompresować, uruchamiając polecenie Powershell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z `CompressPackage` przełącznikiem. Pakiet można wyłączyć za pomocą tego samego `UncompressPackage` polecenia, używając przełącznika.

Następujące polecenie kompresuje pakiet bez kopiowania go do magazynu obrazów. W razie potrzeby można skopiować skompresowany pakiet do jednego lub kilku klastrów sieci szkieletowej `SkipCopy` usług, korzystając z [funkcji Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) bez flagi.
Pakiet zawiera teraz spakowane `code`pliki `config`dla `data` , i pakietów. Manifest aplikacji i manifesty usługi nie są spakowane, ponieważ są one potrzebne dla wielu operacji wewnętrznych. Na przykład udostępnianie pakietów, nazwa typu aplikacji i wyodrębnianie wersji dla niektórych weryfikacji muszą uzyskać dostęp do manifestów. Zipping manifestów uczyniłby te operacje nieefektywne.

```
tree /f .\MyApplicationType
```

```Output
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
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Alternatywnie, można skompresować i skopiować pakiet z [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) w jednym kroku.
Jeśli pakiet jest duży, podaj wystarczająco wysoki limit czasu, aby dać czas na kompresję pakietu i przekazywanie do klastra.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Wewnętrznie sieci szkieletowej usług oblicza sumy kontrolne dla pakietów aplikacji do sprawdzania poprawności. Podczas korzystania z kompresji sumy kontrolne są obliczane na spakowanych wersjach każdego pakietu. Generowanie nowego kodu błyskawicznego z tego samego pakietu aplikacji tworzy różne sumy kontrolne. Aby zapobiec błędom sprawdzania poprawności, należy użyć [inicjowania obsługi administracyjnej diff](service-fabric-application-upgrade-advanced.md). W tej opcji nie należy dołączać niezmienionych pakietów w nowej wersji. Zamiast tego odwołaj się do nich bezpośrednio z manifestu nowej usługi.

Jeśli diff inicjowania obsługi administracyjnej nie jest opcją `code`i `config`należy `data` dołączyć pakiety, wygenerować nowe wersje dla , i pakietów, aby uniknąć niezgodności sumy kontrolnej. Generowanie nowych wersji dla pakietów niezmienionych jest konieczne, gdy używany jest pakiet skompresowany, niezależnie od tego, czy poprzednia wersja używa kompresji, czy nie.

Pakiet jest teraz poprawnie spakowany, zweryfikowany i skompresowany (w razie potrzeby), dzięki czemu jest gotowy do [wdrożenia](service-fabric-deploy-remove-applications.md) w co najmniej jednym klastrze sieci szkieletowej usług.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Kompresowanie pakietów podczas wdrażania przy użyciu programu Visual Studio

Program Visual Studio może poinstruować program Visual `CopyPackageParameters` Studio, aby kompresować `CompressPackage` pakiety `true`podczas wdrażania, dodając element do profilu publikowania i ustawiać atrybut na .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Tworzenie sfpkg

Począwszy od wersji 6.1, sieci szkieletowej usług umożliwia inicjowanie obsługi administracyjnej z magazynu zewnętrznego.
Dzięki tej opcji pakiet aplikacji nie musi być kopiowany do magazynu obrazów. Zamiast tego można utworzyć `sfpkg` i przekazać go do magazynu zewnętrznego, a następnie podać identyfikator URI pobierania do sieci szkieletowej usług podczas inicjowania obsługi administracyjnej. Ten sam pakiet można aprowizować do wielu klastrów. Inicjowanie obsługi administracyjnej z magazynu zewnętrznego oszczędza czas potrzebny do skopiowania pakietu do każdego klastra.

Plik `sfpkg` jest zamkiem błyskawicznym, który zawiera początkowy pakiet aplikacji i ma rozszerzenie ".sfpkg".
Wewnątrz suwaka pakiet aplikacji może być skompresowany lub nieskompresowany. Kompresja pakietu aplikacji wewnątrz kodu pocztowego odbywa się na poziomie kodu, konfiguracji i pakietu danych, jak [wspomniano wcześniej](service-fabric-package-apps.md#compress-a-package).

Aby utworzyć `sfpkg`program , należy rozpocząć od folderu zawierającego oryginalny pakiet aplikacji, skompresowanego lub nie. Następnie użyj dowolnego narzędzia, aby skompresować folder z rozszerzeniem ".sfpkg". Na przykład użyj [pliku ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Należy `sfpkg` przekazać do magazynu zewnętrznego poza pasmem, poza sieci szkieletowej usług. Magazyn zewnętrzny może być dowolny magazyn, który udostępnia REST http lub https punktu końcowego. Podczas inicjowania obsługi administracyjnej sieci `sfpkg` szkieletowej usług wykonuje operację GET, aby pobrać pakiet aplikacji, więc magazyn musi zezwalać na dostęp do odczytu dla pakietu.

Aby aprowizować pakiet, należy użyć zewnętrznego udostępniania, który wymaga pobierania identyfikatora URI i informacji o typie aplikacji.

>[!NOTE]
> Inicjowanie obsługi administracyjnej na podstawie `sfpkg` ścieżki względnej magazynu obrazów nie obsługuje obecnie plików. W związku `sfpkg` z tym nie powinny być kopiowane do magazynu obrazów.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie i usuwanie aplikacji][10] opisuje sposób używania programu PowerShell do zarządzania wystąpieniami aplikacji

[Zarządzanie parametrami aplikacji dla wielu środowisk][11] opisuje sposób konfigurowania parametrów i zmiennych środowiskowych dla różnych wystąpień aplikacji.

[Skonfiguruj zasady zabezpieczeń dla aplikacji,][12] opisano sposób uruchamiania usług w ramach zasad zabezpieczeń w celu ograniczenia dostępu.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md