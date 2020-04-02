---
title: Wdrażanie istniejącego pliku wykonywalnego w sieci szkieletowej usług Azure
description: Dowiedz się, jak spakować istniejącą aplikację jako plik wykonywalny gościa, dzięki czemu można ją wdrożyć w klastrze sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c6c6bc0369593c177b74261da1fd8c15dd73fcb3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520484"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Pakiet i wdrażanie istniejącego pliku wykonywalnego w sieci szkieletowej usług

Podczas pakowania istniejącego pliku wykonywalnego jako [plik wykonywalny gościa](service-fabric-guest-executables-introduction.md)można wybrać opcję użycia szablonu projektu programu Visual Studio lub [ręcznego utworzenia pakietu aplikacji.](#manually) Za pomocą programu Visual Studio struktura pakietu aplikacji i pliki manifestu są tworzone przez nowy szablon projektu dla Ciebie.

> [!TIP]
> Najprostszym sposobem spakowania istniejącego pliku wykonywalnego systemu Windows do usługi jest użycie programu Visual Studio i linuksu do używania programu Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Używanie programu Visual Studio do pakowania i wdrażania istniejącego pliku wykonywalnego

Program Visual Studio udostępnia szablon usługi sieci szkieletowej usług, aby ułatwić wdrażanie pliku wykonywalnego gościa w klastrze sieci szkieletowej usług.

1. Wybierz **pozycję Plik** > **nowego projektu**i utwórz aplikację sieci szkieletowej usług.
2. Wybierz **plik wykonywalny gościa** jako szablon usługi.
3. Kliknij **przycisk Przeglądaj,** aby wybrać folder z plikiem wykonywalnym i wypełnić pozostałe parametry, aby utworzyć usługę.
   * *Zachowanie pakietu kodu*. Można ustawić, aby skopiować całą zawartość folderu do programu Visual Studio Project, co jest przydatne, jeśli plik wykonywalny nie zmienia. Jeśli oczekujesz, że plik wykonywalny zmieni się i chcesz dynamicznie odbierać nowe kompilacje, możesz zamiast tego utworzyć łącze do folderu. Foldery połączone można używać podczas tworzenia projektu aplikacji w programie Visual Studio. Spowoduje to łącza do lokalizacji źródłowej z poziomu projektu, dzięki czemu można zaktualizować plik wykonywalny gościa w źródle docelowym. Te aktualizacje stają się częścią pakietu aplikacji na kompilacji.
   * *Program* określa plik wykonywalny, który powinien zostać uruchomiony w celu uruchomienia usługi.
   * *Argumenty* określa argumenty, które powinny być przekazywane do pliku wykonywalnego. Może to być lista parametrów z argumentami.
   * *WorkingFolder* określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
     * `CodeBase`określa, że katalog roboczy zostanie ustawiony na katalog kodu w`Code` pakiecie aplikacji (katalog pokazany w poprzedniej strukturze plików).
     * `CodePackage`określa, że katalog roboczy zostanie ustawiony na katalog główny`GuestService1Pkg` pakietu aplikacji (pokazanego w poprzedniej strukturze plików).
     * `Work`określa, że pliki są umieszczane w podkatalogu zwanym pracą.
4. Nadaj nazwę usłudze i kliknij przycisk **OK**.
5. Jeśli usługa potrzebuje punktu końcowego do komunikacji, można teraz dodać protokół, port i typ do pliku ServiceManifest.xml. Na przykład: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Teraz można użyć pakietu i opublikować akcję dla lokalnego klastra przez debugowanie rozwiązania w programie Visual Studio. Gdy jest to gotowe, można opublikować aplikację do klastra zdalnego lub zaewidencjonować rozwiązanie do kontroli źródła.
7. Przeczytaj [sprawdź uruchomionej aplikacji,](#check-your-running-application) aby zobaczyć, jak wyświetlić usługę wykonywalną gościa uruchomionej w Eksploratorze sieci szkieletowej usług.

Aby zapoznać się z przykładowym instruktażem, zobacz [Tworzenie pierwszej aplikacji wykonywalnej gościa przy użyciu programu Visual Studio](quickstart-guest-app.md).

### <a name="packaging-multiple-executables-with-visual-studio"></a>Pakowanie wielu plików wykonywalnych za pomocą programu Visual Studio

Za pomocą programu Visual Studio można utworzyć pakiet aplikacji zawierający wiele plików wykonywalnych gościa. Po dodaniu pierwszego pliku wykonywalnego gościa kliknij prawym przyciskiem myszy projekt aplikacji i wybierz **usługę Add->New Service Fabric,** aby dodać drugi projekt wykonywalny gościa do rozwiązania.

> [!NOTE]
> Jeśli zdecydujesz się połączyć źródło w projekcie programu Visual Studio, tworzenie rozwiązania programu Visual Studio, upewnij się, że pakiet aplikacji jest aktualny ze zmianami w źródle.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Użyj Yeoman do pakowania i wdrażania istniejącego pliku wykonywalnego w systemie Linux

Procedura tworzenia i wdrażania pliku wykonywalnego gościa w systemie Linux jest taka sama jak wdrażanie aplikacji csharp lub java.

1. Na terminalu wpisz `yo azuresfguest`.
2. Nadaj nazwę aplikacji.
3. Nazwij usługę i podaj szczegóły, w tym ścieżkę pliku wykonywalnego i parametry, z które musi zostać wywołana.

Yeoman tworzy pakiet aplikacji z odpowiednią aplikacją i plikami manifestu wraz ze skryptami instalacji i odinstalowywania.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Pakowanie wielu plików wykonywalnych przy użyciu Yeoman na Linuksie

Aby dodać kolejną usługę do aplikacji utworzonej już przy użyciu polecenia `yo`, wykonaj następujące czynności:

1. Zmień katalog na katalog główny istniejącej aplikacji.  Na przykład wpisz polecenie `cd ~/YeomanSamples/MyApplication`, jeśli aplikacja `MyApplication` to aplikacja utworzona przez narzędzie Yeoman.
2. Uruchom `yo azuresfguest:AddService` i podaj niezbędne szczegóły.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ręczne pakowanie i wdrażanie istniejącego pliku wykonywalnego

Proces ręcznego pakowania pliku wykonywalnego gościa opiera się na następujących ogólnych krokach:

1. Utwórz strukturę katalogów pakietów.
2. Dodaj kod aplikacji i pliki konfiguracyjne.
3. Edytuj plik manifestu usługi.
4. Edytuj plik manifestu aplikacji.

### <a name="create-the-package-directory-structure"></a>Tworzenie struktury katalogów pakietów

Można rozpocząć od utworzenia struktury katalogów, zgodnie z opisem w [pakiecie aplikacji sieci szkieletowej usług Azure.](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps)

### <a name="add-the-applications-code-and-configuration-files"></a>Dodawanie kodu aplikacji i plików konfiguracyjnych

Po utworzeniu struktury katalogów można dodać kod aplikacji i pliki konfiguracyjne pod katalogami kodu i konfiguracji. Można również utworzyć dodatkowe katalogi lub podkatalogi pod katalogami kodu lub konfiguracji.

Sieć szkieletowa `xcopy` usług wykonuje zawartość katalogu głównego aplikacji, więc nie ma wstępnie zdefiniowanej struktury do użycia innej niż tworzenie dwóch najlepszych katalogów, kodu i ustawień. (Jeśli chcesz, możesz wybrać różne nazwy. Więcej szczegółów znajduje się w następnej sekcji.)

> [!NOTE]
> Upewnij się, że zawiera wszystkie pliki i zależności, które aplikacja potrzebuje. Sieć szkieletowa usług kopiuje zawartość pakietu aplikacji we wszystkich węzłach w klastrze, w którym zostaną wdrożone usługi aplikacji. Pakiet powinien zawierać cały kod, który aplikacja musi uruchomić. Nie należy zakładać, że zależności są już zainstalowane.
>
>

### <a name="edit-the-service-manifest-file"></a>Edytowanie pliku manifestu usługi

Następnym krokiem jest edycja pliku manifestu usługi w celu uwzględnienia następujących informacji:

* Nazwa typu usługi. Jest to identyfikator, który sieci szkieletowej usług używa do identyfikowania usługi.
* Polecenie do uruchomienia aplikacji (ExeHost).
* Dowolny skrypt, który musi zostać uruchomiony, aby skonfigurować aplikację (SetupEntrypoint).

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

Poniższe sekcje przejść przez różne części pliku, które należy zaktualizować.

#### <a name="update-servicetypes"></a>Aktualizowanie typów usług

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Możesz wybrać dowolną nazwę, `ServiceTypeName`dla której chcesz . Wartość jest używana `ApplicationManifest.xml` w pliku do identyfikowania usługi.
* Podaj wartość `UseImplicitHost="true"`. Ten atrybut informuje sieci szkieletowej usług, że usługa jest oparta na niezależnej aplikacji, więc wszystkie sieci szkieletowej usług musi zrobić, to uruchomić go jako proces i monitorować jego kondycję.

#### <a name="update-codepackage"></a>Aktualizacja pakietu kodu
Element CodePackage określa lokalizację (i wersję) kodu usługi.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Element `Name` jest używany do określenia nazwy katalogu w pakiecie aplikacji, który zawiera kod usługi. `CodePackage`również ma `version` atrybut. Może to służyć do określenia wersji kodu, a także potencjalnie może służyć do uaktualniania kodu usługi przy użyciu infrastruktury zarządzania cyklem życia aplikacji w sieci szkieletowej usług.

#### <a name="optional-update-setupentrypoint"></a>Opcjonalnie: Aktualizacja SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Element SetupEntryPoint służy do określania dowolnego pliku wykonywalnego lub wsadowego, który powinien zostać wykonany przed uruchomieniem kodu usługi. Jest to krok opcjonalny, więc nie musi być uwzględniony, jeśli nie jest wymagana inicjalizacja. SetupEntryPoint jest wykonywany przy każdym ponownym uruchomieniu usługi.

Istnieje tylko jeden SetupEntryPoint, więc skrypty instalacyjne muszą być zgrupowane w jednym pliku wsadowym, jeśli konfiguracja aplikacji wymaga wielu skryptów. SetupEntryPoint może wykonywać dowolny typ pliku: pliki wykonywalne, pliki wsadowe i polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu SetupEntryPoint](service-fabric-application-runas-security.md).

W poprzednim przykładzie SetupEntryPoint uruchamia plik `LaunchConfig.cmd` wsadowy o `scripts` nazwie, który znajduje się w podkatalogu katalogu kodu (przy założeniu, że element WorkingFolder jest ustawiony na CodeBase).

#### <a name="update-entrypoint"></a>Aktualizuj punkt wejścia

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Element `EntryPoint` w pliku manifestu usługi służy do określenia sposobu uruchamiania usługi.

Element `ExeHost` określa plik wykonywalny (i argumenty), które powinny być używane do uruchomienia usługi. Opcjonalnie można dodać `IsExternalExecutable="true"` atrybut, `ExeHost` aby wskazać, że program jest zewnętrznym plikiem wykonywalnym poza pakietem kodu. Na przykład `<ExeHost IsExternalExecutable="true">`.

* `Program`określa nazwę pliku wykonywalnego, który powinien uruchomić usługę.
* `Arguments`określa argumenty, które powinny zostać przekazane do pliku wykonywalnego. Może to być lista parametrów z argumentami.
* `WorkingFolder`określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
  * `CodeBase`określa, że katalog roboczy zostanie ustawiony na katalog kodu w`Code` pakiecie aplikacji (katalog w poprzedniej strukturze plików).
  * `CodePackage`określa, że katalog roboczy zostanie ustawiony na katalog główny`GuestService1Pkg` pakietu aplikacji (w poprzedniej strukturze plików).
    * `Work`określa, że pliki są umieszczane w podkatalogu zwanym pracą.

WorkingFolder jest przydatne do ustawienia poprawnego katalogu roboczego, dzięki czemu ścieżki względne mogą być używane przez aplikację lub skrypty inicjowania.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Aktualizowanie punktów końcowych i rejestrowanie się w usłudze nazewnictwa w celu komunikacji

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

W poprzednim przykładzie `Endpoint` element określa punkty końcowe, które aplikacja może nasłuchiwanie. W tym przykładzie aplikacja Node.js nasłuchuje na http na porcie 3000.

Ponadto można poprosić sieci szkieletowej usług, aby opublikować ten punkt końcowy do usługi nazewnictwa, dzięki czemu inne usługi można odnajdywać adres punktu końcowego do tej usługi. Dzięki temu można komunikować się między usługami, które są plikami wykonywalnymi gościa.
Opublikowany adres punktu końcowego jest `UriScheme://IPAddressOrFQDN:Port/PathSuffix`w formularzu . `UriScheme`i `PathSuffix` są opcjonalnymi atrybutami. `IPAddressOrFQDN`jest adresem IP lub w pełni kwalifikowaną nazwą domeny węzła, na który zostanie umieszczony ten plik wykonywalny, i jest obliczana dla Ciebie.

W poniższym przykładzie po wdrożeniu usługi w Eksploratorze `http://10.1.4.92:3000/myapp/` sieci szkieletowej usług zostanie wyświetlony punkt końcowy podobny do opublikowanego dla wystąpienia usługi. Lub jeśli jest to maszyna `http://localhost:3000/myapp/`lokalna, widzisz .

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

Tych adresów z [odwrotnym serwerem proxy](service-fabric-reverseproxy.md) można używać do komunikowania się między usługami.

### <a name="edit-the-application-manifest-file"></a>Edytowanie pliku manifestu aplikacji

Po skonfigurowaniu `Servicemanifest.xml` pliku należy wprowadzić pewne zmiany w `ApplicationManifest.xml` pliku, aby upewnić się, że używany jest poprawny typ i nazwa usługi.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>UsługaManifestImport

W `ServiceManifestImport` elemencie można określić jedną lub więcej usług, które mają zostać uwzględnione w aplikacji. Usługi są przywoływanie z `ServiceManifestName`, który określa `ServiceManifest.xml` nazwę katalogu, w którym znajduje się plik.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurowanie rejestrowania

W przypadku plików wykonywalnych gości warto zobaczyć dzienniki konsoli, aby dowiedzieć się, czy skrypty aplikacji i konfiguracji wykazują błędy.
Przekierowanie konsoli można skonfigurować `ServiceManifest.xml` w `ConsoleRedirection` pliku za pomocą elementu.

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, która jest wdrażana w produkcji, ponieważ może to mieć wpływ na proces pracy awaryjnej aplikacji. *Użyj* tego tylko do lokalnych celów rozwoju i debugowania.  
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

`ConsoleRedirection`może służyć do przekierowania danych wyjściowych konsoli (zarówno stdout, jak i stderr) do katalogu roboczego. Zapewnia to możliwość sprawdzenia, czy nie ma żadnych błędów podczas konfigurowania lub wykonywania aplikacji w klastrze sieci szkieletowej usług.

`FileRetentionCount`określa, ile plików jest zapisywanych w katalogu roboczym. Wartość 5, na przykład oznacza, że pliki dziennika dla poprzednich pięciu wykonań są przechowywane w katalogu roboczym.

`FileMaxSizeInKb`określa maksymalny rozmiar plików dziennika.

Pliki dziennika są zapisywane w jednym z katalogów roboczych usługi. Aby ustalić, gdzie znajdują się pliki, użyj Eksploratora sieci szkieletowej usług, aby określić, który węzeł jest uruchomiony na usłudze i który katalog roboczy jest używany. Ten proces jest omówiony w dalszej części tego artykułu.

## <a name="deployment"></a>Wdrożenie

Ostatnim krokiem jest [wdrożenie aplikacji](service-fabric-deploy-remove-applications.md). W poniższym skrypcie programu PowerShell pokazano, jak wdrożyć aplikację w lokalnym klastrze deweloperów i uruchomić nową usługę sieci szkieletowej usług.

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
> [Skompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów, jeśli pakiet jest duży lub zawiera wiele plików. Czytaj więcej [tutaj](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Usługę sieci szkieletowej usług można wdrożyć w różnych "konfiguracjach". Na przykład można go wdrożyć jako pojedyncze lub wiele wystąpień lub można go wdrożyć w taki sposób, że istnieje jedno wystąpienie usługi w każdym węźle klastra sieci szkieletowej usług.

Parametr `InstanceCount` `New-ServiceFabricService` polecenia cmdlet służy do określenia, ile wystąpień usługi powinny być uruchamiane w klastrze sieci szkieletowej usług. Można ustawić `InstanceCount` wartość, w zależności od typu aplikacji, które wdrażasz. Dwa najbardziej typowe scenariusze to:

* `InstanceCount = "1"`. W takim przypadku tylko jedno wystąpienie usługi jest wdrażany w klastrze. Harmonogram sieci szkieletowej usług określa, w którym węźle usługa ma zostać wdrożona.
* `InstanceCount ="-1"`. W takim przypadku jedno wystąpienie usługi jest wdrażane w każdym węźle w klastrze sieci szkieletowej usług. Wynik jest posiadanie jednego (i tylko jedno) wystąpienie usługi dla każdego węzła w klastrze.

Jest to przydatna konfiguracja dla aplikacji front-end (na przykład punktu końcowego REST), ponieważ aplikacje klienckie muszą "połączyć się" z dowolnym węzłem w klastrze, aby użyć punktu końcowego. Tej konfiguracji można również użyć, gdy na przykład wszystkie węzły klastra sieci szkieletowej usług są połączone z modułem równoważenia obciążenia. Ruch klienta może być następnie rozproszony w usłudze uruchomionej we wszystkich węzłach w klastrze.

## <a name="check-your-running-application"></a>Sprawdź uruchomię aplikację
W Eksploratorze sieci szkieletowej usług zidentyfikuj węzeł, w którym usługa jest uruchomiona. W tym przykładzie działa na Node1:

![Węzeł, w którym usługa jest uruchomiona](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Jeśli przejdziesz do węzła i przejdziesz do aplikacji, zobaczysz istotne informacje o węźle, w tym jego lokalizację na dysku.

![Lokalizacja na dysku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Jeśli przeglądasz katalog przy użyciu Eksploratora serwera, możesz znaleźć katalog roboczy i folder dziennika usługi, jak pokazano na poniższym zrzucie ekranu:

![Lokalizacja dziennika](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak spakować plik wykonywalny gościa i wdrożyć go w sieci szkieletowej usług. Zobacz następujące artykuły, aby uzyskać powiązane informacje i zadania.

* [Przykład do pakowania i wdrażania pliku wykonywalnego gościa,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)w tym link do wstępnej wersji narzędzia do pakowania
* [Próbka dwóch plików wykonywalnych gościa (C# i nodejs) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu rest](https://github.com/Azure-Samples/service-fabric-containers)
* [Tworzenie pierwszej aplikacji sieci szkieletowej usług przy użyciu programu Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
