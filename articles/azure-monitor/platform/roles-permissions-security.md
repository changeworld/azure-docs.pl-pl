---
title: Role, uprawnienia i zabezpieczenia w usłudze Azure Monitor
description: Dowiedz się, jak ograniczyć dostęp do zasobów monitorowania za pomocą wbudowanych ról i uprawnień usługi Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 81309f0b5781e6302887a5b079ed359e70659834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658986"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Role, uprawnienia i zabezpieczenia w usłudze Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wiele zespołów musi ściśle regulować dostęp do danych i ustawień monitorowania. Na przykład, jeśli masz członków zespołu, którzy pracują wyłącznie nad monitorowaniem (inżynierowie pomocy technicznej, inżynierowie DevOps) lub jeśli korzystasz z zarządzanego dostawcy usług, możesz udzielić im dostępu tylko do danych monitorowania, ograniczając jednocześnie ich zdolność do tworzenia, modyfikowania lub usuwać zasoby. W tym artykule pokazano, jak szybko zastosować wbudowaną rolę monitorowania RBAC do użytkownika na platformie Azure lub utworzyć własną rolę niestandardową dla użytkownika, który potrzebuje ograniczonych uprawnień monitorowania. Następnie omówiono zagadnienia dotyczące zabezpieczeń zasobów związanych z usługą Azure Monitor i jak można ograniczyć dostęp do danych, które zawierają.

## <a name="built-in-monitoring-roles"></a>Wbudowane role monitorowania
Wbudowane role usługi Azure Monitor mają na celu ograniczenie dostępu do zasobów w ramach subskrypcji, a jednocześnie umożliwiają osobom odpowiedzialnym za monitorowanie infrastruktury uzyskiwanie i konfigurowanie potrzebnych danych. Usługa Azure Monitor udostępnia dwie gotowe do użycia role: czytnik monitorowania i współautor monitorowania.

### <a name="monitoring-reader"></a>Czytnik monitoringu
Osoby przypisane do roli Czytnik monitorowania mogą wyświetlać wszystkie dane monitorowania w ramach subskrypcji, ale nie mogą modyfikować żadnych zasobów ani edytować żadnych ustawień związanych z monitorowaniem zasobów. Ta rola jest odpowiednia dla użytkowników w organizacji, takich jak inżynierowie pomocy technicznej lub operacyjnej, którzy muszą być w stanie:

* Wyświetlanie pulpitów nawigacyjnych monitorowania w portalu i tworzenie własnych prywatnych pulpitów nawigacyjnych monitorowania.
* Wyświetlanie reguł alertów zdefiniowanych w [alertach platformy Azure](alerts-overview.md)
* Zapytanie dotyczące metryk przy użyciu [interfejsu API REST usługi Azure Monitor,](https://msdn.microsoft.com/library/azure/dn931930.aspx) [poleceń cmdlet programu PowerShell](powershell-quickstart-samples.md)lub [interfejsu wiersza polecenia między platformami](cli-samples.md).
* Kwerenda dziennik aktywności przy użyciu portalu, interfejsu API REST usługi Azure Monitor, poleceń cmdlet programu PowerShell lub wieloplatformowego interfejsu wiersza polecenia.
* Służy do wyświetlania [ustawień diagnostycznych](diagnostic-settings.md) zasobu.
* Wyświetl [profil dziennika](activity-log-export.md) dla subskrypcji.
* Wyświetlanie ustawień skalowania automatycznego.
* Wyświetlanie aktywności i ustawień alertów.
* Uzyskaj dostęp do danych usługi Application Insights i wyświetl dane w UI Analytics.
* Wyszukiwanie danych obszaru roboczego usługi Log Analytics, w tym danych użycia obszaru roboczego.
* Wyświetlanie grup zarządzania usługą Log Analytics.
* Pobierz schemat wyszukiwania w obszarze roboczym usługi Log Analytics.
* Lista pakietów monitorowania w obszarze roboczym usługi Log Analytics.
* Pobieranie i wykonywanie zapisanych wyszukiwań w obszarze roboczym usługi Log Analytics.
* Pobierz konfigurację magazynu obszaru roboczego usługi Log Analytics.

> [!NOTE]
> Ta rola nie daje dostępu do odczytu do danych dziennika, który został przesłany strumieniowo do centrum zdarzeń lub przechowywane na koncie magazynu. [Poniżej](#security-considerations-for-monitoring-data) znajdują się informacje na temat konfigurowania dostępu do tych zasobów.
> 
> 

### <a name="monitoring-contributor"></a>Współautor monitorowania
Osoby przypisane do roli Współautor monitorowania mogą wyświetlać wszystkie dane monitorowania w subskrypcji i tworzyć lub modyfikować ustawienia monitorowania, ale nie mogą modyfikować żadnych innych zasobów. Ta rola jest nadzbiorem roli czytnika monitorowania i jest odpowiednia dla członków zespołu monitorującego organizacji lub dostawców usług zarządzanych, którzy oprócz powyższych uprawnień muszą również mieć możliwość:

* Publikowanie pulpitów nawigacyjnych monitorowania jako udostępnionego pulpitu nawigacyjnego.
* Ustawianie [ustawień diagnostycznych](diagnostic-settings.md) zasobu.\*
* Ustaw [profil dziennika](activity-log-export.md) dla subskrypcji.\*
* Ustaw aktywność i ustawienia reguł alertów za pośrednictwem [alertów platformy Azure](alerts-overview.md).
* Tworzenie testów i składników internetowych usługi Application Insights.
* Klucze udostępnione obszaru roboczego usługi List Log Analytics.
* Włączanie lub wyłączanie pakietów monitorowania w obszarze roboczym usługi Log Analytics.
* Tworzenie i usuwanie i wykonywanie zapisanych wyszukiwań w obszarze roboczym usługi Log Analytics.
* Tworzenie i usuwanie konfiguracji magazynu obszaru roboczego usługi Log Analytics.

\*użytkownik musi również oddzielnie przyznać uprawnienia Klawisze list do zasobu docelowego (konto magazynu lub obszar nazw centrum zdarzeń), aby ustawić profil dziennika lub ustawienie diagnostyczne.

> [!NOTE]
> Ta rola nie daje dostępu do odczytu do danych dziennika, który został przesłany strumieniowo do centrum zdarzeń lub przechowywane na koncie magazynu. [Poniżej](#security-considerations-for-monitoring-data) znajdują się informacje na temat konfigurowania dostępu do tych zasobów.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorowanie uprawnień i niestandardowych ról RBAC
Jeśli powyższe wbudowane role nie spełniają dokładnych potrzeb zespołu, można [utworzyć niestandardową rolę RBAC](../../role-based-access-control/custom-roles.md) z bardziej szczegółowymi uprawnieniami. Poniżej znajdują się typowe operacje RBAC usługi Azure Monitor z ich opisami.

| Operacja | Opis |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Odczyt, zapis, usuń] |Grupy akcji odczytu/zapisu/usuwania. |
| Microsoft.Insights/ActivityLogAlerts/[Odczyt, zapis, usuń] |Alerty dziennika aktywności odczytu/zapisu/usuwania. |
| Microsoft.Insights/AlertRules/[Odczyt, zapis, usuń] |Reguły alertów odczytu/zapisu/usuwania (z klasycznych alertów). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista zdarzeń (historia reguły alertu wyzwalane) dla reguł alertów. Dotyczy to tylko portalu. |
| Microsoft.Insights/AutoscaleSettings/[Odczyt, Zapis, Usuń] |Ustawienia automatycznego skalowania odczytu/zapisu/usuwania. |
| Microsoft.Insights/DiagnosticSettings/[Odczyt, Zapis, Usuń] |Ustawienia diagnostyczne odczytu/zapisu/usuwania. |
| Microsoft.Insights/EventCategories/Read |Wyliczyć wszystkie kategorie możliwe w dzienniku aktywności. Używany przez witrynę Azure portal. |
| Microsoft.Insights/eventtypes/digestevents/Read |To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. |
| Microsoft.Insights/eventtypes/values/Read |Lista zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie ma zastosowanie zarówno do programu, jak i dostępu do dziennika aktywności w portalu. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Odczyt, Zapis, Usuń] | Ustawienia diagnostyczne odczytu/zapisu/usuwania dzienników przepływu sieciowego. |
| Microsoft.Insights/LogDefinitions/Read |To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. |
| Microsoft.Insights/LogProfiles/[Odczyt, zapis, usuń] |Profile dzienników odczytu/zapisu/usuwania (dziennik aktywności przesyłania strumieniowego do centrum zdarzeń lub konta magazynu). |
| Microsoft.Insights/MetricAlerts/[Odczyt, zapis, usuń] |Odczyt/zapis/usuwanie w pobliżu alertów metryk w czasie rzeczywistym |
| Microsoft.Insights/MetricDefinitions/Read |Odczyt definicji metryk (lista dostępnych typów metryk dla zasobu). |
| Microsoft.Insights/Metryki/Odczyt |Odczyt metryki zasobu. |
| Microsoft.Insights/Zarejestruj się/Akcja |Zarejestruj dostawcę zasobów usługi Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Odczyt, Zapis, Usuń] |Alerty dziennika odczytu/zapisu/usuwania w usłudze Azure Monitor. |



> [!NOTE]
> Dostęp do alertów, ustawień diagnostycznych i metryk zasobu wymaga, aby użytkownik miał dostęp do odczytu do typu zasobu i zakresu tego zasobu. Tworzenie ("zapis") ustawienie diagnostyczne lub profilu dziennika, który archiwizuje do konta magazynu lub strumieni do centrum zdarzeń wymaga użytkownika, aby również mieć uprawnienia ListKeys na zasób docelowy.
> 
> 

Na przykład za pomocą powyższej tabeli można utworzyć niestandardową rolę RBAC dla "Czytnik dziennika aktywności" w ten sposób:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Zagadnienia związane z zabezpieczeniami dotyczące monitorowania danych
Dane monitorowania — w szczególności pliki dziennika — mogą zawierać poufne informacje, takie jak adresy IP lub nazwy użytkowników. Dane monitorowania z platformy Azure są dostępne w trzech podstawowych formularzach:

1. Dziennik aktywności, który opisuje wszystkie akcje płaszczyzny kontroli w ramach subskrypcji platformy Azure.
2. dzienniki zasobów, które są dziennikami emitowanymi przez zasób.
3. Metryki, które są emitowane przez zasoby.

Wszystkie trzy z tych typów danych mogą być przechowywane na koncie magazynu lub przesyłane strumieniowo do usługi Event Hub, z których oba są ogólnego przeznaczenia zasobów platformy Azure. Ponieważ są to zasoby ogólnego przeznaczenia, tworzenie, usuwanie i uzyskiwanie do nich dostępu jest uprzywilejowaną operacją zarezerwowaną dla administratora. Zalecamy stosowanie następujących praktyk w zakresie monitorowania zasobów związanych z monitorowaniem, aby zapobiec niewłaściwemu użyciu:

* Użyj jednego dedykowanego konta magazynu do monitorowania danych. Jeśli chcesz rozdzielić dane monitorowania na wiele kont magazynu, nigdy nie udostępniaj użycia konta magazynu między danymi monitorowania i nieobjętymi monitorowaniem, ponieważ może to przypadkowo dać tym, którzy potrzebują tylko dostępu do danych monitorowania (na przykład SIEM innej firmy) dostępu do danych niekontrolujących.
* Użyj jednej, dedykowanej magistrali usług i przestrzeni nazw usługi Event Hub we wszystkich ustawieniach diagnostycznych z tego samego powodu, co powyżej.
* Ogranicz dostęp do kont magazynu związanych z monitorowaniem lub centrów zdarzeń, przechowując je w osobnej grupie zasobów i [użyj zakresu](../../role-based-access-control/overview.md#scope) w rolach monitorowania, aby ograniczyć dostęp tylko do tej grupy zasobów.
* Nigdy nie udzielaj uprawnienia ListKeys dla kont magazynu lub centrów zdarzeń w zakresie subskrypcji, gdy użytkownik potrzebuje tylko dostępu do danych monitorowania. Zamiast tego należy nadać te uprawnienia użytkownikowi w zakresie zasobów lub grupy zasobów (jeśli masz dedykowaną grupę zasobów monitorowania).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Ograniczanie dostępu do kont magazynowych związanych z monitorowaniem
Gdy użytkownik lub aplikacja potrzebuje dostępu do monitorowania danych na koncie magazynu, należy [wygenerować sygnatury dostępu współdzielonego](https://msdn.microsoft.com/library/azure/mt584140.aspx) konta na koncie magazynu, który zawiera dane monitorowania z dostępem tylko do odczytu do magazynu obiektów blob na poziomie usługi. W programie PowerShell może to wyglądać następująco:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Następnie można przekazać token do jednostki, która musi odczytać z tego konta magazynu i można wyświetlić i odczytać ze wszystkich obiektów blob na tym koncie magazynu.

Alternatywnie, jeśli chcesz kontrolować to uprawnienie za pomocą funkcji RBAC, można udzielić tej jednostce uprawnienia Microsoft.Storage/storageAccounts/listkeys/action na tym koncie magazynu. Jest to konieczne dla użytkowników, którzy muszą mieć możliwość ustawienia diagnostycznego lub profilu dziennika do archiwizacji konta magazynu. Na przykład można utworzyć następującą niestandardową rolę RBAC dla użytkownika lub aplikacji, która musi odczytywać tylko z jednego konta magazynu:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Uprawnienie Klawisze list umożliwia użytkownikowi wyświetlanie listy kluczy podstawowego i pomocniczego konta magazynu. Te klucze udzielają użytkownikowi wszystkich podpisanych uprawnień (odczytu, zapisu, tworzenia obiektów blob, usuwania obiektów blob itp.) we wszystkich podpisanych usługach (obiekt blob, kolejka, tabela, plik) na tym koncie magazynu. W miarę możliwości zalecamy korzystanie z opisanego powyżej sygnatury dostępu Współdzielonego konta.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Ograniczanie dostępu do centrów zdarzeń związanych z monitorowaniem
Podobny wzorzec można śledzić za pomocą centrów zdarzeń, ale najpierw musisz utworzyć dedykowaną regułę autoryzacji nasłuchiwać. Jeśli chcesz udzielić, dostęp do aplikacji, która musi tylko nasłuchiwać centrum zdarzeń związanych z monitorowaniem, wykonaj następujące czynności:

1. Utwórz zasady dostępu współdzielonego w centrum zdarzeń, które zostały utworzone do przesyłania strumieniowego danych monitorowania tylko z oświadczeń nasłuchiuj. Można to zrobić w portalu. Na przykład można nazwać to "monitoringReadOnly". Jeśli to możliwe, będziesz chciał przekazać ten klucz bezpośrednio konsumentowi i pominąć następny krok.
2. Jeśli konsument musi mieć możliwość uzyskania klucza ad hoc, przyznać użytkownikowi ListKeys akcji dla tego centrum zdarzeń. Jest to również konieczne dla użytkowników, którzy muszą mieć możliwość ustawienia diagnostycznego lub profilu dziennika do przesyłania strumieniowego do centrów zdarzeń. Na przykład można utworzyć regułę RBAC:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorowanie w zabezpieczonej sieci wirtualnej

Usługa Azure Monitor potrzebuje dostępu do zasobów platformy Azure, aby zapewnić usługi, które można włączyć. Jeśli chcesz monitorować zasoby platformy Azure, jednocześnie zabezpieczając je przed dostępem do publicznego Internetu, możesz włączyć następujące ustawienia.

### <a name="secured-storage-accounts"></a>Zabezpieczone konta magazynu 

Dane monitorowania są często zapisywane na koncie magazynu. Można się upewnić, że nieupoważnioni użytkownicy nie mogą uzyskać dostępu do danych skopiowanych do konta magazynu. Aby zapewnić dodatkowe bezpieczeństwo, można zablokować dostęp do sieci, aby zezwolić autoryzowanym zasobom i zaufanym usługom firmy Microsoft na dostęp do konta magazynu, ograniczając konto magazynu do używania "wybranych sieci".
![Okno dialogowe](./media/roles-permissions-security/secured-storage-example.png) Usługi Azure Storage Dialog Azure Monitor jest uważany za jeden z tych "zaufanych usług firmy Microsoft" Jeśli zezwolisz zaufanym usługom firmy Microsoft na dostęp do zabezpieczonego magazynu, monitor platformy Azure będzie miał dostęp do zabezpieczonego konta magazynu; włączenie pisania dzienników zasobów usługi Azure Monitor, dziennika aktywności i metryk na koncie magazynu w tych chronionych warunkach. Umożliwi to również usługi Log Analytics do odczytu dzienników z zabezpieczonego magazynu.   


Aby uzyskać więcej informacji, zobacz [Zabezpieczenia sieciowe i usługa Azure Storage](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj o RBAC i uprawnieniach w Menedżerze zasobów](../../role-based-access-control/overview.md)
* [Przeczytaj omówienie monitorowania na platformie Azure](../../azure-monitor/overview.md)


