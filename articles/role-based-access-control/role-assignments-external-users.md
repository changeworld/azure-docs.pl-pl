---
title: Zarządzanie dostępem dla użytkowników zewnętrznych na platformie Azure przy użyciu funkcji RBAC | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem dla użytkowników spoza organizacji przy użyciu kontroli dostępu opartej na rolach (RBAC) na platformie Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 98eb104981051bd5e7440954470960977b38286d
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296218"
---
# <a name="manage-access-for-external-users-using-rbac"></a>Zarządzanie dostępem dla użytkowników zewnętrznych przy użyciu funkcji RBAC

Kontrola dostępu oparta na rolach (RBAC) umożliwia lepsze zarządzanie zabezpieczeniami dla dużych organizacji oraz dla małych i średnich firmach praca z zewnętrznym współpracownikom, dostawców lub freelancers, które wymagają dostępu do określonych zasobów w danym środowisku, ale niekoniecznie do całej Infrastruktura lub żadnych zakresów związanych z rozliczeniami. RBAC umożliwia elastyczność będący właścicielem jedną subskrypcją platformy Azure zarządzanych przez administratora konta (usługi roli administrator na poziomie subskrypcji) i mieć wielu użytkowników zaproszenie do pracy w ramach tej samej subskrypcji, ale bez jakichkolwiek praw administracyjnych dla niego.

> [!NOTE]
> Licencje usługi Azure Active Directory lub subskrypcji usługi Office 365 (na przykład: dostęp do usługi Azure Active Directory) pobranego z Centrum nie kwalifikuje się do przy użyciu funkcji RBAC Office 365 Admin.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Przypisz role RBAC w zakresie subskrypcji

Istnieją dwie typowe przykłady dotyczące RBAC jest używana (między innymi):

* Użytkowników zewnętrznych z organizacji (nie jest częścią dzierżawy usługi Azure Active Directory dla użytkownika administracyjnego) zaproszenie do zarządzania niektórych zasobów lub całej subskrypcji
* Praca z użytkownikami w organizacji (są one częścią dzierżawy usługi Azure Active Directory użytkownika), ale należy do różnych zespołów lub grup, wymagających szczegółowego dostępu do całej subskrypcji lub do określonych grup zasobów lub zakresy zasobów w środowisku

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Udziel dostępu na poziomie subskrypcji dla użytkownika poza usługą Azure Active Directory

Role RBAC może zostać przydzielony tylko przez **właścicieli** subskrypcji. W związku z tym administrator musi zalogować się jako użytkownik, który zawiera tę rolę wstępnie przypisany lub została utworzona subskrypcja platformy Azure.

W portalu Azure po zalogowaniu się jako administrator, wybierz "Subskrypcji" i wybierz jedno.
![Subskrypcja bloku w portalu Azure](./media/role-assignments-external-users/0.png) domyślnie, jeśli dla użytkownika administracyjnego kupiła subskrypcji platformy Azure, użytkownik będzie wyświetlany jako **administrator konta**, to jest rola subskrypcji. Aby uzyskać więcej informacji o rolach subskrypcji platformy Azure, zobacz [Dodawanie lub zmienianie ról administrator usługi Azure, które zarządzają subskrypcji lub usługi](../billing/billing-add-change-azure-subscription-administrator.md).

W tym przykładzie użytkownik "alflanigan@outlook.com" jest **właściciela** z "Bezpłatnej wersji próbnej" subskrypcji w usłudze AAD dzierżawy "Dzierżawy Azure Default". Ponieważ ten użytkownik jest twórca subskrypcji platformy Azure z początkowej Account Microsoft "Outlook" (Account Microsoft = programu Outlook, Live itp.) będzie domyślna nazwa domeny dla wszystkich innych użytkowników dodane w tej dzierżawie **"\@ alflaniganuoutlook.onmicrosoft.com"**. Zgodnie z projektem składni nowej domeny jest tworzony przez zestawienie nazwę użytkownika i domenę nazwę użytkownika, który utworzył dzierżawcy i dodawanie rozszerzenia **". onmicrosoft.com"**.
Ponadto użytkownicy mogą zalogować się przy użyciu niestandardowej nazwy domeny w dzierżawie po dodaniu i weryfikowanie jego dla nowej dzierżawy. Aby uzyskać więcej informacji na temat sposobu zweryfikować niestandardowej nazwy domeny w dzierżawie usługi Azure Active Directory, zobacz [Dodawanie niestandardowej nazwy domeny do katalogu](/active-directory/active-directory-add-domain).

W tym przykładzie katalog "Domyślna dzierżawa usługi Azure" zawiera tylko użytkownicy z nazwą domeny "\@alflanigan.onmicrosoft.com".

Po wybraniu subskrypcji, administrator musi kliknij **kontroli dostępu (IAM)** , a następnie **dodania roli**.

![Funkcja IAM kontroli dostępu w portalu Azure](./media/role-assignments-external-users/1.png)

![Dodaj nowego użytkownika w funkcja IAM kontroli dostępu w portalu Azure](./media/role-assignments-external-users/2.png)

Następnym krokiem jest wybranie roli do przypisania i użytkownika, którego rola RBAC zostanie przypisana do. W **roli** menu rozwijanym administratora użytkownik widzi tylko wbudowane role RBAC, które są dostępne w systemie Azure. Aby uzyskać bardziej szczegółowe wyjaśnienia dotyczące poszczególnych ról i ich zakresy możliwe do przypisania, zobacz [wbudowane role](built-in-roles.md).

Następnie administrator musi dodać adres e-mail użytkownika zewnętrznego. Oczekiwane zachowanie jest dla użytkownika zewnętrznego, które nie są wyświetlani w istniejącej dzierżawy. Po Zaproszono użytkownika zewnętrznego, on będą widoczne w obszarze **subskrypcji > kontroli dostępu (IAM)** z wszystkich bieżących użytkowników, które są obecnie przypisane roli RBAC w zakresie subskrypcji.

![Dodaj uprawnienia do nowej roli RBAC](./media/role-assignments-external-users/3.png)

![Lista ról RBAC na poziomie subskrypcji](./media/role-assignments-external-users/4.png)

Użytkownik "chessercarlton@gmail.com" zaproszono jako **właściciela** dla subskrypcji "Bezpłatnej wersji próbnej". Po wysłaniu zaproszenia, zewnętrznych użytkownik otrzyma wiadomość e-mail z potwierdzeniem z link aktywacji.
![wiadomość e-mail z zaproszeniem dla roli RBAC](./media/role-assignments-external-users/5.png)

Trwa spoza organizacji, nowy użytkownik nie ma żadnych istniejących atrybutów w katalogu "Domyślna dzierżawa usługi Azure". Będzie można utworzyć po uzyskaniu zgody użytkownika zewnętrznego mają być rejestrowane w katalogu, który jest skojarzony z subskrypcją został przydzielony do roli.

![wiadomość e-mail zaproszenia dla roli RBAC](./media/role-assignments-external-users/6.png)

Pokazuje użytkownika zewnętrznego w dzierżawcy usługi Azure Active Directory od teraz jako użytkownik zewnętrzny i to można wyświetlić w portalu Azure.

![Użytkownicy portalu Azure usługi active directory bloku azure](./media/role-assignments-external-users/7.png)

W **użytkowników** widoku, użytkownicy zewnętrzni mogą być rozpoznawane przez typ inną ikonę w portalu Azure.

Jednak udzielanie **właściciela** lub **współautora** dostępu do użytkownika zewnętrznego w **subskrypcji** zakresu, nie zezwala na dostęp do katalogu dla użytkownika administracyjnego, chyba że **administratora globalnego** pozwala. W ich właściwości użytkownika **typ użytkownika**, który ma dwie typowe parametry, **elementu członkowskiego** i **gościa** mogą zostać zidentyfikowane. Element członkowski jest użytkownik, który jest zarejestrowany w katalogu, gdy Gość jest użytkownikiem zaproszenie do katalogu z zewnętrznego źródła. Aby uzyskać więcej informacji, zobacz [jak Administratorzy usługi Azure Active Directory dodać użytkowników współpracy B2B](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Upewnij się, że po wprowadzeniu poświadczeń w portalu, użytkownik zewnętrzny wybiera do logowania się w poprawnym katalogu. Tego samego użytkownika może mieć dostęp do wielu katalogów i można wybrać jedną z nich, klikając nazwę użytkownika w góry po prawej stronie w portalu Azure a następnie wybierz odpowiedniego katalogu z listy rozwijanej.

Będąc gościa w katalogu użytkownika zewnętrznego mogą zarządzać zasobami wszystkich subskrypcji platformy Azure, ale nie można uzyskać dostępu do katalogu.

![dostęp ograniczony do portalu Azure usługi azure active directory](./media/role-assignments-external-users/9.png)

Azure Active Directory i subskrypcji platformy Azure nie ma relacji relacji nadrzędny podrzędny, takich jak innych zasobów platformy Azure (na przykład: maszyn wirtualnych, sieci wirtualnych, aplikacje sieci web, magazynu itp.) z subskrypcją platformy Azure. Wszystkie ostatnie jest utworzony, zarządzane i rozliczany w ramach subskrypcji platformy Azure, podczas gdy subskrypcji platformy Azure jest używany do zarządzania dostępem do usługi Azure directory. Aby uzyskać więcej informacji, zobacz [subskrypcji jak Azure jest powiązana z usługą Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Z wszystkich wbudowane role RBAC **właściciela** i **współautora** oferują pełnego zarządzania dostęp do wszystkich zasobów w środowisku różnica, że współautora nie może tworzyć i usuwać nowe role RBAC. Inne role wbudowane, takich jak **współautora maszyny wirtualnej** oferować pełnego zarządzania dostęp tylko do zasobów, jest określany przez nazwę, niezależnie od tego **grupy zasobów** jest tworzona w.

Przypisywanie roli RBAC wbudowanych **Współautor·maszyny·wirtualnej** na poziomie subskrypcji, oznacza, że użytkownikowi przypisano rolę:

* Można wyświetlić wszystkich maszyn wirtualnych niezależnie od daty wdrożenia i grupy zasobów, które są częścią
* Ma dostęp do pełnego zarządzania do maszyn wirtualnych w subskrypcji
* Nie można wyświetlić inne typy zasobów w subskrypcji
* Nie można wykonać operacji zmiany z punktu widzenia rozliczeń

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Przypisywanie roli RBAC wbudowanych do użytkownika zewnętrznego

Dla innego scenariusza, w tym teście użytkownika zewnętrznego "alflanigan@gmail.com" zostanie dodany jako **Współautor·maszyny·wirtualnej**.

![wbudowana Rola współautora maszyny wirtualnej](./media/role-assignments-external-users/11.png)

Normalne zachowanie dla tego użytkownika zewnętrznego z tą rolą wbudowanych jest wyświetlanie i zarządzanie nimi tylko maszyny wirtualne i ich sąsiadujących ze sobą Menedżer zasobów tylko zasoby niezbędne podczas wdrażania. Zgodnie z projektem te role ograniczone zapewniają dostęp tylko do ich zasobów odpowiedniego utworzone w portalu Azure.

![Omówienie roli współautora maszyny wirtualnej w portalu Azure](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Udziel dostępu na poziomie subskrypcji dla użytkownika w tym samym katalogu

Przepływ procesu jest taki sam jak dodawanie użytkownika zewnętrznego, zarówno z perspektywy administracyjnej przyznania roli RBAC, a także użytkownika zostanie im przyznany dostęp do roli. Różnica polega na tym że zaproszonych użytkownik nie będzie otrzymywać żadnych zaproszeń do skorzystania z poczty e-mail, jak wszystkie zakresy zasobów w subskrypcji będą dostępne na pulpicie nawigacyjnym po zalogowaniu się.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Przypisz role RBAC w zakresie grupy zasobów

Przypisywanie roli RBAC **grupy zasobów** zakres ma taki sam proces przypisywania roli na poziomie subskrypcji dla obu typów użytkownicy — zewnętrznym lub wewnętrznym (część z tym samym katalogu). Użytkownicy, którym przypisano rolę RBAC ma zobacz w swoim środowisku tylko grupy zasobów z przypisanym dostępem z **grup zasobów** ikonę w portalu Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Przypisz role RBAC w zakresie zasobów

Przypisywanie roli RBAC w zakresie zasobów na platformie Azure mają identyczne proces przypisywania roli na poziomie subskrypcji lub na poziomie grupy zasobów, po tym samym przepływie pracy w obydwu scenariuszach. Ponownie, użytkowników, którym przypisano rolę RBAC można wyświetlanie tylko tych elementów, które przypisano dostępu do w **wszystkie zasoby** kartę lub bezpośrednio w ich pulpitu nawigacyjnego.

Istotnym elementem do RBAC zarówno w zakresie grupy zasobów lub zasobów zakresie jest przeznaczony dla użytkowników upewnić się zarejestrować się w poprawnym katalogu.

![katalog logowania w portalu Azure](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Przypisz role RBAC dla grupy usługi Azure Active Directory

Wszystkie scenariusze na trzy różne zakresy na platformie Azure przy użyciu funkcji RBAC oferują uprawnienie Zarządzanie, wdrażanie i administrowanie różnych zasobów jako przypisany użytkownik bez konieczności zarządzania osobiste subskrypcji. Niezależnie od przypisano rolę RBAC dla subskrypcji, grupy zasobów lub zasobów zakresu, wszystkie zasoby utworzone dalej przez przypisanych użytkowników są rozliczane zgodnie z jedną subskrypcją platformy Azure, której użytkownicy mają dostęp do. Dzięki temu użytkowników, którzy mają rozliczeń uprawnień administratora dla całej subskrypcji platformy Azure ma pełny przegląd zużycia, niezależnie od tego, kto jest zarządzania zasobami.

W przypadku większych organizacji role RBAC można zastosować w taki sam sposób dla uwzględnieniu perspektywy administrator chce szczegółowego dostęp dla zespołów lub całego działów, indywidualnie dla każdego użytkownika, w związku z tym uwzględnieniu bardzo czas i zarządzanie wydajne opcja grup usługi Azure Active Directory. Przykład ilustrujący **współautora** rola została dodana do jednej z grup w dzierżawie na poziomie subskrypcji.

![Dodaj rolę RBAC dla grup usługi AAD](./media/role-assignments-external-users/14.png)

Grupy te są grup zabezpieczeń, które są udostępniane i zarządzane tylko w ramach usługi Azure Active Directory.

