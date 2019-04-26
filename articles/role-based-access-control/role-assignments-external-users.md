---
title: Zarządzanie dostępem do zasobów platformy Azure dla użytkowników zewnętrznych, korzystając z modelu RBAC | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników spoza organizacji za pomocą kontroli dostępu opartej na rolach (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 91548a4df4a77623978ea4bcb214b76427c026a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345388"
---
# <a name="manage-access-to-azure-resources-for-external-users-using-rbac"></a>Zarządzanie dostępem do zasobów platformy Azure dla użytkowników zewnętrznych, korzystając z modelu RBAC

Kontrola dostępu oparta na rolach (RBAC) umożliwia lepsze zarządzanie zabezpieczeniami dla dużych organizacji i dla małych i średnich firmach praca z zewnętrznych współpracowników, dostawców lub freelancers, którzy potrzebują dostępu do określonych zasobów w danym środowisku, ale niekoniecznie do całego infrastruktury ani żadnych zakresów związanych z rozliczeniami. RBAC umożliwia elastyczną będącej właścicielem jedną subskrypcję platformy Azure zarządza konta administratora (roli administratora usługi na poziomie subskrypcji) i zaprosili wielu użytkowników do pracy w ramach tej samej subskrypcji, ale bez żadnych praw administracyjnych dla niego .

> [!NOTE]
> Subskrypcje usługi Office 365 lub licencji usługi Azure Active Directory (na przykład: Dostęp do usługi Azure Active Directory) udostępnioną za pomocą Centrum administracyjnego nie spełniam jej wymagań przy użyciu funkcji RBAC w programie Microsoft 365.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Przypisz role RBAC w zakresie subskrypcji

Istnieją dwie typowe przykłady dotyczące kontroli RBAC jest używana (między innymi):

* Zewnętrznych użytkowników z organizacji (nie jest częścią użytkownika administratora dzierżawy Azure Active Directory) zaproszenie do zarządzania niektórych zasobów lub subskrypcji całego
* Praca z użytkowników w organizacji (są one częścią dzierżawy usługi Azure Active Directory użytkownika), ale należy do różnych zespołów lub grupy, którzy muszą szczegółową dostępu do całej subskrypcji lub do określonych grup zasobów lub zakresy zasobów w środowisku

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Udzielanie dostępu na poziomie subskrypcji dla użytkownika poza usługą Azure Active Directory

Role RBAC, które mogą być przyznane tylko przez **właścicieli** subskrypcji. W związku z tym administrator musi być zalogowany jako użytkownik posiadający tę rolę wstępnie przypisane lub została utworzona subskrypcja platformy Azure.

W witrynie Azure portal po zalogowaniu się jako administrator, wybierz pozycję "Subskrypcje" i wybierz opcję jedno.
![Blok subskrypcji w witrynie Azure portal](./media/role-assignments-external-users/0.png) domyślnie, jeśli administrator subskrypcji platformy Azure, użytkownik będzie wyświetlany jako **administrator konta**, to jest rola subskrypcji. Aby uzyskać więcej informacji na temat ról subskrypcji platformy Azure, zobacz [Administratorzy subskrypcji platformy Azure Dodaj lub zmień](../billing/billing-add-change-azure-subscription-administrator.md).

W tym przykładzie użytkownik "alflanigan@outlook.com" jest **właściciela** "Bezpłatna wersja próbna" dzierżawy "Domyślna dzierżawa usługi Azure" subskrypcji w usłudze AAD. Ponieważ ten użytkownik jest twórca subskrypcji platformy Azure za pomocą początkowego Account Microsoft "Outlook" (Account Microsoft = programu Outlook, na żywo itp.) będzie domyślna nazwa domeny dla wszystkich innych użytkowników, dodać w tej dzierżawie **"\@ alflaniganuoutlook.onmicrosoft.com"**. Zgodnie z projektem składni nowej domeny jest tworzona przez zestawiania nazwy użytkownika i domena nazwa użytkownika, który utworzył dzierżawy oraz dodawania rozszerzenia **". onmicrosoft.com"**.
Ponadto użytkownicy mogą zarejestrować się przy użyciu niestandardowej nazwy domeny w dzierżawie po dodaniu i weryfikowanie jego dla nowej dzierżawy. Aby uzyskać więcej informacji na temat zweryfikować niestandardowej nazwy domeny w dzierżawie usługi Azure Active Directory, zobacz [Dodawanie niestandardowej nazwy domeny do katalogu](../active-directory/fundamentals/add-custom-domain.md).

W tym przykładzie katalog "Domyślna dzierżawa usługi Azure" zawiera tylko użytkownicy z tą nazwą domeny "\@alflanigan.onmicrosoft.com".

Po wybraniu subskrypcji, należy kliknąć przycisk administratora **kontrola dostępu (IAM)** i następnie **dodać nową rolę**.

![Funkcja zarządzania tożsamościami i Dostępem kontroli dostępu w witrynie Azure portal](./media/role-assignments-external-users/1.png)

![Dodawanie nowego użytkownika w funkcji zarządzania tożsamościami i Dostępem kontroli dostępu w witrynie Azure portal](./media/role-assignments-external-users/2.png)

Następnym krokiem jest wybierz rolę do przypisania i użytkownik, któremu zostanie przypisana rola RBAC do. W **roli** menu rozwijane administratora widzi tylko wbudowane role kontroli RBAC, które są dostępne na platformie Azure. Aby uzyskać bardziej szczegółowe objaśnienia dotyczące poszczególnych ról i ich zakresy możliwe do przypisania, zobacz [wbudowane role zasobów platformy Azure](built-in-roles.md).

Użytkownika administratora musi dodać adres e-mail użytkownika zewnętrznego. To oczekiwane zachowanie dla użytkownika zewnętrznego, które nie są wyświetlani w istniejącej dzierżawy. Po użytkownik zewnętrzny zostali zaproszeni, on będą widoczne w obszarze **subskrypcje > Kontrola dostępu (IAM)** przy użyciu wszystkich bieżących użytkowników, które są obecnie przypisane rolę RBAC w zakresie subskrypcji.

![Dodaj uprawnienia do nowej roli RBAC](./media/role-assignments-external-users/3.png)

![Lista ról RBAC na poziomie subskrypcji](./media/role-assignments-external-users/4.png)

Użytkownik "chessercarlton@gmail.com" został zaproszony jako **właściciela** dla subskrypcji "Bezpłatna wersja próbna". Po wysłaniu zaproszenia, zewnętrzne użytkownik otrzyma wiadomość e-mail z potwierdzeniem z łącze do aktywacji.
![wiadomość e-mail z zaproszeniem dla ról RBAC](./media/role-assignments-external-users/5.png)

Jest spoza organizacji, nowy użytkownik nie ma żadnych istniejących atrybutów w katalogu "Domyślna dzierżawa usługi Azure". Zostaną one utworzone po użytkownik zewnętrzny wyraził zgody rejestruje się w katalogu, który jest skojarzony z tą subskrypcją został przydzielony do roli.

![wiadomości z zaproszeniem dla ról RBAC](./media/role-assignments-external-users/6.png)

Pokazuje użytkownika zewnętrznego w dzierżawie usługi Azure Active Directory od teraz jako użytkownik zewnętrzny i to można wyświetlić w witrynie Azure portal.

![witryny Azure portal użytkowników bloku azure active directory](./media/role-assignments-external-users/7.png)

W **użytkowników** widoku, użytkownicy zewnętrzni mogą być rozpoznawane przez typ inną ikonę w witrynie Azure portal.

Jednak udzielenie **właściciela** lub **Współautor** dostęp do użytkownika zewnętrznego w **subskrypcji** zakresu, nie zezwala na dostęp do katalogu użytkownika administracyjnego, chyba że **Administratora globalnego** to umożliwia. W ich właściwości użytkownika **typ użytkownika**, który ma dwie typowe parametry, **elementu członkowskiego** i **gościa** mogą zostać zidentyfikowane. Element członkowski jest użytkownik, który jest zarejestrowany w katalogu, a Gość jest użytkownikiem, który został zaproszony do katalogu z zewnętrznego źródła. Aby uzyskać więcej informacji, zobacz [jak Administratorzy usługi Azure Active Directory dodać użytkowników we współpracy B2B](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Upewnij się, że po wprowadzeniu poświadczeń w portalu, zewnętrzne użytkownik wybierze poprawnego katalogu do logowania się na. Ten sam użytkownik może mieć dostęp do wielu katalogów i można wybrać jeden z nich, klikając nazwę użytkownika w prawej górnej w witrynie Azure portal a następnie wybierz odpowiedniego katalogu z listy rozwijanej.

Będąc gościa w katalogu, użytkowników zewnętrznych mogą zarządzać wszystkie zasoby w subskrypcji platformy Azure, ale nie można uzyskać dostępu do katalogu.

![dostęp ograniczony do portalu Azure usługi active directory azure](./media/role-assignments-external-users/9.png)

Usługa Azure Active Directory i subskrypcję platformy Azure nie mają relacji relacji nadrzędny podrzędny, podobnie jak inne zasoby platformy Azure (na przykład: maszyny wirtualne, sieci wirtualne, aplikacje sieci web, Magazyn itp.) z subskrypcją platformy Azure. Wszystkie one utworzone, zarządzane i rozliczane w ramach subskrypcji platformy Azure, natomiast subskrypcji platformy Azure służy do zarządzania dostępem do usługi Azure directory. Aby uzyskać więcej informacji, zobacz [subskrypcji platformy Azure jest powiązany z usługą Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Z wszystkie wbudowane role kontroli RBAC **właściciela** i **Współautor** oferują dostęp do pełnego zarządzania, do wszystkich zasobów w środowisku, a różnica, możliwe, że Współautor nie mogą tworzyć i usuwać nowe role RBAC . Inne role wbudowane, takie jak **Współautor maszyny wirtualnej** oferują dostęp do pełnego zarządzania, tylko do zasobów, jest określany przez nazwę, niezależnie od tego **grupy zasobów** są one tworzone w.

Przypisanie wbudowanej roli RBAC **Współautor maszyny wirtualnej** na poziomie subskrypcji, oznacza, że użytkownik przypisany do roli:

* Można wyświetlić wszystkich maszyn wirtualnych niezależnie od tego, daty ich wdrożenia i grupy zasobów, które są częścią
* Dostępem do pełnego zarządzania z maszynami wirtualnymi w subskrypcji
* Nie można wyświetlić inne typy zasobów w subskrypcji
* Nie można wykonać operacji zmiany z punktu widzenia rozliczeń

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Przypisywanie roli wbudowanej RBAC do użytkownika zewnętrznego

Inny scenariusz w tym teście użytkownika zewnętrznego "alflanigan@gmail.com" zostanie dodany jako **Współautor maszyny wirtualnej**.

![wbudowana rola Współautor maszyny wirtualnej](./media/role-assignments-external-users/11.png)

Normalne zachowanie dla tego użytkownika zewnętrznego, z tą rolą wbudowanych jest wyświetlanie i zarządzanie nimi, tylko maszyny wirtualne i ich sąsiadujących zasoby usługi Resource Manager tylko niezbędne podczas wdrażania. Zgodnie z projektem te role ograniczone oferują dostęp tylko do ich odpowiedniego zasoby utworzone w witrynie Azure portal.

![Omówienie roli Współautor maszyny wirtualnej w witrynie Azure portal](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Udzielanie dostępu na poziomie subskrypcji dla użytkownika, w tym samym katalogu

Przepływ procesu jest taka sama jak dodawanie użytkownika zewnętrznego, zarówno z perspektywy administratora, udzielanie rolę RBAC, a także użytkownika zostanie im przyznany dostęp do roli. W tym miejscu różnica polega na tym, że zaproszonego użytkownika nie otrzyma żadnych zaproszeń e-mail ponieważ wszystkie zakresy zasobów w ramach subskrypcji będą dostępne na pulpicie nawigacyjnym po zalogowaniu się.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Przypisz role RBAC w zakresie grupy zasobów

Przypisz rolę RBAC w **grupy zasobów** zakres ma identyczne proces trwa przypisywanie roli na poziomie subskrypcji, dla obu typów użytkowników — zewnętrznego lub wewnętrznego (część tego samego katalogu). Użytkownicy, którym przypisano rolę RBAC jest, aby zobaczyć w swoim środowisku, tylko do grupy zasobów zostały przypisane dostęp z **grup zasobów** ikony w witrynie Azure portal.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Przypisz role RBAC w zakresie zasobów

Przypisz rolę RBAC w zakresie zasobów na platformie Azure ma identyczne proces trwa przypisywanie roli na poziomie subskrypcji lub na poziomie grupy zasobów zgodnie z tym samym przepływie pracy oba scenariusze. Użytkownicy, którym przypisano rolę RBAC można znajduje się w artykule tylko elementy, które zostali przypisani dostęp do jednej w **wszystkie zasoby** karty lub bezpośrednio na pulpicie nawigacyjnym.

Ważnym aspektem dla RBAC, zarówno w zakresie grupy zasobów lub w zakresie zasobów jest przeznaczony dla użytkowników upewnić się zarejestrować się w katalogu poprawny.

![katalog logowania w witrynie Azure portal](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Przypisz role RBAC dla grupy usługi Azure Active Directory

Wszystkie scenariusze przy użyciu funkcji RBAC w trzech różnych zakresów na platformie Azure oferuje uprawnienia zarządzania, wdrażania i administrowania zasobami różnych jako przypisanych użytkowników bez konieczności zarządzania subskrypcją osobistych. Niezależnie od tego dla subskrypcji, grupy zasobów lub zasobu zakresu jest przypisywana rola RBAC, wszystkie zasoby, które są utworzone dalej przypisanych użytkowników są naliczane w ramach jednej subskrypcji platformy Azure, w której użytkownicy mają dostęp do. Dzięki temu użytkownicy, którzy mają rozliczeń uprawnień administratora dla tej subskrypcji platformy Azure cały ma pełny przegląd zużycia, niezależnie od tego, który zarządza zasobami.

W przypadku większych organizacji można stosować w taki sam sposób dla grup usługi Azure Active Directory, biorąc pod uwagę z punktu widzenia użytkownika administrator chce udzielić dostępu szczegółowej całego działów lub zespołów, indywidualnie dla każdego użytkownika, w związku z tym biorąc pod uwagę role RBAC go jako bardzo czasu i zarządzania wydajne opcję. Aby zilustrować ten przykład **Współautor** rola została dodana do jednej z grup w dzierżawie na poziomie subskrypcji.

![Dodaj rolę RBAC dla grup usługi AAD](./media/role-assignments-external-users/14.png)

Te grupy to grupy zabezpieczeń, które są aprowizowane i zarządzane tylko w ramach usługi Azure Active Directory.

