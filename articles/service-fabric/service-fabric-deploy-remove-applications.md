---
title: Wdrażanie aplikacji sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Jak wdrożyć i usunąć aplikacje w sieci szkieletowej usług za pomocą programu PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 0fa7bd1135c099f853b9a3bb66661c0a57a0f7eb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271420"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Wdrażanie i usunąć aplikacje przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Program PowerShell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Raz [typu aplikacji zostały opakowane][10], jest ono gotowe do wdrożenia do klastra usługi sieć szkieletowa usług Azure. Wdrożenie obejmuje następujące trzy kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów.
2. Rejestracja typu aplikacji z obrazu Zapisz ścieżkę względną.
3. Utwórz wystąpienie aplikacji.

Po wdrożonej aplikacji nie jest już potrzebne, można usunąć wystąpienia aplikacji i jej typu aplikacji. Aby całkowicie usunąć aplikację z klastra obejmuje następujące kroki:

1. Usuń (lub usunąć) uruchomione wystąpienie aplikacji.
2. Wyrejestrowywanie typu aplikacji, jeśli nie są już potrzebne.
3. Usuwanie pakietu aplikacji w sklepie obrazu.

Jeśli używasz programu Visual Studio umożliwiające wdrażanie i debugowanie aplikacji w klastrze lokalnym programowanie powyższych kroków obsługi automatycznie za pomocą skryptu programu PowerShell.  Skrypt ten znajduje się w *skryptów* folderu projektu aplikacji. Ten artykuł zawiera tła na tego skryptu czynności, aby można wykonać operacji poza Visual Studio. 

Innym sposobem wdrożenia aplikacji jest użycie zewnętrznego. Pakiet aplikacji może być [spakowanych jako `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) i przekazać do magazynu zewnętrznego. W takim przypadku przekazania do magazynu w obrazie nie jest wymagana. Wdrożenia wymaga następujących czynności:

1. Przekaż `sfpkg` do magazynu zewnętrznego. Zewnętrznym sklepie mogą być dowolnego magazynu, który ujawnia końcowy REST protokołu http lub https.
2. Rejestracja typu aplikacji, przy użyciu identyfikatora URI pobierania zewnętrznych i informacje o typie aplikacji.
2. Utwórz wystąpienie aplikacji.

Oczyszczania usuń wystąpienia aplikacji i wyrejestrowywanie typu aplikacji. Ponieważ pakiet nie został skopiowany do magazynu obrazów, brak tymczasowej lokalizacji do oczyszczania. Inicjowanie obsługi administracyjnej z magazynu zewnętrznego jest dostępnych w programie Service Fabric w wersji 6.1.

>[!NOTE]
> Program Visual Studio nie obsługuje obecnie zewnętrznego.

 
## <a name="connect-to-the-cluster"></a>Łączenie z klastrem
Przed uruchomieniem żadnych poleceń programu PowerShell w tym artykule należy uruchamiać przy użyciu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) do nawiązania połączenia klastra sieci szkieletowej usług. Aby połączyć się z lokalnego klastra projektowego, uruchom następujące polecenie:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Przykłady nawiązywania połączenia zdalnego klastra lub klastra zabezpieczone przy użyciu usługi Azure Active Directory, X509 certyfikatów lub usługi Active Directory systemu Windows, zobacz [Połącz z klastrem bezpiecznego](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Przekaż pakiet aplikacji
Przekazywanie pakietu aplikacji umieszcza je w lokalizacji, który jest dostępny dla wewnętrznych składników usługi Service Fabric.
Jeśli chcesz sprawdzić lokalnie pakietu aplikacji, użyj [ServiceFabricApplicationPackage testu](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) polecenia cmdlet.

[ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) polecenia przekazuje pakiet aplikacji do magazynu obrazu klastra.

Załóżmy, że kompilacji i pakietu aplikacji o nazwie *MyApplication* w programie Visual Studio 2015. Domyślnie nazwa typu aplikacji, które są wymienione w pliku ApplicationManifest.xml jest "MyApplicationType".  Pakiet aplikacji, który zawiera manifest aplikacji konieczne, manifesty usługi i pakietów kodu/config/danych, znajduje się w *C:\Users\<username\>\Documents\Visual 2015\Projects\ w Studio MyApplication\MyApplication\pkg\Debug*. 

Polecenie wyświetla zawartość pakietu aplikacji:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
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

Jeśli pakiet aplikacji jest duży i/lub ma wiele plików, możesz [skompresować je](service-fabric-package-apps.md#compress-a-package). Kompresja zmniejsza rozmiar i liczby plików.
Efektem ubocznym jest to rejestrowania i wyrejestrowywania typ aplikacji są szybsze. Czas przekazywania aktualnie może być mniejsza, zwłaszcza, Jeśli dołączysz czas do skompresowania pakietu. 

Aby skompresować pakietu, należy używać tego samego [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) polecenia. Kompresja można to zrobić oddzielne przesyłaniu, przy użyciu `SkipCopy` flagę lub wraz z operacji przekazywania. Stosowanie kompresji na skompresowanym pakietem jest pusta.
Aby zdekompresować skompresowany pakiet, należy używać tego samego [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z `UncompressPackage` przełącznika.

Następujące polecenie cmdlet kompresuje pakiet, bez kopiowania go do magazynu obrazów. Pakiet zawiera obecnie pliki zip `Code` i `Config` pakietów. Aplikacji i manifestów usługi są nie zip, ponieważ są potrzebne do wielu operacji wewnętrznych (np. pakietu udostępniania, aplikacja nazwa i wersja wyodrębniania typu dla niektórych operacji sprawdzania poprawności). Pakowanie manifesty spowodowałoby, te operacje nieefektywne.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
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

Dla dużych pakietów aplikacji kompresji zajmuje trochę czasu. Aby uzyskać najlepsze rezultaty należy użyć szybkiego dysk SSD. Czasy kompresji i Rozmiar skompresowanych pakietu również się różnić w zależności od zawartości pakietu.
Na przykład w tym miejscu jest statystyki kompresji niektórych pakietów, które wskazują na początkowej i rozmiar skompresowany pakiet z czasem kompresji.

|Początkowy rozmiar (MB)|Liczba plików|Czas kompresji|Skompresowany pakiet rozmiar (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Gdy pakiet jest skompresowana, mogą być przekazywane do jednego lub wielu klastrów sieci szkieletowej usług zgodnie z potrzebami. Mechanizm wdrażania jest takie samo skompresowanym i nieskompresowanym pakietów. Skompresowane pakiety są przechowywane w związku z magazynu obrazu klastra. Pakiety są jako nieskompresowane w węźle przed uruchomieniem aplikacji.


Poniższy przykład przekazuje pakiet do magazynu obrazów do folderu o nazwie "MyApplicationV1":

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Jeśli nie określisz *- ApplicationPackagePathInImageStore* parametru, pakiet aplikacji jest kopiowany do folderu "Debugowanie" w magazynie obrazu.

>[!NOTE]
>**Kopiuj ServiceFabricApplicationPackage** automatycznie wykryje parametry połączenia magazynu odpowiedni obraz, jeśli sesja programu PowerShell jest podłączone do klastra usługi sieć szkieletowa usług. Dla sieci szkieletowej usług w wersji wcześniejszej niż 5.6 **— element ImageStoreConnectionString** jawnie podać argument.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>**Get-ImageStoreConnectionStringFromClusterManifest** polecenia cmdlet, która jest częścią tego modułu programu PowerShell zestawu SDK sieci szkieletowej usług, jest używany do pobierania obrazu parametry połączenia magazynu.  Aby zaimportować moduł zestawu SDK, uruchom polecenie:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Zobacz [zrozumieć parametry połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) o dodatkowe informacje dotyczące magazynu obrazów i obrazu przechowywanie parametrów połączenia.
>
>
>

Czas potrzebny na wysłanie pakietu różni się w zależności od wielu czynników. Niektóre z tych czynników to liczba plików w pakietu, rozmiar pakietu, a rozmiary plików. Szybkość sieci między maszyną źródłową i klaster sieci szkieletowej usług ma wpływ również na czas przekazywania. Domyślny limit czasu dla [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) to 30 minut.
W zależności od czynników opisane należy zwiększyć limit czasu. Kompresji pakietu w wywołaniu kopiowania, należy również wziąć pod uwagę podczas kompresji.



## <a name="register-the-application-package"></a>Zarejestruj pakiet aplikacji
Typ aplikacji i wersji zadeklarowane w manifeście aplikacji staną się dostępne po zarejestrowaniu pakietu aplikacji. System odczytuje pakietów przekazano w poprzednim kroku, sprawdza, czy pakiet przetwarza zawartości pakietu i kopiuje przetworzonych pakietu do lokalizacji systemu wewnętrznego.  

Uruchom [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) polecenia cmdlet, aby zarejestrować typ aplikacji w klastrze i udostępnienie jej do wdrożenia:

### <a name="register-the-application-package-copied-to-image-store"></a>Zarejestruj pakiet aplikacji, skopiować do magazynu obrazu
Jeśli pakiet został wcześniej skopiowany do magazynu obrazów, operacji rejestru określa ścieżkę względną w magazynie obrazu.

```powershell
PS C:\> Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" to folder, w magazynie obrazu, w którym znajduje się pakiet aplikacji. Typ aplikacji o nazwie "MyApplicationType" i wersji "1.0.0", (zarówno znajdują się w manifeście aplikacji) jest zarejestrowany w klastrze.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Zarejestruj pakiet aplikacji, skopiować do magazynu zewnętrznego
Począwszy od platformy Service Fabric w wersji 6.1, udostępnić obsługuje pobieranie pakietu z magazynu zewnętrznego. Pobierz identyfikator URI reprezentuje ścieżkę do [ `sfpkg` pakiet aplikacji](service-fabric-package-apps.md#create-an-sfpkg) skąd można pobrać pakiet aplikacji za pomocą protokołów HTTP lub HTTPS. Pakiet musi mieć wcześniej przekazany do tej lokalizacji zewnętrznych. Identyfikator URI muszą zezwalać na dostęp do odczytu, więc usługi sieć szkieletowa można pobrać pliku. `sfpkg` Pliku musi mieć rozszerzenie ".sfpkg". Operacja inicjowania obsługi administracyjnej powinna zawierać informacje typu aplikacji, tak jak w manifeście aplikacji.

```
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

[ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) polecenie zwraca tylko wtedy, gdy system został pomyślnie zarejestrowany pakiet aplikacji. Jak długo trwa rejestracja zależy od rozmiaru i zawartości pakietu aplikacji. W razie potrzeby **- TimeoutSec** parametru można podać dłuższego limitu czasu (domyślny limit czasu wynosi 60 sekund).

Jeśli masz aplikację duży pakiet lub jeśli występują przekroczenia limitu czasu, użyj **- Async** parametru. Polecenie zwraca, jeśli klaster akceptuje polecenie rejestracji. Operacja rejestracji będzie nadal występować, zgodnie z potrzebami.
[Get ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) polecenie wyświetla wersje typu aplikacji i ich stan rejestracji. To polecenie służy do określania, kiedy odbywa się rejestracji.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji w magazynie obrazów
Jeśli pakiet został skopiowany do magazynu obrazu, usuń ją z lokalizacji tymczasowej po pomyślnym zarejestrowaniu aplikacji. Usunięcie pakietów aplikacji w sklepie obrazu zwolni zasoby systemowe. Utrzymywanie pakietów aplikacji nieużywane korzysta z magazynu danych na dysku i prowadzi do problemów z wydajnością aplikacji.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Tworzenie aplikacji
Można utworzyć wystąpienia aplikacji z dowolnej wersji typu aplikacji, który został pomyślnie zarejestrowany za pomocą [ServiceFabricApplication nowy](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) polecenia cmdlet. Nazwy poszczególnych aplikacji musi rozpoczynać się od *"fabric:"* schemat i muszą być unikatowe dla każdego wystąpienia aplikacji. Wszystkie usługi domyślne zdefiniowane w manifeście aplikacji typu aplikacji docelowej są również tworzone.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Dla dowolnej wersji danego typu zarejestrowanej aplikacji można tworzyć wiele wystąpień aplikacji. Każde wystąpienie aplikacji jest uruchamiany w izolacji z katalogu roboczego i procesu.

Aby zobaczyć, które o nazwie aplikacje i usługi są uruchomione w klastrze, uruchom [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) i [Get ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) poleceń cmdlet:

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Usuwanie aplikacji
Wystąpienie aplikacji nie jest już potrzebne, możesz trwale usunąć go przy użyciu nazwy [ServiceFabricApplication Usuń](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) polecenia cmdlet. [Usuń ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatycznie usuwa wszystkie usługi należące do aplikacji, jak również i stałe usunięcie wszystkich stan usługi. 

> [!WARNING]
> Ta operacja jest nieodwracalna i nie można odzyskać stan aplikacji.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Wyrejestrowywanie typu aplikacji
W przypadku konkretnej wersji typu aplikacji nie jest potrzebna, należy wyrejestrować typu aplikacji przy użyciu [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) polecenia cmdlet. Wyrejestrowywanie typy aplikacji nieużywane wersje miejsca używanego przez składnik image store przez usunięcie plików typu aplikacji. Wyrejestrowywanie typu aplikacji nie spowoduje usunięcia pakietu aplikacji, skopiować do lokalizacji tymczasowej magazynu obrazu użyto kopiowania do magazynu obrazów. Typ aplikacji można wyrejestrować tak długo, jak wystąpienia są tworzone na nim żadnych aplikacji, a nie do czasu aplikacji uaktualnień odwołuje się do niego.

Uruchom [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) do sprawdzenia typów aplikacji w danym momencie zarejestrowany w klastrze:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Uruchom [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) wyrejestrować określonych typów aplikacji:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Element ImageStoreConnectionString wprowadza się ServiceFabricApplicationPackage kopiowania
Zestaw SDK usług sieci szkieletowej środowiska ma już poprawne ustawienia domyślne ustawienie. Jednak w razie potrzeby element ImageStoreConnectionString dla wszystkich poleceń powinna być zgodna wartość, która używa klastra sieci szkieletowej usług. Element ImageStoreConnectionString można znaleźć w manifeście klastra pobrany przy użyciu [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) i polecenia Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

**Get-ImageStoreConnectionStringFromClusterManifest** polecenia cmdlet, która jest częścią tego modułu programu PowerShell zestawu SDK sieci szkieletowej usług, jest używany do pobierania obrazu parametry połączenia magazynu.  Aby zaimportować moduł zestawu SDK, uruchom polecenie:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Element ImageStoreConnectionString znajduje się w manifeście klastra:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zobacz [zrozumieć parametry połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) o dodatkowe informacje dotyczące magazynu obrazów i obrazu przechowywanie parametrów połączenia.

### <a name="deploy-large-application-package"></a>Wdrażanie pakietu dużych aplikacji
Problem: [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) upłynie limit czasu dla pakietu aplikacji duże (kolejność GB).
Wypróbuj:
- Określ większego limitu czasu dla [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) polecenie z `TimeoutSec` parametru. Domyślny limit czasu wynosi 30 minut.
- Sprawdź połączenie sieciowe między maszyną źródłową a klastra. Jeśli połączenie jest powolne, rozważ użycie maszynę z lepszą połączenia sieciowego.
Jeśli komputer kliencki znajduje się w regionie innym niż klaster, należy rozważyć użycie komputerze klienckim w bliżej lub tego samego regionu co klaster.
- Sprawdź, czy osiągnięto ograniczania zewnętrznych. Na przykład gdy składnik image store jest skonfigurowany do używania usługi azure storage, można ograniczenie przekazywania.

Problem: Pakiet przekazywania została ukończona pomyślnie, ale [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) upłynie limit czasu. Wypróbuj:
- [Kompresuj pakietu](service-fabric-package-apps.md#compress-a-package) przed rozpoczęciem kopiowania do magazynu obrazów.
Kompresja zmniejsza rozmiar i wykonaj to liczba plików, co z kolei zmniejsza ilość ruchu sieciowego i działają w tej sieci szkieletowej usług. Operacja przekazywania może przebiegać wolniej, (zwłaszcza, Jeśli dołączysz podczas kompresji), ale rejestrowania i wyrejestrowania typ aplikacji są szybsze.
- Określ większego limitu czasu dla [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z `TimeoutSec` parametru.
- Określ `Async` przełączać [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca, gdy polecenie akceptuje klastra i rejestracji typu aplikacji asynchronicznie. Z tego powodu jest niepotrzebna, aby określić w tym przypadku wyższe przekroczenia limitu czasu. [Get ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) polecenie wyświetla listę wszystkich wersji typu pomyślnie zarejestrowano aplikacji i ich stan rejestracji. To polecenie służy do określania, kiedy odbywa się rejestracji.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Wdrażanie pakietu aplikacji z wielu plików
Problem: [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) limitu czasu dla pakietu aplikacji z wielu plików (kolejność tysięcy).
Wypróbuj:
- [Kompresuj pakietu](service-fabric-package-apps.md#compress-a-package) przed rozpoczęciem kopiowania do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większego limitu czasu dla [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z `TimeoutSec` parametru.
- Określ `Async` przełączać [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca, gdy polecenie akceptuje klastra i rejestracji typu aplikacji asynchronicznie.
Z tego powodu jest niepotrzebna, aby określić w tym przypadku wyższe przekroczenia limitu czasu. [Get ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) polecenie wyświetla listę wszystkich wersji typu pomyślnie zarejestrowano aplikacji i ich stan rejestracji. To polecenie służy do określania, kiedy odbywa się rejestracji.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie pakietu aplikacji](service-fabric-package-apps.md)

[Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

[Wprowadzenie kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą sieci szkieletowej usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Model aplikacji w sieci szkieletowej usług](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
