---
title: Wdrażanie aplikacji w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Jak wdrażać i usuwać aplikacje w usłudze Service Fabric przy użyciu programu PowerShell.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: f0f66cd32721e277cbd6e4578b0e58bb201ee966
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393274"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Wdrażanie i usunąć aplikacje przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Program PowerShell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Gdy [typu aplikacja została spakowana][10], jest gotowa do wdrożenia w klastrze usługi Azure Service Fabric. Wdrożenie obejmuje następujące trzy kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów.
2. Za pomocą obrazu Zapisz ścieżkę względną, zarejestrować typ aplikacji.
3. Tworzy wystąpienie aplikacji.

Po wdrożonej aplikacji nie jest już wymagane, możesz usunąć wystąpienie aplikacji i typu aplikacji. Aby całkowicie usunąć aplikację z klastra obejmuje następujące czynności:

1. Usuń (lub usuwać) uruchamianie wystąpienia aplikacji.
2. Jeśli nie są już potrzebne, należy wyrejestrować typ aplikacji.
3. Usuń pakiet aplikacji z magazynu obrazów.

Jeśli używasz programu Visual Studio umożliwiające wdrażanie i debugowanie aplikacji na lokalnego klastra projektowego powyższych kroków są obsługiwane automatycznie za pomocą skryptu programu PowerShell.  Ten skrypt znajduje się w *skrypty* folderu projektu aplikacji. W tym artykule podano ogólne informacje na ten skrypt czynności, aby wykonywać te same operacje poza programem Visual Studio. 

Jest innym sposobem, aby wdrożyć aplikację za pomocą zewnętrznego. Pakiet aplikacji może być [spakowanych jako `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) i przekazać do magazynu zewnętrznego. W tym przypadku przekazać go do magazynu obrazów nie jest wymagana. Wdrożenie musi mieć następujące czynności:

1. Przekaż `sfpkg` do zewnętrznego magazynu. Zewnętrzny magazyn może być dowolnego magazynu, który uwidacznia punkt końcowy http lub https REST.
2. Zarejestrować typ aplikacji przy użyciu zewnętrznego identyfikatora URI pobierania i informacje o typie aplikacji.
2. Tworzy wystąpienie aplikacji.

Czyszczenie usuń wystąpienia aplikacji i wyrejestrować typ aplikacji. Ponieważ pakiet nie został skopiowany do magazynu obrazów, nie ma żadnych lokalizacji tymczasowej, aby oczyścić. Inicjowanie obsługi administracyjnej z magazynu zewnętrznego jest dostępna, począwszy od usługi Service Fabric wersji 6.1.

>[!NOTE]
> Program Visual Studio nie obsługuje obecnie zewnętrznego.

 

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Przed uruchomieniem dowolnych poleceń programu PowerShell w tym artykule, zawsze uruchomić przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) do łączenia z klastrem usługi Service Fabric. Aby połączyć z lokalnego klastra projektowego, uruchom następujące polecenie:

```powershell
Connect-ServiceFabricCluster
```

Przykłady łączenia do zdalnego klastra lub klastra, zabezpieczone przy użyciu usługi Azure Active Directory, X509 certyfikatów lub usługi Windows Active Directory, zobacz [nawiązywanie połączenia z zabezpieczonym klastrem](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Przekazywanie pakietu aplikacji

Przekazywanie pakietu aplikacji umieszczane w lokalizacji, który jest dostępny dla wewnętrznych składników usługi Service Fabric.
Jeśli chcesz zweryfikować pakietu aplikacji lokalnie, użyj [ServiceFabricApplicationPackage testu](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) polecenia cmdlet.

[ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) polecenia przekazuje pakiet aplikacji do magazynu obrazów klastra.

Załóżmy, że zostanie utworzona i tworzenie pakietu aplikacji o nazwie *MyApplication* w programie Visual Studio 2015. Domyślnie nazwa typu aplikacji, które są wymienione w ApplicationManifest.xml jest "MyApplicationType".  Pakiet aplikacji, który zawiera manifest aplikacji konieczne manifesty usługi i pakietów kodu/config/danych, znajduje się w *C:\Users\<username\>\Documents\Visual Studio 2015\Projects\ MyApplication\MyApplication\pkg\Debug*. 

Następujące polecenie wyświetla zawartość pakietu aplikacji:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Jeśli pakiet aplikacji jest duży i/lub ma wiele plików, możesz to zrobić [skompresować je](service-fabric-package-apps.md#compress-a-package). Kompresja zmniejsza rozmiar i liczba plików.
Efektem ubocznym jest oznacza rejestrowanie i wyrejestrowanie typu aplikacji są realizowane szybciej. Czas przekazywania może być niższa obecnie, zwłaszcza, jeśli uwzględnia czasu kompresowanie pakietu. 

Aby skompresować pakietu, należy używać tego samego [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) polecenia. Kompresji może odbywać się oddzielnie od przekazywania, przy użyciu `SkipCopy` flagi lub wraz z operacji przekazywania. Stosowanie kompresji w skompresowanym pakiecie jest pusta.
Aby zdekompresować skompresowany pakiet, należy używać tego samego [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) polecenia `UncompressPackage` przełącznika.

Następujące polecenie cmdlet kompresuje pakietu bez kopiowania go do magazynu obrazów. Ten pakiet zawiera teraz pliki zip `Code` i `Config` pakietów. Manifesty aplikacji i usługi są nie zip, ponieważ są one wymagane przez wiele operacji wewnętrznych (takich jak pakiet do udostępniania, aplikacji typu nazwą i wersją wyodrębnianie dla niektórych operacji sprawdzania poprawności). Kompresowanie manifesty czyniłyby, te operacje nieefektywne.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

W przypadku dużych aplikacji pakietów kompresji jest czasochłonne. Aby uzyskać najlepsze wyniki Użyj szybki dysk SSD. Czasy kompresji i rozmiaru skompresowanego pakietu również różnią się zależnie od zawartości pakietu.
Na przykład Oto statystyki kompresji dla niektórych pakietów, które wskazują na początkowym i rozmiaru skompresowanego pakietu, z czasem kompresji.

|Początkowy rozmiar (MB)|Liczba plików|Czas kompresji|Skompresowany pakiet rozmiar (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Gdy pakiet jest skompresowany, mogła być przekazana do jednego lub wielu klastrów usługi Service Fabric zgodnie z potrzebami. Mechanizm wdrażania to ten sam skompresowanym i nieskompresowanym pakietów. Skompresowane pakiety są przechowywane w związku z tym w magazynu obrazów klastra. Pakiety są dekompresowane w węźle przed uruchomieniem aplikacji.


Poniższy przykład przekazuje pakiet do magazynu obrazów w folderze o nazwie "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Jeśli nie określisz *- ApplicationPackagePathInImageStore* parametru, pakiet aplikacji jest kopiowany do folderu "Debugowanie" w magazynie obrazów.

>[!NOTE]
>**Kopiuj ServiceFabricApplicationPackage** automatycznie wykryje parametry połączenia magazynu odpowiedni obraz, jeśli sesja programu PowerShell jest podłączony do klastra usługi Service Fabric. Dla usługi Service Fabric w wersji wcześniejszej niż 5.6 **- ImageStoreConnectionString** argument muszą zostać podane jawnie.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>**Get ImageStoreConnectionStringFromClusterManifest** polecenia cmdlet, który jest częścią tego modułu Service Fabric SDK programu PowerShell, jest używana do pobierania parametrów połączenia magazynu obrazów.  Aby zaimportować moduł SDK, uruchom polecenie:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Zobacz [zrozumieć parametry połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) dodatkowych informacji na temat magazynu obrazów i obrazów można przechowywać w parametrach połączenia.
>
>
>

Czas potrzebny na wysłanie pakietu różnią się zależnie od wielu czynników. Niektóre z tych czynników kilka plików pakietu, rozmiar pakietu i rozmiarów plików. Szybkość sieci między maszyną źródłową a klastra usługi Service Fabric ma również wpływ na czas przekazywania. Domyślny limit czasu [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) to 30 minut.
W zależności od czynników opisany może być konieczne zwiększenie limitu czasu. Czy kompresowanie pakietu w wywołaniu kopiowania, należy również wziąć pod uwagę podczas kompresji.



## <a name="register-the-application-package"></a>Zarejestruj pakiet aplikacji

Typ i wersja aplikacji zadeklarowane w manifeście aplikacji stają się dostępne do użycia po zarejestrowaniu pakietu aplikacji. System odczytuje pakiet, który został przekazany w poprzednim kroku, sprawdza pakiet, przetwarza zawartość pakietu i kopiuje przetwarzania pakietu do lokalizacji systemu wewnętrznego.  

Uruchom [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) polecenia cmdlet, aby zarejestrować typ aplikacji w klastrze i udostępnić wdrożenia:

### <a name="register-the-application-package-copied-to-image-store"></a>Zarejestruj pakiet aplikacji jest kopiowane do magazynu obrazów

Gdy pakiet został wcześniej skopiowany do magazynu obrazów, operacja rejestru określa ścieżkę względną w magazynie obrazów.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" jest to folder w magazynie obrazów, w którym znajduje się pakiet aplikacji. Typ aplikacji o nazwie "MyApplicationType" i wersji "1.0.0" (zarówno znajdują się w manifeście aplikacji) jest teraz zarejestrowany w klastrze.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Zarejestruj pakiet aplikacji jest kopiowany do magazynu zewnętrznego

Począwszy od usługi Service Fabric wersji 6.1, aprowizować obsługuje pobieranie pakietu z magazynu zewnętrznego. Pobieranie identyfikatora URI reprezentuje ścieżkę do [ `sfpkg` pakiet aplikacji](service-fabric-package-apps.md#create-an-sfpkg) skąd można pobrać pakietu aplikacji przy użyciu protokołów HTTP lub HTTPS. Pakiet musi zostały wcześniej przekazane do tej lokalizacji zewnętrznej. Identyfikator URI musi zezwalać na dostęp do odczytu, więc usługi Service Fabric można pobrać plik. `sfpkg` Pliku musi mieć rozszerzenie "sfpkg". Operacja aprowizacji powinien zawierać typ informacje o aplikacji, tak jak w manifeście aplikacji.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

[ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) polecenie zwraca tylko wtedy, gdy system pomyślnie zarejestrowała pakietu aplikacji. Jak długo trwa rejestracji zależy od rozmiaru i zawartości pakietu aplikacji. Jeśli to konieczne, **- TimeoutSec** parametru może służyć do Podaj dłuższego limitu czasu (domyślna wartość limitu czasu wynosi 60 sekund).

W przypadku dużych aplikacji, pakietów lub jeśli występują przekroczenia limitu czasu, użyj **- Async** parametru. Polecenie zwraca, gdy klaster akceptuje polecenie rejestracji. Operacja rejestracji jest kontynuowane zgodnie z potrzebami.
[Get ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) polecenie wyświetla listę wersje typu aplikacji i ich stan rejestracji. To polecenie służy do określenia po zakończeniu rejestracji.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji z magazynu obrazu

Jeśli pakiet został skopiowany do magazynu obrazów, należy usunąć ją z lokalizacji tymczasowej, po pomyślnym zarejestrowaniu aplikacji. Usuwanie pakietów aplikacji w sklepie obraz zwolnienie zasobów systemowych. Utrzymywanie pakiety aplikacji nieużywane wykorzystuje Magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Tworzenie aplikacji

Można utworzyć wystąpienie aplikacji z dowolną wersję typu aplikacji, który został pomyślnie zarejestrowany za pomocą [New ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) polecenia cmdlet. Nazwy poszczególnych aplikacji musi rozpoczynać się *"Service fabric:"* schemat i muszą być unikatowe dla każdego wystąpienia aplikacji. Domyślne zdefiniowanych żadnych usług w manifeście aplikacji typ aplikacji docelowej są również tworzone.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Wiele wystąpień aplikacji mogą być tworzone dla dowolnej wersji danego typu zarejestrowanej aplikacji. Każde wystąpienie aplikacji działa w izolacji z katalogu roboczego i procesu.

Aby zobaczyć, który o nazwie aplikacje i usługi są uruchomione w klastrze, uruchom [Get ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) i [Get ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) poleceń cmdlet:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Gdy wystąpienie aplikacji nie jest już potrzebny, można trwale usunąć ją za pomocą nazwy [ServiceFabricApplication Usuń](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) polecenia cmdlet. [Usuń ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatycznie usuwa wszystkie usługi, które należą do aplikacji oraz, trwałe usunięcie wszystkich stanów usługi. 

> [!WARNING]
> Nie można cofnąć tej operacji i nie można odzyskać stan aplikacji.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Wyrejestrowanie typu aplikacji

Gdy określoną wersję typu aplikacji nie jest już potrzebny, należy wyrejestrować typ aplikacji przy użyciu [ServiceFabricApplicationType Wyrejestruj](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) polecenia cmdlet. Wyrejestrowywanie typy aplikacji nieużywane zwalnia miejsca do magazynowania używane przez magazynu obrazów, usuwając pliki typu aplikacji. Wyrejestrowanie typu aplikacji nie powoduje usunięcia pakietu aplikacji, skopiowane do lokalizacji tymczasowej magazynu obrazu, jeśli użyto kopiowania do magazynu obrazów. Typ aplikacji można wyrejestrować tak długo, jak żadne aplikacje są tworzone na niej, a nie oczekujące aplikacji uaktualnień odwołuje się do niego.

Uruchom [Get ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) wyświetlić typy aplikacji w danym momencie zarejestrowany w klastrze:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Uruchom [ServiceFabricApplicationType Wyrejestruj](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) wyrejestrować typ aplikacji:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage asks for an ImageStoreConnectionString

Środowisko usługi Service Fabric SDK powinno mieć już poprawne ustawienia domyślne, skonfiguruj. Ale jeśli to konieczne, ImageStoreConnectionString dla wszystkich poleceń powinna być zgodna wartość, która używa klastra usługi Service Fabric. W manifeście klastra można znaleźć ImageStoreConnectionString pobrany przy użyciu [Get ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) i polecenia Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

**Get ImageStoreConnectionStringFromClusterManifest** polecenia cmdlet, który jest częścią tego modułu Service Fabric SDK programu PowerShell, jest używana do pobierania parametrów połączenia magazynu obrazów.  Aby zaimportować moduł SDK, uruchom polecenie:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString znajduje się w manifeście klastra:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zobacz [zrozumieć parametry połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) dodatkowych informacji na temat magazynu obrazów i obrazów można przechowywać w parametrach połączenia.

### <a name="deploy-large-application-package"></a>Wdrażanie pakietu dużych aplikacji.

Problem: [Kopiuj ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) upłynie limit czasu dla dużych aplikacji pakietu (kolejność GB).
Wypróbuj:
- Określ większego limitu czasu dla [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) polecenia z `TimeoutSec` parametru. Domyślnie limit czasu to 30 minut.
- Sprawdź połączenie sieciowe między maszyną źródłową i klastra. Jeśli połączenie jest powolne, rozważ użycie na maszynie z połączeniem sieciowym lepiej.
Jeśli komputer kliencki znajduje się w regionie innym niż klaster, należy wziąć pod uwagę przy użyciu komputerze klienckim w regionie bliżej lub tym samym co klaster.
- Sprawdź, czy osiągasz ograniczania zewnętrznych. Na przykład gdy Kreator obrazów jest skonfigurowany do używania usługi azure storage, może być ograniczenie przekazywania.

Problem: Przekazywanie pakietu zostało ukończone pomyślnie, ale [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) upłynie limit czasu. Wypróbuj:
- [Kompresowanie pakietu](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem ich do magazynu obrazów.
Kompresja zmniejsza rozmiar i liczba plików, który z kolei ogranicza ilość ruchu sieciowego i pracować z tej usługi Service Fabric, należy wykonać. Operacja przekazywania może być niższa, (zwłaszcza, Jeśli dołączysz podczas kompresji), ale rejestru i wyrejestrować typ aplikacji są realizowane szybciej.
- Określ większego limitu czasu dla [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z `TimeoutSec` parametru.
- Określ `Async` przełączać [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca, gdy klaster akceptuje polecenie rejestracji typu aplikacja kontynuuje asynchronicznie. Z tego powodu nie ma potrzeby Aby określić wyższy limit czasu, w tym przypadku. [Get ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) polecenie wyświetla listę wszystkich wersji typu pomyślnie zarejestrowanej aplikacji i ich stan rejestracji. To polecenie służy do określenia po zakończeniu rejestracji.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Wdrażanie pakietu aplikacji przy użyciu wielu plików

Problem: [Zarejestruj ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) upłynie limit czasu dla pakietu aplikacji zawierających wiele plików (kolejność tysięcy).
Wypróbuj:
- [Kompresowanie pakietu](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem ich do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większego limitu czasu dla [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z `TimeoutSec` parametru.
- Określ `Async` przełączać [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca, gdy klaster akceptuje polecenie rejestracji typu aplikacja kontynuuje asynchronicznie.
Z tego powodu nie ma potrzeby Aby określić wyższy limit czasu, w tym przypadku. [Get ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) polecenie wyświetla listę wszystkich wersji typu pomyślnie zarejestrowanej aplikacji i ich stan rejestracji. To polecenie służy do określenia po zakończeniu rejestracji.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie pakietu aplikacji](service-fabric-package-apps.md)

[Uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md)

[Wprowadzenie kondycji usługi Service Fabric](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelowanie aplikacji w usłudze Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md