---
title: Wdrażanie istniejącego pliku wykonywalnego na sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pakiet istniejącą aplikację jako gość pliku wykonywalnego, dlatego może on zostać wdrożony do klastra usługi sieć szkieletowa usług.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell
ms.openlocfilehash: 99d34d59bb9d55ff074d454fe4544917c4e91110
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205991"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Pakiet, a następnie wdrożyć istniejącego pliku wykonywalnego sieci szkieletowej usług
Podczas pakowania istniejącego pliku wykonywalnego jako [pliku wykonywalnego gościa](service-fabric-guest-executables-introduction.md), można wybrać jedną, aby użyć szablonu projektu programu Visual Studio lub [ręcznie utworzyć pakiet aplikacji](#manually). Za pomocą programu Visual Studio, struktura pakietu aplikacji i pliki manifestu są tworzone przez nowy szablon projektu dla Ciebie.

> [!TIP]
> Najprostszym sposobem pakietu Windows istniejącego pliku wykonywalnego do usługi jest użycie programu Visual Studio i w systemie Linux, aby użyć narzędzia Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Pakiet, a następnie wdrożyć istniejący plik wykonywalny za pomocą programu Visual Studio
Program Visual Studio udostępnia szablonu usługi Service Fabric ułatwiają wdrażanie pliku wykonywalnego gościa do klastra usługi sieć szkieletowa usług.

1. Wybierz **pliku** > **nowy projekt**i tworzenie aplikacji sieci szkieletowej usług.
2. Wybierz **pliku wykonywalnego gościa** jako szablonu usługi.
3. Kliknij przycisk **Przeglądaj** wybierz folder z pliku wykonywalnego i Wypełnij pozostałe parametry, aby utworzyć usługę.
   * *Zachowanie pakietu kodu*. Można ustawić tak, aby skopiować całą zawartość folderu do projektu programu Visual Studio, co jest przydatne, jeśli plik wykonywalny nie ulega zmianie. Jeśli plik wykonywalny, aby zmienić i mieć możliwość dynamicznie odebrania nowych kompilacji, możesz zamiast tego łącza do folderu. Podczas tworzenia projektu aplikacji w programie Visual Studio, można użyć połączonego folderów. Tego łącza z lokalizacji źródłowej, w ramach projektu, dzięki czemu można zaktualizować pliku wykonywalnego gościa miejsca docelowego źródła. Aktualizacje mogące spowodować staną się częścią pakietu aplikacji podczas kompilacji.
   * *Program* Określa plik wykonywalny, który powinien być wykonywany w celu uruchomienia usługi.
   * *Argumenty* Określa argumenty, które powinny być przekazywane do pliku wykonywalnego. Może być lista parametrów z argumentami.
   * *WorkingFolder* Określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
     * `CodeBase` Określa, że katalog roboczy ma można ustawić katalogu kodu w pakiecie aplikacji (`Code` katalog pokazywany w poprzednim struktury plików).
     * `CodePackage` Określa, że katalog roboczy ma być ustawiany na katalog główny pakietu aplikacji (`GuestService1Pkg` pokazano w poprzednim struktury plików).
     * `Work` Określa, czy pliki są umieszczane w podkatalogu o nazwie pracy.
4. Nadaj nazwę usłudze i kliknij przycisk **OK**.
5. Jeśli usługa wymaga punktu końcowego do komunikacji, protokół, port i typ można teraz dodać do pliku ServiceManifest.xml. Na przykład: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Można teraz za pomocą pakietu i opublikować akcji względem klastra lokalnego przez debugowanie rozwiązań w programie Visual Studio. Po wykonaniu tych czynności możesz opublikować aplikację do zdalnego klastra lub zaewidencjonować rozwiązanie do kontroli źródła.
7. Odczyt [Sprawdź aplikację usługi uruchomione](#check-your-running-application) aby zobaczyć sposób wyświetlania pliku wykonywalnego usługi gościa w narzędziu Service Fabric Explorer.

Na przykład wskazówki, zobacz [tworzenie pierwszej aplikacji pliku wykonywalnego gościa za pomocą programu Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Użyj narzędzia Yeoman do pakietu i wdrażanie istniejącego pliku wykonywalnego w systemie Linux

Procedura tworzenia i wdrażania Gość pliku wykonywalnego w systemie Linux jest taka sama jak wdrażanie aplikacji csharp lub java.

1. Na terminalu wpisz `yo azuresfguest`.
2. Nadaj nazwę aplikacji.
3. Nazwę usługi i podaj szczegóły tym ścieżka pliku wykonywalnego i parametrów, które muszą być wywoływane z.

Narzędzia yeoman tworzy pakiet aplikacji z odpowiednią aplikację i pliki manifestu wraz z instalowania i odinstalowywania skryptów.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ręcznie pakietów i wdrożyć istniejącego pliku wykonywalnego
Proces ręcznie pakowania pliku wykonywalnego gościa jest oparta na następujące ogólne czynności:

1. Utwórz strukturę katalogu pakietu.
2. Dodaj pliki kodu i konfiguracji aplikacji.
3. Edytuj plik manifestu usługi.
4. Przeprowadź edycję pliku manifestu aplikacji.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Utwórz strukturę katalogów pakietu
Możesz uruchomić tworzenie struktury katalogów, zgodnie z opisem w poprzedniej sekcji "Struktura pliku pakietu aplikacji".

### <a name="add-the-applications-code-and-configuration-files"></a>Dodawanie plików kodu i konfiguracji aplikacji
Po utworzeniu struktury katalogów, można dodać kod aplikacji i pliki konfiguracyjne w katalogach kod i konfiguracji. Można również utworzyć dodatkowe katalogi lub podkatalogi w obszarze katalogi kodu lub konfiguracji.

Sieć szkieletowa usług jest `xcopy` zawartości katalogu głównego aplikacji, więc nie ma wstępnie zdefiniowanych struktury do użycia innego niż tworzenie dwa katalogi top, kodu i ustawienia. (Można wybrać różne nazwy elementu, jeśli chcesz. Bardziej szczegółowe informacje są w następnej sekcji).

> [!NOTE]
> Upewnij się, czy zawiera wszystkie pliki i zależności przez aplikację. Sieć szkieletowa usług kopiuje zawartość pakietu aplikacji na wszystkich węzłach w klastrze, w którym będzie można wdrożyć aplikacji usługi. Pakiet powinien zawierać żadnego kodu, który ma być uruchamiana aplikacja. Zakłada się, że zależności są już zainstalowane.
>
>

### <a name="edit-the-service-manifest-file"></a>Edytuj plik manifestu usługi
Następnym krokiem jest do edycji pliku manifestu usługi, aby uwzględnić następujące informacje:

* Nazwa typu usługi. To jest identyfikator używaną do identyfikowania usługi sieć szkieletowa usług.
* Polecenie używane do uruchamiania aplikacji (w elemencie ExeHost).
* Skrypt, który musi zostać uruchomione, aby skonfigurować aplikację (SetupEntrypoint).

Poniżej przedstawiono przykład `ServiceManifest.xml` pliku:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Poniższe sekcje zapoznać się z różnych części pliku, który należy zaktualizować.

#### <a name="update-servicetypes"></a>Serivcetype aktualizacji
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Można wybrać dowolną nazwę, która ma `ServiceTypeName`. Wartość jest używana w `ApplicationManifest.xml` pliku do identyfikowania usługi.
* Określ `UseImplicitHost="true"`. Ten atrybut informuje usługi Service Fabric, że usługa jest oparta na niezależne aplikacji, tak aby wszystkie potrzeby sieci szkieletowej usług w celu uruchomienia go jako proces i monitorowanie kondycji.

#### <a name="update-codepackage"></a>Aktualizowanie elementu CodePackage
Element elementu CodePackage Określa lokalizację (i wersji) kodu usługi.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` Elementu służy do określenia nazwy katalogu w pakiecie aplikacji, który zawiera kod usługi. `CodePackage` ma również `version` atrybutu. Może służyć do określania wersji kodu i może również służyć do uaktualniania kodu usługi za pomocą infrastruktury zarządzania cyklem życia aplikacji w sieci szkieletowej usług.

#### <a name="optional-update-setupentrypoint"></a>Opcjonalnie: SetupEntrypoint aktualizacji
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint element służy do określania pliku wykonywalnego lub wsadowe pliku, który ma być wykonany przed uruchomieniem kodu usługi. Krok opcjonalny, jest więc nie musi być uwzględniony, jeśli nie wymagają inicjalizacji nie istnieje. Element SetupEntryPoint jest wykonywane przy każdym ponownym uruchomieniu usługi.

Istnieje tylko jeden element SetupEntryPoint, więc skryptów instalacji konieczne zgrupowane w pliku wsadowym pojedynczego, jeśli instalacja aplikacji wymaga wielu skryptów. Element SetupEntryPoint można wykonywać dowolny typ pliku: pliki wykonywalne, pliki wsadowe i poleceń cmdlet programu PowerShell. Aby uzyskać więcej informacji, zobacz [skonfigurować SetupEntryPoint](service-fabric-application-runas-security.md).

W powyższym przykładzie element SetupEntryPoint uruchamia plik wsadowy o nazwie `LaunchConfig.cmd` znajdującej się w `scripts` podkatalogu w katalogu kodu (zakładając, że WorkingFolder element jest ustawiony na wartość CodeBase).

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

`EntryPoint` Elementu w pliku manifestu usługi służy do określenia sposobu uruchamiania usługi. `ExeHost` Element Określa plik wykonywalny (i argumenty) należy uruchomić usługę.

* `Program` Określa nazwę pliku wykonywalnego, który należy uruchomić usługę.
* `Arguments` Określa argumenty, które powinny być przekazywane do pliku wykonywalnego. Może być lista parametrów z argumentami.
* `WorkingFolder` Określa katalog roboczy dla procesu, który ma zostać uruchomiony. Można określić trzy wartości:
  * `CodeBase` Określa, że katalog roboczy ma można ustawić katalogu kodu w pakiecie aplikacji (`Code` katalogu w poprzednim struktury plików).
  * `CodePackage` Określa, że katalog roboczy ma być ustawiany na katalog główny pakietu aplikacji (`GuestService1Pkg` w poprzednim struktury plików).
    * `Work` Określa, czy pliki są umieszczane w podkatalogu o nazwie pracy.

WorkingFolder przydaje się do ustawienia poprawny katalog roboczy, aby ścieżek względnych mogą być używane przez skrypty aplikacji lub inicjowania.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Zaktualizuj punktów końcowych i zarejestrować w usłudze nazewnictwa do komunikacji
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
W powyższym przykładzie `Endpoint` element określa punkty końcowe, które można nasłuchiwać aplikacji. W tym przykładzie aplikacji Node.js nasłuchuje http na porcie 3000.

Ponadto można zadawać sieci szkieletowej usług, aby opublikować ten punkt końcowy Naming Service, inne usługi można odnaleźć adres punktu końcowego do tej usługi. Dzięki temu można mieć możliwości komunikacji między usługami, które są pliki wykonywalne gościa.
Adres punktu końcowego opublikowanych ma postać `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` i `PathSuffix` są opcjonalne atrybuty. `IPAddressOrFQDN` jest adresem IP lub nazwy FQDN węzła dotyczącymi pobiera tego pliku wykonywalnego, a jest obliczana automatycznie.

W poniższym przykładzie, po wdrożeniu usługi Service Fabric Explorer jest widoczny podobne do punktu końcowego `http://10.1.4.92:3000/myapp/` opublikowany dla wystąpienia usługi. Lub jeśli jest to komputer lokalny, zobacz `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Korzystając z tych adresów z [odwrotny serwer proxy](service-fabric-reverseproxy.md) do komunikacji między usługami.

### <a name="edit-the-application-manifest-file"></a>Edytowanie pliku manifestu aplikacji
Po skonfigurowaniu `Servicemanifest.xml` pliku, należy wprowadzić kilka zmian, aby `ApplicationManifest.xml` plik, aby zapewnić prawidłowe usługi typem i nazwą są używane.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
W `ServiceManifestImport` elementu, można określić jedną lub kilka usług, które mają zostać uwzględnione w aplikacji. Usługi są przywoływane z `ServiceManifestName`, który określa nazwę katalogu, w którym `ServiceManifest.xml` znajduje się plik.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurowanie rejestrowania
Dla plików wykonywalnych gościa warto można wyświetlać dzienniki konsoli, aby dowiedzieć się, jeśli skryptów aplikacji i konfiguracji wyświetlać żadnych błędów.
Przekierowywanie konsoli można skonfigurować w `ServiceManifest.xml` plik za pomocą `ConsoleRedirection` elementu.

> [!WARNING]
> Nigdy nie używaj zasad przekierowania konsoli w aplikacji, które zostało wdrożone w środowisku produkcyjnym, ponieważ może to wpłynąć na pracę awaryjną aplikacji. *Tylko* użyć tej funkcji dla rozwoju lokalnych i debugowania.  
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

`ConsoleRedirection` można przekierować dane wyjściowe konsoli (stdout i stderr) do katalogu roboczego. To pozwala, aby sprawdzić, czy podczas instalacji lub wykonanie aplikacji w klastrze usługi sieć szkieletowa nie ma żadnych błędów.

`FileRetentionCount` Określa, ile plików są zapisywane w katalogu roboczym. Na przykład wartość 5, oznacza, że pliki dziennika dla poprzedniego wykonaniami pięć są przechowywane w katalogu roboczym.

`FileMaxSizeInKb` Określa maksymalny rozmiar plików dziennika.

Pliki dziennika są zapisywane w jednym z katalogów roboczych usługi. Aby określić, gdzie znajdują się pliki, użyj Eksploratora usługi sieć szkieletowa ustalenie, który węzeł usługa jest uruchomiona na i katalog roboczy, który jest używany. Ten proces jest uwzględnione w dalszej części tego artykułu.

## <a name="deployment"></a>Wdrożenie
Ostatnim krokiem jest [wdrożyć aplikację](service-fabric-deploy-remove-applications.md). Poniższy skrypt programu PowerShell pokazuje, jak wdrożyć aplikację na lokalny klaster projektowy oraz uruchamianie nowej usługi sieć szkieletowa usług.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Kompresuj pakietu](service-fabric-package-apps.md#compress-a-package) przed rozpoczęciem kopiowania do magazynu w obrazie, jeśli pakiet jest duży lub ma wiele plików. Dowiedz się więcej [tutaj](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Usługa sieci szkieletowej usług można wdrożyć w różnych "konfiguracji." Na przykład można wdrożyć jako jednego lub wielu wystąpień lub mogą być wdrażane w taki sposób, że istnieje jedno wystąpienie usługi, w każdym węźle klastra sieci szkieletowej usług.

`InstanceCount` Parametr `New-ServiceFabricService` polecenia cmdlet jest używana do określania, jak wiele wystąpień usługi powinna być uruchamiana w klastrze usługi sieć szkieletowa usług. Można ustawić `InstanceCount` wartość, w zależności od typu aplikacji, w której wdrażasz. Dwa najbardziej typowe scenariusze są następujące:

* `InstanceCount = "1"`. W takim przypadku tylko jedno wystąpienie usługi zostało wdrożone w klastrze. Harmonogram usługi sieć szkieletowa Określa, który węzeł ma zostać wdrożony w usługi.
* `InstanceCount ="-1"`. W takim przypadku jedno wystąpienie usługi jest wdrażana na każdym węźle w klastrze usługi sieć szkieletowa usług. Wynik ma jeden (i tylko jeden) wystąpienia usługi dla każdego węzła w klastrze.

Jest to przydatne Konfiguracja frontonu aplikacji (na przykład punkt końcowy REST), ponieważ aplikacje klienckie muszą "Połącz" dla każdego z węzłów w klastrze, aby użyć punktu końcowego. Tę konfigurację można również gdy na przykład wszystkie węzły klastra sieci szkieletowej usług są połączone z modułem równoważenia obciążenia. Ruch klientów następnie mogą być rozproszone na usługę, która jest uruchomiona na wszystkich węzłach w klastrze.

## <a name="check-your-running-application"></a>Sprawdź uruchomionej aplikacji
W narzędziu Service Fabric Explorer Zidentyfikuj węzła, w którym usługa jest uruchomiona. W tym przykładzie jest uruchamiany na Node1:

![Węzeł, na którym jest uruchomiona usługa](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Przejdź do węzła, przejdź do aplikacji, zapoznaj się z informacjami węzła istotne, łącznie z tej lokalizacji na dysku.

![Miejsce na dysku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Przejdź do katalogu za pomocą Eksploratora serwera można znaleźć katalogu roboczego i folder dziennika usługi, jak pokazano na poniższym zrzucie ekranu: 

![Lokalizacja dziennika](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule zapoznaniu pakietu pliku wykonywalnego gościa i wdrożyć ją sieci szkieletowej usług. Zobacz następujące artykuły dla zadania i powiązane informacje.

* [Przykład dla pakowanie i wdrażanie pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), łącznie z łączem do wstępną narzędzia pakowania
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikacji za pośrednictwem usługi nazw za pomocą usługi REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Wdrażanie wielu aplikacji wykonywalnych gości](service-fabric-deploy-multiple-apps.md)
* [Tworzenie pierwszej aplikacji sieci szkieletowej usług za pomocą programu Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
