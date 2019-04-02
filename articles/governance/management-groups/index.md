---
title: Organizowanie zasobów przy użyciu grup zarządzania platformy Azure — nadzór na platformie Azure
description: 'Dowiedz się więcej na temat grup zarządzania, sposobu działania ich uprawnień i korzystania z nich.'
author: rthorn17
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.date: 02/20/2019
ms.author: rithorn
ms.topic: overview
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizowanie zasobów przy użyciu grup zarządzania platformy Azure

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.

Na przykład możesz zastosować do grupy zarządzania zasady ograniczające regiony dostępne na potrzeby tworzenia maszyny wirtualnej. Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcji i zasobów w ramach tej grupy zarządzania, zezwalając na tworzenie maszyn wirtualnych tylko w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcji

Można utworzyć elastyczną strukturę grup zarządzania i subskrypcji w celu organizowania zasobów w hierarchię na potrzeby ujednoliconego zarządzania zasadami i dostępem. Na poniższym diagramie przedstawiono przykład tworzenia hierarchii dla nadzoru przy użyciu grup zarządzania.

![Przykład drzewa hierarchii grupy zarządzania](./media/tree.png)

Utwórz hierarchię w celu zastosowania zasad — na przykład ograniczenia lokalizacji maszyn wirtualnych w grupie „Production” do regionu Zachodnie stany USA. Te zasady będą dziedziczone przez obie subskrypcje umowy EA w ramach tej grupy zarządzania i będą stosowane do wszystkich maszyn wirtualnych w ramach tych subskrypcji. Te zasady zabezpieczeń nie mogą zostać zmienione przez właściciela zasobu lub subskrypcji, co zapewnia ulepszony nadzór.

Innym scenariuszem, w którym można użyć grup zarządzania, jest zapewnienie użytkownikom dostępu do wielu subskrypcji. Przenosząc wiele subskrypcji do tej grupy zarządzania, można utworzyć jedno przypisanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) do grupy zarządzania, które będzie dziedziczyło ten dostęp do wszystkich subskrypcji.
Jedno przypisanie grupy zarządzania może zapewnić użytkownikom dostęp do wszystkiego, czego potrzebują, bez konieczności tworzenia skryptów RBAC dla wielu subskrypcji.

### <a name="important-facts-about-management-groups"></a>Ważne informacje dotyczące grup zarządzania

- W jednym katalogu może być obsługiwane 10000 grup zarządzania.
- Drzewo grupy zarządzania może obsługiwać maksymalnie sześć poziomów głębokości.
  - Ten limit nie obejmuje poziomu głównego lub poziomu subskrypcji.
- Każda grupa zarządzania i subskrypcja może obsługiwać tylko jeden element nadrzędny.
- Każda grupa zarządzania może mieć wiele elementów podrzędnych.
- Wszystkie subskrypcje i grupy zarządzania znajdują się w jednej hierarchii w każdym katalogu. Zobacz [Ważne informacje dotyczące głównej grupy zarządzania](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Główna grupa zarządzania dla każdego katalogu

Do każdego katalogu jest przypisywana jedna grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania.
Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. [Administrator globalny usługi Azure AD musi podnieść swój poziom uprawnień](../../role-based-access-control/elevate-access-global-admin.md), aby początkowo być właścicielem tej głównej grupy. Gdy administrator jest właścicielem grupy, może przypisać dowolną rolę RBAC innym użytkownikom lub grupom katalogu, aby zarządzać hierarchią.

### <a name="important-facts-about-the-root-management-group"></a>Ważne informacje dotyczące głównej grupy zarządzania

- Nazwa i identyfikator głównej grupy zarządzania są nadawane domyślnie. Nazwę wyświetlaną można zaktualizować w dowolnym momencie, aby była wyświetlana inaczej w witrynie Azure Portal.
  - Nazwą będzie „Główna grupa dzierżawy”.
  - Identyfikatorem będzie identyfikator usługi Azure Active Directory.
- Głównej grupy zarządzania nie można przenieść ani usunąć, w odróżnieniu od innych grup zarządzania.  
- Wszystkie subskrypcje i grupy zarządzania składają się do jednej głównej grupy zarządzania w katalogu.
  - Wszystkie zasoby w katalogu składają się do głównej grupy zarządzania dla globalnego zarządzania.
  - Nowe subskrypcje są automatycznie domyślnie dodawane do głównej grupy zarządzania podczas tworzenia.
- Główna grupa zarządzania jest widoczna dla wszystkich klientów platformy Azure, ale nie wszyscy klienci mają dostęp do zarządzania tą główną grupą zarządzania.
  - Każdy, kto ma dostęp do subskrypcji, może zobaczyć kontekst, w którym ta subskrypcja znajduje się w hierarchii.  
  - Nikt nie otrzymuje domyślnego dostępu do głównej grupy zarządzania. Administratorzy globalni usługi Azure AD są jedynymi użytkownikami, którzy mogą samodzielnie podnieść swój poziom uprawnień, aby uzyskać dostęp.  Gdy administratorzy globalni mają dostęp, mogą przypisać dowolne role RBAC innym użytkownikom w celu zarządzania.  

> [!IMPORTANT]
> Wszystkie przypisania dostępu użytkowników lub zasad w głównej grupie zarządzania **dotyczą wszystkich zasobów w katalogu**.
> W związku z tym wszyscy klienci powinni ocenić potrzebę posiadania elementów zdefiniowanych w tym zakresie.
> Przypisania dostępu użytkowników i zasad powinny być „niezbędne” tylko w tym zakresie.  

## <a name="initial-setup-of-management-groups"></a>Konfiguracja początkowa grup zarządzania

Kiedy dowolny użytkownik rozpoczyna korzystanie z grup zarządzania, wykonywany jest proces konfiguracji początkowej. Pierwszym jego krokiem jest utworzenie głównej grupy zarządzania w katalogu. Po utworzeniu tej grupy wszystkie istniejące subskrypcje, które znajdują się w katalogu, stają się elementami podrzędnymi głównej grupy zarządzania. Celem tego procesu jest upewnienie się, że istnieje tylko jedna hierarchia grup zarządzania w katalogu. Pojedyncza hierarchia w katalogu umożliwia klientom administracyjnym klientom stosowanie globalnego dostępu i zasad, których inni klienci w katalogu nie mogą obejść. Wszystkie przypisania w głównej grupie będą stosowane do całej hierarchii, która obejmuje wszystkie grupy zarządzania, subskrypcje, grupy zasobów i zasoby w dzierżawie usługi Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Problemy z wyświetlaniem wszystkich subskrypcji

W przypadku niektórych katalogów, które rozpoczęły korzystanie z grup zarządzania niedługo po udostępnieniu wersji zapoznawczej (przed 25 czerwca 2018 r.), mógł wystąpić problem polegający na tym, że nie umieszczono wszystkich subskrypcji w hierarchii.  Procesy umieszczania subskrypcji w hierarchii były implementowane po przypisaniu roli lub zasad w głównej grupie zarządzania w katalogu.

### <a name="how-to-resolve-the-issue"></a>Jak rozwiązać ten problem

Istnieją dwie opcje rozwiązania tego problemu.

1. Usuń wszystkie przypisania ról i zasad z głównej grupy zarządzania
   1. Usunięcie wszystkich przypisań ról i zasad z głównej grupy zarządzania spowoduje wypełnienie hierarchii przez usługę wszystkimi subskrypcjami podczas następnego cyklu nocnego.  Ten proces ma na celu zapewnienie, że nie został przypadkowo udzielony dostęp lub nie przypisano przypadkowo zasad do wszystkich subskrypcji dzierżawy.
   1. Najlepszym sposobem wykonania tego procesu bez wpływu na usługi jest zastosowanie przypisań ról lub zasad na poziomie o jeden niższym niż główna grupa zarządzania. Wtedy będzie można usunąć wszystkie przypisania z zakresu głównego.
1. Bezpośrednio wywołaj interfejs API, aby rozpocząć proces wypełniania
   1. Każdy klient w katalogu może wywołać interfejs API *TenantBackfillStatusRequest* lub *StartTenantBackfillRequest*. Wywołanie interfejsu API StartTenantBackfillRequest powoduje rozpoczęcie procesu początkowej konfiguracji, który polega na przeniesieniu wszystkich subskrypcji do hierarchii. Ten proces rozpoczyna również wymuszanie dodawania wszystkich nowych subskrypcji jako elementu podrzędnego głównej grupy zarządzania. Ten proces można wykonać bez zmiany przydziałów na poziomie głównej grupy. Wywołując ten interfejs API, potwierdzasz, że dowolne zasady i przypisania dostępu określone w grupie głównej mogą być stosowane do wszystkich subskrypcji.

Jeśli masz pytania na temat tego procesu wypełniania, napisz na adres managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Grupy zarządzania platformy Azure obsługują [kontrolę dostępu opartą na rolach (RBAC) na platformie Azure](../../role-based-access-control/overview.md) dla wszystkich uprawnień dostępu do zasobów oraz definicji ról.
Te uprawnienia są dziedziczone do zasobów podrzędnych, które istnieją w hierarchii. Dowolną wbudowaną rolę RBAC można przypisać do grupy zarządzania, aby była dziedziczona w dół hierarchii do zasobów.
Na przykład można przypisać do grupy zarządzania rolę RBAC Współautor maszyny wirtualnej. Ta rola nie ma żadnej akcji wykonywanej na grupie zarządzania, ale będzie dziedziczona do wszystkich maszyn wirtualnych w ramach tej grupy zarządzania.

Na poniższym wykresie przedstawiono listę ról i obsługiwane akcje na grupach zarządzania.

| Nazwa roli RBAC             | Przycisk Utwórz | Zmiana nazwy | Przenoszenie** | Usuwanie | Przypisywanie dostępu | Przypisywanie zasad | Odczyt  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Właściciel                       | X      | X      | X      | X      | X             | X             | X     |
|Współautor                 | X      | X      | X      | X      |               |               | X     |
|Współautor grupy zarządzania*             | X      | X      | X      | X      |               |               | X     |
|Czytelnik                      |        |        |        |        |               |               | X     |
|Czytelnik grupy zarządzania*                  |        |        |        |        |               |               | X     |
|Współautor zasad zasobów |        |        |        |        |               | X             |       |
|Administrator dostępu użytkowników   |        |        |        |        | X             |               |       |

*: Role Współautor grupy zarządzania i Czytelnik grupy zarządzania zezwalają użytkownikom na wykonywanie tych akcji tylko dla zakresu grupy zarządzania.  
**: Przypisania ról w głównej grupie zarządzania nie są wymagane, aby przenieść subskrypcję lub grupę zarządzania do tej grupy lub z niej.  Aby uzyskać szczegółowe informacje o przenoszeniu elementów w hierarchii, zobacz [Zarządzanie zasobami przy użyciu grup zarządzania](manage.md).

### <a name="custom-rbac-role-definition-and-assignment"></a>Definicja i przypisanie niestandardowej roli RBAC

Niestandardowe role RBAC nie są obecnie obsługiwane dla grup zarządzania. Zobacz [forum opinii dotyczących grup zarządzania](https://aka.ms/mgfeedback), aby sprawdzić stan tego elementu.

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md). Możesz wyszukiwać wszystkie zdarzenia, które wystąpiły w grupie zarządzania w tej samej lokalizacji centralnej co inne zasoby platformy Azure.  Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

![Dzienniki aktywności z grupami zarządzania](media/al-mg.png)

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)