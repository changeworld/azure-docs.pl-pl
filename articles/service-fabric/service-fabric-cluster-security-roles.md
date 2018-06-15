---
title: 'Zabezpieczenia klastra usługi sieć szkieletowa: role klient | Dokumentacja firmy Microsoft'
description: W tym artykule opisano dwa klienta role i uprawnienia do ról.
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
ms.openlocfilehash: 39b77813e3494e809b92738924544e196d89a166
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204655"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług
Sieć szkieletowa usług Azure obsługuje dwa typy kontroli różny dostęp dla klientów, które są podłączone do klastra usługi sieć szkieletowa: administratora i użytkownika. Kontrola dostępu umożliwia administratora klastrów, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników, co klaster bardziej bezpieczne.  

**Administratorzy** mają pełny dostęp do funkcji zarządzania (w tym możliwości odczytu/zapisu). Domyślnie **użytkowników** może zawierać tylko do odczytu możliwości zarządzania (na przykład możliwość wykonywania kwerend) i możliwość usuwania aplikacji i usług.

Role dwóch klienta (administratora i klient) określa się podczas tworzenia klastra, podając oddzielne certyfikaty dla każdego. Zobacz [zabezpieczeń klastra sieci szkieletowej usług](service-fabric-cluster-security.md) szczegółowe informacje na temat konfigurowania bezpiecznej klastra sieci szkieletowej usług.

## <a name="default-access-control-settings"></a>Domyślne ustawienia kontroli dostępu
Typ kontroli dostępu administratora ma pełny dostęp do wszystkich interfejsów API klienta fabricclient z rolą. Może wykonywać wszystkie odczyty i zapisy na klaster sieci szkieletowej usług, w tym następujące operacje:

### <a name="application-and-service-operations"></a>Operacje usług i aplikacji
* **CreateService**: Tworzenie usługi                             
* **CreateServiceFromTemplate**: tworzenie za pomocą szablonu usługi                             
* **Funkcji updateservice modułu**: Usługa aktualizacji                             
* **DeleteService**: usuwanie usługi                             
* **ProvisionApplicationType**: aprowizacji typu aplikacji                             
* **Operacji CreateApplication**: tworzenie aplikacji                               
* **Funkcji deleteapplication modułu**: usuwanie aplikacji                             
* **UpgradeApplication**: uruchomienie lub przerywania uaktualnienia aplikacji                             
* **UnprovisionApplicationType**: aplikacji typu wstrzymania obsługi administracyjnej.                             
* **MoveNextUpgradeDomain**: wznawianie uaktualniania aplikacji z domeną jawne aktualizacji                             
* **ReportUpgradeHealth**: wznawianie uaktualniania aplikacji z Bieżący postęp uaktualniania                             
* **ReportHealth**: raportowania kondycji                             
* **PredeployPackageToNode**: przedwdrożeniowe interfejsu API                            
* **CodePackageControl**: ponowne uruchamianie pakiety kodu                             
* **RecoverPartition**: odzyskiwanie partycji                             
* **RecoverPartitions**: odzyskiwanie partycji                             
* **RecoverServicePartitions**: odzyskiwanie partycji usługi                             
* **RecoverSystemPartitions**: odzyskiwanie partycji usług systemowych                             

### <a name="cluster-operations"></a>Operacje klastra
* **ProvisionFabric**: MSI i/lub klastra manifestu inicjowania obsługi administracyjnej                             
* **UpgradeFabric**: uruchomienie uaktualnienia klastra                             
* **UnprovisionFabric**: MSI i/lub klastra manifestu wstrzymania obsługi administracyjnej                         
* **MoveNextFabricUpgradeDomain**: wznawianie uaktualniania klastra z domeną jawne aktualizacji                             
* **ReportFabricUpgradeHealth**: wznawianie uaktualniania klastra z Bieżący postęp uaktualniania                             
* **StartInfrastructureTask**: Uruchamianie zadań infrastruktury                             
* **FinishInfrastructureTask**: Kończenie zadań infrastruktury                             
* **InvokeInfrastructureCommand**: infrastruktury zadań zarządzania poleceń                              
* **ActivateNode**: aktywowanie węzła                             
* **DeactivateNode**: dezaktywacji węzła                             
* **DeactivateNodesBatch**: dezaktywowanie wiele węzłów                             
* **RemoveNodeDeactivations**: Przywracanie dezaktywacji na wielu węzłach                             
* **GetNodeDeactivationStatus**: Sprawdzanie stanu dezaktywację.                             
* **NodeStateRemoved**: raportowania stanu węzła usunięte                             
* **ReportFault**: raportowanie błędów                             
* **Operację FileContent**: obraz transfer plików klienta magazynu (zewnętrzne do klastra)                             
* **FileDownload**: obrazu (zewnętrznych do klastra) inicjowanie pobierania pliku klienta magazynu                             
* **InternalList**: obraz magazynu klienta pliku listy operacji (wewnętrzny)                             
* **Usuń**: Operacja usuwania klienta magazynu obrazu                              
* **Przekaż**: operacja przekazywania klienta magazynu obrazu                             
* **NodeControl**: uruchamianie, zatrzymywanie i ponowne uruchamianie węzłów                             
* **MoveReplicaControl**: przenoszenie replik z jednego węzła do innego                             

### <a name="miscellaneous-operations"></a>Inne operacje
* **Polecenie ping**: klient polecenia ping                             
* **Zapytanie**: wszystkie zapytania dozwolone
* **NameExists**: sprawdza istnienie identyfikatora URI nazewnictwa                             

Typ kontroli dostępu użytkowników jest domyślnie ograniczony do następujących czynności: 

* **EnumerateSubnames**: nazewnictwa URI — wyliczenie                             
* **EnumerateProperties**: nazw właściwości — wyliczenie                             
* **PropertyReadBatch**: operacje odczytu dla nazw właściwości                             
* **GetServiceDescription**: long sondowania usługi powiadomień i odczytywanie ich opisy usług                             
* **ResolveService**: Rozpoznawanie usługi opartej na zgodne                             
* **ResolveNameOwner**: rozpoznawanie nazw właściciela identyfikatora URI                             
* **ResolvePartition**: rozpoznawania usług systemowych                             
* **ServiceNotifications**: powiadomień usługi oparty na zdarzeniach                             
* **GetUpgradeStatus**: sondowanie statusu uaktualnienia aplikacji                             
* **GetFabricUpgradeStatus**: sondowanie statusu uaktualnienia klastra                             
* **InvokeInfrastructureQuery**: badania infrastruktury zadań                             
* **Lista**: operacja listy plików klienta magazynu obrazu                             
* **Funkcji resetpartitionload modułu**: resetowanie obciążenia dla jednostki trybu failover                             
* **Toggleverboseserviceplacementhealthreporting modułu**: przełączanie raportowania kondycji umieszczania pełne usługi                             

Kontrola dostępu administratora ma też dostęp do poprzedniej operacji.

## <a name="changing-default-settings-for-client-roles"></a>Zmiana ustawień domyślnych dla ról klienta
W pliku manifestu klastra musisz podać możliwości administratora do klienta w razie potrzeby. Ustawienia domyślne można zmienić, przechodząc do **ustawienia sieci szkieletowej** opcję podczas [Tworzenie klastra](service-fabric-cluster-creation-via-portal.md)i zapewnianie poprzedniego ustawienia w **nazwa**,  **Administrator**, **użytkownika**, i **wartość** pól.

## <a name="next-steps"></a>Kolejne kroki
[Zabezpieczenia klastra sieci szkieletowej usług](service-fabric-cluster-security.md)

[Tworzenie klastra sieci szkieletowej usług](service-fabric-cluster-creation-via-portal.md)

