---
title: Uaktualnianie aplikacji usługi Service Fabric przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono proces wdrażania aplikacji usługi Service Fabric, zmiana kodu i wprowadza uaktualnienie przy użyciu programu PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: c8f56897380bc3108cb979d9d15e7dbd0a329064
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614370"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Uaktualnianie aplikacji usługi Service Fabric przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Program Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Najczęściej używane i uaktualnienia zaleca monitorowanych uaktualnienia stopniowego.  Usługa Azure Service Fabric monitoruje kondycję aplikacji, trwa uaktualnianie oparte na zestawie zasad dotyczących kondycji. Po uaktualnieniu domena aktualizacji (UD) usługi Service Fabric ocenia kondycję aplikacji i przechodzi do następnej domeny aktualizacji albo kończy się niepowodzeniem uaktualnienia w zależności od zasad dotyczących kondycji.

Uaktualnienie monitorowanej aplikacji mogą być wykonywane przy użyciu zarządzane lub natywne interfejsy API, PowerShell, interfejsu wiersza polecenia platformy Azure, Java lub REST. Aby uzyskać instrukcje dotyczące wykonywania uaktualnienia przy użyciu programu Visual Studio, zobacz [uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md).

Za pomocą usługi Service Fabric monitorowane stopniowe administrator aplikacji można skonfigurować zasady oceny kondycji, korzystającą z usługi Service Fabric w celu ustalenia, czy aplikacja jest w dobrej kondycji. Ponadto administrator może skonfigurować akcję do wykonania, kiedy nie powiedzie się Ocena kondycji (na przykład podczas automatycznego wycofywania.) W tej sekcji przedstawiono uaktualniania monitorowanych przy użyciu jednego z przykładowych zestawach SDK, które używa programu PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1: Tworzenie i wdrażanie przykładu obiektów wizualnych
Tworzenie i publikowanie aplikacji, klikając prawym przyciskiem myszy projekt aplikacji **VisualObjectsApplication,** i wybierając polecenie **Publikuj** polecenia.  Aby uzyskać więcej informacji, zobacz [samouczek dotyczący uaktualniania aplikacji usługi Service Fabric](service-fabric-application-upgrade-tutorial.md).  Alternatywnie można użyć programu PowerShell do wdrażania aplikacji.

> [!NOTE]
> Przed dowolnego polecenia usługi Service Fabric mogą być używane w programie PowerShell, należy najpierw Połącz się z klastrem przy użyciu `Connect-ServiceFabricCluster` polecenia cmdlet. Podobnie zakłada się, że klaster ma już został skonfigurowany na komputerze lokalnym. Zobacz artykuł [Konfigurowanie środowiska deweloperskiego usługi Service Fabric](service-fabric-get-started.md).
> 
> 

Po utworzeniu projektu w programie Visual Studio, można użyć polecenia programu PowerShell [ServiceFabricApplicationPackage kopiowania](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) można skopiować pakiet aplikacji do ImageStore. Jeśli chcesz zweryfikować pakietu aplikacji lokalnie, użyj [ServiceFabricApplicationPackage testu](/powershell/module/servicefabric/test-servicefabricapplicationpackage) polecenia cmdlet. Następnym krokiem jest, aby zarejestrować aplikację do środowiska uruchomieniowego usługi Service Fabric przy użyciu [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype) polecenia cmdlet. Następny krok to można uruchomić wystąpienia aplikacji przy użyciu [New ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) polecenia cmdlet.  Te trzy kroki są analogiczne do korzystania z **Wdróż** element menu w programie Visual Studio.  Po ukończeniu inicjowania obsługi, należy wyczyścić pakiet aplikacji skopiowane z magazynu obrazów w celu zmniejszenia zasoby używane.  Jeśli aplikacja nie jest już wymagane, należy go wyrejestrować z tego samego powodu. Zobacz [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) Aby uzyskać więcej informacji.

Teraz, możesz użyć [narzędzia Service Fabric Explorer, aby wyświetlić klaster i aplikacja](service-fabric-visualizing-your-cluster.md). Aplikacja ma usługi sieci web, którego może być przejście w przeglądarce Internet Explorer, wpisując [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) na pasku adresu.  Niektóre obiekty visual przestawne poruszanie się na ekranie powinna być widoczna.  Ponadto można użyć [Get ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) można sprawdzić stanu aplikacji.

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizacja przykładu obiektów wizualnych
Może się okazać, że z wersją, która została wdrożona w kroku 1, obiektów wizualnych nie Obróć. Teraz uaktualnić tę aplikację do jednego gdzie również obrócić obiektów wizualnych.

Wybierz projekt VisualObjects.ActorService w ramach rozwiązania VisualObjects, a następnie otwórz plik StatefulVisualObjectActor.cs. W tym pliku, przejdź do metody `MoveObject`, komentarz `this.State.Move()`i usuń znaczniki komentarza `this.State.Move(true)`. Ta zmiana obraca obiekty po uaktualnieniu usługi.

Musimy też zaktualizować *ServiceManifest.xml* pliku projektu (w obszarze PackageRoot) **VisualObjects.ActorService**. Aktualizacja *CodePackage* i wersji usługi w wersji 2.0 i odpowiednie wiersze w *ServiceManifest.xml* pliku.
Możesz użyć programu Visual Studio *edytować plik manifestu* opcję po użytkownik kliknij prawym przyciskiem myszy rozwiązanie, aby wprowadzić zmiany w pliku manifestu.

Po dokonaniu zmian manifestu powinien wyglądać podobnie do następującej (wyróżnione fragmenty pokazują zmiany):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Teraz *ApplicationManifest.xml* pliku (znajdującym się **VisualObjects** projekt **VisualObjects** rozwiązania) zostanie zaktualizowany do wersji 2.0 **VisualObjects.ActorService** projektu. Ponadto aplikacji został zaktualizowany do wersji 2.0.0.0 lub nowszej z 1.0.0.0. *ApplicationManifest.xml* powinno wyglądać podobnie do następującego fragmentu kodu:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Teraz, skompiluj projekt, wybierając tylko **ActorService** projektu, a następnie kliknij prawym przyciskiem myszy i wybierając **kompilacji** w programie Visual Studio. Jeśli wybierzesz **ponowna kompilacja**, należy zaktualizować wersje dla wszystkich projektów, ponieważ kod może zostać zmieniona. Następnie możemy pakietu zaktualizowaną aplikację, klikając prawym przyciskiem myszy ***VisualObjectsApplication***, wybierając z Menu usługi Service Fabric i wybierając **pakietu**. Ta akcja powoduje utworzenie pakietu aplikacji, które można wdrożyć.  Zaktualizowano aplikacja jest gotowa do wdrożenia.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3:  Podejmij decyzję zasady dotyczące kondycji i parametry uaktualniania
Zapoznaj się z [parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesu uaktualniania](service-fabric-application-upgrade.md) uzyskać dobre zrozumienie różnych parametry uaktualniania, limity czasu i kondycji kryterium stosowane. W tym przewodniku kryterium oceny kondycji usługi jest wartość domyślną (i zalecana) wartości, które oznacza, że wszystkie usługi i wystąpień powinna być *dobrej kondycji* po uaktualnieniu.  

Jednak umożliwia zwiększenie *HealthCheckStableDuration* do 180 sekund (tak, aby te usługi są w dobrej kondycji przez co najmniej 120 sekund przed uaktualnienia przechodzi do następnej domeny aktualizacji).  Załóżmy również ustawić *UpgradeDomainTimeout* jako 1200 sekund i *UpgradeTimeout* do 3000 sekund.

Na koniec spróbujmy również ustawić *UpgradeFailureAction* do wycofania. Ta opcja wymaga usługi Service Fabric wycofać aplikację do poprzedniej wersji, w przypadku napotkania problemów podczas uaktualniania. W związku z tym podczas uruchamiania (odbywa się w kroku 4), są określone następujące parametry:

FailureAction = wycofywania

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4: Przygotowywanie aplikacji dla uaktualnienie
Teraz aplikacja jest utworzone i gotowe do uaktualnienia. Jeśli otworzysz okno programu PowerShell jako administrator i wpisz [Get ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), powinien pozwalają dowiedzieć się, czy jest to typ aplikacji 1.0.0.0 **VisualObjects** , wdrożeniu.  

Pakiet aplikacji znajduje się w następującej ścieżce względnej, gdzie nieskompresowane zestawu SDK usługi Service Fabric - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Folderu "Package" powinien znajdować się w tym katalogu, w którym jest przechowywany pakiet aplikacji. Sprawdź sygnatury czasowe do zapewnienia, że jest najnowszą kompilację (konieczne może się okazać zmodyfikowanie ścieżki odpowiednio także).

Teraz możemy skopiować pakiet zaktualizowaną aplikację do ImageStore sieci szkieletowej usługi (gdzie pakiety aplikacji są przechowywane przez usługę Service Fabric). Parametr *ApplicationPackagePathInImageStore* informuje usługi Service Fabric, w którym można znaleźć pakietu aplikacji. Umieściliśmy zaktualizowaną aplikację "VisualObjects\_V2" za pomocą następującego polecenia (może być konieczne ponownie odpowiednio zmodyfikować ścieżki).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Następnym krokiem jest zarejestrowanie tej aplikacji usługi Service Fabric, które mogą być wykonywane przy użyciu [ServiceFabricApplicationType rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) polecenia:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Jeśli poprzednie polecenie nie powiedzie się, prawdopodobnie konieczne odbudowanie wszystkich usług. Jak wspomniano wcześniej w kroku 2, może być konieczne zaktualizuj swoją wersję usługi sieci Web.

Zalecane jest, usuń pakiet aplikacji, po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji w sklepie obraz zwolnienie zasobów systemowych.  Utrzymywanie pakiety aplikacji nieużywane wykorzystuje Magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5. Uruchom operację uaktualniania aplikacji
Teraz jesteśmy gotowi do rozpoczęcia uaktualnienia aplikacji za pomocą [Start ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) polecenia:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Nazwa aplikacji jest taki sam, jak zostało opisane w *ApplicationManifest.xml* pliku. Usługa Service Fabric używa tej nazwy do identyfikowania danej aplikacji jest uaktualniane. Jeśli ustawisz limity czasu się zbyt krótki, możesz napotkać komunikat o błędzie z informacją, problem. Zapoznaj się z sekcją rozwiązywania problemów lub zwiększyć limity czasu.

Teraz kontynuowane uaktualnienia aplikacji, można monitorować za pomocą narzędzia Service Fabric Explorer lub za pomocą [Get ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) polecenia programu PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

W ciągu kilku minut, stan, który uzyskano przy użyciu poprzedniego polecenia programu PowerShell, powinien podać uaktualniono wszystkich domen aktualizacji (ukończono). I możesz znaleźć, obiektów wizualnych w oknie przeglądarki zostały uruchomione, obracanie!

Możesz spróbować uaktualnienie z wersji 2 do wersji 3 lub w wersji 2 do wersji 1 w charakterze ćwiczenia. Przenoszenie z wersji 2 do wersji 1 jest również uważana za uaktualnienie. Poeksperymentuj z limitów czasu i zasad dotyczących kondycji z siebie uczynić je znasz. W przypadku wdrażania klastra usługi Azure parametry muszą odpowiednio ustawić. Warto ustawić limity czasu ostrożnie.

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu Visual Studio.

Kontrolować, jak uaktualnić aplikację przy użyciu [parametry uaktualniania](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych poznanie sposobu używania [serializacja danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów podczas uaktualniania aplikacji korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

