---
title: Jak containerize Twojego mikrousług sieć szkieletowa usług Azure (wersja zapoznawcza)
description: Sieć szkieletowa usług Azure została dodana nowa funkcja containerize Twojego mikrousług sieci szkieletowej usług. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: 3741e74e70769d186da2757b43ca60bbb1e78a1f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Jak containerize Twojego usługi sieć szkieletowa Reliable Services i Reliable Actors (wersja zapoznawcza)

Sieć szkieletowa usług obsługuje containerizing mikrousług sieci szkieletowej usług (niezawodnej usługi i usługi niezawodnego aktora na podstawie). Aby uzyskać więcej informacji, zobacz [usługi sieć szkieletowa kontenery](service-fabric-containers-overview.md).

Ta funkcja jest dostępna w wersji zapoznawczej i w tym artykule przedstawiono różne czynności wymagane do zainstalowania usługi uruchomione w kontenerze.  

> [!NOTE]
> Ta funkcja jest w wersji zapoznawczej i nie jest obsługiwana w środowisku produkcyjnym. Obecnie ta funkcja działa tylko dla systemu Windows. Aby uruchomić kontenerów, klaster musi być uruchomiona w systemie Windows Server 2016 z kontenerów.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Kroki, aby containerize Twojej aplikacji sieci szkieletowej usług

1. Otwórz aplikację usługi Service Fabric w programie Visual Studio.

2. Dodaj klasę [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) do projektu. Kod w tej klasie jest pomocnika można prawidłowo załadować dane binarne środowiska uruchomieniowego platformy Service Fabric wewnątrz aplikacji podczas uruchomienia wewnątrz kontenera.

3. Dla każdego pakietu kodu, który ma zostać containerize, inicjowanie modułu ładującego wejścia programu punktu. Dodaj Konstruktor statyczny pokazano poniższy fragment kodu do pliku punktu wejścia programu.

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

4. Tworzenie i [pakietu](service-fabric-package-apps.md#Package-App) Twojego projektu. Aby skompilować i utworzyć pakiet, kliknij prawym przyciskiem myszy projekt aplikacji w Eksploratorze rozwiązań i wybierz polecenie **pakietu** polecenia.

5. Dla każdego pakietu kodu konieczne containerize, uruchom skrypt programu PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Użycie jest następujący:
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  Skrypt tworzy folder z artefaktami Docker na $dockerPackageOutputDirectoryPath. Zmodyfikuj wygenerowany plik Dockerfile uwidacznia żadnych portów, uruchom Instalatora, skrypty itp., na podstawie Twoich potrzeb.

6. Następnie należy [kompilacji](service-fabric-get-started-containers.md#Build-Containers) i [wypychania](service-fabric-get-started-containers.md#Push-Containers) pakietu kontenera Docker do repozytorium.

7. Modyfikowanie pliku ApplicationManifest.xml i ServiceManifest.xml, aby dodać kontener obrazu, informacje o repozytorium, uwierzytelniania rejestru i mapowania portów do hosta. Do modyfikowania manifesty, zobacz [tworzenie aplikacji kontenera platformy Azure Service Fabric](service-fabric-get-started-containers.md). Definicja pakietu kodu w manifeście usługi wymaga zastąpienia przy użyciu odpowiedniego kontenera obrazu. Upewnij się zmienić typ ContainerHost punktu wejścia.

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

8. Dodaj mapowanie portu na hoście dla replikatora i punktu końcowego usługi. Ponieważ oba te porty są przypisywane w czasie wykonywania przez sieć szkieletowa usług, ContainerPort ma ustawioną wartość zero do korzystania z portu przypisanego do mapowania.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Aby przetestować tę aplikację, należy wdrożyć w klastrze działa w wersji 5.7 lub nowszej. Ponadto należy edytować i zaktualizuj ustawienia klastra, aby włączyć tę funkcję w wersji zapoznawczej. Wykonaj kroki opisane w tym [artykułu](service-fabric-cluster-fabric-settings.md) Aby dodać ustawienie wyświetlane dalej.
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
10. Następny [wdrażanie](service-fabric-deploy-remove-applications.md) pakiet aplikacji edytowanych do tego klastra.

Teraz powinien mieć konteneryzowanych aplikacji usługi Service Fabric systemem klastra.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-get-started-containers.md).
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.
