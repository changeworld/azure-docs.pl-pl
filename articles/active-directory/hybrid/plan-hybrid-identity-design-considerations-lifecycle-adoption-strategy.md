---
title: Projektowania tożsamości hybrydowej — strategia wdrożenia cyklu życia Azure | Dokumentacja firmy Microsoft
description: Pozwala zdefiniować zadania zarządzania tożsamości hybrydowej zgodnie z opcjami dostępnymi na potrzeby poszczególnych fazach cyklu życia.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b312f284d557c2c1344b82b9fcd570d3392077
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381289"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Określić strategia wdrożenia cyklu życia tożsamości hybrydowej
W tym zadaniu należy zdefiniować strategii zarządzania tożsamości do hybrydowego tożsamości rozwiązania w celu spełnienia wymagań biznesowych zdefiniowaną w [określić zadania związane z zarządzaniem tożsamości hybrydowej](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Aby zdefiniować zadania zarządzania tożsamości hybrydowej zgodnie z cyklem życia tożsamości end-to-end, przedstawione wcześniej w tym kroku, trzeba będzie należy rozważyć opcje, które są dostępne dla każdej fazy cyklu życia.

## <a name="access-management-and-provisioning"></a>Zarządzanie dostępem i Inicjowanie obsługi administracyjnej
Za pomocą konta dobre rozwiązania do zarządzania dostępem Twoja organizacja może śledzić dokładnie kto ma dostęp do informacji w całej organizacji.

Kontrola dostępu jest funkcją krytyczne scentralizowany, pojedynczym punktem inicjowania obsługi administracyjnej systemu. Oprócz ochrony informacji poufnych, kontroli dostępu do udostępnienia istniejących kont, które mają niezatwierdzonych autoryzacji lub są już wymagane. Aby kontrolować przestarzałe konta, system łączy razem konta informacje o udostępnianiu z wiarygodnych informacji o użytkownikach, którzy posiadają kont. Informacje o tożsamości użytkownika autorytatywny jest zwykle zapewniana w bazach danych i katalogów zasobów ludzkich.

Konta w zaawansowanych przedsiębiorstw IT mają setki parametry, które definiują władze, a te informacje mogą być kontrolowane przez system inicjowania obsługi administracyjnej. Nowi użytkownicy mogą zostać zidentyfikowane z zapewniające z autorytatywnego źródła danych. Funkcja zatwierdzania żądania dostępu inicjuje procesy, które zatwierdzenia (lub nie) Inicjowanie obsługi administracyjnej dla nich zasobów.

| Faza zarządzania cyklem życia | W środowisku lokalnym | Chmura | Połączenie hybrydowe |
| --- | --- | --- | --- |
| Zarządzanie kontami i inicjowania obsługi |Za pomocą roli serwera usług Active Directory® Domain Services (AD DS), utworzyć skalowalne, bezpieczne i łatwe w zarządzaniu infrastrukturę do zarządzania użytkownikami i zasobami oraz zapewnić obsługę aplikacji obsługujących katalogi, takich jak Microsoft® Exchange Server. <br><br> [Możesz aprowizować grupy w usługach AD DS za pomocą programu Identity manager](https://technet.microsoft.com/library/ff686261.aspx) <br>[Można udostępnić użytkownikom w usługach AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Administratorzy mogą używać kontroli dostępu, aby zarządzać dostępem użytkowników do zasobów udostępnionych ze względów bezpieczeństwa. W usłudze Active Directory, kontroli dostępu jest przeprowadzany na poziomie obiektu przez ustawienie różnych poziomów dostępu lub uprawnień do obiektów, takich jak Pełna kontrola, zapisu, odczytu lub Brak dostępu. Kontrola dostępu w usłudze Active Directory definiuje sposób różni użytkownicy mogą używać obiektów usługi Active Directory. Domyślnie uprawnienia do obiektów w usłudze Active Directory zostały ustawione na najbezpieczniejsze ustawienie. |Należy utworzyć konta dla wszystkich użytkowników, którzy będą miały dostęp usługi w chmurze firmy Microsoft. Można również zmienić konta użytkowników lub usuwać je, gdy nie są już potrzebne. Domyślnie użytkownicy nie mają uprawnień administratora, ale możesz opcjonalnie przypisać je. <br><br> W usłudze Azure Active Directory jedną z najważniejszych funkcji jest możliwość zarządzania dostępem do zasobów. Te zasoby mogą być częścią katalogu, jak w przypadku uprawnień do zarządzania obiektami za pośrednictwem ról w katalogu, lub znajdować się poza katalogiem, jak w przypadku aplikacji SaaS, usług platformy Azure, witryn programu SharePoint lub zasobów lokalnych. <br><br> Centrum z usługi Azure Active Directory dostępu rozwiązania do zarządzania jest grupą zabezpieczeń. Właściciel zasobu (lub administrator katalogu) może przypisać grupę, aby udzielić określonych uprawnień dostępu do jego zasobów. Członkowie grupy będą świadczone dostępu i właściciel zasobu może delegować uprawnienia do zarządzania listą elementów członkowskich grupy do kogoś innego — takich jak Menedżer działu lub administrator pomocy technicznej<br> <br> Grupy zarządzania, w sekcji usługi Azure AD zawiera więcej informacji o zarządzaniu dostępem za pomocą grup. |Rozszerzanie tożsamości usługi Active Directory na chmurę za pośrednictwem synchronizacji i Federacji |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Oparta na rolach dostęp kontrolować (RBAC) używa ról i inicjowania obsługi zasad w celu oceny, testowania i wymuszać swoje procesy biznesowe i reguł, do przyznawania dostępu użytkownikom. Kluczowe Administratorzy tworzą zasady inicjowania obsługi i przypisywania użytkowników do ról i definiują zestawy uprawnień do zasobów dla tych ról. RBAC rozszerza rozwiązanie do zarządzania tożsamościami oparty na oprogramowaniu procesy są używane i obniżyć ręczne interakcji z użytkownikiem w procesie inicjowania obsługi administracyjnej.
Usługa Azure AD RBAC umożliwia firmie ograniczyć liczbę operacji, które osoba można zrobić, gdy ma on dostęp do witryny Azure portal. Przy użyciu RBAC do kontrolowania dostępu do portalu, Administratorzy IT urzędu certyfikacji Delegowanie dostępu za pomocą następujących metod zarządzania dostępu:

* **Przypisanie roli oparte na grupach**: Dostęp można przypisać do grup usługi Azure AD, które mogą być synchronizowane z lokalnej usługi Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, których organizacja wprowadziła narzędzia i procesy do zarządzania grupami. Można również użyć funkcji zarządzania delegowanej grupy usługi Azure AD Premium.
* **Wykorzystaj wbudowane role na platformie Azure**: Można użyć trzech ról — właściciela, współautora i czytelnika, upewnij się, że użytkownicy i grupy mają uprawnienia do wykonania zadania, których potrzebują do wykonania swoich zadań.
* **Szczegółowe dostęp do zasobów**: Role można przypisać do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub poszczególnych zasobów platformy Azure, takich jak witryny sieci Web lub bazy danych. W ten sposób możesz upewnij się, że użytkownicy mają dostęp do wszystkich zasobów, które są im potrzebne i Brak dostępu do zasobów, które ich nie trzeba zarządzać.

## <a name="provisioning-and-other-customization-options"></a>Inicjowanie obsługi administracyjnej i innych opcji dostosowywania
Twój zespół może być plany biznesowe i wymagania ile zdecydować dostosować rozwiązanie tożsamości. Na przykład duże przedsiębiorstwo może wymagać planu stopniowe wdrożenie dla przepływów pracy i niestandardowych kart, które opiera się na osi czasu, stopniowo aprowizacji aplikacji, które są powszechnie używane w różnych lokalizacjach geograficznych. Inny plan dostosowania może zawierać dwóch lub więcej aplikacji do udostępnienia w całej organizacji, po pomyślnym przeprowadzeniu testów. Można dostosować interakcji aplikacji użytkownika i procedury dotyczące inicjowania obsługi administracyjnej zasobów może ulec zmianie, aby pomieścić automatyczne Inicjowanie obsługi.

Można anulować obsługę można usunąć usługi lub składnika. Anulowanie aprowizacji konta oznacza na przykład, że konto zostało usunięte z zasobu.

Model hybrydowej inicjowania obsługi administracyjnej zasobów łączy żądania i opartej na rolach metod, które są obsługiwane przez usługę Azure AD. Dla podzbioru pracowników lub zarządzanych systemów biznesowych chcieć zautomatyzować dostęp przy użyciu przypisywania na podstawie ról. Firma może również obsługiwać wszystkie żądania dostępu lub wyjątki za pośrednictwem modelu opartego na żądaniach. Niektóre firmy mogą rozpoczynać się ręcznego przypisania i rozwój kierunku z hybrydowego modelu z zamiarem wdrożenia w pełni oparta na rolach w przyszłości.

Innych firm, może okazać się niepraktyczne powodów biznesowych do osiągnięcia pełną opartej na rolach inicjowania obsługi administracyjnej, a docelowy podejście hybrydowe, jako cel odpowiedniej. Nadal innych firm, które mogą satysfakcjonujący tylko na podstawie żądań inicjowania obsługi administracyjnej, a nie chcesz inwestować dodatkowego nakładu pracy do definiowania zasad i zarządzanie nimi opartej na rolach, automatycznego inicjowania obsługi administracyjnej.

## <a name="license-management"></a>Zarządzanie licencjami
Zarządzanie oparte na grupach licencyjnych w usłudze Azure AD umożliwia przypisywanie użytkowników do grupy zabezpieczeń Administratorzy i usługi Azure AD automatycznie spowoduje przypisanie licencji do wszystkich elementów członkowskich grupy. Jeśli użytkownik jest następnie dodawane do lub usunięte z grupy, licencji zostaną automatycznie przypisane lub usunięte odpowiednio.

Można użyć grup, które są synchronizowani z lokalnej usługi AD lub zarządzania nimi w usłudze Azure AD. Parowanie to za pomocą usługi Azure AD premium samoobsługowego zarządzania grupami można łatwo delegować przypisywania licencji do odpowiednich inne osoby podejmujące decyzje. Możesz mieć pewność, że problemów, takich jak konflikty licencji i brakujące dane lokalizacji są automatycznie posortowane.

## <a name="self-regulating-user-administration"></a>Samoregulujące Administracja użytkownikami
Po uruchomieniu organizacji do aprowizacji zasobów we wszystkich organizacjach wewnętrznego implementuje są samoregulujące funkcji administrowania użytkowników. Zalety i korzyści inicjowania obsługi administracyjnej użytkowników może realizować między granicami organizacji. W tym środowisku zmianę stanu użytkownika są automatycznie odzwierciedlane w prawa dostępu między granicami organizacji i różnych lokalizacji geograficznych. Można zmniejszyć koszty obsługi administracyjnej i usprawniać procesy dostępu i zatwierdzania. Implementacja zdaje sobie sprawę pełnego potencjału Implementowanie kontroli dostępu opartej na rolach do zarządzania dostępem end-to-end w Twojej organizacji. Można zmniejszyć koszty administracyjne za pomocą zautomatyzowanych procedur rządzące Inicjowanie obsługi użytkowników. Możesz zwiększyć bezpieczeństwo, automatyzując wymuszanie zasad zabezpieczeń i usprawnić i scentralizowane zarządzanie cyklem życia użytkowników i Inicjowanie obsługi zasobu dla tych grup dużej liczby użytkowników.

> [!NOTE]
> Aby uzyskać więcej informacji zobacz Konfigurowanie usługi Azure AD w celu samoobsługowego zarządzania dostępem do aplikacji
> 
> 

(W oparciu o uprawnienie) na podstawie licencji usługi Azure AD usług pracy przez aktywowanie subskrypcji w Twojej dzierżawie katalogu/usługi Azure AD. Gdy subskrypcja jest aktywna możliwości usługi można zarządzane przez administratorów usługi katalogowej/i używane przez licencjonowanych użytkowników. 

## <a name="integration-with-other-3rd-party-providers"></a>Integracja z innych dostawców 3

Usługa Azure Active Directory zawiera pojedynczy znak i rozszerzone zabezpieczenia dostępu do aplikacji do tysięcy aplikacji SaaS i lokalnych aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definiowanie zarządzania synchronizacji
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów, zarówno lokalnych, jak i w chmurze. Dzięki tej integracji użytkowników i organizacji można korzystać z następujących czynności:

* Organizacje mogą przydzielać użytkownikom wspólną tożsamość hybrydowa w lokalnych lub usług w chmurze korzystanie z usługi Active Directory systemu Windows Server, a następnie nawiąż połączenie z usługi Azure Active Directory.
* Administratorzy mogą udostępniać dostępu warunkowego opartego na zasób aplikacji, urządzenia i tożsamości użytkownika, lokalizacji sieciowej i uwierzytelniania wieloskładnikowego.
* Użytkownicy mogą korzystać z ich wspólną tożsamość za pomocą konta w usłudze Azure AD do usługi Office 365, Intune, aplikacje SaaS i aplikacje innych producentów.
* Deweloperzy mogą tworzyć aplikacje, które korzystają z wspólnego modelu tożsamości Integrowanie aplikacji w usłudze Active Directory w środowisku lokalnym lub na platformie Azure dla aplikacji opartych na chmurze

Poniższa ilustracja zawiera przykład ogólny widok procesu synchronizacji tożsamości.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Proces synchronizacji tożsamości

Przejrzyj poniższą tabelę, aby porównać opcji synchronizacji:

| Opcja zarządzania synchronizacji | Zalety | Wady |
| --- | --- | --- |
| Na podstawie synchronizacji (przy użyciu narzędzia DirSync lub AADConnect) |Użytkownicy i grupy synchronizowane z lokalną i chmurą <br>  **Kontrola zasad**: Zasady kont, można ustawić za pomocą usługi Active Directory, która umożliwia administratorowi Zarządzanie zasady dotyczące haseł, stacji roboczej, ograniczenia, formanty blokady i uzyskać więcej informacji, bez konieczności wykonywania dodatkowych zadań w chmurze.  <br>  **Kontrola dostępu**: Można ograniczyć dostęp do usługi w chmurze, tak, aby usług jest możliwy za pośrednictwem środowiska firmy za pośrednictwem serwerach w trybie online lub obu. <br>  Ograniczona obsługa wywołania: Jeśli użytkownicy mają mniejszą liczbę haseł do zapamiętania, są one mniej prawdopodobne zapomnieć je. <br>  Zabezpieczenia: Tożsamości użytkowników i informacji są chronione, ponieważ wszystkie serwery i usług używanych w rejestracji jednokrotnej, są zarządzane i kontrolowany w środowisku lokalnym. <br>  Obsługa silnego uwierzytelniania: Za pomocą silnego uwierzytelniania (nazywane również uwierzytelnianie dwuskładnikowe) z usługą w chmurze. Jednak jeśli używasz silne uwierzytelnianie, należy użyć logowania jednokrotnego. | |
| Na podstawie federacyjnych (za pośrednictwem usług AD FS) |Włączone przez usługę tokenu zabezpieczającego (STS). Po skonfigurowaniu usługi tokenu Zabezpieczającego w celu zapewnienia dostępu rejestracji jednokrotnej z usługą w chmurze firmy Microsoft, zostanie utworzona relację zaufania federacji między lokalnej usługi STS i domeny federacyjnej, wskazana w dzierżawie usługi Azure AD. <br> Umożliwia użytkownikom końcowym użyć tego samego zestawu poświadczeń, aby uzyskać dostęp do wielu zasobów <br>Użytkownicy końcowi nie jest konieczne Obsługa wielu zestawów poświadczeń. Jeszcze, użytkownicy muszą podawać swoich poświadczeń do każdego z nich uczestniczących w programie zasobów., scenariusze B2B i B2C obsługiwane. |Wymaga sprawdzenia personelu, wdrażania i utrzymywania dedykowanych na lokalnych serwerach usług AD FS. Brak ograniczeń dotyczących używania silnego uwierzytelniania, jeśli planujesz użyć usług AD FS do usługi STS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usług AD FS 2.0 zaawansowane opcje](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

