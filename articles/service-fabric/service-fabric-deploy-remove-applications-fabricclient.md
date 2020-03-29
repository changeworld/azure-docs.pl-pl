---
title: Wdrażanie sieci szkieletowej usług Azure za pomocą fabricclient
description: Użyj interfejsów API FabricClient do wdrażania i usuwania aplikacji w sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 25b874d1be8ab50d8076ff8fe9423c8cc0187512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376974"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Wdrażanie i usuwanie aplikacji przy użyciu aplikacji FabricClient
> [!div class="op_single_selector"]
> * [Menedżer zasobów](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Po [spakowaniu typu aplikacji][10]jest gotowy do wdrożenia w klastrze sieci szkieletowej usług Azure. Wdrożenie obejmuje następujące trzy kroki:

1. Prześlij pakiet aplikacji do magazynu obrazów
2. Zarejestruj typ aplikacji
3. Usuwanie pakietu aplikacji ze sklepu z obrazami
4. Tworzenie wystąpienia aplikacji

Po wdrożeniu aplikacji i uruchomieniu wystąpienia w klastrze można usunąć wystąpienie aplikacji i jej typ aplikacji. Całkowicie usuń aplikację z klastra, wykonując następujące kroki:

1. Usuwanie (lub usuwanie) uruchomionego wystąpienia aplikacji
2. Wyrejestruj typ aplikacji, jeśli nie jest już potrzebny

Jeśli używasz programu Visual Studio do wdrażania i debugowania aplikacji w lokalnym klastrze rozwoju, wszystkie poprzednie kroki są obsługiwane automatycznie za pośrednictwem skryptu programu PowerShell.  Ten skrypt znajduje się w folderze *Skrypty* projektu aplikacji. Ten artykuł zawiera tło dotyczące tego skryptu, dzięki czemu można wykonać te same operacje poza programem Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Łączenie z klastrem
Połącz się z klastrem, tworząc [wystąpienie FabricClient](/dotnet/api/system.fabric.fabricclient) przed uruchomieniem któregokolwiek z przykładów kodu w tym artykule. Przykłady łączenia się z lokalnym klastrem deweloperów lub zdalnym klastrem lub klastrem zabezpieczonym przy użyciu usługi Azure Active Directory, certyfikatów X509 lub usługi Windows Active Directory zobacz [Łączenie się z bezpiecznym klastrem](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Aby połączyć się z lokalnym klastrem programistycznym, uruchom następujący przykład:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Prześlij pakiet aplikacji
Załóżmy, że tworzysz i pakujesz aplikację o nazwie *MyApplication* w programie Visual Studio. Domyślnie nazwa typu aplikacji wymieniona w pliku ApplicationManifest.xml to "MyApplicationType".  Pakiet aplikacji, który zawiera niezbędny manifest aplikacji, manifesty usług i pakiety kodu/konfiguracji/danych, znajduje się w *folderze C:\Użytkownicy\&lt;nazwa _nazwa&gt;\Dokumenty\Visual Studio 2019\Projekty\MyApplication\MyApplication\pkg\Debug*.

Przekazywanie pakietu aplikacji umieszcza go w lokalizacji, która jest dostępna dla wewnętrznych składników sieci szkieletowej usług. Usługa sieci szkieletowej weryfikuje pakiet aplikacji podczas rejestracji pakietu aplikacji. Jeśli jednak chcesz zweryfikować pakiet aplikacji lokalnie (czyli przed przekazaniem), użyj polecenia cmdlet [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

Interfejs API [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) przekazuje pakiet aplikacji do magazynu obrazów klastra. 

Jeśli pakiet aplikacji jest duży i/lub zawiera wiele plików, można [go skompresować](service-fabric-package-apps.md#compress-a-package) i skopiować do magazynu obrazów za pomocą programu PowerShell. Kompresja zmniejsza rozmiar i liczbę plików.

Zobacz [Opis ciągu połączenia magazynu obrazów, aby](service-fabric-image-store-connection-string.md) uzyskać dodatkowe informacje o ciągu połączenia magazynu obrazów i magazynu obrazów.

## <a name="register-the-application-package"></a>Zarejestruj pakiet zgłoszeniowy
Typ aplikacji i wersja zadeklarowana w manifeście aplikacji stają się dostępne do użycia, gdy pakiet aplikacji jest zarejestrowany. System odczytuje pakiet przekazany w poprzednim kroku, weryfikuje pakiet, przetwarza zawartość pakietu i kopiuje przetworzony pakiet do wewnętrznej lokalizacji systemu.  

Interfejs API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) rejestruje typ aplikacji w klastrze i udostępnia go do wdrożenia.

Interfejs API [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) zawiera informacje o wszystkich pomyślnie zarejestrowanych typach aplikacji. Za pomocą tego interfejsu API można określić, kiedy rejestracja jest wykonywana.

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji ze sklepu z obrazami
Zaleca się usunięcie pakietu aplikacji po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe.  Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dysku i prowadzi do problemów z wydajnością aplikacji. Usuń pakiet aplikacji ze sklepu z obrazami za pomocą interfejsu [API RemoveApplicationPackage.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage)

## <a name="create-an-application-instance"></a>Tworzenie wystąpienia aplikacji
Można utworzyć wystąpienia aplikacji z dowolnego typu aplikacji, który został pomyślnie zarejestrowany przy użyciu [createApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) INTERFEJSU API. Nazwa każdej aplikacji musi zaczynać się od schematu *"fabric:"* i musi być unikatowa dla każdego wystąpienia aplikacji (w klastrze). Wszystkie usługi domyślne zdefiniowane w manifeście aplikacji docelowego typu aplikacji są również tworzone.

Wiele wystąpień aplikacji można utworzyć dla dowolnej wersji zarejestrowanego typu aplikacji. Każde wystąpienie aplikacji jest uruchamiane w izolacji, z własnym katalogiem roboczym i zestawem procesów.

Aby zobaczyć, które nazwane aplikacje i usługi są uruchomione w klastrze, uruchom [interfejsy API GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) i [GetServiceListAsync.](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)

## <a name="create-a-service-instance"></a>Tworzenie wystąpienia usługi
Można utworzyć wystąpienia usługi z typu usługi przy użyciu interfejsu API [CreateServiceAsync.](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync)  Jeśli usługa jest zadeklarowana jako usługa domyślna w manifeście aplikacji, usługa jest tworzone podczas tworzenia wystąpienia aplikacji.  Wywołanie [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API dla usługi, która jest już utworzona zwróci wyjątek typu FabricException. Wyjątek będzie zawierać kod błędu o wartości FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Usuwanie wystąpienia usługi
Gdy wystąpienie usługi nie jest już potrzebne, można go usunąć z uruchomionego wystąpienia aplikacji, wywołując interfejs API [DeleteServiceAsync.](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync)  

> [!WARNING]
> Tej operacji nie można cofnąć i nie można odzyskać stanu usługi.

## <a name="remove-an-application-instance"></a>Usuwanie wystąpienia aplikacji
Gdy wystąpienie aplikacji nie jest już potrzebne, można trwale usunąć go według nazwy przy użyciu interfejsu API [DeleteApplicationAsync.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatycznie usuwa wszystkie usługi, które należą do aplikacji, jak również, trwale usuwając wszystkie stany usługi.

> [!WARNING]
> Tej operacji nie można cofnąć i nie można odzyskać stanu aplikacji.

## <a name="unregister-an-application-type"></a>Wyrejestrowywać typ aplikacji
Gdy określona wersja typu aplikacji nie jest już potrzebna, należy wyrejestrować tę określoną wersję typu aplikacji przy użyciu interfejsu API [Unregister-ServiceFabricApplicationType.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) Wyrejestrowanie nieużywanych wersji typów aplikacji zwalnia miejsce do magazynowania używane przez magazyn obrazów. Wersja typu aplikacji może być wyrejestrowana, o ile nie zostanie szebrana żadna aplikacja dla tej wersji typu aplikacji. Ponadto typ aplikacji nie może mieć żadnych oczekujących uaktualnień aplikacji są odwoływanie się do tej wersji typu aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage prosi o ImageStoreConnectionString
Środowisko zestawu SDK sieci szkieletowej usług powinno mieć już skonfigurowane poprawne ustawienia domyślne. Ale w razie potrzeby ImageStoreConnectionString dla wszystkich poleceń powinny odpowiadać wartości, która używa klastra sieci szkieletowej usług. ImageStoreConnectionString można znaleźć w manifeście klastra, pobranym za pomocą poleceń [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) i Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
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
Problem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API przesunie czas dla dużego pakietu aplikacji (kolejność GB).
Spróbuj:
- Określ większy limit czasu dla [metody CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) z parametrem. `timeout` Domyślnie limit czasu wynosi 30 minut.
- Sprawdź połączenie sieciowe między komputerem źródłowym a klastrem. Jeśli połączenie jest wolne, należy rozważyć użycie komputera z lepszym połączeniem sieciowym.
Jeśli komputer kliencki znajduje się w innym regionie niż klaster, należy rozważyć użycie komputera klienckiego w bliżej lub tym samym regionie co klaster.
- Sprawdź, czy uderzasz w dławienie zewnętrzne. Na przykład, gdy magazyn obrazów jest skonfigurowany do używania usługi Azure Storage, przekazywanie może być ograniczone.

Problem: Pakiet przekazywania został zakończony pomyślnie, ale terminy interfejsu API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) zostały przesunie się. Spróbuj:
- [Skompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów.
Kompresja zmniejsza rozmiar i liczbę plików, co z kolei zmniejsza ilość ruchu i pracy, które sieci szkieletowej usług musi wykonać. Operacja przekazywania może być wolniejsza (zwłaszcza jeśli uwzględnisz czas kompresji), ale zarejestruj i wyrejestruj typ aplikacji są szybsze.
- Określ większy limit czasu dla interfejsu API `timeout` [provisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) z parametrem.

### <a name="deploy-application-package-with-many-files"></a>Wdrażanie pakietu aplikacji z wieloma plikami
Problem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) times out for an application package with many files (order of thousands).
Spróbuj:
- [Skompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większy limit czasu dla [provisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) z parametrem. `timeout`

## <a name="code-example"></a>Przykładowy kod
Poniższy przykład kopiuje pakiet aplikacji do magazynu obrazów i aprowije typ aplikacji. Następnie przykład tworzy wystąpienie aplikacji i tworzy wystąpienie usługi. Na koniec przykład usuwa wystąpienie aplikacji, unprovisions typu aplikacji i usuwa pakiet aplikacji z magazynu obrazów.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Następne kroki
[Uaktualnienie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

[Wprowadzenie kondycji sieci usług](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą sieci szkieletowej usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelowanie aplikacji w sieci szkieletowej usług](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
