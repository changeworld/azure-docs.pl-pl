---
title: Wdrażanie istniejącego pliku wykonywalnego w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pakiet istniejącą aplikację jako plik wykonywalny gościa, aby można było wdrożyć w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: aljo
ms.openlocfilehash: bfac14c598b405a398cad916787aa3312589bfd1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393573"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Pakowanie i wdrażanie istniejącego pliku wykonywalnego do usługi Service Fabric
Podczas pakowania istniejącego pliku wykonywalnego jako [pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md), możesz wybrać użycie szablonu projektu programu Visual Studio lub [ręcznie utworzyć pakiet aplikacji](#manually). W programie Visual Studio struktury pakietu aplikacji i plików manifestu są tworzone przez nowy szablon projektu.

> [!TIP]
> Najprostszym sposobem, aby utworzyć pakiet z istniejącego pliku wykonywalnego w usłudze Windows jest użycie programu Visual Studio i w systemie Linux, aby użyć narzędzia Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Pakowanie i wdrażanie istniejącego pliku wykonywalnego przy użyciu programu Visual Studio
Program Visual Studio udostępnia usługi Service Fabric szablon usługi ułatwiający wdrażanie pliku wykonywalnego gościa w klastrze usługi Service Fabric.

1. Wybierz **pliku** > **nowy projekt**i Utwórz aplikację usługi Service Fabric.
2. Wybierz **pliku wykonywalnego gościa** jako szablon usługi.
3. Kliknij przycisk **Przeglądaj** wybierz folder zawierający plik wykonywalny i wypełniając pozostałe parametry, aby utworzyć usługę.
   * *Zachowanie pakietu kodu*. Można ustawić tak, aby skopiować całą zawartość folderu do projektu programu Visual Studio, co jest przydatne, jeśli plik wykonywalny nie zmienia się. Jeśli oczekujesz, że plik wykonywalny do zmieniających się i mają możliwość odebrania nowych kompilacji dynamicznie, można połączyć do folderu, zamiast tego. Podczas tworzenia projektu aplikacji w programie Visual Studio, można użyć połączonego folderów. Łącze do lokalizacji źródłowej z w obrębie projektu, dzięki czemu można zaktualizować pliku wykonywalnego gościa w jego miejsce docelowe źródło. Te aktualizacje stają się częścią pakietu aplikacji podczas kompilacji.
   * *Program* Określa plik wykonywalny, który powinien być uruchamiany, aby uruchomić usługę.
   * *Argumenty* Określa argumenty, które powinny być przekazywane do pliku wykonywalnego. Można go, aby uzyskać listę parametrów z argumentami.
   * *WorkingFolder* Określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
     * `CodeBase` Określa, że katalog roboczy będzie można ustawić katalog kodu w pakiecie aplikacji (`Code` katalogu pokazano w poprzednim struktury plików).
     * `CodePackage` Określa, czy katalog roboczy ma być ustawiony na katalog główny pakietu aplikacji (`GuestService1Pkg` pokazano w poprzednim struktury plików).
     * `Work` Określa, że pliki są umieszczane w podkatalogu o nazwie pracy.
4. Nadaj nazwę usłudze i kliknij przycisk **OK**.
5. Jeśli usługa wymaga punktu końcowego na potrzeby komunikacji, możesz teraz dodać protokół, port i typ do pliku ServiceManifest.xml. Na przykład: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Możesz teraz za pomocą pakietu i opublikuj akcji względem klastra lokalnego, debugowania rozwiązań w programie Visual Studio. Gdy wszystko będzie gotowe, możesz opublikować aplikację w klastrze zdalnym lub zaewidencjonować rozwiązanie do kontroli źródła.
7. Odczyt [Sprawdź uruchomionej aplikacji](#check-your-running-application) aby zobaczyć, jak wyświetlać swoje usługi wykonywalnej gościa w narzędziu Service Fabric Explorer.

Przewodnik po przykładzie można zobaczyć [tworzenie pierwszej aplikacji wykonywalnych gościa za pomocą programu Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Użyj narzędzia Yeoman, aby pakiet i wdrażanie istniejącego pliku wykonywalnego w systemie Linux

Procedura tworzenia i wdrażania pliku wykonywalnego w systemie Linux gościa jest taka sama jak wdrażanie aplikacji csharp lub java.

1. Na terminalu wpisz `yo azuresfguest`.
2. Nadaj nazwę aplikacji.
3. Nadaj swojej usłudze nazwę, a następnie podaj szczegóły, w tym ścieżki pliku wykonywalnego i parametry, które musi zostać wywołana z.

Narzędzie yeoman tworzy pakiet aplikacji z odpowiedniej aplikacji i plików manifestu wraz z instalowania i odinstalowywania skryptów.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ręczne tworzenie pakietów i wdrażanie istniejącego pliku wykonywalnego
Proces pakowania ręcznie wykonywalnej gościa opiera się na następujące ogólne kroki:

1. Utwórz strukturę katalogu pakietu.
2. Dodaj pliki kodu i konfiguracji aplikacji.
3. Edytuj plik manifestu usługi.
4. Edytuj plik manifestu aplikacji.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Tworzenie struktury katalogów pakietu
Możesz uruchomić tworzenie struktury katalogów, zgodnie z opisem w [pakietów usługi Azure Service Fabric App](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Dodaj pliki kodu i konfiguracji aplikacji
Po utworzeniu struktury katalogów, możesz dodać kod aplikacji i plików konfiguracji w ramach kodu i konfiguracji. Można również utworzyć dodatkowe katalogi lub podkatalogi w kodzie lub konfiguracji katalogów.

Usługa Service Fabric jest `xcopy` zawartości katalogu głównego aplikacji, więc nie ma wstępnie zdefiniowanych struktury do użycia innego niż tworzenie dwa najważniejsze katalogi, kodu i ustawień. (Można wybrać różne nazwy, jeśli chcesz. Szczegółowe informacje są w następnej sekcji).

> [!NOTE]
> Upewnij się, że wszystkie pliki i zależności, których potrzebuje aplikacja. Usługa Service Fabric kopiuje zawartość pakietu aplikacji na wszystkich węzłach w klastrze, gdzie usług aplikacji do wdrożenia. Pakiet powinien zawierać cały kod, który ma być uruchamiana aplikacja. Nie należy zakładać, że zależności są już zainstalowane.
>
>

### <a name="edit-the-service-manifest-file"></a>Edytuj plik manifestu usługi
Następnym krokiem jest zmodyfikowanie pliku manifestu usługi, aby podać następujące informacje:

* Nazwa typu usługi. Jest to identyfikator, który korzysta z usługi Service Fabric do identyfikowania usługi.
* Polecenie używane do uruchamiania aplikacji (ExeHost).
* Skrypt, który ma zostać uruchomiona, aby skonfigurować aplikację (SetupEntrypoint).

Oto przykład `ServiceManifest.xml` pliku:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Poniższe sekcje przechodzi przez różne części pliku który chcesz zaktualizować.

#### <a name="update-servicetypes"></a>Aktualizuj ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Możesz wybrać dowolną nazwę, który ma zostać `ServiceTypeName`. Wartość jest używana w `ApplicationManifest.xml` pliku do identyfikowania usługi.
* Określ `UseImplicitHost="true"`. Ten atrybut informują usługę Service Fabric, że usługa jest oparta na aplikację samodzielną, więc wszystkiego usługi Service Fabric w celu uruchomienia go jako procesu i monitorować jego kondycję.

#### <a name="update-codepackage"></a>Aktualizuj CodePackage
CodePackage element określa lokalizację (i wersji) z kodem usługi.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` Element jest używany do określenia nazwy katalogu w pakiecie aplikacji, która zawiera kod usługi. `CodePackage` ma również `version` atrybutu. Może służyć do określenia wersji kodu, a także potencjalnie może służyć do uaktualnienia kodu usługi za pomocą infrastruktury zarządzania cyklem życia aplikacji w usłudze Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Opcjonalnie: Aktualizuj SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint element jest używany do określania dowolnego pliku wykonywalnego lub pliku wsadowego, które mają zostać wykonane, zanim zostanie uruchomiony kodu usługi. Jest to opcjonalny krok, więc nie musi być uwzględniony, jeśli inicjowanie nie jest wymagane. SetupEntryPoint jest wykonywane za każdym razem, gdy usługa zostanie ponownie uruchomiona.

Jest tylko jeden SetupEntryPoint, dlatego skrypty instalacji muszą być zgrupowane w pliku wsadowym pojedynczego, jeśli instalacja aplikacji wymaga wiele skryptów. SetupEntryPoint można wykonywać każdy typ pliku: pliki wykonywalne, pliki wsadowe i poleceń cmdlet programu PowerShell. Aby uzyskać więcej informacji, zobacz [skonfigurować SetupEntryPoint](service-fabric-application-runas-security.md).

W powyższym przykładzie SetupEntryPoint jest uruchamiane plik wsadowy o nazwie `LaunchConfig.cmd` znajdującą się w `scripts` podkatalogu katalogu kodu (zakładając, że WorkingFolder element jest ustawiony na bazie kodu).

#### <a name="update-entrypoint"></a>Update EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

`EntryPoint` Elementu w pliku manifestu usługi służy do określania sposobu uruchamiania usługi.

`ExeHost` Element Określa plik wykonywalny (i argumenty) które mają być używane do uruchomienia usługi. Możesz opcjonalnie dodać `IsExternalExecutable="true"` atrybutu `ExeHost` do wskazania, że program jest zewnętrzny plik wykonywalny poza pakiet kodu. Na przykład `<ExeHost IsExternalExecutable="true">`.

* `Program` Określa nazwę pliku wykonywalnego, który należy uruchomić usługę.
* `Arguments` Określa argumenty, które powinny być przekazywane do pliku wykonywalnego. Można go, aby uzyskać listę parametrów z argumentami.
* `WorkingFolder` Określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
  * `CodeBase` Określa, że katalog roboczy będzie można ustawić katalog kodu w pakiecie aplikacji (`Code` katalogu w poprzednim struktury plików).
  * `CodePackage` Określa, czy katalog roboczy ma być ustawiony na katalog główny pakietu aplikacji (`GuestService1Pkg` w poprzednim struktury plików).
    * `Work` Określa, że pliki są umieszczane w podkatalogu o nazwie pracy.

WorkingFolder przydaje się konfigurowanie prawidłowy katalog roboczy ścieżek względnych mogą być używane przez aplikację lub inicjowania skryptów.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Zaktualizuj punkty końcowe i rejestracji w usłudze nazewnictwa komunikacji
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
W powyższym przykładzie `Endpoint` element określa punkty końcowe, które aplikacja może nasłuchiwać na. W tym przykładzie aplikacja Node.js nasłuchuje od protokołu http na porcie 3000.

Ponadto możesz poprosić usługi Service Fabric, aby opublikować tego punktu końcowego w usłudze nazewnictwa, dzięki czemu inne usługi mogą odnajdywać adres punktu końcowego do tej usługi. Dzięki temu można mieć możliwość komunikacji między usługami, które są pliki wykonywalne gościa.
Adres punktu końcowego opublikowanej ma postać `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` i `PathSuffix` są opcjonalne atrybuty. `IPAddressOrFQDN` adres IP lub w pełni kwalifikowana nazwa domeny węzła tego pliku wykonywalnego pobiera umieszczone na i jest obliczana dla Ciebie.

W poniższym przykładzie, po wdrożeniu usługi Service Fabric Explorer zobaczysz punktu końcowego, podobnego do `http://10.1.4.92:3000/myapp/` opublikowane dla wystąpienia usługi. Lub jeśli jest to komputer lokalny, zostanie wyświetlony `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Można użyć tych adresów z [zwrotny serwer proxy](service-fabric-reverseproxy.md) do komunikacji między usługami.

### <a name="edit-the-application-manifest-file"></a>Edytuj plik manifestu aplikacji
Po skonfigurowaniu `Servicemanifest.xml` pliku, należy wprowadzić pewne zmiany do `ApplicationManifest.xml` pliku, aby zapewnić, że prawidłowego typu i nazwy są używane.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
W `ServiceManifestImport` elementu, można określić co najmniej jednej usługi, które mają zostać uwzględnione w aplikacji. Usługi są przywoływane z `ServiceManifestName`, który określa nazwę katalogu, gdzie `ServiceManifest.xml` znajduje się plik.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurowanie rejestrowania
Dla plików wykonywalnych gościa jest przydatne można było zobaczyć dzienniki konsoli, aby dowiedzieć się, w przypadku skryptów aplikacji i konfiguracji wyświetlane ewentualne błędy.
Przekierowywanie konsoli można skonfigurować w `ServiceManifest.xml` plików przy użyciu `ConsoleRedirection` elementu.

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, która jest wdrażana w środowisku produkcyjnym, ponieważ może to wpłynąć na pracę awaryjną aplikacji. *Tylko* używane dla rozwoju lokalnych i celów debugowania.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` można przekierować dane wyjściowe konsoli (stdout i stderr) do katalogu roboczego. Zapewnia to możliwość sprawdzenia, czy nie wystąpiły żadne błędy podczas instalacji lub wykonywania aplikacji w klastrze usługi Service Fabric.

`FileRetentionCount` Określa, ile pliki są zapisywane w katalogu roboczym. Wartość 5, na przykład oznacza, że pliki dzienników do poprzednich wykonań pięć są przechowywane w katalogu roboczym.

`FileMaxSizeInKb` Określa maksymalny rozmiar plików dziennika.

Pliki dziennika są zapisywane w jednym z katalogów roboczych tej usługi. Aby określić, gdzie znajdują się pliki, Usługa Service Fabric Explorer, aby określić, który węzeł jest zasilany z, a katalog roboczy, który jest używany. Ten proces został omówiony w dalszej części tego artykułu.

## <a name="deployment"></a>Wdrożenie
Ostatnim krokiem jest [wdrożyć aplikację](service-fabric-deploy-remove-applications.md). Poniższy skrypt programu PowerShell pokazuje, jak wdrożyć aplikację na lokalny klaster projektowy, a następnie uruchom nową usługę Service Fabric.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Kompresowanie pakietu](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów, jeśli pakiet jest duży lub ma wiele plików. Dowiedz się więcej [tutaj](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Usługi Service Fabric można wdrożyć w różnych "konfiguracji". Na przykład można je było wdrożyć jako jednego lub wielu wystąpień lub mogą być wdrażane w taki sposób, że istnieje jedno wystąpienie usługi w każdym węźle klastra usługi Service Fabric.

`InstanceCount` Parametru `New-ServiceFabricService` polecenia cmdlet jest używany do określenia, ile wystąpień usługi powinien zostać uruchomiony w klastrze usługi Service Fabric. Możesz ustawić `InstanceCount` wartość, w zależności od typu aplikacji, w której przeprowadzasz wdrożenie. Dwie najbardziej typowe scenariusze są następujące:

* `InstanceCount = "1"`. W takim przypadku tylko jedno wystąpienie usługi jest wdrażana w klastrze. Harmonogram usługi Service Fabric Określa, który węzeł będzie można wdrożyć na usługę.
* `InstanceCount ="-1"`. W tym przypadku jedno wystąpienie usługi jest wdrażana w każdym węźle w klastrze usługi Service Fabric. Wynik ma jeden (i tylko jeden) wystąpienie usługi dla każdego węzła w klastrze.

Jest to przydatne w konfiguracji dla aplikacji frontonu (na przykład punktu końcowego REST), ponieważ aplikacje klienckie wymagają "Połącz" do dowolnego z węzłów w klastrze, aby używać punktu końcowego. Tę konfigurację można również gdy na przykład, wszystkie węzły klastra usługi Service Fabric są połączone z modułem równoważenia obciążenia. Ruch sieciowy klienta mogą być następnie rozproszone między usługa, która działa na wszystkich węzłach w klastrze.

## <a name="check-your-running-application"></a>Sprawdź uruchomionej aplikacji
W narzędziu Service Fabric Explorer zidentyfikować węzeł, w którym usługa jest uruchomiona. W tym przykładzie jest uruchamiany na Węzeł1:

![Węzeł, w którym jest uruchomiona usługa](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Jeśli przejdź do węzła, a następnie przejdź do aplikacji, zapoznaj się z informacjami essential węzła, łącznie z jego lokalizacji na dysku.

![Miejsce na dysku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Przejdź do katalogu za pomocą Eksploratora serwera można znaleźć katalogu roboczego i folderu dzienników usługi, jak pokazano na poniższym zrzucie ekranu: 

![Lokalizacja dziennika](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule mają pokazaliśmy, jak pakiet wykonywalnej gościa i wdrożyć ją w usłudze Service Fabric. Zobacz następujące artykuły, aby uzyskać powiązane informacje i zadania.

* [Przykład pakowanie i wdrażanie wykonywalnej gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), łącznie z łączem do wstępną narzędzia pakowania
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikowania się za pośrednictwem usługi nazewnictwa przy użyciu usługi REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Wdrażanie wielu aplikacji wykonywalnych gości](service-fabric-deploy-multiple-apps.md)
* [Tworzenie pierwszej aplikacji usługi Service Fabric przy użyciu programu Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
