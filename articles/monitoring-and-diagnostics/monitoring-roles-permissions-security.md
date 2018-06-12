---
title: Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń z monitorem Azure
description: Dowiedz się, jak używać monitora Azure wbudowane role i uprawnienia do ograniczania dostępu do monitorowania zasobów.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 82ec2b71ee83d2ec697fa44521c103b11976f1ed
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264606"
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń z monitorem Azure
Wiele zespołów należy ściśle regulowania dostępu do danych i ustawienia monitorowania. Na przykład, jeśli masz członków zespołu, którzy używają wyłącznie na monitorowanie (pracowników działu pomocy technicznej, metodyki devops engineers) lub jeśli korzystasz z dostawcą usługi zarządzanej, można przyznać im dostęp do danych tylko do monitorowania podczas ograniczania możliwość tworzenia, modyfikowania, lub Usuwanie zasobów. W tym artykule pokazano, jak szybko zastosować wbudowane monitorowania roli RBAC użytkownikowi na platformie Azure lub tworzenie własnych niestandardowych ról dla użytkownika, który wymaga ograniczonych uprawnień monitorowania. Następnie zawiera omówienie zagadnienia dotyczące zabezpieczeń dla zasobów związanych z monitora Azure i jak można ograniczyć dostęp do danych, które zawierają.

## <a name="built-in-monitoring-roles"></a>Wbudowane role monitorowania
Azure Monitor wbudowane role mają pomóc ograniczyć dostęp do zasobów w ramach subskrypcji, ograniczając osoby odpowiedzialne za monitorowanie infrastruktury do konfigurowania tych danych. Azure Monitor oferuje dwie role poza pole: czytnik monitorowania i współautor monitorowania.

### <a name="monitoring-reader"></a>Czytelnik monitorowania
Osoby przypisać rolę czytelnika monitorowania można wyświetlić wszystkie dane monitorowania w ramach subskrypcji, ale nie można zmodyfikować dowolnego zasobu ani edytować ustawienia związane z monitorowania zasobów. Ta rola jest odpowiednia dla użytkowników w organizacji, takich jak inżynierów pomocy technicznej lub operacje, którzy muszą mieć możliwość:

* Wyświetlać pulpity nawigacyjne monitorowania w portalu i tworzyć własne prywatne pulpity nawigacyjne monitorowania.
* Wyświetl reguły alertu zdefiniowane w [alerty Azure](monitoring-overview-unified-alerts.md)
* Zapytania dotyczące używania metryki [interfejsu API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn931930.aspx), [poleceń cmdlet programu PowerShell](insights-powershell-samples.md), lub [interfejsu wiersza polecenia i platform](insights-cli-samples.md).
* Wyślij zapytanie do dziennika aktywności za pomocą portalu, interfejsu API REST Monitor Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia i platform.
* Widok [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) dla zasobu.
* Widok [dziennika profilu](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) dla subskrypcji.
* Wyświetl ustawienia skalowania automatycznego.
* Widok alertów działania i ustawienia.
* Dostęp do danych usługi Application Insights i wyświetlanie danych w module analiz AI.
* Wyszukiwanie danych obszaru roboczego analizy dzienników w tym dane użycia dla obszaru roboczego.
* Wyświetlanie grup zarządzania analizy dzienników.
* Pobieranie schematu wyszukiwania analizy dzienników.
* Lista pakietów analizy analizy dzienników.
* Pobierz i wykonać analizy dzienników zapisane wyszukiwania.
* Pobieranie konfiguracji magazynu analizy dzienników.

> [!NOTE]
> Ta rola nie daje dostęp do odczytu dziennika dane przesyłane strumieniowo do Centrum zdarzeń lub przechowywane na koncie magazynu. [Zobacz poniżej](#security-considerations-for-monitoring-data) informacji na temat konfigurowania dostępu do tych zasobów.
> 
> 

### <a name="monitoring-contributor"></a>Współautor monitorowania
Osoby przypisano rolę współautora monitorowania można wyświetlić wszystkie dane monitorowania w ramach subskrypcji i utworzyć lub zmodyfikować ustawienia monitorowania, ale nie można zmodyfikować żadnych innych zasobów. Ta rola jest nadzbiorem rolę czytelnika monitorowania i jest odpowiednia dla członków zespołu monitorowania lub dostawcy usług zarządzanych, którzy Oprócz powyższego uprawnienia muszą być również możliwość organizacji:

* Pulpity nawigacyjne monitorowania należy opublikować jako udostępnionego pulpitu nawigacyjnego.
* Ustaw [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) dla resource.*
* Ustaw [dziennika profilu](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) dla subscription.*
* Ustaw działania reguły alertów i ustawienia za pośrednictwem [alerty Azure](monitoring-overview-unified-alerts.md).
* Tworzenie testów sieci web usługi Application Insights i składniki.
* Obszar roboczy analizy dzienników listy udostępnionych kluczy.
* Włącz lub wyłącz analizy dzienników analizy pakietów.
* Tworzenie i usuwanie i wykonywanie analizy dzienników zapisane wyszukiwania.
* Tworzenie i usuwanie konfiguracji magazynu analizy dzienników.

* użytkownika również oddzielnie musi dysponować uprawnieniami ListKeys nad zasobem docelowym (magazynu konta lub zdarzenia koncentratora przestrzeń nazw) można ustawić profil dziennika lub ustawienie diagnostyczne.

> [!NOTE]
> Ta rola nie daje dostęp do odczytu dziennika dane przesyłane strumieniowo do Centrum zdarzeń lub przechowywane na koncie magazynu. [Zobacz poniżej](#security-considerations-for-monitoring-data) informacji na temat konfigurowania dostępu do tych zasobów.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorowanie uprawnienia i niestandardowe role RBAC
Jeśli powyższe wbudowane role nie dokładną potrzeb zespołu, możesz [utworzyć niestandardową rolę RBAC](../role-based-access-control/custom-roles.md) z uprawnieniami bardziej szczegółowego. Poniżej przedstawiono typowe operacje Azure RBAC monitora z ich opisy.

| Operacja | Opis |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Akcja odczytu/zapisu/usuwania grupy. |
| Usuń Microsoft.Insights/ActivityLogAlerts/[Read, zapisu] |Alerty dziennika aktywności odczytu/zapisu/usuwania. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Reguły alertów odczytu/zapisu/usuwania (od alertów klasycznego). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista zdarzeń (Historia reguły alertów są wyzwalane) dla reguł alertów. Ma zastosowanie tylko do portalu. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Ustawienia skalowania automatycznego odczytu/zapisu/usuwania. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Ustawienia diagnostyki odczytu/zapisu/usuwania. |
| Microsoft.Insights/EventCategories/Read |Wylicza wszystkie kategorie dostępne w dzienniku aktywności. Używane w portalu Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. |
| Microsoft.Insights/eventtypes/values/Read |Wyświetl zdarzenia dziennika aktywności (zdarzeń zarządzania) w ramach subskrypcji. To uprawnienie jest dotyczy zarówno programowe, jak i portalu dostęp do dziennika aktywności. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Odczyt/zapis/usuwania ustawień diagnostycznych dzienników przepływ sieci. |
| Microsoft.Insights/LogDefinitions/Read |To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Profile odczytu/zapisu/usuwania dziennika (streaming dziennik aktywności na koncie zdarzenia koncentratora lub magazynu). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Odczyt/zapis/usuwania niemal w czasie rzeczywistym metryki alertów |
| Microsoft.Insights/MetricDefinitions/Read |Odczytaj definicje metryki (listy dostępnych typów metryki dla zasobu). |
| Microsoft.Insights/Metrics/Read |Odczytać metryki dla zasobu. |
| Microsoft.Insights/Register/Action |Zarejestruj dostawcę zasobów Azure monitora. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Alerty odczytu/zapisu/usuwania dziennika dla usługi Application Insights. |



> [!NOTE]
> Dostęp do alertów, ustawienia diagnostyki i metryki dla zasobu wymaga, czy użytkownik ma dostęp do odczytu do zasobu typie i zakresie tego zasobu. Tworzenie ("zapis") diagnostycznych ustawienie lub dziennika profil, który archiwa do konta magazynu lub strumieni do usługi event hubs wymaga od użytkownika musi mieć uprawnienie ListKeys również nad zasobem docelowym.
> 
> 

Na przykład przy użyciu powyższej tabeli, można utworzyć niestandardową rolę RBAC dla "działania czytnik dziennika" następująco:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Zagadnienia dotyczące zabezpieczeń dla danych monitorowania
Dane monitorowania — szczególnie pliki dziennika — mogą zawierać poufne informacje, takie jak adresy IP lub nazwy użytkownika. Monitorowanie danych z platformy Azure składa się z trzech podstawowych formularzy:

1. Dziennik aktywności w tym artykule opisano wszystkie akcje płaszczyzny kontroli w ramach subskrypcji platformy Azure.
2. Dzienniki diagnostyczne, które są emitowane przez zasób dzienników.
3. Metryki, które są emitowane przez zasoby.

Wszystkie trzy z następujących typów danych może przechowywane na koncie magazynu lub przesyłane strumieniowo do Centrum zdarzeń, które są ogólnego przeznaczenia zasobów platformy Azure. Ponieważ są to zasoby ogólnego przeznaczenia, tworzenie, usuwanie i uzyskiwanie dostępu do nich jest operacją uprzywilejowanych zastrzeżone przez administratora. Sugerujemy, użyj następujących wskazówek dla zasobów związanych z monitorowaniem aby uniemożliwić nadużycia:

* Użyj konta magazynu jednego, przeznaczonego dla danych monitorowania. Jeśli chcesz podzielić monitorowanie na wiele kont magazynu, nigdy nie udostępniaj użycia konta magazynu między monitorowania i -monitorowania danych, jak to przypadkowo może dać tych, którzy potrzebują tylko dostępu do monitorowania danych (na przykład SIEM innych firm) dostęp do innych niż monitorowania danych.
* Użyj jednego, przeznaczonego nazw usługi Service Bus lub Centrum zdarzeń na wszystkich ustawień diagnostycznych z tej samej przyczyny jak wyżej.
* Ograniczanie dostępu do kont magazynu związanych z monitorowaniem lub event hubs trzymając je w oddzielnej grupie zasobów, i [Użyj zakresu](../role-based-access-control/overview.md#scope) na poszczególnych ról monitorowania, aby ograniczyć dostęp do tej grupy zasobów.
* Nigdy nie należy przyznać uprawnienia ListKeys dla konta magazynu lub centra zdarzeń w zakresie subskrypcji, gdy użytkownik potrzebuje tylko dostęp do danych monitorowania. Zamiast tego należy przekazać te uprawnienia użytkownika na zasób lub grupa zasobów (Jeśli masz dedykowane monitorowania grupy zasobów) zakresu.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Ograniczanie dostępu do kont magazynu związanych z monitorowaniem
Gdy użytkownik lub aplikacja potrzebuje dostępu do danych na koncie magazynu monitorowania, należy [Generowanie sygnatury dostępu Współdzielonego konta](https://msdn.microsoft.com/library/azure/mt584140.aspx) na koncie magazynu, który zawiera dane monitorowania z poziomu usługi dostęp tylko do odczytu do magazynu obiektów blob. W programie PowerShell może to wyglądać tak:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Można następnie przekazać token do jednostki, że potrzebuje do odczytu z magazynu konta, a można wyświetlić listę i odczytywać wszystkie obiekty BLOB na tym koncie magazynu.

Alternatywnie Jeśli chcesz kontrolować te uprawnienia z RBAC można przyznać jednostkę uprawnienia Microsoft.Storage/storageAccounts/listkeys/action na tym koncie magazynu określonym. Jest to konieczne, użytkownicy muszą mieć możliwość ustawienia diagnostyki lub logowania profilu archiwizacji konto magazynu. Na przykład można utworzyć następującej roli niestandardowej RBAC dla użytkownika lub aplikacji, która potrzebuje tylko do odczytu z jednego konta magazynu:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> Uprawnienie ListKeys umożliwia użytkownikowi listy kluczy konta magazynu podstawowego i pomocniczego. Te klucze Przyznaj użytkownikowi wszystkie podpisane uprawnienia (Odczyt, zapis, tworzenie obiektów blob, Usuń obiekty BLOB itp.) we wszystkich podpisany usług (obiektu blob, kolejki, tabeli, plik) na tym koncie magazynu. Zalecamy używanie sygnatury dostępu Współdzielonego konta opisane powyżej, gdy jest to możliwe.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Ograniczanie dostępu do centrów zdarzeń związanych z monitorowaniem
Z usługą event hubs można wykonać tego samego typu, ale najpierw należy utworzyć regułę autoryzacji dedykowanych nasłuchiwania. Jeśli chcesz udzielić dostępu do aplikacji, która musi tylko do nasłuchiwania na centra zdarzeń związanych z monitorowaniem, wykonaj następujące czynności:

1. Utwórz zasady dostępu współużytkowanego na koncentratory zdarzeń, które zostały utworzone dla strumienia danych monitorowania z tylko oświadczenia nasłuchiwania. Można to zrobić w portalu. Na przykład użytkownik może wywołać ją "monitoringReadOnly." Jeśli to możliwe należy podać klucz bezpośrednio do użytkownika i przejdź do następnego kroku.
2. Jeśli konsument musi mieć możliwość klucza ad hoc, Przyznaj użytkownikowi akcji ListKeys zdarzenia koncentratora. To jest również dla użytkowników, którzy muszą mieć możliwość ustawienia diagnostyki lub zaloguj się do strumienia do usługi event hubs profilu. Na przykład można utworzyć regułę RBAC:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>Kolejne kroki
* [Przeczytaj informacje o RBAC i uprawnienia w programie Menedżer zasobów](../role-based-access-control/overview.md)
* [Zapoznanie się z omówieniem monitorowania na platformie Azure](monitoring-overview.md)

