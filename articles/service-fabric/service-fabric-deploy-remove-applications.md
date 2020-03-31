---
title: Wdrażanie sieci szkieletowej usług Azure za pomocą programu PowerShell
description: Dowiedz się więcej o usuwaniu i wdrażaniu aplikacji w sieci szkieletowej usług Azure oraz o wykonywaniu tych akcji w programie Powershell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e3fdd194f2949f1246e991968e02b3278f33f7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282512"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Menedżer zasobów](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Po [spakowaniu typu aplikacji][10]jest gotowy do wdrożenia w klastrze sieci szkieletowej usług Azure. Wdrożenie obejmuje następujące trzy kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów.
2. Zarejestruj typ aplikacji ze ścieżką względną magazynu obrazów.
3. Utwórz wystąpienie aplikacji.

Gdy wdrożona aplikacja nie jest już wymagana, można usunąć wystąpienie aplikacji i jej typ aplikacji. Aby całkowicie usunąć aplikację z klastra obejmuje następujące kroki:

1. Usuń (lub usuń) uruchomione wystąpienie aplikacji.
2. Wyrejestruj typ aplikacji, jeśli nie jest już potrzebny.
3. Usuń pakiet aplikacji ze sklepu z obrazami.

Jeśli używasz programu Visual Studio do wdrażania i debugowania aplikacji w lokalnym klastrze rozwoju, wszystkie poprzednie kroki są obsługiwane automatycznie za pośrednictwem skryptu programu PowerShell.  Ten skrypt znajduje się w folderze *Skrypty* projektu aplikacji. Ten artykuł zawiera tło dotyczące tego skryptu, dzięki czemu można wykonywać te same operacje poza programem Visual Studio. 

Innym sposobem wdrożenia aplikacji jest przy użyciu aprowidyzowania zewnętrznego. Pakiet aplikacji można [spakować `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) jako i przekazać do magazynu zewnętrznego. W takim przypadku przekazywanie do magazynu obrazów nie jest potrzebne. Wdrożenie wymaga następujących kroków:

1. Prześlij go `sfpkg` do magazynu zewnętrznego. Magazyn zewnętrzny może być dowolny magazyn, który udostępnia REST http lub https punktu końcowego.
2. Zarejestruj typ aplikacji przy użyciu zewnętrznego identyfikatora URI pobierania i informacji o typie aplikacji.
2. Utwórz wystąpienie aplikacji.

W celu oczyszczenia usuń wystąpienia aplikacji i wyrejestruj typ aplikacji. Ponieważ pakiet nie został skopiowany do magazynu obrazów, nie ma tymczasowej lokalizacji do czyszczenia. Inicjowanie obsługi administracyjnej z magazynu zewnętrznego jest dostępne począwszy od sieci szkieletowej usług w wersji 6.1.

>[!NOTE]
> Visual Studio obecnie nie obsługuje aprowidyzowania zewnętrznego.

 

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Przed uruchomieniem jakichkolwiek poleceń programu PowerShell w tym artykule, zawsze należy rozpocząć przy użyciu [Connect-ServiceFabricCluster,](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) aby połączyć się z klastrem sieci szkieletowej usług. Aby połączyć się z lokalnym klastrem programistycznym, uruchom następujące czynności:

```powershell
Connect-ServiceFabricCluster
```

Przykłady łączenia się z klastrem zdalnym lub klastrem zabezpieczonym przy użyciu usługi Azure Active Directory, certyfikatów X509 lub usługi Windows Active Directory zobacz [Łączenie się z bezpiecznym klastrem](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Prześlij pakiet aplikacji

Przekazywanie pakietu aplikacji umieszcza go w lokalizacji, która jest dostępna dla wewnętrznych składników sieci szkieletowej usług.
Jeśli chcesz zweryfikować pakiet aplikacji lokalnie, użyj polecenia cmdlet [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

Polecenie [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) przekazuje pakiet aplikacji do magazynu obrazów klastra.

Załóżmy, że tworzysz i pakujesz aplikację o nazwie *MyApplication* w programie Visual Studio 2015. Domyślnie nazwa typu aplikacji wymieniona w pliku ApplicationManifest.xml to "MyApplicationType".  Pakiet aplikacji, który zawiera niezbędny manifest aplikacji, manifesty usług i pakiety kodu/konfiguracji/danych, znajduje się w *folderze C:\Użytkownicy\<\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

Następujące polecenie zawiera listę zawartości pakietu aplikacji:

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

Jeśli pakiet aplikacji jest duży i/lub zawiera wiele plików, można [go skompresować](service-fabric-package-apps.md#compress-a-package). Kompresja zmniejsza rozmiar i liczbę plików.
Efektem ubocznym jest to, że rejestrowanie i odsezewanie typu aplikacji jest szybsze. Czas przesyłania może być obecnie wolniejszy, zwłaszcza jeśli uwzględnisz czas na skompresowanie pakietu. 

Aby skompresować pakiet, użyj tego samego polecenia [Copy-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Kompresję można wykonać oddzielnie od `SkipCopy` przekazywania, za pomocą flagi lub razem z operacją przekazywania. Zastosowanie kompresji na skompresowanym pakiecie jest nie-op.
Aby wyłączyć skompresowany pakiet, użyj tego samego polecenia [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z przełącznikiem. `UncompressPackage`

Następujące polecenie cmdlet kompresuje pakiet bez kopiowania go do magazynu obrazów. Pakiet zawiera teraz spakowane `Code` pliki `Config` dla i pakietów. Manifesty aplikacji i usługi nie są spakowane, ponieważ są potrzebne dla wielu operacji wewnętrznych (takich jak udostępnianie pakietów, nazwa typu aplikacji i wyodrębnianie wersji dla niektórych weryfikacji). Zipping manifestów uczyniłby te operacje nieefektywne.

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

W przypadku dużych pakietów aplikacji kompresja wymaga czasu. Aby uzyskać najlepsze wyniki, użyj szybkiego dysku SSD. Czas kompresji i rozmiar skompresowanego pakietu również różnią się w zależności od zawartości pakietu.
Na przykład oto statystyki kompresji dla niektórych pakietów, które pokazują rozmiar pakietu początkowego i skompresowanego z czasem kompresji.

|Rozmiar początkowy (MB)|Liczba plików|Czas kompresji|Rozmiar pakietu skompresowanego (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Po skompresowanie pakietu, można przekazać do jednego lub wielu klastrów sieci szkieletowej usług w razie potrzeby. Mechanizm wdrażania jest taki sam dla pakietów skompresowanych i nieskompresowanych. Skompresowane pakiety są przechowywane jako takie w magazynie obrazów klastra. Pakiety są nieskompresowane w węźle, przed uruchomieniem aplikacji.


Poniższy przykład przekazuje pakiet do magazynu obrazów do folderu o nazwie "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Jeśli nie określisz *parametru -ApplicationPackagePathInImageStore,* pakiet aplikacji zostanie skopiowany do folderu "Debug" w magazynie obrazów.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** automatycznie wykryje odpowiedni ciąg połączenia magazynu obrazów, jeśli sesja programu PowerShell jest połączona z klastrem sieci szkieletowej usług. W przypadku wersji sieci szkieletowej usług starszych niż 5.6 argument **-ImageStoreConnectionString** musi być jawnie podany.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Polecenie cmdlet **Get-ImageStoreConnectionStringFromClusterManifest,** które jest częścią modułu PowerShell sieci szkieletowej usług, służy do uzyskania ciągu połączenia magazynu obrazów.  Aby zaimportować moduł SDK, uruchom:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Zobacz [Opis ciągu połączenia magazynu obrazów, aby](service-fabric-image-store-connection-string.md) uzyskać dodatkowe informacje o ciągu połączenia magazynu obrazów i magazynu obrazów.
>
>
>

Czas potrzebny do przesłania pakietu różni się w zależności od wielu czynników. Niektóre z tych czynników to liczba plików w pakiecie, rozmiar pakietu i rozmiary plików. Szybkość sieci między komputerem źródłowym a klastrem sieci szkieletowej usług również wpływa na czas przekazywania. Domyślny limit czasu dla [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) wynosi 30 minut.
W zależności od opisanych czynników może być wymagane zwiększenie limitu czasu. Jeśli kompresujesz pakiet w wywołaniu kopiowania, należy również wziąć pod uwagę czas kompresji.



## <a name="register-the-application-package"></a>Zarejestruj pakiet zgłoszeniowy

Typ aplikacji i wersja zadeklarowana w manifeście aplikacji stają się dostępne do użycia, gdy pakiet aplikacji jest zarejestrowany. System odczytuje pakiet przekazany w poprzednim kroku, weryfikuje pakiet, przetwarza zawartość pakietu i kopiuje przetworzony pakiet do wewnętrznej lokalizacji systemu.  

Uruchom polecenie cmdlet [Register-ServiceFabricApplicationType,](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) aby zarejestrować typ aplikacji w klastrze i udostępnić go do wdrożenia:

### <a name="register-the-application-package-copied-to-image-store"></a>Zarejestruj pakiet aplikacji skopiowany do magazynu obrazów

Gdy pakiet został wcześniej skopiowany do magazynu obrazów, operacja rejestru określa ścieżkę względną w magazynie obrazów.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" to folder w magazynie obrazów, w którym znajduje się pakiet aplikacji. Typ aplikacji o nazwie "MyApplicationType" i wersji "1.0.0" (oba znajdują się w manifeście aplikacji) jest teraz zarejestrowany w klastrze.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Rejestrowanie pakietu aplikacji skopiowanego do magazynu zewnętrznego

Począwszy od sieci szkieletowej usług w wersji 6.1, aprowizowanie obsługuje pobieranie pakietu z magazynu zewnętrznego. Identyfikator URI pobierania reprezentuje ścieżkę do [ `sfpkg` pakietu aplikacji,](service-fabric-package-apps.md#create-an-sfpkg) z którego można pobrać pakiet aplikacji przy użyciu protokołów HTTP lub HTTPS. Pakiet musi być wcześniej przekazany do tej lokalizacji zewnętrznej. Identyfikator URI musi zezwalać na dostęp do odczytu, aby sieci szkieletowej usług można pobrać plik. Plik `sfpkg` musi mieć rozszerzenie ".sfpkg". Operacja inicjowania powinna zawierać informacje o typie aplikacji, zgodnie z manifestem aplikacji.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Polecenie [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) zwraca tylko wtedy, gdy system pomyślnie zarejestrował pakiet aplikacji. Czas rejestracji zależy od rozmiaru i zawartości pakietu aplikacji. W razie potrzeby parametr **-TimeoutSec** może służyć do dostarczania dłuższego limitu czasu (domyślny limit czasu wynosi 60 sekund).

Jeśli masz duży pakiet aplikacji lub jeśli występują limity czasu, należy użyć **parametru -Async.** Polecenie zwraca, gdy klaster akceptuje polecenie rejestru. Operacja rejestru jest kontynuowana zgodnie z potrzebami.
Polecenie [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) zawiera listę wersji typu aplikacji i ich stanu rejestracji. Za pomocą tego polecenia można określić, kiedy rejestracja jest wykonywana.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji ze sklepu z obrazami

Jeśli pakiet został skopiowany do magazynu obrazów, należy usunąć go z tymczasowej lokalizacji po pomyślnym zarejestrowaniu aplikacji. Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe. Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dysku i prowadzi do problemów z wydajnością aplikacji.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Tworzenie aplikacji

Można utworzyć wystąpienia aplikacji z dowolnej wersji typu aplikacji, która została pomyślnie zarejestrowana przy użyciu polecenia cmdlet [New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) Nazwa każdej aplikacji musi zaczynać się od schematu *"fabric:"* i musi być unikatowa dla każdego wystąpienia aplikacji. Wszystkie usługi domyślne zdefiniowane w manifeście aplikacji docelowego typu aplikacji są również tworzone.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Wiele wystąpień aplikacji można utworzyć dla dowolnej wersji zarejestrowanego typu aplikacji. Każde wystąpienie aplikacji jest uruchamiane w izolacji, z własnym katalogiem roboczym i procesem.

Aby zobaczyć, które nazwane aplikacje i usługi są uruchomione w klastrze, uruchom polecenia cmdlet [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) i [Get-ServiceFabricService:](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps)

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

Gdy wystąpienie aplikacji nie jest już potrzebne, można je trwale usunąć za pomocą nazwy przy użyciu polecenia cmdlet [Remove-ServiceFabricApplication.](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatycznie usuwa wszystkie usługi, które należą do aplikacji, jak również, trwale usuwając cały stan usługi. 

> [!WARNING]
> Tej operacji nie można cofnąć i nie można odzyskać stanu aplikacji.

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

## <a name="unregister-an-application-type"></a>Wyrejestrowywać typ aplikacji

Gdy określona wersja typu aplikacji nie jest już potrzebna, należy wyrejestrować typ aplikacji przy użyciu polecenia cmdlet [Unregister-ServiceFabricApplicationType.](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) Wyrejestrowanie nieużywanych typów aplikacji zwalnia miejsce do magazynowania obrazów przez usunięcie plików typu aplikacji. Wyrejestrowanie typu aplikacji nie powoduje usunięcia pakietu aplikacji skopiowanego do tymczasowej lokalizacji magazynu obrazów, jeśli użyto kopii do magazynu obrazów. Typ aplikacji może być wyrejestrowany, o ile nie ma żadnych aplikacji wystąpienia przeciwko niemu i nie oczekujące uaktualnienia aplikacji są odwoływanie się do niego.

Uruchom [Get-ServiceFabricApplicationType,](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) aby wyświetlić typy aplikacji aktualnie zarejestrowanych w klastrze:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Uruchom [Unregister-ServiceFabricApplicationType,](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) aby wyrejestrować określony typ aplikacji:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage prosi o ImageStoreConnectionString

Środowisko zestawu SDK sieci szkieletowej usług powinno mieć już skonfigurowane poprawne ustawienia domyślne. Ale w razie potrzeby ImageStoreConnectionString dla wszystkich poleceń powinny odpowiadać wartości, która używa klastra sieci szkieletowej usług. ImageStoreConnectionString można znaleźć w manifeście klastra, pobranym za pomocą poleceń [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) i Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Polecenie cmdlet **Get-ImageStoreConnectionStringFromClusterManifest,** które jest częścią modułu PowerShell sieci szkieletowej usług, służy do uzyskania ciągu połączenia magazynu obrazów.  Aby zaimportować moduł SDK, uruchom:

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

Zobacz [Opis ciągu połączenia magazynu obrazów, aby](service-fabric-image-store-connection-string.md) uzyskać dodatkowe informacje o ciągu połączenia magazynu obrazów i magazynu obrazów.

### <a name="deploy-large-application-package"></a>Wdrażanie dużego pakietu aplikacji

Problem: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) przesunie się w czasie dla dużego pakietu aplikacji (kolejność GB).
Spróbuj:
- Określ większy limit czasu dla [polecenia Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z parametrem. `TimeoutSec` Domyślnie limit czasu wynosi 30 minut.
- Sprawdź połączenie sieciowe między komputerem źródłowym a klastrem. Jeśli połączenie jest wolne, należy rozważyć użycie komputera z lepszym połączeniem sieciowym.
Jeśli komputer kliencki znajduje się w innym regionie niż klaster, należy rozważyć użycie komputera klienckiego w bliżej lub tym samym regionie co klaster.
- Sprawdź, czy uderzasz w dławienie zewnętrzne. Na przykład, gdy magazyn obrazów jest skonfigurowany do używania usługi Azure Storage, przekazywanie może być ograniczone.

Problem: Pakiet przekazywania został zakończony pomyślnie, ale przesuń termin [rejestracji usługi UsługiFabricApplicationType.](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) Spróbuj:
- [Skompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów.
Kompresja zmniejsza rozmiar i liczbę plików, co z kolei zmniejsza ilość ruchu i pracy, które sieci szkieletowej usług musi wykonać. Operacja przekazywania może być wolniejsza (zwłaszcza jeśli uwzględnisz czas kompresji), ale zarejestruj i odeślij typ aplikacji są szybsze.
- Określ większy limit czasu dla [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z parametrem. `TimeoutSec`
- Określ `Async` przełącznik dla [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca, gdy klaster akceptuje polecenie, a rejestracja typu aplikacji jest kontynuowana asynchronicznie. Z tego powodu nie ma potrzeby określania wyższego limitu czasu w tym przypadku. Polecenie [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) zawiera listę wszystkich pomyślnie zarejestrowanych wersji typu aplikacji i ich stanu rejestracji. Za pomocą tego polecenia można określić, kiedy rejestracja jest wykonywana.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Wdrażanie pakietu aplikacji z wieloma plikami

Problem: [Zarejestruj się-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) przesunie się czas czasu dla pakietu aplikacji z wieloma plikami (kolejność tysięcy).
Spróbuj:
- [Skompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większy limit czasu dla [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z parametrem. `TimeoutSec`
- Określ `Async` przełącznik dla [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca, gdy klaster akceptuje polecenie, a rejestracja typu aplikacji jest kontynuowana asynchronicznie.
Z tego powodu nie ma potrzeby określania wyższego limitu czasu w tym przypadku. Polecenie [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) zawiera listę wszystkich pomyślnie zarejestrowanych wersji typu aplikacji i ich stanu rejestracji. Za pomocą tego polecenia można określić, kiedy rejestracja jest wykonywana.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Następne kroki

[Tworzenie pakietu aplikacji](service-fabric-package-apps.md)

[Uaktualnienie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

[Wprowadzenie kondycji sieci usług](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą sieci szkieletowej usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelowanie aplikacji w sieci szkieletowej usług](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md