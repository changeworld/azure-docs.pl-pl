---
title: Uruchamianie skryptu po uruchomieniu usługi Azure Service Fabric
description: Dowiedz się, jak skonfigurować zasady dla punktu wejścia konfiguracji usługi sieci szkieletowej usług i uruchomić skrypt w czasie uruchamiania usługi.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464273"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Uruchamianie skryptu uruchamiania usługi za pomocą użytkownika lokalnego lub konta systemowego
Przed uruchomieniem pliku wykonywalnego usługi sieci szkieletowej usług może być konieczne uruchomienie pracy konfiguracyjnej lub konfiguracyjnej.  Na przykład konfigurowanie zmiennych środowiskowych. Można określić skrypt do uruchomienia przed uruchomieniem pliku wykonywalnego usługi w manifeście usługi dla usługi. Konfigurując zasady RunAs dla punktu wejścia konfiguracji usługi, można zmienić konto, na którym działa plik wykonywalny instalatora.  Oddzielny punkt wejścia instalacji umożliwia uruchamianie konfiguracji o wysokich uprawnieniach przez krótki okres czasu, dzięki czemu plik wykonywalny hosta usługi nie musi działać z wysokimi uprawnieniami przez dłuższy czas.

Punkt wejścia instalatora **(SetupEntryPoint** w [manifeście usługi)](service-fabric-application-and-service-manifests.md)jest uprzywilejowanym punktem wejścia, który domyślnie jest uruchamiany z tymi samymi poświadczeniami co sieć szkieletowa usług (zazwyczaj konto *usługi sieciowej)* przed jakimkolwiek innym punktem wejścia. Plik wykonywalny określony przez **entrypoint** jest zazwyczaj długo działającym hostem usługi. Plik wykonywalny **EntryPoint** jest uruchamiany po pomyślnym zamknięciu pliku wykonywalnego **SetupEntryPoint.** Wynikowy proces jest monitorowany i uruchamiany ponownie i rozpoczyna się od **instalatora,** jeśli kiedykolwiek zakończy się lub ulegnie awarii. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurowanie punktu wejścia usługi instalatora
Poniżej przedstawiono prosty przykład manifestu usługi dla usługi bezstanowej, który określa skrypt instalacyjny *MySetup.bat* w usłudze **SetupEntryPoint**.  **Argumenty** są używane do przekazywania argumentów do skryptu po jego uruchomieniu.

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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurowanie zasad dla punktu wejścia konfiguracji usługi
Domyślnie plik wykonywalny punktu wejścia instalatora usługi jest uruchamiany w tych samych poświadczeniach co sieć szkieletowa usług (zazwyczaj konto *Usługi sieciowej).*  W manifeście aplikacji można zmienić uprawnienia zabezpieczeń do uruchamiania skryptu startowego na lokalnym koncie systemowym lub koncie administratora.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurowanie zasad przy użyciu lokalnego konta systemowego
W poniższym przykładzie manifestu aplikacji pokazano, jak skonfigurować punkt wejścia konfiguracji usługi do uruchamiania w ramach konta administratora użytkownika (SetupAdminUser).

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

Najpierw należy utworzyć sekcję **Zleceniodawców** o nazwie użytkownika, taką jak SetupAdminUser. Konto użytkownika SetupAdminUser jest członkiem grupy systemowej Administratorzy.

Następnie w sekcji **ServiceManifestImport** skonfiguruj zasady, aby zastosować tego podmiotu do **SetupEntryPoint**. Ta zasada informuje sieci szkieletowej usług, że po uruchomieniu pliku **MySetup.bat** należy uruchomić jako SetupAdminUser (z uprawnieniami administratora). Ponieważ *zasady nie* zostały zastosowane do głównego punktu wejścia, kod w **myServiceHost.exe** jest uruchamiany w ramach konta **networkservice** systemu. Jest to domyślne konto, które wszystkie punkty wejścia usługi są uruchamiane jako.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurowanie zasad przy użyciu lokalnych kont systemowych
Często zaleca się uruchamianie skryptu startowego przy użyciu lokalnego konta systemowego, a nie konta administratora. Uruchamianie zasad RunAs jako członka grupy Administratorzy zazwyczaj nie działa dobrze, ponieważ komputery mają domyślnie włączoną kontrolę dostępu użytkownika(UAC). W takich przypadkach zaleca się uruchomienie setupentrypoint jako localsystem, a nie jako użytkownik lokalny dodany do grupy Administratorzy. W poniższym przykładzie pokazano ustawienie, że program SetupEntryPoint ma być uruchamiany jako system lokalny:

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
> W przypadku klastrów systemu Linux, aby uruchomić usługę lub punkt wejścia instalacji jako **główny,** można określić **Typ konta** jako **System lokalny**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Uruchamianie skryptu z punktu wejścia instalatora
Teraz dodaj skrypt rozruchowy do projektu, aby uruchomić je w ramach uprawnień administratora. 

W programie Visual Studio kliknij prawym przyciskiem myszy projekt usługi i dodaj nowy plik o nazwie *MySetup.bat*.

Następnie upewnij się, że plik *MySetup.bat* znajduje się w pakiecie usługi. Domyślnie tak nie jest. Wybierz plik, kliknij prawym przyciskiem myszy, aby uzyskać menu kontekstowe, a następnie wybierz polecenie **Właściwości**. W oknie dialogowym Właściwości upewnij się, że **funkcja Kopiowanie do katalogu wyjściowego** jest ustawiona na **Kopiowanie, jeśli jest nowsza**. Zobacz poniższy zrzut ekranu.

![Plik wsadowy Programu Visual Studio CopyToOutput dla pliku wsadowego programu SetupEntryPoint][image1]

Teraz edytuj plik *MySetup.bat* i dodaj następujące polecenia, ustaw zmienną środowiskową systemu i wyjmij plik tekstowy:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Następnie skompiluj i wdrażaj rozwiązanie w lokalnym klastrze deweloperów. Po uruchomieniu usługi, jak pokazano w [Eksploratorze sieci szkieletowej usług,](service-fabric-visualizing-your-cluster.md)widać, że plik MySetup.bat zakończył się pomyślnie na dwa sposoby. Otwórz wiersz polecenia programu PowerShell i wpisz:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Następnie zanotuj nazwę węzła, w którym usługa została wdrożona i uruchomiona w [Eksploratorze sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md). Na przykład węzeł 2. Następnie przejdź do folderu roboczego wystąpienia aplikacji, aby znaleźć plik out.txt, który pokazuje wartość **TestVariable**. Na przykład jeśli ta usługa została wdrożona w węźle 2, możesz przejść do tej ścieżki dla **MyApplicationType:**

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Uruchamianie poleceń programu PowerShell z punktu wejścia instalatora
Aby uruchomić program PowerShell z punktu **SetupEntryPoint,** można uruchomić program **PowerShell.exe** w pliku wsadowym wskazującym plik programu PowerShell. Najpierw dodaj plik programu PowerShell do projektu usługi — na przykład **MySetup.ps1**. Pamiętaj, aby ustawić *copy if nowsze* właściwości, tak aby plik jest również zawarte w pakiecie usługi. W poniższym przykładzie przedstawiono przykładowy plik wsadowy uruchamiany plik programu PowerShell o nazwie MySetup.ps1, który ustawia zmienną środowiskową systemu o nazwie **TestVariable**.

MySetup.bat, aby uruchomić plik programu PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

W pliku programu PowerShell dodaj następujące elementy, aby ustawić zmienną środowiskową systemu:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Domyślnie po uruchomieniu pliku wsadowego, patrzy na folder aplikacji o nazwie **pracy** dla plików. W takim przypadku, gdy działa MySetup.bat, chcemy to znaleźć plik MySetup.ps1 w tym samym folderze, który jest **folderem pakietu kodu** aplikacji. Aby zmienić ten folder, ustaw folder roboczy:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Debugowanie skryptu startowego lokalnie przy użyciu przekierowania konsoli
Od czasu do czasu jest to przydatne do celów debugowania, aby zobaczyć dane wyjściowe konsoli z uruchamiania skryptu konfiguracji. Zasady przekierowania konsoli można ustawić w punkcie wejścia instalatora w manifeście usługi, który zapisuje dane wyjściowe w pliku. Dane wyjściowe pliku są zapisywane w folderze aplikacji **nazywanym logiem** w węźle klastra, w którym aplikacja jest wdrażana i uruchamiana. 

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, która jest wdrażana w produkcji, ponieważ może to mieć wpływ na proces pracy awaryjnej aplikacji. *Użyj* tego tylko do lokalnych celów rozwoju i debugowania.  
> 
> 

Poniższy przykład manifestu usługi pokazuje ustawienie przekierowania konsoli z wartością FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Jeśli teraz zmienisz plik MySetup.ps1, aby napisać polecenie **Echo,** zostanie ono wpisane do pliku wyjściowego do celów debugowania:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Po debugowaniu skryptu należy natychmiast usunąć tę zasadę przekierowania konsoli.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o zabezpieczeniach aplikacji i usług](service-fabric-application-and-service-security.md)
* [Opis modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
