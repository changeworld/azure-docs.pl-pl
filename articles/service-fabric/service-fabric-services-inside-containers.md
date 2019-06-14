---
title: Konteneryzowanie usługi Azure Service Fabric na Windows
description: Dowiedz się, jak konteneryzowanie usług usługi Service Fabric Reliable Services i Reliable Actors w Windows.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: aljo, anmola
ms.openlocfilehash: caed77234646654d151b64d2c80b7231342f6d8c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050474"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Konteneryzowanie Twojego usług usługi Service Fabric Reliable Services i Reliable Actors w Windows

Usługa Service Fabric obsługuje konteneryzowania mikrousług usługi Service Fabric (usług Reliable Services i Reliable Actor na podstawie usług). Aby uzyskać więcej informacji, zobacz [usługi Service fabric, kontenerów](service-fabric-containers-overview.md).

Ten dokument zawiera wskazówki dotyczące usługi uruchomionej w kontenerze Windows.

> [!NOTE]
> Obecnie ta funkcja działa tylko dla Windows. Do uruchamiania kontenerów, klaster musi działać w systemie Windows Server 2016 z kontenerami.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Kroki, aby konteneryzowanie aplikacji usługi Service Fabric

1. Otwórz aplikację usługi Service Fabric w programie Visual Studio.

2. Dodaj klasę [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) do projektu. Kod tej klasy jest pomocnika można poprawnie załadować danych binarnych środowiska uruchomieniowego usługi Service Fabric w Twojej aplikacji podczas uruchamiania w kontenerze.

3. Dla każdego pakietu kodu, który chcesz konteneryzowanie, zainicjować modułu ładującego na wpis programu punktu. Dodaj Konstruktor statyczny pokazano w poniższym fragmencie kodu do pliku punktu wejścia programu.

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

4. Tworzenie i [pakietu](service-fabric-package-apps.md#Package-App) projektu. Aby skompilować i utworzyć pakiet, kliknij prawym przyciskiem myszy projekt aplikacji w Eksploratorze rozwiązań, a następnie wybierz **pakietu** polecenia.

5. Dla każdego pakietu kodu należy konteneryzowanie, uruchom skrypt programu PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Użycie jest następująca:

    Pełny .NET
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
      Skrypt tworzy folder z artefaktami platformy Docker w $dockerPackageOutputDirectoryPath. Wygenerowany plik Dockerfile, aby zmodyfikować `expose` żadnych portów i uruchomić skrypty instalacyjne i tak dalej. zgodnie z potrzebami.

6. Następnie trzeba [kompilacji](service-fabric-get-started-containers.md#Build-Containers) i [wypychania](service-fabric-get-started-containers.md#Push-Containers) pakietu kontenera platformy Docker do repozytorium.

7. Zmodyfikuj ApplicationManifest.xml i ServiceManifest.xml, aby dodać obraz kontenera, informacje o repozytorium, uwierzytelnianie rejestru i mapowania portów na hoście. Do modyfikowania manifestów, zobacz [tworzenie aplikacji kontenera usługi Azure Service Fabric](service-fabric-get-started-containers.md). Definicja pakietu kodu w manifeście usługi musi zostać zamieniona odpowiadających im obrazów kontenera. Upewnij się zmienić typ ContainerHost punktu wejścia.

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

8. Dodaj mapowanie port do hosta, replikatora i punktu końcowego usługi. Ponieważ oba te porty są przypisywane w czasie wykonywania przez usługę Service Fabric, ContainerPort jest równa zero do korzystania z portu przypisanego do mapowania.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Do konfigurowania trybu izolacji kontenera, zobacz [Konfigurowanie trybu izolacji]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). System Windows obsługuje dwa tryby izolacji dla kontenerów: tryb procesu oraz tryb funkcji Hyper-V. Poniższe fragmenty kodu pokazują, jak tryb izolacji można określić w pliku manifestu aplikacji.

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
> Domyślnie aplikacje usługi Service Fabric mają dostęp do środowiska uruchomieniowego usługi Service Fabric, w postaci punkt końcowy, akceptując żądania specyficzne dla aplikacji. Rozważ wyłączenie dostępu, gdy aplikacja udostępnia niezaufanego kodu. Aby uzyskać więcej informacji, zobacz [najważniejsze wskazówki dotyczące zabezpieczeń w usłudze Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Aby wyłączyć dostęp do środowiska uruchomieniowego usługi Service Fabric, Dodaj następujące ustawienie w sekcji zasady manifest aplikacji odpowiadający manifestu usługi importowany, w następujący sposób:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Aby przetestować tę aplikację, należy wdrożyć w klastrze, na którym uruchomiony jest w wersji 5.7 lub nowszej. Środowisko uruchomieniowe w wersji 6.1 lub niższe należy edytować i aktualizować ustawienia klastra, aby włączyć tę funkcję w wersji zapoznawczej. Wykonaj kroki opisane w tym [artykułu](service-fabric-cluster-fabric-settings.md) Aby dodać ustawienie pokazano dalej.
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

11. Następny [wdrażanie](service-fabric-deploy-remove-applications.md) pakiet aplikacji edytowanych do tego klastra.

Teraz masz konteneryzowanych aplikacji usługi Service Fabric klaster uruchomiony.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-get-started-containers.md).
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.
