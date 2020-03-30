---
title: Konteneryzowanie usług sieci szkieletowej usług Azure w systemie Windows
description: Dowiedz się, jak konteneryzować usługi niezawodne usługi sieci szkieletowej usług i usługi Reliable Actors w systemie Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 9fe5980c13f655f8f30cc42771971a5015460420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466187"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Konteneryzowanie usług Reliable Services i Reliable Actors usługi Service Fabric w systemie Windows

Usługa Sieci szkieletowej obsługuje konteneryzacji mikrousług sieci szkieletowej usług (niezawodne usługi i usługi oparte na niezawodnym aktora). Aby uzyskać więcej informacji, zobacz [kontenery sieci szkieletowej usługi](service-fabric-containers-overview.md).

Ten dokument zawiera wskazówki, aby uzyskać usługi uruchomione wewnątrz kontenera systemu Windows.

> [!NOTE]
> Obecnie ta funkcja działa tylko w systemie Windows. Aby uruchomić kontenery, klaster musi być uruchomiony w systemie Windows Server 2016 z kontenerami.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Kroki konteneryzacji aplikacji sieci szkieletowej usług

1. Otwórz aplikację sieci szkieletowej usług w programie Visual Studio.

2. Dodaj [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) klasy do projektu. Kod w tej klasie jest pomocnikiem, aby poprawnie załadować pliki binarne środowiska uruchomieniowego sieci szkieletowej usługi wewnątrz aplikacji podczas uruchamiania wewnątrz kontenera.

3. Dla każdego pakietu kodu, który chcesz konteneryzować, zaiwarować moduł ładujący w punkcie wejścia programu. Dodaj konstruktora statycznego pokazanego we wpisie kodu do pliku punktu wejścia programu.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Tworzenie i [pakowanie](service-fabric-package-apps.md#Package-App) projektu. Aby utworzyć i utworzyć pakiet, kliknij prawym przyciskiem myszy projekt aplikacji w Eksploratorze rozwiązań i wybierz polecenie **Pakiet.**

5. Dla każdego pakietu kodu, który należy konteneryzować, uruchom skrypt programu PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Użycie jest następujące:

    Pełna wartość .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Skrypt tworzy folder z artefaktami platformy Docker w $dockerPackageOutputDirectoryPath. Zmodyfikuj wygenerowany plik dockerfile do `expose` dowolnych portów, uruchom skrypty konfiguracji i tak dalej. w zależności od potrzeb.

6. Następnie należy [skompilować](service-fabric-get-started-containers.md#Build-Containers) i [wypchnąć](service-fabric-get-started-containers.md#Push-Containers) pakiet kontenera platformy Docker do repozytorium.

7. Zmodyfikuj plik ApplicationManifest.xml i ServiceManifest.xml, aby dodać obraz kontenera, informacje o repozytorium, uwierzytelnianie rejestru i mapowanie między portami. Aby zmodyfikować manifesty, zobacz [Tworzenie aplikacji kontenera usługi Azure Service.](service-fabric-get-started-containers.md) Definicja pakietu kodu w manifeście usługi musi zostać zastąpiona odpowiednim obrazem kontenera. Upewnij się, aby zmienić EntryPoint na Typ ContainerHost.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Dodaj mapowanie port-host dla punktu końcowego replikatora i usługi. Ponieważ oba te porty są przypisane w czasie wykonywania przez sieci szkieletowej usług, ContainerPort jest ustawiona na zero, aby użyć przypisanego portu do mapowania.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Aby skonfigurować tryb izolacji kontenera, zobacz [Konfigurowanie trybu izolacji]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). System Windows obsługuje dwa tryby izolacji dla kontenerów: tryb procesu oraz tryb funkcji Hyper-V. Poniższe fragmenty kodu pokazują, jak tryb izolacji jest określony w pliku manifestu aplikacji.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Domyślnie aplikacje sieci szkieletowej usług mają dostęp do środowiska uruchomieniowego sieci szkieletowej usług w postaci punktu końcowego akceptującego żądania specyficzne dla aplikacji. Należy rozważyć wyłączenie tego dostępu, gdy aplikacja obsługuje niezaufany kod. Aby uzyskać więcej informacji, zobacz [najważniejsze wskazówki dotyczące zabezpieczeń w sieci szkieletowej usług](service-fabric-best-practices-security.md#platform-isolation). Aby wyłączyć dostęp do środowiska uruchomieniowego sieci szkieletowej usług, dodaj następujące ustawienie w sekcji Zasady manifestu aplikacji odpowiadającego manifestowi importowanej usługi w następujący sposób:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Aby przetestować tę aplikację, należy wdrożyć ją w klastrze z systemem w wersji 5.7 lub nowszej. W przypadku środowiska wykonawczego w wersji 6.1 lub niższej należy edytować i aktualizować ustawienia klastra, aby włączyć tę funkcję podglądu. Wykonaj czynności opisane w tym [artykule,](service-fabric-cluster-fabric-settings.md) aby dodać ustawienie pokazane dalej.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Następnie [wdrożyć](service-fabric-deploy-remove-applications.md) edytowany pakiet aplikacji do tego klastra.

Teraz powinna być konteneryzowana aplikacja sieci szkieletowej usług z uruchomionym klastrem.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-get-started-containers.md).
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.
