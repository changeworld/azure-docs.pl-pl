---
title: 'Zabezpieczenia klastra sieci szkieletowej usług: role klientów'
description: W tym artykule opisano dwie role klienta i uprawnienia przewidziane do ról.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451896"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług
Usługa Azure Service Fabric obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są połączeni z klastrem sieci szkieletowej usług: administrator i użytkownik. Kontrola dostępu umożliwia administratorowi klastra ograniczenie dostępu do niektórych operacji klastra dla różnych grup użytkowników, dzięki czemu klaster jest bezpieczniejszy.  

**Administratorzy** mają pełny dostęp do funkcji zarządzania (w tym funkcji odczytu/zapisu). Domyślnie **użytkownicy** mają dostęp do odczytu tylko do funkcji zarządzania (na przykład możliwości zapytań) i możliwość rozpoznawania aplikacji i usług.

Określ dwie role klienta (administratora i klienta) w momencie tworzenia klastra, udostępniając oddzielne certyfikaty dla każdego z nich. Zobacz [Zabezpieczenia klastra sieci szkieletowej usług, aby](service-fabric-cluster-security.md) uzyskać szczegółowe informacje na temat konfigurowania bezpiecznego klastra sieci szkieletowej usług.

## <a name="default-access-control-settings"></a>Domyślne ustawienia kontroli dostępu
Typ kontroli dostępu administratora ma pełny dostęp do wszystkich interfejsów API FabricClient. Można wykonywać wszelkie odczyty i zapisy w klastrze sieci szkieletowej usług, w tym następujące operacje:

### <a name="application-and-service-operations"></a>Operacje aplikacji i usług
* **CreateService**: tworzenie usługi                             
* **CreateServiceFromTemplate**: tworzenie usługi z szablonu                             
* **UpdateService**: aktualizacje usługi                             
* **DeleteService**: usuwanie usługi                             
* **ProvisionApplicationType**: inicjowanie obsługi administracyjnej typów aplikacji                             
* **CreateApplication**: tworzenie aplikacji                               
* **DeleteApplication**: usunięcie aplikacji                             
* **UpgradeApplication**: uruchamianie lub przerywanie uaktualnień aplikacji                             
* **UnprovisionApplicationType**: typ aplikacji nieobjętej obsługi administracyjnej                             
* **MoveNextUpgradeDomain**: wznawianie uaktualnień aplikacji z domeną aktualizacji jawnej                             
* **ReportUpgradeHealth**: wznawianie uaktualnień aplikacji z bieżącym postępem uaktualniania                             
* **ReportHealth**: raportowanie stanu zdrowia                             
* **PredeployPackageToNode**: interfejs API przedseployment                            
* **CodePackageControl**: ponowne uruchamianie pakietów kodu                             
* **RecoverPartition**: odzyskiwanie partycji                             
* **RecoverPartitions**: odzyskiwanie partycji                             
* **RecoverServicePartitions**: odzyskiwanie partycji usługi                             
* **RecoverSystemPartitions**: odzyskiwanie partycji usługi systemowej                             

### <a name="cluster-operations"></a>Operacje klastra
* **ProvisionFabric**: MSI i/lub manifest klastra inicjowania obsługi administracyjnej                             
* **UpgradeFabric**: uruchamianie uaktualnień klastra                             
* **UnprovisionFabric**: MSI i/lub manifest klastra nieobjęcie obsługi administracyjnej                         
* **MoveNextFabricUpgradeDomain**: wznawianie uaktualnień klastra z domeną aktualizacji jawnej                             
* **ReportFabricUpgradeHealth**: wznawianie uaktualnień klastra z bieżącym postępem uaktualniania                             
* **StartInfrastructureTask**: uruchamianie zadań infrastrukturalnych                             
* **FinishInfrastructureTask**: wykańczanie zadań infrastrukturalnych                             
* **InvokeInfrastructureCommand**: polecenia zarządzania zadaniami infrastruktury                              
* **ActivateNode**: aktywowanie węzła                             
* **Dezaktywacja:** dezaktywacja węzła                             
* **DeactivateNodesBatch**: dezaktywacja wielu węzłów                             
* **UsuńDelegacje:** przywracanie dezaktywacji w wielu węzłach                             
* **GetNodeDeactivationStatus**: sprawdzanie stanu dezaktywacji                             
* **NodeStateRemoved : usunięto**stan węzła raportowania                             
* **ReportFault**: zgłaszanie błędów                             
* **FileContent**: transfer plików klienta magazynu obrazów (zewnętrzny do klastra)                             
* **FileDownload**: inicjacja pobierania plików klienta magazynu obrazów (zewnętrzna dla klastra)                             
* **InternalList**: operacja listy plików klienta magazynu obrazów (wewnętrzna)                             
* **Usuń**: operacja usuwania klienta magazynu obrazów                              
* **Upload**: operacja przekazywania przez klienta sklepu z obrazami                             
* **NodeControl**: uruchamianie, zatrzymywanie i ponowne uruchamianie węzłów                             
* **MoveReplicaControl**: przenoszenie replik z jednego węzła do drugiego                             

### <a name="miscellaneous-operations"></a>Różne operacje
* **Ping**: pingi klientów                             
* **Kwerenda:** wszystkie zapytania dozwolone
* **NameExists**: nazywanie kontroli istnienia identyfikatora URI                             

Typ kontroli dostępu użytkownika jest domyślnie ograniczony do następujących operacji: 

* **Wyliczajnasubnames**: wyliczenie identyfikatora URI                             
* **Wyliczajwłaściwości**: wyliczenie właściwości nazewnictwa                             
* **PropertyReadBatch**: operacje odczytu właściwości nazewnictwa                             
* **GetServiceDescription**: powiadomienia o usługach długiej sondy i odczytywanie opisów usług                             
* **ResolveService**: rozwiązywanie problemów z usługami opartymi na skargach                             
* **ResolveNameOwner**: rozpoznawanie właściciela identyfikatora URI nazewnictwa                             
* **ResolvePartition**: rozwiązywanie problemów z usługami systemowymi                             
* **ServiceNotifications**: powiadomienia o usługach opartych na zdarzeniach                             
* **GetUpgradeStatus**: stan uaktualnienia aplikacji sondowania                             
* **GetFabricUpgradeStatus**: stan uaktualnienia klastra sondowania                             
* **InvokeInfrastructureQuery**: wykonywanie zapytań o zadania infrastruktury                             
* **Lista**: operacja listy plików klienta magazynu obrazów                             
* **ResetPartitionLoad**: resetowanie obciążenia dla jednostki trybu failover                             
* **ToggleVerboseServicePlacementHealthReporting**: przełączanie pełne raportowanie kondycji umiejscowienia usługi                             

Kontrola dostępu administratora ma również dostęp do poprzednich operacji.

## <a name="changing-default-settings-for-client-roles"></a>Zmienianie ustawień domyślnych ról klientów
W pliku manifestu klastra można zapewnić klientowi możliwości administratora w razie potrzeby. Ustawienia domyślne można zmienić, przechodząc do opcji **Ustawienia sieci szkieletowej** podczas [tworzenia klastra](service-fabric-cluster-creation-via-portal.md)i udostępniając poprzednie ustawienia w polach **nazwa**, **admin,** **użytkownik**i **wartość.**

## <a name="next-steps"></a>Następne kroki
[Zabezpieczenia klastra sieci szkieletowej usług](service-fabric-cluster-security.md)

[Tworzenie klastra sieci szkieletowej usług](service-fabric-cluster-creation-via-portal.md)

