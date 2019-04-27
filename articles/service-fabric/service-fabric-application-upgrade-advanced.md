---
title: Zaawansowane tematy związane z uaktualnieniami aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono niektóre zaawansowane tematy dotyczące uaktualniania aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3cdddac74552b56dfe3567adf30f1a05b6eb8e24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616538"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uaktualnianie aplikacji usługi Service Fabric: Tematy zaawansowane
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Dodawanie lub usuwanie typów usług podczas uaktualniania aplikacji
Jeśli nowy typ usługi jest dodawany do opublikowanej aplikacji w ramach uaktualnienia, nowy typ usługi zostanie dodany do wdrożonej aplikacji. Takie uaktualnienie nie wpływa na wystąpień usługi, które zostały już część aplikacji, ale można utworzyć wystąpienia typu usługi, który został dodany, dla nowego typu usługi w aktywnej (zobacz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Podobnie można usunąć typy usług z aplikacji w ramach uaktualnienia. Jednak przed przystąpieniem do uaktualniania należy usunąć wszystkich wystąpień usługi typu usunięte być to usługi (zobacz [ServiceFabricService Usuń](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Tryb uaktualniania ręcznego
> [!NOTE]
> *Monitorowanej* tryb uaktualniania jest zalecane w przypadku wszystkich uaktualnień usługi Service Fabric.
> *UnmonitoredManual* tryb uaktualniania, tylko mają być uwzględniane w uaktualnienia nie powiodło się lub został wstrzymany. 
>
>

W *monitorowanej* trybie, Usługa Service Fabric mają zastosowanie zasady dotyczące kondycji, aby upewnić się, że aplikacja jest w dobrej kondycji, w miarę postępów uaktualnienia. Naruszenia zasad dotyczących kondycji, a następnie uaktualnienia jest zawieszone lub automatycznie wycofana w zależności od określonego *FailureAction*.

W *UnmonitoredManual* tryb, administrator aplikacji ma całkowitej kontroli nad postęp uaktualnienia. Ten tryb jest przydatny w przypadku stosowania zasad oceny kondycji niestandardowych lub wykonywanie nietypowe uaktualnień, aby pominąć całkowicie monitorowania kondycji (np. aplikacja jest już utraty danych). Uaktualnienie w tym trybie spowoduje wstrzymanie po wykonaniu każdego UD i musi być jawnie wznowić, przy użyciu [ServiceFabricApplicationUpgrade Wznów](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Podczas uaktualniania jest wstrzymana, a chcesz być wznowione przez użytkownika, wyświetli jego stan uaktualnienia *RollforwardPending* (zobacz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Na koniec *UnmonitoredAuto* tryb jest przydatne w przypadku wykonywania uaktualnienia szybkie iteracje podczas tworzenia usług i testowania, ponieważ dane wejściowe użytkownika nie jest wymagane, a żadne zasady dotyczące kondycji aplikacji są oceniane.

## <a name="upgrade-with-a-diff-package"></a>Uaktualnienie z pakietem różnic
Zamiast aprowizacji pakietu kompletnej aplikacji, uaktualnienia można również przeprowadzić przez diff pakietów, które zawierają tylko zaktualizowane pakiety danych/config/kodu wraz z manifestu kompletnej aplikacji, a następnie Zakończ manifestów usługi aprowizacji. Kompletna aplikacja pakiety są tylko wymaganych dla początkowej instalacji aplikacji do klastra. Kolejne uaktualnienia może być z kompletnej aplikacji, pakietów lub pakietów różnic.  

Wszystkie odwołania w manifest aplikacji i manifestów usługi pakietu różnic, którego nie można znaleźć w pakiecie aplikacji jest automatycznie zastępowany przy użyciu wersji obecnie ustanowione.

Scenariusze dotyczące korzystania z pakietu różnice są następujące:

* Jeśli masz pakiet dużych aplikacji, który odwołuje się do kilku plików manifestu usługi i/lub kilka pakiety kodu, konfiguracji pakietów lub pakietów danych.
* W przypadku procesu kompilacji system wdrożenia, który generuje układ kompilacji bezpośrednio z poziomu aplikacji. W tym przypadku nawet, jeśli kod nie została zmieniona, nowo skompilowany zestaw nie uzyskać różne sumy kontrolnej. Przy użyciu pakietu pełnej aplikacji wymaga zaktualizowania wersji na wszystkie pakiety kodu. Przy użyciu pakietu różnicowego, należy podać tylko zmienione pliki i pliki manifestu, w których uległa zmianie wersji.

Po uaktualnieniu aplikacji przy użyciu programu Visual Studio, pakiet różnic została opublikowana automatycznie. Aby ręcznie utworzyć pakiet diff, manifest aplikacji i manifestów usługi muszą zostać zaktualizowane, ale tylko zmienione pakiety powinny być uwzględnione w pakiet końcowy aplikacji.

Na przykład Zacznijmy od następującej aplikacji (podany w celu ułatwienia zrozumienia numery wersji):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Załóżmy, że chcesz zaktualizować tylko pakiet kodu klasy service1 przy użyciu pakietu różnic. Zaktualizowano aplikacja ma następujące zmiany wersji:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

W takim przypadku należy zaktualizować manifest aplikacji 2.0.0 i manifestu usługi service1 odzwierciedlały aktualizacji pakietu kodu. Folder dla pakietu aplikacji będzie mieć następującą strukturę:

```text
app1/
  service1/
    code/
```

Innymi słowy zwykle Utwórz pakiet kompletnej aplikacji, a następnie usuń wszystkie foldery pakietów kodu/config/danych, dla których wersja nie zmienił się.

## <a name="rolling-back-application-upgrades"></a>Wycofywanie uaktualnienia aplikacji

Podczas uaktualnienia może być przeniesiona do przodu w jednym z trzech trybów (*monitorowanej*, *UnmonitoredAuto*, lub *UnmonitoredManual*), są tylko można cofnąć w dowolnym *UnmonitoredAuto* lub *UnmonitoredManual* trybu. Stopniowe ponownie *UnmonitoredAuto* tryb działa tak samo jak stopniowego do przodu, z wyjątkiem, wartością domyślną *UpgradeReplicaSetCheckTimeout* różni się — zobacz [aplikacji Parametry uaktualniania](service-fabric-application-upgrade-parameters.md). Stopniowe ponownie *UnmonitoredManual* tryb działa tak samo jak stopniowego do przodu — wycofywanie spowoduje wstrzymanie po wykonaniu każdego UD i musi być jawnie wznowić, przy użyciu [ Wznów ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) kontynuować wycofywania.

Wycofywanie zmian może być wyzwalane automatycznie kiedy zasady dotyczące kondycji dla uaktualnienie w *monitorowanej* tryb z *FailureAction* z *wycofywania* naruszenia (zobacz [Parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md)) lub jawnie przy użyciu [Start ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Podczas wycofywania, wartość *UpgradeReplicaSetCheckTimeout* i nadal można zmienić trybu w każdej chwili [ServiceFabricApplicationUpgrade aktualizacji](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnienie z aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontrolować, jak uaktualnić aplikację przy użyciu [parametry uaktualniania](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych poznanie sposobu używania [serializacja danych](service-fabric-application-upgrade-data-serialization.md).

Rozwiązywanie typowych problemów podczas uaktualniania aplikacji korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
