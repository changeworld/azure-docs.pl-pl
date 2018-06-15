---
title: Uaktualnianie aplikacji sieci szkieletowej usług za pomocą programu PowerShell | Dokumentacja firmy Microsoft
description: Ten artykuł przeprowadzi Cię przez proces wdrażania aplikacji usługi Service Fabric, zmiana kodu i wprowadza uaktualnienia przy użyciu programu PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 44f4dc3a9c876e383a6e4df8ef5f467f2b93eaa9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205488"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Uaktualnianie aplikacji sieci szkieletowej usług za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Program Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Najczęściej używane i monitorowanych uaktualnienia stopniowego jest zalecane podejście do uaktualnienia.  Sieć szkieletowa usług Azure monitoruje kondycję aplikacji uaktualniany na podstawie zestawu zasad dotyczących kondycji. Po uaktualnieniu domeny aktualizacji (UD) usługi sieć szkieletowa ocenia kondycję aplikacji i przechodzi do następnej domeny aktualizacji lub niepowodzenia uaktualniania, w zależności od zasad dotyczących kondycji.

Uaktualnienie monitorowanej aplikacji mogą być wykonywane przy użyciu zarządzanym lub macierzystym interfejsów API środowiska PowerShell i REST. Aby uzyskać instrukcje dotyczące wykonywania uaktualnienia przy użyciu programu Visual Studio, zobacz [uaktualniania aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md).

Stopniowe monitorowania sieci szkieletowej usług administrator aplikacji można skonfigurować zasad oceny kondycji korzystającą z sieci szkieletowej usług w celu ustalenia, czy aplikacja jest w dobrej kondycji. Ponadto administrator może skonfigurować akcji do wykonania, gdy oceny kondycji nie powiodło się (na przykład podczas automatycznego wycofania.) Ta sekcja przeprowadzi Cię przez monitorowanych uaktualnienia dla jednego z przykłady zestawu SDK, które korzysta z programu PowerShell. Następujące Microsoft Virtual Academy wideo również przeprowadzi Cię przez kolejne uaktualnienia aplikacji: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1: Tworzenie i wdrażanie próbka obiektów Visual
Tworzenie i publikowanie aplikacji, klikając prawym przyciskiem myszy projekt aplikacji **VisualObjectsApplication,** i wybierając **publikowania** polecenia.  Aby uzyskać więcej informacji, zobacz [samouczek uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade-tutorial.md).  Alternatywnie można użyć programu PowerShell do wdrożenia aplikacji.

> [!NOTE]
> Przed jakichkolwiek poleceń sieci szkieletowej usług mogą być używane w programu PowerShell, należy najpierw Połącz się z klastrem przy użyciu `Connect-ServiceFabricCluster` polecenia cmdlet. Podobnie zakłada się, że klaster ma już został skonfigurowany na komputerze lokalnym. Zapoznaj się z artykułem na [konfigurowania środowiska deweloperskiego sieci szkieletowej usług](service-fabric-get-started.md).
> 
> 

Po utworzeniu projektu programu Visual Studio, możesz użyć polecenia środowiska PowerShell [ServiceFabricApplicationPackage kopiowania](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) Aby skopiować pakiet aplikacji do magazynu ImageStore. Jeśli chcesz sprawdzić lokalnie pakietu aplikacji, użyj [ServiceFabricApplicationPackage testu](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) polecenia cmdlet. Następnym krokiem jest można zarejestrować aplikacji przy użyciu środowiska uruchomieniowego platformy Service Fabric [ServiceFabricApplicationType rejestru](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) polecenia cmdlet. Następny krok się uruchomić wystąpienie aplikacji przy użyciu [ServiceFabricApplication nowy](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) polecenia cmdlet.  Te trzy kroki są podobne do przy użyciu **Wdróż** element menu w programie Visual Studio.  Po ukończeniu inicjowania obsługi należy wyczyścić pakiet aplikacji skopiowanych z magazynu obrazów w celu zmniejszenia używanych zasobów.  Jeśli aplikacja nie jest już wymagane, należy wyrejestrować z tego samego powodu. Zobacz [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) Aby uzyskać więcej informacji.

Teraz, można użyć [Service Fabric Explorer, aby wyświetlić klastra i aplikacji](service-fabric-visualizing-your-cluster.md). Aplikacja ma usługi sieci web, który może zostać przesłane do programu Internet Explorer, wpisując [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) na pasku adresu.  Niektóre obiekty visual przestawne przenoszenia na ekranie powinna zostać wyświetlona.  Ponadto można użyć [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) do sprawdzania stanu aplikacji.

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Zaktualizuj próbka obiektów Visual
Można zauważyć, że przy użyciu wersji, który został wdrożony w kroku 1, obiekt visual nie Obróć. Umożliwia uaktualnienie tej aplikacji na taki, gdzie obiekty widoczne również obracać.

Wybierz projekt VisualObjects.ActorService w ramach rozwiązania VisualObjects, a następnie otwórz plik StatefulVisualObjectActor.cs. W tym pliku, przejdź do metody `MoveObject`, komentarz `this.State.Move()`i Usuń komentarz `this.State.Move(true)`. Ta zmiana obraca obiekty po uaktualnieniu usługi.

Ponadto należy zaktualizować *ServiceManifest.xml* pliku (w ramach elementu PackageRoot) projektu **VisualObjects.ActorService**. Aktualizacja *elementu CodePackage* i wersja 2.0 oraz odpowiednich wierszy w *ServiceManifest.xml* pliku.
Można użyć programu Visual Studio *edytować pliki manifestu* opcję po kliknięciu prawym przyciskiem myszy rozwiązanie, aby wprowadzić zmiany pliku manifestu.

Po wprowadzeniu zmian plik manifestu powinien wyglądać następująco (wyróżniony części Pokaż zmiany):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Teraz *ApplicationManifest.xml* pliku (w obszarze **VisualObjects** projektu w obszarze **VisualObjects** rozwiązania) zostanie zaktualizowany do wersji 2.0 **VisualObjects.ActorService** projektu. Ponadto wersja aplikacji zostało zaktualizowane do 2.0.0.0 z 1.0.0.0. *ApplicationManifest.xml* powinno wyglądać podobnie do następującego fragmentu kodu:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Teraz, skompiluj projekt, wybierając tylko **ActorService** projektu, a następnie prawym przyciskiem myszy i wybierając **kompilacji** w programie Visual Studio. W przypadku wybrania **odbudowanie wszystkiego**, należy zaktualizować wersje dla wszystkich projektów, ponieważ kod może zostać zmieniona. Następnie umożliwia pakiet aplikacji zaktualizowane, klikając prawym przyciskiem myszy ***VisualObjectsApplication***, wybierając z Menu sieci szkieletowej usług i wybierając **pakietu**. Ta akcja powoduje utworzenie pakietu aplikacji, które można wdrożyć.  Zaktualizowano aplikacja jest gotowa do wdrożenia.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3: Wybierz zasady dotyczące kondycji i parametry uaktualnienia
Zapoznaj się z [parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesu uaktualniania](service-fabric-application-upgrade.md) można uzyskać dobrą znajomość różnych parametrów uaktualnienia, limity czasu i zastosować kryterium kondycji. W ramach tego przewodnika kryterium oceny kondycji usługi jest ustawioną wartość domyślną (i zalecana) wartości, które oznacza, że wszystkie usługi i wystąpień powinny być *dobrej kondycji* po uaktualnieniu.  

Jednak ta funkcja pozwala zwiększyć *HealthCheckStableDuration* do 60 sekund (tak, aby usługi są w dobrej kondycji dla co najmniej 20 sekund przed uaktualnienia przechodzi do następnej domeny aktualizacji).  Umożliwia również ustawić *UpgradeDomainTimeout* do 1200 sekund i *UpgradeTimeout* do 3000 sekund.

Ponadto umożliwia także ustawić *UpgradeFailureAction* można wycofać. Ta opcja wymaga sieci szkieletowej usług wycofać aplikację do poprzedniej wersji, w przypadku napotkania problemów podczas uaktualniania. W związku z tym podczas rozpoczynania uaktualniania (w kroku 4), są określone następujące parametry:

FailureAction = wycofywania

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4: Przygotowywanie aplikacji do uaktualnienia
Teraz aplikacji jest zbudowany i gotowa do uaktualnienia. Jeśli Otwórz okno programu PowerShell jako administrator, a typ [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), powinien pozwalają wiedzieli, że jest on typ aplikacji 1.0.0.0 **VisualObjects** który jest wdrażany.  

Pakiet aplikacji są przechowywane w następującej ścieżce względnej, gdzie nieskompresowane zestawu SDK usług sieci szkieletowej — *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Folder "Pakietu" powinien znajdować się w tym katalogu, w którym jest przechowywany pakiet aplikacji. Sprawdź sygnatury czasowe do zapewnienia, że jest ostatniej kompilacji (może być konieczne ścieżki odpowiednio zmodyfikuj również).

Teraz Skopiuj pakiet zaktualizowanej aplikacji do magazynu ImageStore sieci szkieletowej usług, które zostały (gdzie pakiety aplikacji są przechowywane przez usługi sieć szkieletowa). Parametr *ApplicationPackagePathInImageStore* informuje o tym, gdzie można znaleźć pakietu aplikacji sieci szkieletowej usług. Testujemy zaktualizowaną aplikację "VisualObjects\_V2" przy użyciu następującego polecenia (konieczne może być ponownie odpowiednio zmodyfikuj ścieżki).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Następnym krokiem jest zarejestrować tej aplikacji za pomocą usługi Service Fabric, która może zostać wykonana przy użyciu [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) polecenia:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Jeśli poprzednie polecenie nie powiedzie się, prawdopodobnie konieczność ponownej kompilacji wszystkich usług. Jak wspomniano w kroku 2, może być konieczne zaktualizuj swoją wersję usługi sieci Web.

Zalecane jest, usuń pakiet aplikacji, po pomyślnym zarejestrowaniu aplikacji.  Usunięcie pakietów aplikacji w sklepie obrazu zwolni zasoby systemowe.  Utrzymywanie pakietów aplikacji nieużywane korzysta z magazynu danych na dysku i prowadzi do problemów z wydajnością aplikacji.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5: Uruchom operację uaktualniania aplikacji
Teraz możemy wszystko jest gotowe do uruchamiania uaktualniania aplikacji przy użyciu [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) polecenia:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Nazwa aplikacji jest taka sama, jak zostało opisane w *ApplicationManifest.xml* pliku. Sieć szkieletowa usług używa tej nazwy do identyfikowania, która aplikacja jest wprowadzenie uaktualniony. Jeśli ustawisz limity czasu się zbyt krótki, możesz napotkać komunikat o błędzie stwierdzający, problem. Zobacz sekcję dotyczącą rozwiązywania problemów, lub Zwiększ limity czasu.

Teraz kontynuowane uaktualniania aplikacji, można monitorować za pomocą Eksploratora usługi sieć szkieletowa, lub za pomocą [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) polecenia programu PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Za kilka minut, stan, który uzyskano przy użyciu poprzedniego polecenia programu PowerShell, powinny prezentować, że wszystkie domeny aktualizacji zostały uaktualnione (ukończone). I powinien znajdować się, że uruchomiony obracanie visual obiektów w oknie przeglądarki!

Możesz spróbować uaktualniania z wersji 2 do wersji 3 lub w wersji 2 do wersji 1, ponieważ wykonywania. Przenoszenie z wersji 2 do wersji 1 jest traktowana jako uaktualnienia. Odtwarzanie z limitów czasu i zasady dotyczące kondycji z siebie uczynić z nich korzystać. W przypadku wdrażania klastra platformy Azure, parametry trzeba odpowiednio ustawione. Warto konserwatywnie Ustaw limity czasu.

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji za pomocą programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu Visual Studio.

Kontrolowanie sposobu uaktualnienia aplikacji przy użyciu [parametry uaktualnienia](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych przez poznanie [szeregowanie danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywania typowych problemów w uaktualnień aplikacji, korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

