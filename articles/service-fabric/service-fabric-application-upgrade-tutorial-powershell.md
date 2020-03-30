---
title: Uaktualnienie aplikacji sieci szkieletowej usługi przy użyciu programu PowerShell
description: W tym artykule ominia doświadczenie wdrażania aplikacji sieci szkieletowej usług, zmienianie kodu i wdrażanie uaktualnienia przy użyciu programu PowerShell.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: b113b5a1042518e3b0d86e53796c5fe49afed418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426782"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Uaktualnienie aplikacji sieci szkieletowej usług przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Najczęściej używane i zalecane podejście uaktualnienia jest monitorowane uaktualnienie stopniowe.  Usługa Azure Service Fabric monitoruje kondycję aplikacji uaktualniane na podstawie zestawu zasad kondycji. Po uaktualnieniu domeny aktualizacji (UD) sieci szkieletowej usług ocenia kondycję aplikacji i przechodzi do następnej domeny aktualizacji lub nie powiedzie się uaktualnienie w zależności od zasad kondycji.

Monitorowane uaktualnienie aplikacji można wykonać przy użyciu zarządzanych lub natywnych interfejsów API, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka Java lub REST. Aby uzyskać instrukcje dotyczące wykonywania uaktualnienia przy użyciu programu Visual Studio, zobacz [Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md).

W przypadku uaktualnień stopniowych monitorowanych przez sieć usług administrator aplikacji może skonfigurować zasady oceny kondycji używane przez sieć szkieletową usług do określenia, czy aplikacja jest w dobrej kondycji. Ponadto administrator może skonfigurować akcję, która ma zostać podjęta, gdy ocena kondycji nie powiedzie się (na przykład wykonanie automatycznego wycofywania). W tej sekcji przechodzi przez monitorowane uaktualnienie dla jednego z przykładów SDK, który używa programu PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1: Tworzenie i wdrażanie przykładu obiektów wizualnych
Skompiluj i opublikuj aplikację, klikając prawym przyciskiem myszy projekt aplikacji **VisualObjectsApplication** i wybierając polecenie **Publikuj.**  Aby uzyskać więcej informacji, zobacz [samouczek uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade-tutorial.md).  Alternatywnie można użyć programu PowerShell do wdrożenia aplikacji.

> [!NOTE]
> Zanim którekolwiek z poleceń sieci szkieletowej usług może być używane w programie `Connect-ServiceFabricCluster` PowerShell, należy najpierw połączyć się z klastrem przy użyciu polecenia cmdlet. Podobnie zakłada się, że klaster został już skonfigurowany na komputerze lokalnym. Zobacz artykuł dotyczący [konfigurowania środowiska programistycznego sieci szkieletowej](service-fabric-get-started.md)usług .
> 
> 

Po nakręceniu projektu w programie Visual Studio można użyć polecenia Programu PowerShell [Copy-ServiceFabricApplicationPackage,](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) aby skopiować pakiet aplikacji do magazynu imagestore. Jeśli chcesz zweryfikować pakiet aplikacji lokalnie, użyj polecenia cmdlet [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage) Następnym krokiem jest zarejestrowanie aplikacji w czasie wykonywania sieci szkieletowej usług przy użyciu polecenia cmdlet [Register-ServiceFabricApplicationType.](/powershell/module/servicefabric/register-servicefabricapplicationtype) Poniższym krokiem jest uruchomienie wystąpienia aplikacji przy użyciu polecenia cmdlet [aplikacji New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)  Te trzy kroki są analogiczne do korzystania z elementu menu **Wdrażanie** w programie Visual Studio.  Po zakończeniu inicjowania obsługi administracyjnej należy wyczyścić skopiowany pakiet aplikacji z magazynu obrazów w celu zmniejszenia zasobów zużytych.  Jeśli typ aplikacji nie jest już wymagane, powinny być wyrejestrowane z tego samego powodu. Aby uzyskać więcej [informacji, zobacz Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell.](service-fabric-application-upgrade-tutorial-powershell.md)

Teraz można użyć [Eksploratora sieci szkieletowej usług, aby wyświetlić klaster i aplikację](service-fabric-visualizing-your-cluster.md). Aplikacja ma usługę sieci web, do których można nawigować w programie Internet Explorer, wpisując [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) na pasku adresu.  Na ekranie powinny być widoczne pewne pływające obiekty wizualne.  Ponadto można użyć [Get-ServiceFabricApplication,](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) aby sprawdzić stan aplikacji.

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizowanie przykładu obiektów wizualnych
Można zauważyć, że w wersji, która została wdrożona w kroku 1, obiekty wizualne nie obracają się. Uaktualnijmy tę aplikację do tej, w której obiekty wizualne również się obracają.

Wybierz projekt VisualObjects.ActorService w rozwiązaniu VisualObjects i otwórz plik StatefulVisualObjectActor.cs. W tym pliku przejdź `MoveObject`do metody `this.State.Move()`, comment `this.State.Move(true)`out i uncomment . Ta zmiana obraca obiekty po uaktualnieniu usługi.

Musimy również zaktualizować plik *ServiceManifest.xml* (w obszarze PackageRoot) projektu **VisualObjects.ActorService**. Zaktualizuj *CodePackage* i wersję usługi do wersji 2.0 oraz odpowiednie wiersze w pliku *ServiceManifest.xml.*
Opcji *Edycji manifestu* programu Visual Studio można użyć po kliknięciu prawym przyciskiem myszy rozwiązania, aby wprowadzić zmiany w pliku manifestu.

Po wjedniejszce manifest powinien wyglądać następująco (wyróżnione fragmenty pokazują zmiany):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Teraz *plik ApplicationManifest.xml* (znaleziony w ramach projektu **VisualObjects** w ramach rozwiązania **VisualObjects)** jest aktualizowany do wersji 2.0 projektu **VisualObjects.ActorService.** Ponadto wersja aplikacji jest aktualizowana do wersji 2.0.0.0 z wersji 1.0.0.0. *ApplicationManifest.xml* powinien wyglądać następująco:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Teraz skompiluj projekt, wybierając tylko projekt **ActorService,** a następnie klikając prawym przyciskiem myszy i wybierając opcję **Kompilacja** w programie Visual Studio. Jeśli wybierzesz **Przebuduj wszystkie**, należy zaktualizować wersje dla wszystkich projektów, ponieważ kod uległby zmianie. Następnie zapakujmy zaktualizowaną aplikację, klikając prawym przyciskiem myszy ***visualobjectsApplication***, wybierając menu sieci szkieletowej usług i wybierając **pozycję Pakiet**. Ta akcja tworzy pakiet aplikacji, który można wdrożyć.  Zaktualizowana aplikacja jest gotowa do wdrożenia.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3: Zdecyduj o zasadach dotyczących zdrowia i parametrach aktualizacji
Zapoznaj się z [parametrami uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesem uaktualniania,](service-fabric-application-upgrade.md) aby dobrze zrozumieć różne parametry uaktualnienia, limity czasu i zastosowane kryterium kondycji. W tym instruktażu kryterium oceny kondycji usługi jest ustawione na wartości domyślne (i zalecane), co oznacza, że wszystkie usługi i wystąpienia powinny być *w dobrej kondycji* po uaktualnieniu.  

Jednak zwiększmy *HealthCheckStableDuration* do 180 sekund (tak, że usługi są w dobrej kondycji przez co najmniej 120 sekund przed uaktualnienie przechodzi do następnej domeny aktualizacji).  Ustawmy również *UpgradeDomainTimeout* na 1200 sekund i *UpgradeTimeout* na 3000 sekund.

Na koniec ustawmy również *UpgradeFailureAction* do wycofania. Ta opcja wymaga sieci szkieletowej usług, aby wycofać aplikację do poprzedniej wersji, jeśli napotka żadnych problemów podczas uaktualniania. W związku z tym podczas uruchamiania uaktualnienia (w kroku 4) określono następujące parametry:

FailureAction = Wycofywanie

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4: Przygotowanie aplikacji do uaktualnienia
Teraz aplikacja jest zbudowana i gotowa do uaktualnienia. Jeśli otworzysz okno programu PowerShell jako administrator i [wpiszesz Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), powinieneś poinformować, że jest to typ aplikacji 1.0.0.0 **VisualObjects,** który został wdrożony.  

Pakiet aplikacji jest przechowywany pod następującą ścieżką względną, w której nieskompresowano zestawu SDK sieci szkieletowej usług — *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. W tym katalogu należy znaleźć folder "Pakiet", w którym przechowywany jest pakiet aplikacji. Sprawdź sygnatury czasowe, aby upewnić się, że jest to najnowsza kompilacja (może być konieczne zmodyfikowanie ścieżek odpowiednio również).

Teraz skopiujmy zaktualizowany pakiet aplikacji do magazynu obrazów sieci szkieletowej usług (gdzie pakiety aplikacji są przechowywane przez sieci szkieletowej usług). Parametr *ApplicationPackagePathInImageStore* informuje sieci szkieletowej usług, gdzie można znaleźć pakiet aplikacji. Umieściliśmy zaktualizowaną aplikację w "VisualObjects\_V2" z następującym poleceniem (może być konieczne ponowne zmodyfikowanie ścieżek).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Następnym krokiem jest zarejestrowanie tej aplikacji w sieci szkieletowej usług, które można wykonać za pomocą [polecenia Register-ServiceFabricApplicationType:](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Jeśli poprzednie polecenie nie powiedzie się, jest prawdopodobne, że trzeba odbudować wszystkie usługi. Jak wspomniano w kroku 2, może być również trzeba zaktualizować wersję webservice, jak również.

Zaleca się usunięcie pakietu aplikacji po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe.  Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dysku i prowadzi do problemów z wydajnością aplikacji.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5: Rozpocznij uaktualnienie aplikacji
Teraz wszyscy jesteśmy ustawione, aby rozpocząć uaktualnienie aplikacji za pomocą [polecenia Start-ServiceFabricApplicationUplicationUpgrade:](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps)

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Nazwa aplikacji jest taka sama, jak została opisana w pliku *ApplicationManifest.xml.* Usługa Sieci szkieletowej używa tej nazwy, aby zidentyfikować, która aplikacja jest coraz uaktualniony. Jeśli ustawisz limity czasu jako zbyt krótkie, może wystąpić komunikat o błędzie informujący o problemie. Zapoznaj się z sekcją rozwiązywania problemów lub zwiększ limity czasu.

Teraz w miarę postępu uaktualniania aplikacji można go monitorować za pomocą Eksploratora sieci szkieletowej usług lub za pomocą polecenia [Get-ServiceFabricApplicationUplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

W ciągu kilku minut stan, który został nadany przy użyciu poprzedniego polecenia programu PowerShell, powinien określać, że wszystkie domeny aktualizacji zostały uaktualnione (ukończone). I powinno się okazać, że obiekty wizualne w oknie przeglądarki zaczęły się obracać!

Możesz spróbować uaktualnić z wersji 2 do wersji 3 lub z wersji 2 do wersji 1 jako ćwiczenie. Przejście z wersji 2 do wersji 1 jest również uważane za uaktualnienie. Graj z limitami czasu i polityką zdrowotną, aby się z nimi zapoznać. Podczas wdrażania w klastrze platformy Azure parametry muszą być odpowiednio ustawione. Dobrze jest ustawić limity czasu zachowawczo.

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu Visual Studio.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Upewnij się, że uaktualnienia aplikacji są zgodne, ucząc się, jak używać [serializacji danych.](service-fabric-application-upgrade-data-serialization.md)

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [tematów zaawansowanych](service-fabric-application-upgrade-advanced.md).

Rozwiąż typowe problemy w uaktualnieniach aplikacji, odwołując się do kroków [rozwiązywania problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

