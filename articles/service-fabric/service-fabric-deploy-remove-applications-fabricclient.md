---
title: Wdrażanie aplikacji w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Przy użyciu interfejsów API FabricClient wdrażać i usuwać aplikacje w usłudze Service Fabric.
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
ms.openlocfilehash: 4b2d88004696515169ffde96b50d2771bcc1a669
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428133"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Wdrażanie i usunąć aplikacje przy użyciu FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Program PowerShell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Gdy [typu aplikacja została spakowana][10], jest gotowa do wdrożenia w klastrze usługi Azure Service Fabric. Wdrożenie obejmuje następujące trzy kroki:

1. Przekazywanie pakietu aplikacji do magazynu obrazów
2. Zarejestrować typ aplikacji
3. Usuń pakiet aplikacji z magazynu obrazów
4. Tworzenie instancji aplikacji

Po wdrażanie aplikacji i wystąpienie można uruchomić w klastrze, możesz usunąć wystąpienie aplikacji i typu aplikacji. Całkowicie usunąć aplikację z klastra, wykonując następujące czynności:

1. Usuń (lub usuwać) uruchamianie wystąpienia aplikacji
2. Wyrejestrować typ aplikacji, jeśli nie są już potrzebne

Jeśli używasz programu Visual Studio umożliwiające wdrażanie i debugowanie aplikacji na lokalnego klastra projektowego powyższych kroków są obsługiwane automatycznie za pomocą skryptu programu PowerShell.  Ten skrypt znajduje się w *skrypty* folderu projektu aplikacji. W tym artykule podano ogólne informacje na ten skrypt czynności, aby zrobić tych samych operacji poza programem Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Łączenie z klastrem
Łączenie z klastrem, tworząc [FabricClient](/dotnet/api/system.fabric.fabricclient) wystąpienia przed uruchomieniem dowolnych z przykładów kodu w tym artykule. Przykłady łączenia na lokalny klaster projektowy zdalnego klastra lub klastra zabezpieczone przy użyciu usługi Azure Active Directory, X509 certyfikatów lub usługi Windows Active Directory, zobacz [nawiązywanie połączenia z zabezpieczonym klastrem](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Aby połączyć z lokalnego klastra projektowego, uruchom poniższy przykład:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Przekazywanie pakietu aplikacji
Załóżmy, że zostanie utworzona i tworzenie pakietu aplikacji o nazwie *MyApplication* w programie Visual Studio. Domyślnie nazwa typu aplikacji, które są wymienione w ApplicationManifest.xml jest "MyApplicationType".  Pakiet aplikacji, który zawiera manifest aplikacji konieczne manifesty usługi i pakietów kodu/config/danych, znajduje się w *C:\Users\&lt; nazwa_użytkownika&gt;\Documents\Visual Studio 2019\Projects\ MyApplication\MyApplication\pkg\Debug*.

Przekazywanie pakietu aplikacji umieszczane w lokalizacji, który jest dostępny dla wewnętrznych składników usługi Service Fabric. Usługa Service Fabric sprawdza pakiet aplikacji, podczas rejestracji pakietu aplikacji. Jednak jeśli chcesz zweryfikować lokalnie za pomocą pakietu aplikacji (czyli przed przekazaniem), użyj [ServiceFabricApplicationPackage testu](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) polecenia cmdlet.

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) interfejs API przekazuje pakiet aplikacji do magazynu obrazów klastra. 

Jeśli pakiet aplikacji jest duży i/lub ma wiele plików, możesz to zrobić [skompresować je](service-fabric-package-apps.md#compress-a-package) i skopiować go do magazynu obrazów przy użyciu programu PowerShell. Kompresja zmniejsza rozmiar i liczba plików.

Zobacz [zrozumieć parametry połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) dodatkowych informacji na temat magazynu obrazów i obrazów można przechowywać w parametrach połączenia.

## <a name="register-the-application-package"></a>Zarejestruj pakiet aplikacji
Typ i wersja aplikacji zadeklarowane w manifeście aplikacji stają się dostępne do użycia po zarejestrowaniu pakietu aplikacji. System odczytuje pakiet, który został przekazany w poprzednim kroku, sprawdza pakiet, przetwarza zawartość pakietu i kopiuje przetwarzania pakietu do lokalizacji systemu wewnętrznego.  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) rejestrów interfejsu API, aplikacji typu w klastrze i udostępnić go do wdrożenia.

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API zawiera informacje o wszystkich typów pomyślnie zarejestrowana aplikacja. Aby określić, po zakończeniu rejestracji, można użyć tego interfejsu API.

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji z magazynu obrazu
Zalecane jest, usuń pakiet aplikacji, po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji w sklepie obraz zwolnienie zasobów systemowych.  Utrzymywanie pakiety aplikacji nieużywane wykorzystuje Magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji. Usuń pakiet aplikacji z magazynu obrazów przy użyciu [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) interfejsu API.

## <a name="create-an-application-instance"></a>Utwórz wystąpienie aplikacji
Można utworzyć wystąpienie aplikacji z aplikacji dowolnego typu, który został pomyślnie zarejestrowany za pomocą [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) interfejsu API. Nazwy poszczególnych aplikacji musi rozpoczynać się *"Service fabric:"* schemat i muszą być unikatowe dla każdego wystąpienia aplikacji (w ramach klastra). Domyślne zdefiniowanych żadnych usług w manifeście aplikacji typ aplikacji docelowej są również tworzone.

Wiele wystąpień aplikacji mogą być tworzone dla dowolnej wersji danego typu zarejestrowanej aplikacji. Każde wystąpienie aplikacji działa w izolacji z katalogu roboczego i zestaw procesów.

Aby zobaczyć, który o nazwie aplikacje i usługi są uruchomione w klastrze, uruchom [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) i [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) interfejsów API.

## <a name="create-a-service-instance"></a>Tworzenie wystąpienia usługi
Można utworzyć wystąpienie usługi z typem usługi przy użyciu [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) interfejsu API.  Jeśli usługa jest zadeklarowany jako domyślnej usługi w manifeście aplikacji, usługa zostanie uruchomiony, jeśli aplikacja zostanie uruchomiony.  Wywoływanie [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) interfejs API usługi, który jest już uruchomiony zwróci wyjątek typu FabricException. Wyjątek będzie zawierać kod błędu z wartością FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Usuń wystąpienie usługi
Gdy wystąpienie usługi nie jest już potrzebny, możesz go usunąć, uruchamianie wystąpienia aplikacji, wywołując [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) interfejsu API.  

> [!WARNING]
> Nie można cofnąć tej operacji i nie można odzyskać stan usługi.

## <a name="remove-an-application-instance"></a>Usuń wystąpienie aplikacji
Gdy wystąpienie aplikacji nie jest już potrzebny, można trwale usunąć ją za pomocą nazwy [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) interfejsu API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatycznie usuwa wszystkie usługi, które należą do aplikacji oraz, trwałe usunięcie wszystkich stanów usługi.

> [!WARNING]
> Nie można cofnąć tej operacji i nie można odzyskać stan aplikacji.

## <a name="unregister-an-application-type"></a>Wyrejestrowanie typu aplikacji
Gdy określoną wersję typu aplikacji nie jest już potrzebny, należy wyrejestrować tej konkretnej wersji typu aplikacji przy użyciu [ServiceFabricApplicationType Wyrejestruj](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) interfejsu API. Wyrejestrowywanie nieużywane wersje aplikacji typy zwalnia miejsca do magazynowania korzystają z magazynu obrazów. Można wyrejestrować wersję typu aplikacji, tak długo, jak aplikacje nie są tworzone dla danej wersji typu aplikacji. Typ aplikacji mogą mieć również, żadna aplikacja oczekujących uaktualnień odwołują się do tej wersji typu aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage asks for an ImageStoreConnectionString
Środowisko usługi Service Fabric SDK powinno mieć już poprawne ustawienia domyślne, skonfiguruj. Ale jeśli to konieczne, ImageStoreConnectionString dla wszystkich poleceń powinna być zgodna wartość, która używa klastra usługi Service Fabric. W manifeście klastra można znaleźć ImageStoreConnectionString pobrany przy użyciu [Get ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) i polecenia Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
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
Problem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API upłynie limit czasu dla dużych aplikacji pakietu (kolejność GB).
Wypróbuj:
- Określ większego limitu czasu dla [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metoda, za pomocą `timeout` parametru. Domyślnie limit czasu to 30 minut.
- Sprawdź połączenie sieciowe między maszyną źródłową i klastra. Jeśli połączenie jest powolne, rozważ użycie na maszynie z połączeniem sieciowym lepiej.
Jeśli komputer kliencki znajduje się w regionie innym niż klaster, należy wziąć pod uwagę przy użyciu komputerze klienckim w regionie bliżej lub tym samym co klaster.
- Sprawdź, czy osiągasz ograniczania zewnętrznych. Na przykład gdy Kreator obrazów jest skonfigurowany do używania usługi azure storage, może być ograniczenie przekazywania.

Problem: Przekazywanie pakietu zostało ukończone pomyślnie, ale [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) upłynie limit czasu interfejsu API. Wypróbuj:
- [Kompresowanie pakietu](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem ich do magazynu obrazów.
Kompresja zmniejsza rozmiar i liczba plików, który z kolei ogranicza ilość ruchu sieciowego i pracować z tej usługi Service Fabric, należy wykonać. Operacja przekazywania może być niższa, (zwłaszcza, Jeśli dołączysz podczas kompresji), ale rejestrowanie i wyrejestrowywanie aplikacji typu są realizowane szybciej.
- Określ większego limitu czasu dla [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) interfejsu API za pomocą `timeout` parametru.

### <a name="deploy-application-package-with-many-files"></a>Wdrażanie pakietu aplikacji przy użyciu wielu plików
Problem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) upłynie limit czasu dla pakietu aplikacji zawierających wiele plików (kolejność tysięcy).
Wypróbuj:
- [Kompresowanie pakietu](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem ich do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większego limitu czasu dla [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) z `timeout` parametru.

## <a name="code-example"></a>Przykładowy kod
Poniższy przykład kopiuje pakietu aplikacji do magazynu obrazów i obsługuje typ aplikacji. Następnie przykład tworzy wystąpienie aplikacji i tworzy wystąpienie usługi. Na koniec przykładzie spowoduje usunięcie wystąpienia aplikacji, Wstrzymuje obsługę administracyjną typ aplikacji i usuwa pakiet aplikacji z magazynu obrazów.

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

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md)

[Wprowadzenie kondycji usługi Service Fabric](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelowanie aplikacji w usłudze Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
