---
title: Tematy dotyczące uaktualniania aplikacji zaawansowanego | Dokumentacja firmy Microsoft
description: W tym artykule omówiono niektóre tematy zaawansowane dotyczące uaktualniania aplikacji sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 168d944f72c1409b5b69c9ab7c07f7fcfa04c7c8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uaktualnianie aplikacji usługi Service Fabric: Tematy zaawansowane
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Dodawanie lub usuwanie typów usług podczas uaktualniania aplikacji
Jeśli nowy typ usługi zostanie dodany do opublikowanej aplikacji w ramach uaktualnienia, nowy typ usługi jest dodawany do wdrożonej aplikacji. Takie uaktualnienia nie wpływa na wystąpień usługi, które zostały już część aplikacji, ale można utworzyć wystąpienia typu usługi, która została dodana w aktywnej nowego typu usługi (zobacz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Podobnie można usunąć z aplikacji w ramach uaktualnienia typów usług. Jednak przed przystąpieniem do uaktualniania należy usunąć wszystkie wystąpienia usługi usunięte być do usługi typu (zobacz [ServiceFabricService Usuń](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Ręczny tryb uaktualniania
> [!NOTE]
> *Monitorowanej* zalecany jest tryb uaktualniania dla wszystkich uaktualnienia sieci szkieletowej usług.
> *UnmonitoredManual* tryb uaktualniania należy traktować tylko do uaktualnienia nie powiodło się lub został wstrzymany. 
>
>

W *monitorowanej* tryb, Service Fabric ma zastosowanie zasad dotyczących kondycji, aby upewnić się, że aplikacja jest w dobrej kondycji w trakcie uaktualniania. Naruszenia zasad dotyczących kondycji, a następnie Uaktualnianie jest wstrzymana lub automatycznie wycofana w zależności od określonego *FailureAction*.

W *UnmonitoredManual* tryb, administrator aplikacji ma pełną kontrolę nad postęp uaktualnienia. Ten tryb jest przydatne, gdy stosowanie zasad oceny kondycji niestandardowych lub wykonywania nietypowe uaktualnień, aby pominąć całkowicie monitorowania kondycji (np. aplikacja jest już utraty danych). Uaktualnienie w tym trybie zawiesi się po zakończeniu każdej UD i musi być jawnie wznowiona przy użyciu [polecenie Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Jeśli uaktualnienie zostało zawieszone i gotowa do można wznowić przez użytkownika, będzie wyświetlany jego stan uaktualnienia *RollforwardPending* (zobacz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Na koniec *UnmonitoredAuto* tryb jest przydatne w przypadku wykonywania iteracji szybkiego uaktualnienia podczas projektowania usługi lub testowania, ponieważ nie danych wejściowych użytkownika jest wymagane, a żadne zasady dotyczące kondycji aplikacji są oceniane.

## <a name="upgrade-with-a-diff-package"></a>Uaktualnienie z pakietem różnicowego
Zamiast inicjowania obsługi pakietu kompletna aplikacja, uaktualnienia można także wykonać aprowizując różnicowego pakiety, które zawierają tylko zaktualizowanych pakietów kodu/config/danych wraz z manifestu kompletna aplikacja i ukończyć manifestów usługi. Kompletna aplikacja pakiety są tylko wymagane początkowej instalacji aplikacji do klastra. Kolejne uaktualnienia może być kompletna aplikacja pakiety lub pakiety różnicowego.  

Wszystkie odwołania w manifeście aplikacji lub usługi manifesty pakietu różnicowego, którego nie można znaleźć w pakiecie aplikacji jest on automatycznie zastępowany wersją obecnie elastycznie.

Dostępne są następujące scenariusze korzystania z pakietu różnic:

* Jeśli masz pakietu dużej aplikacji, który odwołuje się wiele plików manifestu usługi i/lub kilka pakietów kodu, pakiety konfiguracji lub pakietów danych.
* Jeśli masz proces kompilacji systemu wdrożenia, w którym generuje układu kompilacji bezpośrednio z aplikacji. W takim przypadku mimo że nie zmieniła się kod, nowo zbudowany zestawy uzyskać różne sumy kontrolnej. Przy użyciu pakietu pełnej aplikacji należy zaktualizować wersję na wszystkie pakiety kodu. Przy użyciu pakietu różnicowego, musisz tylko podać zmienionych plików i pliki manifestu, których wersja uległa zmianie.

Po uaktualnieniu aplikacji przy użyciu programu Visual Studio, pakietu różnic jest automatycznie publikowany. Aby ręcznie utworzyć pakiet różnicowego, należy zaktualizować manifest aplikacji i manifestów usługi, ale tylko zmienione pakiety powinny być uwzględnione w pakiet końcowe aplikacji.

Na przykład Zacznijmy następującej aplikacji (dostępne w celu ułatwienia zrozumienia numery wersji):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Załóżmy, że chcesz zaktualizować tylko kod pakietu programu service1 przy użyciu pakietu różnic. Zaktualizowano aplikacji ma następujące zmiany wersji:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

W takim przypadku należy zaktualizować manifest aplikacji 2.0.0 i manifest usługi service1 w celu uwzględnienia aktualizacji pakietu kodu. Folder dla pakietu aplikacji może mieć następującą strukturę:

```text
app1/
  service1/
    code/
```

Innymi słowy Utwórz pakiet kompletna aplikacja normalnie, a następnie usuń wszystkie foldery pakietu kodu/config/danych, dla których wersja nie zmieniła się.

## <a name="rolling-back-application-upgrades"></a>Wycofywanie uaktualnienia aplikacji

Podczas uaktualnienia można przeniesiona do przodu w jednym z trzech trybów (*monitorowanej*, *UnmonitoredAuto*, lub *UnmonitoredManual*), ich tylko można wycofać w obu *UnmonitoredAuto* lub *UnmonitoredManual* tryb. Stopniowe w *UnmonitoredAuto* tryb działa tak samo wycofywaniem do przodu, z wyjątkiem który wartość domyślną *UpgradeReplicaSetCheckTimeout* różni — zobacz [aplikacji Parametry uaktualnienia](service-fabric-application-upgrade-parameters.md). Stopniowe w *UnmonitoredManual* tryb działa tak samo wycofywaniem do przodu — wycofywanie zawiesi się po zakończeniu każdej UD i musi być jawnie wznowiona przy użyciu [ Polecenie resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) kontynuować wycofywanie.

Wycofywanie zmian mogą być wyzwalane automatycznie po zasady dotyczące kondycji uaktualnienia w *monitorowanej* tryb z *FailureAction* z *wycofywania* naruszenia (zobacz [Parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md)) lub jawnie przy użyciu [Start ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Podczas wycofywania wartość *UpgradeReplicaSetCheckTimeout* i można je zmienić tryb w dowolnej chwili za pomocą [ServiceFabricApplicationUpgrade aktualizacji](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji za pomocą Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu PowerShell.

Kontrolowanie sposobu uaktualnienia aplikacji przy użyciu [uaktualnienia parametrów](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych przez poznanie [szeregowanie danych](service-fabric-application-upgrade-data-serialization.md).

Rozwiązywania typowych problemów w uaktualnień aplikacji, korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
