---
title: Organizowanie zasobów przy użyciu grup zarządzania platformy Azure
description: Dowiedz się więcej na temat grup zarządzania i sposobu ich używania.
author: rthorn17
manager: rithorn
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/18/2018
ms.author: rithorn
ms.openlocfilehash: d031059f9811cedb703fec4920e00fd1b2e3f877
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045353"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizowanie zasobów przy użyciu grup zarządzania platformy Azure

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.

Na przykład możesz zastosować do grupy zarządzania zasady ograniczające regiony dostępne na potrzeby tworzenia maszyny wirtualnej. Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcji i zasobów w ramach tej grupy zarządzania, zezwalając na tworzenie maszyn wirtualnych tylko w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcji

Można utworzyć elastyczną strukturę grup zarządzania i subskrypcji w celu organizowania zasobów w hierarchię na potrzeby ujednoliconego zarządzania zasadami i dostępem. Na poniższym diagramie przedstawiono przykład tworzenia hierarchii dla nadzoru przy użyciu grup zarządzania.

![drzewo](./media/MG_overview.png)

Dzięki utworzeniu hierarchii jak w tym przykładzie można zastosować zasady, na przykład ograniczenie lokalizacji maszyny wirtualnej do regionu Zachodnie stany USA, w grupie „Infrastructure Team management group” (Grupa zarządzania zespołu infrastruktury), aby włączyć wewnętrzne zasady zgodności i zabezpieczeń. Te zasady będą dziedziczone przez obie subskrypcje umowy EA w ramach tej grupy zarządzania i będą stosowane do wszystkich maszyn wirtualnych w ramach tych subskrypcji. Ponieważ zasady są dziedziczone z grupy zarządzania do subskrypcji, te zasady zabezpieczeń nie mogą zostać zmienione przez właściciela zasobu lub subskrypcji, co zapewnia ulepszony nadzór.

Innym scenariuszem, w którym można użyć grup zarządzania, jest zapewnienie użytkownikom dostępu do wielu subskrypcji. Przenosząc wiele subskrypcji do tej grupy zarządzania, można utworzyć jedno przypisanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) do grupy zarządzania, które będzie dziedziczyło ten dostęp do wszystkich subskrypcji.
Bez konieczności tworzenia skryptów przypisań RBAC dla wielu subskrypcji jedno przypisanie grupy zarządzania może umożliwić użytkownikom dostęp do wszystkiego, czego potrzebują.

### <a name="important-facts-about-management-groups"></a>Ważne informacje dotyczące grup zarządzania

- W jednym katalogu może być obsługiwane 10000 grup zarządzania.
- Drzewo grupy zarządzania może obsługiwać maksymalnie sześć poziomów głębokości.
  - Ten limit nie obejmuje poziomu głównego lub poziomu subskrypcji.
- Każda grupa zarządzania i subskrypcja może obsługiwać tylko jeden element nadrzędny.
- Każda grupa zarządzania może mieć wiele elementów podrzędnych.
- Wszystkie subskrypcje i grupy zarządzania są zawarte w jednej hierarchii w każdym katalogu. Zobacz [Ważne informacje dotyczące głównej grupy zarządzania](#important-facts-about-the-root-management-group), aby zapoznać się z wyjątkami w wersji zapoznawczej.

## <a name="root-management-group-for-each-directory"></a>Główna grupa zarządzania dla każdego katalogu

Do każdego katalogu jest przypisywana jedna grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania.
Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. [Administrator katalogu musi podnieść swój poziom uprawnień](../../role-based-access-control/elevate-access-global-admin.md), aby początkowo być właścicielem tej głównej grupy. Gdy administrator jest właścicielem grupy, może przypisać dowolną rolę RBAC innym użytkownikom lub grupom katalogu, aby zarządzać hierarchią.

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
  - Nikt nie otrzymuje domyślnego dostępu do głównej grupy zarządzania. Administratorzy globalni katalogu są jedynymi użytkownikami, którzy mogą samodzielnie podnieść swój poziom uprawnień, aby uzyskać dostęp.  Gdy administratorzy katalogu mają dostęp, mogą przypisać dowolne role RBAC innym użytkownikom w celu zarządzania.  

> [!NOTE]
> Jeśli Twój katalog zaczął korzystać z usługi grup zarządzania przed 25.06.2018 r., może nie być skonfigurowany ze wszystkimi subskrypcjami w hierarchii. Zespół ds. grup zarządzania wstecznie aktualizuje każdy katalog, który rozpoczął korzystanie z grup zarządzania w publicznej wersji zapoznawczej przed tą datą, w lipcu/sierpnia 2018 r. Wszystkie subskrypcje w katalogach zostaną ustawione jako elementy podrzędne głównej grupy zarządzania.
>
> Jeśli masz pytania na temat tego procesu, skontaktuj się z managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Konfiguracja początkowa grup zarządzania

Kiedy dowolny użytkownik rozpoczyna korzystanie z grup zarządzania, wykonywany jest proces konfiguracji początkowej. Pierwszym jego krokiem jest utworzenie głównej grupy zarządzania w katalogu. Po utworzeniu tej grupy wszystkie istniejące subskrypcje, które znajdują się w katalogu, stają się elementami podrzędnymi głównej grupy zarządzania. Celem tego procesu jest upewnienie się, że istnieje tylko jedna hierarchia grup zarządzania w katalogu. Pojedyncza hierarchia w katalogu umożliwia klientom administracyjnym klientom stosowanie globalnego dostępu i zasad, których inni klienci w katalogu nie mogą obejść. Wszystkie przypisania w głównej grupie będą stosowane do wszystkich grup zarządzania, subskrypcji, grup zasobów i zasobów w katalogu dzięki istnieniu tylko jednej hierarchii w katalogu.

> [!IMPORTANT]
> Wszystkie przypisania dostępu użytkowników lub zasad w głównej grupie zarządzania **dotyczą wszystkich zasobów w katalogu**.
> W związku z tym wszyscy klienci powinni ocenić potrzebę posiadania elementów zdefiniowanych w tym zakresie.
> Przypisania dostępu użytkowników i zasad powinny być „niezbędne” tylko w tym zakresie.  
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Grupy zarządzania platformy Azure obsługują [kontrolę dostępu opartą na rolach (RBAC) na platformie Azure](../../role-based-access-control/overview.md) dla wszystkich uprawnień dostępu do zasobów oraz definicji ról.
Te uprawnienia są dziedziczone do zasobów podrzędnych, które istnieją w hierarchii. Dowolną wbudowaną rolę RBAC można przypisać do grupy zarządzania, aby była dziedziczona w dół hierarchii do zasobów.
Na przykład można przypisać do grupy zarządzania rolę RBAC Współautor maszyny wirtualnej. Ta rola nie ma żadnej akcji wykonywanej na grupie zarządzania, ale będzie dziedziczona do wszystkich maszyn wirtualnych w ramach tej grupy zarządzania.

Na poniższym wykresie przedstawiono listę ról i obsługiwane akcje na grupach zarządzania.

| Nazwa roli RBAC             | Przycisk Utwórz | Zmiana nazwy | Move | Usuwanie | Przypisywanie dostępu | Przypisywanie zasad | Odczyt  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Właściciel                       | X      | X      | X    | X      | X             | X             | X     |
|Współautor                 | X      | X      | X    | X      |               |               | X     |
|Współautor grupy zarządzania*             | X      | X      | X    | X      |               |               | X     |
|Czytelnik                      |        |        |      |        |               |               | X     |
|Czytelnik grupy zarządzania*                  |        |        |      |        |               |               | X     |
|Współautor zasad zasobów |        |        |      |        |               | X             |       |
|Administrator dostępu użytkowników   |        |        |      |        | X             |               |       |

*: role Współautor grupy zarządzania i Czytelnik grupy zarządzania zezwalają użytkownikom na wykonywanie tych akcji tylko dla zakresu grupy zarządzania.  

### <a name="custom-rbac-role-definition-and-assignment"></a>Definicja i przypisanie niestandardowej roli RBAC

Niestandardowe role RBAC nie są obecnie obsługiwane dla grup zarządzania. Zobacz [forum opinii dotyczących grup zarządzania](https://aka.ms/mgfeedback), aby sprawdzić stan tego elementu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](manage.md)
- [Instalowanie modułu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Przegląd specyfikacji interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)