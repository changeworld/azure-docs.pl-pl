---
title: 'Zabezpieczenia klastra usługi Service Fabric: role klienta | Dokumentacja firmy Microsoft'
description: W tym artykule opisano role dwóch klientów i uprawnienia dostarczane do ról.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: ''
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ed000dc4be1ae45382d688d4a596ec745c69d0bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711162"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Kontrola dostępu oparta na rolach dla klientów usługi Service Fabric
Usługa Azure Service Fabric obsługuje dwa typy kontroli dostępu do różnych klientów, które są podłączone do klastra usługi Service Fabric: administratora i użytkownika. Kontrola dostępu pozwala administrator klastra ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników, dzięki czemu klaster jest bardziej bezpieczne.  

**Administratorzy** mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu/zapisu). Domyślnie **użytkowników** tylko mają dostęp do odczytu do możliwości zarządzania (na przykład, zapytanie możliwości) i możliwość usuwania aplikacji i usług.

Należy określić role dwóch klientów (administratora i klientów) w czasie tworzenia klastra, podając oddzielnych certyfikatów dla każdego. Zobacz [zabezpieczeń klastra usługi Service Fabric](service-fabric-cluster-security.md) Aby uzyskać szczegółowe informacje na temat tworzenia bezpiecznego klastra usługi Service Fabric.

## <a name="default-access-control-settings"></a>Domyślne ustawienia kontroli dostępu
Typ kontroli dostępu administratora ma pełny dostęp do wszystkich interfejsów API FabricClient. Może wykonywać żadnych operacji odczytu i zapisu na klastrze usługi Service Fabric, w tym następujące operacje:

### <a name="application-and-service-operations"></a>Aplikacji i operacje usługi
* **CreateService**: Tworzenie usługi                             
* **CreateServiceFromTemplate**: tworzenie za pomocą szablonu usługi                             
* **UpdateService**: Usługa aktualizacji                             
* **Funkcja DeleteService**: usuwanie usługi                             
* **ProvisionApplicationType**: aprowizacji typu aplikacji                             
* **Operacji CreateApplication**: tworzenie aplikacji                               
* **DeleteApplication**: usuwanie aplikacji                             
* **UpgradeApplication**: uruchamia się lub przerywania uaktualnień aplikacji                             
* **UnprovisionApplicationType**: cofnięcie aprowizacji typu aplikacji                             
* **MoveNextUpgradeDomain**: wznawianie uaktualnień aplikacji za pomocą domeny aktualizacji jawne                             
* **ReportUpgradeHealth**: wznawianie uaktualnień aplikacji za pomocą Bieżący postęp uaktualniania                             
* **ReportHealth**: raportowania kondycji                             
* **PredeployPackageToNode**: przedwdrożeniowe interfejsu API                            
* **CodePackageControl**: ponowne uruchamianie pakiety kodu                             
* **RecoverPartition**: odzyskiwanie partycji                             
* **RecoverPartitions**: odzyskiwanie partycji                             
* **RecoverServicePartitions**: odzyskiwanie partycje usługi                             
* **RecoverSystemPartitions**: odzyskiwanie partycji usług systemowych                             

### <a name="cluster-operations"></a>Operacje klastra
* **ProvisionFabric**: Plik MSI i/lub klastra manifestu, inicjowanie obsługi administracyjnej                             
* **UpgradeFabric**: uruchamianie Uaktualnianie klastra                             
* **UnprovisionFabric**: Plik MSI i/lub klastra manifestu, cofnięcie aprowizacji                         
* **MoveNextFabricUpgradeDomain**: wznawianie Uaktualnianie klastra za pomocą domeny aktualizacji jawne                             
* **ReportFabricUpgradeHealth**: wznawianie Uaktualnianie klastra za pomocą Bieżący postęp uaktualniania                             
* **StartInfrastructureTask**: Uruchamianie zadań związanych z infrastrukturą                             
* **FinishInfrastructureTask**: Kończenie zadań związanych z infrastrukturą                             
* **InvokeInfrastructureCommand**: infrastruktura zadań zarządzania poleceń                              
* **ActivateNode**: aktywowanie węzła                             
* **DeactivateNode**: dezaktywowanie węzła                             
* **DeactivateNodesBatch**: dezaktywowanie wiele węzłów                             
* **RemoveNodeDeactivations**: Przywracanie dezaktywacji na wielu węzłach                             
* **GetNodeDeactivationStatus**: Sprawdzanie stanu dezaktywacji                             
* **NodeStateRemoved**: raportowanie stanu węzła usunięte                             
* **ReportFault**: raportowanie błędów                             
* **Operację FileContent**: obraz transfer plików klienta magazynu (zewnętrznie w stosunku do klastra)                             
* **FileDownload**: obrazów (zewnętrzna do klastra) Rozpoczęcie pobierania pliku klienta magazynu                             
* **InternalList**: obraz magazynu klienta pliku operacja wygenerowania listy (wewnętrzny)                             
* **Usuń**: operacja usunięcia klienta magazynu obrazów                              
* **Przekaż**: operacji wysyłania klienta magazynu obrazów                             
* **NodeControl**: uruchamianie, zatrzymywanie i ponowne uruchamianie węzłów                             
* **MoveReplicaControl**: przenoszenie repliki z jednego węzła do innego                             

### <a name="miscellaneous-operations"></a>Inne operacje
* **Polecenie ping**: ping klienta                             
* **Zapytanie**: wszystkie zapytania dozwolone
* **NameExists**: sprawdza obecność identyfikatora URI nazewnictwa                             

Typ kontroli dostępu użytkowników jest domyślnie ograniczony do następujących czynności: 

* **EnumerateSubnames**: nazw identyfikatora URI, wyliczenie                             
* **EnumerateProperties**: nazw właściwości, wyliczenie                             
* **PropertyReadBatch**: operacje odczytu dla nazw właściwości                             
* **GetServiceDescription**: powiadomień dotyczących usług długich sondowania i odczytywanie ich opisy usług                             
* **ResolveService**: usługa oparta na zgodne rozwiązania                             
* **ResolveNameOwner**: rozpoznawania nazw właściciela identyfikatora URI                             
* **ResolvePartition**: rozpoznawanie usług systemowych                             
* **ServiceNotifications**: powiadomień dotyczących usług opartych na zdarzeniach                             
* **GetUpgradeStatus**: sondowania stanu uaktualnienia aplikacji                             
* **GetFabricUpgradeStatus**: sondowania stanu uaktualnienia klastra                             
* **InvokeInfrastructureQuery**: wykonywanie zapytań zadań związanych z infrastrukturą                             
* **Lista**: operacja wygenerowania listy plików klienta magazynu obrazów                             
* **ResetPartitionLoad**: resetowanie obciążenia w przypadku pojedynczej jednostki trybu failover                             
* **ToggleVerboseServicePlacementHealthReporting**: przełączanie, raportowanie stanu umieszczania pełne usługi                             

Kontrola dostępu administracyjnego ma także dostęp do poprzednich operacji.

## <a name="changing-default-settings-for-client-roles"></a>Zmiana ustawień domyślnych dla ról klienta
W pliku manifestu klastra można podać możliwości administratora do klienta, jeśli to konieczne. Ustawienia domyślne można zmienić, przechodząc do **ustawień sieci szkieletowej** opcji podczas [Tworzenie klastra](service-fabric-cluster-creation-via-portal.md)i określeniu poprzednich ustawień w **nazwa**,  **Administrator**, **użytkownika**, i **wartość** pola.

## <a name="next-steps"></a>Kolejne kroki
[Zabezpieczenia klastra usługi Service Fabric](service-fabric-cluster-security.md)

[Tworzenie klastra usługi Service Fabric](service-fabric-cluster-creation-via-portal.md)

