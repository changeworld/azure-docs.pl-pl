---
title: Uruchom skrypt po uruchomieniu usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania zasad dla punktu wejścia instalacji usługi sieci szkieletowej usług i uruchom skrypt na czas uruchamiania usługi.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: bd2bb0d05029237242b42225a2c846c78a7c6de9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Uruchamianie skryptu uruchamiania usługi za pomocą użytkownika lokalnego lub konta systemowego
Przed uruchamiania pliku wykonywalnego usługi Service Fabric może być konieczne uruchomienie dodatkowych czynności konfiguracyjnych lub ustawień.  Na przykład Konfigurowanie zmiennych środowiskowych. Można określić skrypt do uruchomienia przed rozpoczęciem plik wykonywalny usługi uruchamiania w manifeście usługi dla usługi. Konfigurując zasady Uruchom jako dla usługi punktu wejścia instalacji można zmienić konto, które wykonywalnego konfiguracji jest uruchamiana.  Punkt wejścia oddzielne ustawienia umożliwia uruchamianie konfigurację wysokiej privilged krótkim czasie, więc pliku wykonywalnego hosta usługi, nie trzeba uruchomić z wysokiego poziomu uprawnień przez dłuższy czas.

Punkt wejścia instalacji (**SetupEntryPoint** w [manifestu usługi](service-fabric-application-and-service-manifests.md)) jest punktem wejścia uprzywilejowanych wykonywana domyślnie z tymi samymi poświadczeniami, jak sieć szkieletowa usług (zazwyczaj  *Usługa sieciowa* konta) przed innymi punktu wejścia. Plik wykonywalny, który jest określony przez **punktu wejścia** jest zazwyczaj długotrwałe hosta usługi. **Punktu wejścia** plik wykonywalny jest uruchamiany **SetupEntryPoint** plik wykonywalny kończy się pomyślnie. Wynikowy proces jest monitorowane i ponownie uruchomione i ponownie rozpoczyna się od **SetupEntryPoint** Jeśli kiedykolwiek kończy lub ulegnie awarii. 

## <a name="configure-the-service-setup-entry-point"></a>Skonfiguruj punkt wejścia usługi Instalatora
Przykład manifestu usługi simple, usługi bezstanowej określający skrypt instalacyjny *MySetup.bat* w usłudze **SetupEntryPoint**.  **Argumenty** używany do przekazywania argumenty do skryptu, po uruchomieniu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Skonfiguruj zasady dla punktu wejścia instalacji usługi
Domyślnie wykonywalnym punktu wejścia instalacji usługi zostanie uruchomiona w ramach tych samych poświadczeń jako usługi sieci szkieletowej (zazwyczaj *NetworkService* konta).  W manifeście aplikacji można zmienić uprawnienia zabezpieczeń do uruchamiania skryptu uruchomienia w ramach lokalnego konta systemowego lub konta administratora.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurowanie zasad przy użyciu konta systemu lokalnego
W poniższym przykładzie manifestu aplikacji pokazuje, jak skonfigurować punkt wejścia instalacji usługi dla konta administratora (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Najpierw utwórz **podmiotów** sekcji przy użyciu nazwy użytkownika, takie jak SetupAdminUser. SetupAdminUser konto użytkownika jest członkiem grupy administratorów systemu.

Następnie w obszarze **ServiceManifestImport** Skonfiguruj zasady do zastosowania tego podmiotu zabezpieczeń do **SetupEntryPoint**. Ta zasada sieci szkieletowej usług informuje, że w przypadku **MySetup.bat** jest uruchamiany plik powinno być ono uruchomione jako SetupAdminUser (z uprawnieniami administratora). Ponieważ masz *nie* zastosować zasady do główny punkt wejścia, kod w **MyServiceHost.exe** działa w systemie **NetworkService** konta. Jest to wszystkich punktów wejścia usługi są uruchamiane jako konto domyślne.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Skonfiguruj zasady za pomocą konta system lokalny
Często zaleca się uruchomienie skryptu uruchamiania przy użyciu konta systemu lokalnego, a nie konta administratora. Uruchamianie zasad RunAs jako członek grupy Administratorzy, zwykle nie działa prawidłowo, ponieważ komputery mają dostęp kontroli użytkownika (UAC) domyślnie włączone. W takich przypadkach zaleca się Uruchom element SetupEntryPoint jako system lokalny, a nie jako użytkownik lokalny dodany do grupy administratorów. W poniższym przykładzie przedstawiono ustawienie SetupEntryPoint do uruchamiania jako system lokalny:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
> W przypadku klastrów systemu Linux do uruchamiania usługi lub ustawienia punkt wejścia jako **głównego**, można określić **AccountType** jako **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Uruchom skrypt z punktu wejścia instalacji
Teraz Dodaj uruchamianie skryptu do projektu, aby była uruchamiana z uprawnieniami administratora. 

W programie Visual Studio, kliknij prawym przyciskiem myszy projekt usługi i Dodaj nowy plik o nazwie *MySetup.bat*.

Następnie upewnij się, że *MySetup.bat* plik znajduje się w pakiecie usługi. Domyślnie nie jest. Wybierz plik, kliknij prawym przyciskiem myszy, aby uzyskać menu kontekstowego, a następnie wybierz pozycję **właściwości**. W oknie dialogowym właściwości, upewnij się, że **Kopiuj do katalogu wyjściowego** ustawiono **Kopiuj, jeśli nowszy**. Zobacz poniższy zrzut ekranu.

![Visual Studio CopyToOutput SetupEntryPoint pliku wsadowego][image1]

Teraz edytować *MySetup.bat* i dodaj następujące polecenia Ustaw zmienną środowiskową systemu i wyjściowego pliku tekstowego:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Następnie tworzenia i wdrażania rozwiązania do lokalnego klastra projektowego. Po uruchomieniu usługi, jak pokazano w [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), widać, że plik MySetup.bat zakończyła się pomyślnie na dwa sposoby. Otwórz wiersz polecenia programu PowerShell i wpisz:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Zanotuj nazwę węzła, w którym wdrożony i uruchamiane w usługi [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Na przykład węzeł 2. Następnie przejdź do folderu roboczego wystąpienia aplikacji można znaleźć pliku out.txt, który zawiera wartości **TestVariable**. Na przykład, jeśli ta usługa została wdrożona na węźle 2, następnie można przejść do tej ścieżki dla **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Uruchom polecenia programu PowerShell z punktu wejścia instalacji
Do uruchamiania programu PowerShell z **SetupEntryPoint** punktu, możesz uruchomić **PowerShell.exe** w pliku wsadowego, który wskazuje plik programu PowerShell. Najpierw dodaj plik programu PowerShell do projektu usługi — na przykład **MySetup.ps1**. Należy pamiętać o ustawieniu *Kopiuj, jeśli nowszy* właściwość tak, że plik znajduje się również w pakiecie usługi. W poniższym przykładzie przedstawiono przykładowy plik partii, która rozpoczyna się plik programu PowerShell o nazwie MySetup.ps1, który określa zmienną środowiskową systemu o nazwie **TestVariable**.

MySetup.bat, aby uruchomić plik programu PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

W pliku programu PowerShell należy dodać następujące polecenie, aby ustawić zmienną środowiskową systemu:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Domyślnie podczas uruchamiania pliku wsadowego wygląda w folderze aplikacji o nazwie **pracy** plików. W takim przypadku po uruchomieniu MySetup.bat chcemy, aby znaleźć plik MySetup.ps1 w tym samym folderze, który jest aplikacja **pakietu kodu** folderu. Aby zmienić ten folder, ustawianie folderu roboczego:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Debugowanie skryptu uruchamiania lokalnie za pomocą konsoli
Czasami jest przydatne w przypadku debugowania, aby wyświetlić dane wyjściowe konsoli uruchamiania skryptu Instalatora. Można ustawić zasad przekierowania konsoli dla punktu wejścia instalacji w manifeście usługi, który zapisuje dane wyjściowe do pliku. Plik wyjściowy jest zapisywany w folderze aplikacji o nazwie **dziennika** w węźle klastra, w którym aplikacja jest wdrożona i uruchom. 

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, które zostało wdrożone w środowisku produkcyjnym, ponieważ może to wpłynąć na pracę awaryjną aplikacji. *Tylko* użyć tej funkcji dla rozwoju lokalnych i debugowania.  
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
> Po debugowania skryptu, natychmiast usunąć te zasady przekierowania konsoli.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o aplikacji i usług zabezpieczeń](service-fabric-application-and-service-security.md)
* [Zrozumienie model aplikacji](service-fabric-application-model.md)
* [Określ zasoby w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
