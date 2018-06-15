---
title: Wdrażanie aplikacji sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Przy użyciu interfejsów API klienta fabricclient z rolą wdrażania i usunąć aplikacje w sieci szkieletowej usług.
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
ms.openlocfilehash: 50c487e6bad2a009b4f719d44b129ba860432e28
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207472"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Wdrażanie i usunąć aplikacje przy użyciu klienta fabricclient z rolą
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Program PowerShell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Raz [typu aplikacji zostały opakowane][10], jest ono gotowe do wdrożenia do klastra usługi sieć szkieletowa usług Azure. Wdrożenie obejmuje następujące trzy kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów
2. Rejestracja typu aplikacji
3. Usuwanie pakietu aplikacji w magazynie obrazów
4. Tworzenie wystąpienia aplikacji

Po wdrożeniu aplikacji i wystąpienie jest uruchomione w klastrze, można usunąć wystąpienia aplikacji i jej typu aplikacji. Aby całkowicie usunąć aplikację z klastra obejmuje następujące kroki:

1. Usuń (lub usunąć) uruchomione wystąpienie aplikacji
2. Wyrejestrowywanie typu aplikacji, jeśli nie są już potrzebne

Jeśli używasz programu Visual Studio umożliwiające wdrażanie i debugowanie aplikacji w klastrze lokalnym programowanie powyższych kroków obsługi automatycznie za pomocą skryptu programu PowerShell.  Skrypt ten znajduje się w *skryptów* folderu projektu aplikacji. Ten artykuł zawiera tła na tego skryptu czynności, aby można wykonać operacji poza Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Łączenie z klastrem
Połącz się z klastrem, tworząc [klienta fabricclient z rolą](/dotnet/api/system.fabric.fabricclient) wystąpienia przed uruchomieniem dowolnych przykłady kodu w tym artykule. Przykłady łączenia na lokalny klaster projektowy zdalnego klastra lub klastra zabezpieczone przy użyciu usługi Azure Active Directory, X509 certyfikatów lub usługi Active Directory systemu Windows, zobacz [Połącz z klastrem bezpiecznego](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Aby połączyć się z lokalnego klastra projektowego, uruchom następujące polecenie:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Przekaż pakiet aplikacji
Załóżmy, że kompilacji i pakietu aplikacji o nazwie *MyApplication* w programie Visual Studio. Domyślnie nazwa typu aplikacji, które są wymienione w pliku ApplicationManifest.xml jest "MyApplicationType".  Pakiet aplikacji, który zawiera manifest aplikacji konieczne, manifesty usługi i pakietów kodu/config/danych, znajduje się w *C:\Users\&lt; nazwa użytkownika&gt;\Documents\Visual 2017\Projects\ w Studio MyApplication\MyApplication\pkg\Debug*.

Przekazywanie pakietu aplikacji umieszcza je w lokalizacji, który jest dostępny dla wewnętrznych składników usługi Service Fabric. Sieć szkieletowa usług sprawdza, czy pakiet aplikacji podczas rejestracji pakietu aplikacji. Jeśli chcesz sprawdzić lokalnie pakietu aplikacji (tj. przed przekazaniem), jednak użyć [ServiceFabricApplicationPackage testu](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) polecenia cmdlet.

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) interfejsu API zostanie przesłany pakiet aplikacji do magazynu obrazu klastra. 

Jeśli pakiet aplikacji jest duży i/lub ma wiele plików, możesz [skompresować je](service-fabric-package-apps.md#compress-a-package) i skopiować go do magazynu obrazów przy użyciu programu PowerShell. Kompresja zmniejsza rozmiar i liczby plików.

Zobacz [zrozumieć parametry połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) o dodatkowe informacje dotyczące magazynu obrazów i obrazu przechowywanie parametrów połączenia.

## <a name="register-the-application-package"></a>Zarejestruj pakiet aplikacji
Typ aplikacji i wersji zadeklarowane w manifeście aplikacji staną się dostępne po zarejestrowaniu pakietu aplikacji. System odczytuje pakietów przekazano w poprzednim kroku, sprawdza, czy pakiet przetwarza zawartości pakietu i kopiuje przetworzonych pakietu do lokalizacji systemu wewnętrznego.  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) rejestrów interfejsu API w aplikacji wpisz w klastrze i udostępnienie jej do wdrożenia.

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) interfejsu API zawiera informacje o wszystkich typów aplikacji pomyślnie zarejestrowany. Ten interfejs API służy do określania, kiedy odbywa się rejestracji.

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji w magazynie obrazów
Zalecane jest, usuń pakiet aplikacji, po pomyślnym zarejestrowaniu aplikacji.  Usunięcie pakietów aplikacji w sklepie obrazu zwolni zasoby systemowe.  Utrzymywanie pakietów aplikacji nieużywane korzysta z magazynu danych na dysku i prowadzi do problemów z wydajnością aplikacji. Usuwanie pakietu aplikacji z magazynu obrazu przy użyciu [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) interfejsu API.

## <a name="create-an-application-instance"></a>Utwórz wystąpienie aplikacji
Można utworzyć wystąpienia aplikacji z dowolnego typu aplikacji, który został pomyślnie zarejestrowany za pomocą [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) interfejsu API. Nazwy poszczególnych aplikacji musi rozpoczynać się od *"fabric:"* schemat i muszą być unikatowe dla każdego wystąpienia aplikacji (w ramach klastra). Wszystkie usługi domyślne zdefiniowane w manifeście aplikacji typu aplikacji docelowej są również tworzone.

Dla dowolnej wersji danego typu zarejestrowanej aplikacji można tworzyć wiele wystąpień aplikacji. Każde wystąpienie aplikacji jest uruchamiany w izolacji z katalogu roboczego i zestaw procesów.

Aby zobaczyć, które o nazwie aplikacje i usługi są uruchomione w klastrze, uruchom [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) i [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) interfejsów API.

## <a name="create-a-service-instance"></a>Utwórz wystąpienie usługi
Można utworzyć wystąpienia usługi z typem usługi za pomocą [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) interfejsu API.  Jeśli usługa jest zadeklarowany jako domyślna usługa w manifeście aplikacji, usługa zostanie uruchomiony, gdy aplikacja zostanie uruchomiony.  Wywoływanie [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) interfejsu API usługi, który już jest utworzone zwróci wyjątek typu FabricException zawierający kod błędu z wartością FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Usuń wystąpienia usługi
Wystąpienie usługi nie jest już potrzebne, możesz usunąć go z uruchomionym wystąpienia aplikacji przez wywołanie metody [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) interfejsu API.  

> [!WARNING]
> Ta operacja jest nieodwracalna i nie można odzyskać stan usługi.

## <a name="remove-an-application-instance"></a>Usuwanie wystąpienia aplikacji
Wystąpienie aplikacji nie jest już potrzebne, możesz trwale usunąć go przy użyciu nazwy [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) interfejsu API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatycznie usuwa wszystkie usługi należące do aplikacji, jak również i stałe usunięcie wszystkich stan usługi.

> [!WARNING]
> Ta operacja jest nieodwracalna i nie można odzyskać stan aplikacji.

## <a name="unregister-an-application-type"></a>Wyrejestrowywanie typu aplikacji
W przypadku konkretnej wersji typu aplikacji nie jest potrzebna, należy wyrejestrować tej konkretnej wersji typu aplikacji przy użyciu [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) interfejsu API. Wyrejestrowywanie nieużywane wersje aplikacji typów zwalnia miejsca używanego przez składnik image store. Wersja typu aplikacji można wyrejestrować tak długo, jak wystąpienia żadne aplikacje nie są tworzone przy użyciu wersji tego typu aplikacji i żadnych uaktualnień aplikacji oczekujących odwołują się do tej wersji typu aplikacji.

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
Problem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) interfejsu API upłynie limit czasu dla pakietu aplikacji duże (kolejność GB).
Wypróbuj:
- Określ większego limitu czasu dla [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metody z `timeout` parametru. Domyślny limit czasu wynosi 30 minut.
- Sprawdź połączenie sieciowe między maszyną źródłową a klastra. Jeśli połączenie jest powolne, rozważ użycie maszynę z lepszą połączenia sieciowego.
Jeśli komputer kliencki znajduje się w regionie innym niż klaster, należy rozważyć użycie komputerze klienckim w bliżej lub tego samego regionu co klaster.
- Sprawdź, czy osiągnięto ograniczania zewnętrznych. Na przykład gdy składnik image store jest skonfigurowany do używania usługi azure storage, można ograniczenie przekazywania.

Problem: Pakiet przekazywania została ukończona pomyślnie, ale [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) limitu czasu interfejsu API. Wypróbuj:
- [Kompresuj pakietu](service-fabric-package-apps.md#compress-a-package) przed rozpoczęciem kopiowania do magazynu obrazów.
Kompresja zmniejsza rozmiar i wykonaj to liczba plików, co z kolei zmniejsza ilość ruchu sieciowego i działają w tej sieci szkieletowej usług. Operacja przekazywania może przebiegać wolniej, (zwłaszcza, Jeśli dołączysz podczas kompresji), ale rejestrowania i wyrejestrowania typ aplikacji są szybsze.
- Określ większego limitu czasu dla [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) interfejsu API za pomocą `timeout` parametru.

### <a name="deploy-application-package-with-many-files"></a>Wdrażanie pakietu aplikacji z wielu plików
Problem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) limitu czasu dla pakietu aplikacji z wielu plików (kolejność tysięcy).
Wypróbuj:
- [Kompresuj pakietu](service-fabric-package-apps.md#compress-a-package) przed rozpoczęciem kopiowania do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większego limitu czasu dla [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) z `timeout` parametru.

## <a name="code-example"></a>Przykładowy kod
Poniższy przykład kopiuje pakietu aplikacji do magazynu obrazów, udostępnia typ aplikacji, tworzy wystąpienie aplikacji, tworzy wystąpienie usługi, usuwa wystąpienie aplikacji, wyrejestrowywania przepisy typ aplikacji i usuwa pakiet aplikacji w sklepie obrazu.

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
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
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

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

[Wprowadzenie kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą sieci szkieletowej usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Model aplikacji w sieci szkieletowej usług](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
