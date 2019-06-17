---
title: Uruchom skrypt po uruchomieniu usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady dla punktu wejścia Instalatora usługi Service Fabric i uruchamiania skryptu na czas uruchamiania usługi.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 76be814e0dd4c054fc3a873716dbfe395eeeb2dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837800"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Uruchamianie skryptu uruchamiania usługi za pomocą użytkownika lokalnego lub konta systemowego
Zanim uruchamiania pliku wykonywalnego usługi Service Fabric może być konieczne uruchomienie jakąś pracę konfiguracji lub ustawień.  Na przykład Konfigurowanie zmiennych środowiskowych. Można określić skrypt do uruchomienia przed uruchamiania pliku wykonywalnego usługi w manifeście usługi dla usługi. Przez skonfigurowanie zasad RunAs dla punktu wejścia Instalatora usługi można zmienić konto, które wykonywalnego konfiguracji jest uruchamiana.  Punkt wejścia oddzielne ustawienia umożliwia uruchamianie konfiguracji wysokim poziomem uprawnień, przez krótki okres czasu, więc wykonywalnego host usługi nie ma konieczności uruchomienia z wysokim poziomem uprawnień na dłuższy czas.

Ustawienia punktu wejścia (**SetupEntryPoint** w [manifestu usługi](service-fabric-application-and-service-manifests.md)) jest punktem wejścia uprzywilejowane, która domyślnie jest uruchamiany z tymi samymi poświadczeniami co Usługa Service Fabric (zazwyczaj  *Usługa sieciowa* konta) przed innymi punktu wejścia. Plik wykonywalny, który jest określony przez **punktu wejścia** jest zazwyczaj długotrwałych hosta usługi. **Punktu wejścia** plik wykonywalny jest uruchamiany **SetupEntryPoint** plik wykonywalny kończy się pomyślnie. Wynikowy proces monitorowania i ponownie uruchomiony i ponownie zaczyna **SetupEntryPoint** Jeśli nigdy nie kończy się lub ulega awarii. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurowanie punktu wejścia usługi instalatora
Przykład manifestu prostą usługę, usługi bezstanowej, który określa skrypt instalacyjny *MySetup.bat* w usłudze **SetupEntryPoint**.  **Argumenty** jest używany, aby przekazać argumenty do skryptu, po jego uruchomieniu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Skonfiguruj zasady dla punktu wejścia usługi Instalatora
Domyślnie plik wykonywalny punktu wejścia Instalatora usługi jest uruchamiane w ramach tych samych poświadczeń usługi Service Fabric (zazwyczaj *NetworkService* konta).  W manifeście aplikacji możesz zmienić uprawnienia zabezpieczeń do uruchamiania skryptu uruchamiania w ramach konta systemu lokalnego lub konta administratora.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Skonfiguruj zasady za pomocą konta system lokalny
W poniższym przykładzie manifest aplikacji pokazuje, jak skonfigurować punktu wejścia usługi Instalatora, uruchamiany w kontekście konta administratora (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Najpierw utwórz **podmiotów** sekcji z nazwą użytkownika, takie jak SetupAdminUser. Konto użytkownika SetupAdminUser jest członkiem grupy systemowej Administratorzy.

Następnie w obszarze **ServiceManifestImport** sekcji, skonfiguruj zasady do zastosowania tej jednostki do **SetupEntryPoint**. Te zasady informują usługę Service Fabric, że w przypadku **MySetup.bat** jest uruchamiany plik należy uruchamiać jako SetupAdminUser (z uprawnieniami administratora). Ponieważ masz *nie* zastosować zasady do główny punkt wejścia, kod w **MyServiceHost.exe** działa w systemie **NetworkService** konta. Jest to domyślne konto dla wszystkich punktów wejścia usługi są uruchamiane jako.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Skonfiguruj zasady za pomocą konta system lokalny
Często jest uruchomienie skryptu uruchamiania przy użyciu konta systemu lokalnego, a nie konta administratora. Uruchamianie zasad RunAs jako członek grupy Administratorzy zwykle nie działa dobrze, ponieważ komputery mają dostęp do kontroli użytkownika (UAC) domyślnie włączone. W takich przypadkach zaleca się uruchom SetupEntryPoint jako system lokalny, a nie jako użytkownik lokalny dodane do grupy administratorów. Poniższy przykład pokazuje, ustawienie SetupEntryPoint do uruchamiania jako system lokalny:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> W przypadku klastrów systemu Linux do uruchamiania usługi lub instalacji punkt wejścia jako **głównego**, można określić **AccountType** jako **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Uruchom skrypt z punktu wejścia instalacji
Uruchamianie skryptu można teraz dodać do projektu, aby była uruchamiana z uprawnieniami administratora. 

W programie Visual Studio, kliknij prawym przyciskiem myszy projekt usługi i Dodaj nowy plik o nazwie *MySetup.bat*.

Następnie upewnij się, że *MySetup.bat* plik jest uwzględniony w pakiecie usługi. Domyślnie nie jest. Wybierz plik, kliknij prawym przyciskiem myszy, aby uzyskać menu kontekstowe i wybierz **właściwości**. W oknie dialogowym właściwości, upewnij się, że **Kopiuj do katalogu wyjściowego** ustawiono **Kopiuj Jeśli nowszy**. Zobacz poniższy zrzut ekranu.

![Visual Studio CopyToOutput w pliku wsadowego SetupEntryPoint][image1]

Teraz edytować *MySetup.bat* pliku i dodaj następujące polecenia, ustaw zmienną środowiskową systemu, a dane wyjściowe pliku tekstowego:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Następnie kompilacji i wdrażania rozwiązania do lokalnego klastra projektowego. Po uruchomieniu usługi, jak pokazano na [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), możesz zobaczyć, że plik MySetup.bat powiodła się na dwa sposoby. Otwórz wiersz polecenia programu PowerShell i wpisz:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Zanotuj nazwę węzła, która wdrożenie i pracę w usłudze [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Na przykład węzeł 2. Następnie przejdź do folderu roboczego wystąpienia aplikacji można znaleźć pliku out.txt, który pokazuje wartość **TestVariable**. Na przykład, jeśli ta usługa była wdrożona na węźle 2, następnie możesz przejść do tej ścieżki dla **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Uruchom polecenia programu PowerShell z punktu wejścia Instalatora
Aby uruchomić program PowerShell z **SetupEntryPoint** punktu, można uruchomić **PowerShell.exe** w pliku wsadowym, który wskazuje plik programu PowerShell. Najpierw dodaj plik programu PowerShell do usługi projektu — na przykład **MySetup.ps1**. Pamiętaj, aby ustawić *Kopiuj Jeśli nowszy* właściwość tak, że plik znajduje się również w pakiecie usługi. W poniższym przykładzie pokazano przykładowy plik wsadowy, który rozpoczyna się w pliku programu PowerShell o nazwie MySetup.ps1, który Ustawia systemową zmienną środowiskową o nazwie **TestVariable**.

MySetup.bat można uruchomić pliku programu PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

W pliku programu PowerShell należy dodać następujące polecenie, aby ustawić zmienną środowiskową systemu:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Domyślnie po uruchomieniu pliku wsadowego, szuka w folderze aplikacji o nazwie **pracy** dla plików. W takim przypadku po uruchomieniu MySetup.bat chcemy, aby ten element, aby znaleźć plik MySetup.ps1 w tym samym folderze, który jest aplikacją **pakietu kodu** folderu. Aby zmienić ten folder, ustaw folder roboczy:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Debugowanie skryptu uruchamiania lokalnie przy użyciu konsoli przekierowania
Od czasu do czasu jest to przydatne na potrzeby, aby wyświetlić dane wyjściowe konsoli z uruchamianie skryptu konfiguracji debugowania. Możesz ustawić zasady przekierowywania konsoli w punkcie wejścia Instalatora w manifeście usługi, która zapisuje dane wyjściowe do pliku. Plik wyjściowy plik zostanie zapisany do folderu aplikacji o nazwie **dziennika** w węźle klastra, w którym aplikacja jest wdrażany i uruchamiany. 

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, która jest wdrażana w środowisku produkcyjnym, ponieważ może to wpłynąć na pracę awaryjną aplikacji. *Tylko* używane dla rozwoju lokalnych i celów debugowania.  
> 
> 

W poniższym przykładzie manifestu usługi zawiera ustawienia przekierowywania konsoli z wartością FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Jeśli zmienisz teraz MySetup.ps1 pliku do zapisu **Echo** polecenia, to będzie zapisywać do pliku wyjściowego na potrzeby debugowania:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Po debugowania skryptu natychmiast usunąć te zasady przekierowywania konsoli.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o aplikacji i usług zabezpieczeń](service-fabric-application-and-service-security.md)
* [Informacje o modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
