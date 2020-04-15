---
title: Organizowanie zasobów za pomocą grup zarządzania — Azure Governance
description: Dowiedz się więcej na temat grup zarządzania, sposobu działania ich uprawnień i korzystania z nich.
ms.date: 04/15/2020
ms.topic: overview
ms.openlocfilehash: cc60e4555f0fb2b920b8061fb044ce5dde990d38
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381539"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizowanie zasobów przy użyciu grup zarządzania platformy Azure

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.
Wszystkie subskrypcje w ramach jednej grupy zarządzania muszą ufać tej samej dzierżawie usługi Azure Active Directory.

Na przykład możesz zastosować do grupy zarządzania zasady ograniczające regiony dostępne na potrzeby tworzenia maszyny wirtualnej. Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcji i zasobów w ramach tej grupy zarządzania, zezwalając na tworzenie maszyn wirtualnych tylko w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcji

Można utworzyć elastyczną strukturę grup zarządzania i subskrypcji w celu organizowania zasobów w hierarchię na potrzeby ujednoliconego zarządzania zasadami i dostępem. Na poniższym diagramie przedstawiono przykład tworzenia hierarchii dla nadzoru przy użyciu grup zarządzania.

:::image type="content" source="./media/tree.png" alt-text="Przykład drzewa hierarchii grupy zarządzania" border="false":::

Możesz utworzyć hierarchię stosującą zasady, na przykład ograniczające lokalizacje maszyn wirtualnych do regionu Zachodnie stany USA w grupie o nazwie „Produkcja”. Ta zasada będzie dziedziczyć na wszystkich subskrypcjach umowy Enterprise Agreement (EA), które są elementami podrzędnymi tej grupy zarządzania i będą stosowane do wszystkich maszyn wirtualnych w ramach tych subskrypcji. Te zasady zabezpieczeń nie mogą zostać zmienione przez właściciela zasobu lub subskrypcji, co zapewnia ulepszony nadzór.

Innym scenariuszem, w którym można użyć grup zarządzania, jest zapewnienie użytkownikom dostępu do wielu subskrypcji. Przenosząc wiele subskrypcji do tej grupy zarządzania, można utworzyć jedno przypisanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) do grupy zarządzania, które będzie dziedziczyło ten dostęp do wszystkich subskrypcji. Jedno przypisanie grupy zarządzania może zapewnić użytkownikom dostęp do wszystkiego, czego potrzebują, bez konieczności tworzenia skryptów RBAC dla wielu subskrypcji.

### <a name="important-facts-about-management-groups"></a>Ważne informacje dotyczące grup zarządzania

- W jednym katalogu może być obsługiwane 10000 grup zarządzania.
- Drzewo grupy zarządzania może obsługiwać maksymalnie sześć poziomów głębokości.
  - Ten limit nie obejmuje poziomu głównego lub poziomu subskrypcji.
- Każda grupa zarządzania i subskrypcja może obsługiwać tylko jeden element nadrzędny.
- Każda grupa zarządzania może mieć wiele elementów podrzędnych.
- Wszystkie subskrypcje i grupy zarządzania znajdują się w jednej hierarchii w każdym katalogu. Zobacz [Ważne informacje dotyczące głównej grupy zarządzania](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Główna grupa zarządzania dla każdego katalogu

Do każdego katalogu jest przypisywana jedna grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. [Administrator globalny usługi Azure AD musi podnieść swój poziom uprawnień](../../role-based-access-control/elevate-access-global-admin.md) do roli Administrator dostępu użytkowników, aby początkowo być właścicielem tej głównej grupy. Po podniesieniu poziomu dostępu administrator może przypisać dowolną rolę RBAC innym użytkownikom lub grupom w katalogu, aby zarządzać hierarchią. Jako administrator możesz przypisać własne konto jako właściciela głównej grupy zarządzania.

### <a name="important-facts-about-the-root-management-group"></a>Ważne informacje dotyczące głównej grupy zarządzania

- Domyślnie nazwa wyświetlana głównej grupy zarządzania to **Grupa główna dzierżawy**. Identyfikatorem jest identyfikator usługi Azure Active Directory.
- Aby zmienić nazwę wyświetlaną, Twoje konto musi mieć przypisaną rolę właściciela lub współautora w głównej grupie zarządzania. Zobacz [Zmienianie nazwy grupy zarządzania w](manage.md#change-the-name-of-a-management-group) celu zaktualizowania nazwy grupy zarządzania.
- Głównej grupy zarządzania nie można przenieść ani usunąć, w odróżnieniu od innych grup zarządzania.  
- Wszystkie subskrypcje i grupy zarządzania składają się do jednej głównej grupy zarządzania w katalogu.
  - Wszystkie zasoby w katalogu składają się do głównej grupy zarządzania dla globalnego zarządzania.
  - Nowe subskrypcje są automatycznie domyślnie dodawane do głównej grupy zarządzania podczas tworzenia.
- Główna grupa zarządzania jest widoczna dla wszystkich klientów platformy Azure, ale nie wszyscy klienci mają dostęp do zarządzania tą główną grupą zarządzania.
  - Każdy, kto ma dostęp do subskrypcji, może zobaczyć kontekst, w którym ta subskrypcja znajduje się w hierarchii.  
  - Nikt nie otrzymuje domyślnego dostępu do głównej grupy zarządzania. Administratorzy globalni usługi Azure AD są jedynymi użytkownikami, którzy mogą samodzielnie podnieść swój poziom uprawnień, aby uzyskać dostęp. Po uzyskaniu dostępu do głównej grupy zarządzania administratorzy globalni mogą przypisać dowolną rolę RBAC innym użytkownikom do zarządzania  
    go.
- W SDK główna grupa zarządzania lub "Katalog główny dzierżawcy" działa jako grupa zarządzania.

> [!IMPORTANT]
> Wszystkie przypisania dostępu użytkowników lub zasad w głównej grupie zarządzania **dotyczą wszystkich zasobów w katalogu**. W związku z tym wszyscy klienci powinni ocenić potrzebę posiadania elementów zdefiniowanych w tym zakresie. Dostęp użytkowników i przypisania zasad powinny być "Must Have" tylko w tym  
> Zakres.

## <a name="initial-setup-of-management-groups"></a>Konfiguracja początkowa grup zarządzania

Kiedy dowolny użytkownik rozpoczyna korzystanie z grup zarządzania, wykonywany jest proces konfiguracji początkowej. Pierwszym jego krokiem jest utworzenie głównej grupy zarządzania w katalogu. Po utworzeniu tej grupy wszystkie istniejące subskrypcje, które znajdują się w katalogu, stają się elementami podrzędnymi głównej grupy zarządzania.
Celem tego procesu jest upewnienie się, że istnieje tylko jedna hierarchia grup zarządzania w katalogu. Pojedyncza hierarchia w katalogu umożliwia klientom administracyjnym klientom stosowanie globalnego dostępu i zasad, których inni klienci w katalogu nie mogą obejść. Wszystkie przypisania w głównej grupie będą stosowane do całej hierarchii, która obejmuje wszystkie grupy zarządzania, subskrypcje, grupy zasobów i zasoby w dzierżawie usługi Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Problemy z wyświetlaniem wszystkich subskrypcji

W przypadku niektórych katalogów, które rozpoczęły korzystanie z grup zarządzania niedługo po udostępnieniu wersji zapoznawczej (przed 25 czerwca 2018 r.), mógł wystąpić problem polegający na tym, że nie wszystkie subskrypcje zostały umieszczone w hierarchii. Proces umieszczania subskrypcji w hierarchii był implementowany po przypisaniu roli lub zasad w głównej grupie zarządzania w katalogu.

### <a name="how-to-resolve-the-issue"></a>Jak rozwiązać ten problem

Istnieją dwie opcje rozwiązania tego problemu.

- Usuń wszystkie przypisania ról i zasad z głównej grupy zarządzania
  - Usunięcie wszystkich przypisań ról i zasad z głównej grupy zarządzania spowoduje wypełnienie hierarchii przez usługę wszystkimi subskrypcjami podczas następnego cyklu nocnego. Ten proces ma na celu zapewnienie, że nie został przypadkowo udzielony dostęp lub nie przypisano przypadkowo zasad do wszystkich subskrypcji dzierżawy.
  - Najlepszym sposobem wykonania tego procesu bez wpływu na usługi jest zastosowanie przypisań ról lub zasad na poziomie o jeden niższym niż główna grupa zarządzania. Wtedy będzie można usunąć wszystkie przypisania z zakresu głównego.
- Bezpośrednio wywołaj interfejs API, aby rozpocząć proces wypełniania
  - Każdy klient w katalogu może wywołać interfejs API _TenantBackfillStatusRequest_ lub _StartTenantBackfillRequest_. Wywołanie interfejsu API StartTenantBackfillRequest powoduje rozpoczęcie procesu początkowej konfiguracji, który polega na przeniesieniu wszystkich subskrypcji do hierarchii. Ten proces rozpoczyna również wymuszanie dodawania wszystkich nowych subskrypcji jako elementu podrzędnego głównej grupy zarządzania.
    Ten proces można wykonać bez zmiany przydziałów na poziomie głównej grupy. Wywołując ten interfejs API, potwierdzasz, że dowolne zasady i przypisania dostępu określone w grupie głównej mogą być stosowane do wszystkich subskrypcji.

Jeśli masz pytania na temat tego procesu wypełniania, napisz na adres `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Grupy zarządzania platformy Azure obsługują [kontrolę dostępu opartą na rolach (RBAC) na platformie Azure](../../role-based-access-control/overview.md) dla wszystkich uprawnień dostępu do zasobów oraz definicji ról.
Te uprawnienia są dziedziczone do zasobów podrzędnych, które istnieją w hierarchii. Dowolną rolę RBAC można przypisać do grupy zarządzania, która odziedziczy hierarchię do zasobów. Na przykład można przypisać do grupy zarządzania rolę RBAC Współautor maszyny wirtualnej. Ta rola nie ma żadnej akcji wykonywanej na grupie zarządzania, ale będzie dziedziczona do wszystkich maszyn wirtualnych w ramach tej grupy zarządzania.

Na poniższym wykresie przedstawiono listę ról i obsługiwane akcje na grupach zarządzania.

| Nazwa roli RBAC             | Utwórz | Zmień nazwę | Przenieść\*\* | Usuń | Przypisywanie dostępu | Przypisywanie zasad | Odczyt  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Właściciel                       | X      | X      | X        | X      | X             | X             | X     |
|Współautor                 | X      | X      | X        | X      |               |               | X     |
|Współpracownik MG\*            | X      | X      | X        | X      |               |               | X     |
|Czytelnik                      |        |        |          |        |               |               | X     |
|Czytnik MG\*                 |        |        |          |        |               |               | X     |
|Współautor zasad zasobów |        |        |          |        |               | X             |       |
|Administrator dostępu użytkowników   |        |        |          |        | X             | X             |       |

\*: MG Contributor i MG Reader zezwalają użytkownikom tylko na wykonywania tych akcji w zakresie grupy zarządzania.  
\*\*: Przypisania ról w głównej grupie zarządzania nie są wymagane do przenoszenia subskrypcji lub grupy zarządzania do i z niej. Aby uzyskać szczegółowe informacje o przenoszeniu elementów w hierarchii, zobacz [Zarządzanie zasobami przy użyciu grup zarządzania](manage.md).

## <a name="custom-rbac-role-definition-and-assignment"></a>Niestandardowa definicja i przypisanie roli RBAC

Niestandardowa obsługa ról RBAC dla grup zarządzania jest obecnie w wersji zapoznawczej z pewnymi [ograniczeniami](#limitations). Zakres grupy zarządzania można zdefiniować w możliwym do przypisania zakresie definicji roli. Ta niestandardowa rola RBAC będzie wówczas dostępna do przypisania w tej grupie zarządzania oraz we wszystkich zawartych w niej grupach zarządzania, subskrypcjach, grupach zasobów lub zasobach. Ta rola niestandardowa będzie dziedziczyć hierarchię tak jak każda wbudowana rola.  

### <a name="example-definition"></a>Przykładowa definicja

[Definiowanie i tworzenie roli niestandardowej](../../role-based-access-control/custom-roles.md) nie zmienia się wraz z dołączaniem grup zarządzania. Pełna ścieżka służy do definiowania grupy zarządzania **/providers/Microsoft.Management/managementgroups/{groupId}**.

Użyj identyfikatora grupy zarządzania, a nie nazwy wyświetlanej grupy zarządzania. Ten typowy błąd występuje, ponieważ oba są niestandardowe zdefiniowane pola podczas tworzenia grupy zarządzania.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problemy z łamaniem definicji roli i ścieżki hierarchii przydziałów

Definicje ról są przypisywane zakres w dowolnym miejscu w hierarchii grupy zarządzania. Definicję roli można zdefiniować w nadrzędnej grupie zarządzania, podczas gdy rzeczywiste przypisanie roli istnieje w subskrypcji podrzędnej. Ponieważ istnieje relacja między dwoma elementami, podczas próby oddzielenia przypisania od jego definicji zostanie wyświetlony błąd.

Na przykład przyjrzyjmy się małej sekcji hierarchii dla wizualizacji.

:::image type="content" source="./media/subtree.png" alt-text="poddrąb" border="false":::

Załóżmy, że w grupie zarządzania marketingiem zdefiniowano rolę niestandardową. Ta rola niestandardowa jest następnie przypisywana w dwóch bezpłatnych subskrypcjach próbnych.  

Jeśli spróbujemy przenieść jedną z tych subskrypcji, aby być dziewką grupy zarządzania produkcją, ten ruch spowoduje przerwanie ścieżki z przypisania roli subskrypcji do definicji roli grupy zarządzania marketingiem. W tym scenariuszu pojawi się błąd informujący, że przeniesienie nie jest dozwolone, ponieważ spowoduje to przerwanie tej relacji.  

Istnieje kilka różnych opcji, aby naprawić ten scenariusz:
- Usuń przypisanie roli z subskrypcji przed przeniesieniem subskrypcji do nowego nadrzędnego MG.
- Dodaj subskrypcję do zakresu przypisywania definicji roli.
- Zmień zakres przypisywalny w definicji roli. W powyższym przykładzie można zaktualizować zakresy przypisywalne z marketingu do głównej grupy zarządzania, tak aby definicja mogła zostać osiągnięta przez obie gałęzie hierarchii.  
- Utwórz dodatkową rolę niestandardową, która zostanie zdefiniowana w innej gałęzi. Ta nowa rola będzie wymagać przypisania roli, które mają być zmienione w subskrypcji również.  

### <a name="limitations"></a>Ograniczenia  

Istnieją ograniczenia, które istnieją podczas korzystania z ról niestandardowych w grupach zarządzania. 

 - Można zdefiniować tylko jedną grupę zarządzania w zakresach przypisywalnych nowej roli. To ograniczenie jest w celu zmniejszenia liczby sytuacji, w których definicje ról i przypisania ról są rozłączone. Dzieje się tak, gdy subskrypcja lub grupa zarządzania z przypisaniem roli jest przenoszona do innego obiektu nadrzędnego, który nie ma definicji roli.  
 - Akcje RBAC Data Plane nie mogą być definiowane w rolach niestandardowych grupy zarządzania. To ograniczenie jest w miejscu, ponieważ istnieje problem z opóźnieniem z akcji RBAC aktualizowanie dostawców zasobów płaszczyzny danych. Ten problem opóźnienia jest opracowywany i te akcje zostaną wyłączone z definicji roli w celu zmniejszenia ryzyka.
 - Usługa Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie przypisywalnym definicji roli. Jeśli na liście znajduje się literówka lub niepoprawny identyfikator grupy zarządzania, definicja roli będzie nadal tworzona.  

## <a name="moving-management-groups-and-subscriptions"></a>Przenoszenie grup zarządzania i subskrypcji 

Aby grupa zarządzania lub subskrypcja była dziewką innej grupy zarządzania, należy ocenić trzy reguły jako prawdziwe.

Jeśli wykonujesz akcję przenoszenia, musisz: 

- Uprawnienia do zapisu grupy zarządzania i przypisania roli w podrzędnej subskrypcji lub grupie zarządzania.
  - Wbudowany przykład roli **Właściciel**
- Dostęp do zapisu grupy zarządzania w docelowej nadrzędnej grupie zarządzania.
  - Przykład roli wbudowanej: **właściciel**, **współautor**, **współautor grupy zarządzania**
- Dostęp do zapisu grupy zarządzania w istniejącej nadrzędnej grupie zarządzania.
  - Przykład roli wbudowanej: **właściciel**, **współautor**, **współautor grupy zarządzania**

**Wyjątek:** Jeśli obiekt docelowy lub istniejąca nadrzędna grupa zarządzania jest główną grupą zarządzania, wymagania dotyczące uprawnień nie mają zastosowania. Ponieważ główna grupa zarządzania jest domyślnym miejscem docelowym dla wszystkich nowych grup zarządzania i subskrypcji, nie potrzebujesz uprawnień do przenoszenia elementu.

Jeśli rola Właściciela w subskrypcji jest dziedziczona z bieżącej grupy zarządzania, cele przenoszenia są ograniczone. Subskrypcję można przenieść tylko do innej grupy zarządzania, w której masz rolę Właściciel. Nie można przenieść go do grupy zarządzania, w której jesteś współautorem, ponieważ utracisz własność subskrypcji. Jeśli jesteś bezpośrednio przypisany do roli Właściciel dla subskrypcji (nie dziedziczone z grupy zarządzania), można przenieść go do dowolnej grupy zarządzania, gdzie jesteś współautorem.

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md). Możesz wyszukiwać wszystkie zdarzenia, które wystąpiły w grupie zarządzania w tej samej lokalizacji centralnej co inne zasoby platformy Azure. Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

:::image type="content" source="./media/al-mg.png" alt-text="Dzienniki aktywności z grupami zarządzania" border="false":::

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](./create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](./manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)
