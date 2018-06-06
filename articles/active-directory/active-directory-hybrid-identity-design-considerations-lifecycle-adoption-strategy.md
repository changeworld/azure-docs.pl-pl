---
title: Tożsamość hybrydowym - strategii wdrażania cyklu życia Azure | Dokumentacja firmy Microsoft
description: Umożliwia definiowanie zadań zarządzania tożsamości hybrydowej zgodnie z opcji dostępnych dla każdej fazy cyklu życia.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 911e65719bff0a86e3280482296bbb320fd30377
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801834"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Określić strategii wdrażania cyklu życia tożsamości hybrydowej
W tym zadaniu będziesz definiować strategii zarządzania tożsamościami dla hybrydowych tożsamości rozwiązanie, aby spełniać wymagania biznesowe zdefiniowaną w [określić zadań zarządzania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Aby zdefiniować zadania zarządzania tożsamości hybrydowej zgodnie z cyklem życia tożsamości na trasie przedstawione wcześniej w tym kroku, należy wziąć pod uwagę opcji dostępnych dla każdej fazy cyklu życia.

## <a name="access-management-and-provisioning"></a>Zarządzanie dostępem i inicjowania obsługi
Rozwiązania zarządzania kontem dobrych dostępu, w organizacji można śledzić dokładnie kto ma dostęp do informacji w organizacji.

Kontrola dostępu jest krytyczne funkcję inicjowania obsługi administracyjnej systemu scentralizowanego, jednego miejsca. Oprócz ochrony informacji poufnych, kontroli dostępu ujawnia istniejące konta, które mają niezatwierdzonych zezwolenia lub są już wymagane. Aby kontrolować konta przestarzałe, system łącza razem konta informacje o udostępnianiu z autorytatywne informacje o użytkownikach, którzy właścicielem konta. Informacje o tożsamości użytkownika autorytatywne zwykle są przechowywane w baz danych i katalogów zasobów ludzkich.

Kont w zaawansowanej przedsiębiorstw IT mają setki parametrów, które definiują urzędy, a informacje te mogą być kontrolowane przez inicjowania obsługi administracyjnej systemu. Dane, które zapewniają z wiarygodne źródło może zidentyfikować nowych użytkowników. Możliwość zatwierdzenia żądań dostępu inicjuje procesy, które zatwierdzenia (lub nie) inicjowania obsługi administracyjnej dla nich zasobów.

| Faza zarządzania cyklem życia | Lokalnie | Chmura | Połączenie hybrydowe |
| --- | --- | --- | --- |
| Zarządzanie kontami i inicjowania obsługi |Za pomocą roli serwera usług domenowych Active Directory® (AD DS), możesz utworzyć skalowalne, bezpieczną i łatwą w obsłudze infrastrukturę do zarządzania użytkownikami i zasobami i zapewniają obsługę aplikacji obsługujących katalogi, takich jak Microsoft® Exchange Server. <br><br> [Można udostępniać grup w usługach AD DS za pomocą programu Identity manager](https://technet.microsoft.com/library/ff686261.aspx) <br>[ Umożliwia obsługę użytkowników w usługach AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Administratorzy mogą używać kontroli dostępu do zarządzania dostępem użytkowników do zasobów udostępnionych ze względów bezpieczeństwa. W usłudze Active Directory, podawana jest kontrola dostępu na poziomie obiektu przez ustawienie różnych poziomów dostępu lub uprawnień do obiektów, takich jak Pełna kontrola, odczytu, zapisu lub Brak dostępu. Kontrola dostępu w usłudze Active Directory definiuje sposób różnych użytkowników za pomocą obiektów usługi Active Directory. Domyślnie uprawnienia do obiektów w usłudze Active Directory ustawiono najbezpieczniejsze ustawienie. |Należy utworzyć konta dla wszystkich użytkowników, którzy będą uzyskiwać dostęp do usługi w chmurze firmy Microsoft. Można również zmienić konta użytkowników lub usuwania ich, gdy nie są już potrzebne. Domyślnie użytkownicy nie mają uprawnień administratora, ale Opcjonalnie można przypisać. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami w usłudze Azure AD](active-directory-create-users.md). <br><br> W usłudze Azure Active Directory jednym z najważniejszych funkcji jest możliwość zarządzania dostępem do zasobów. Te zasoby mogą być częścią katalogu, tak jak w przypadku uprawnień do zarządzania obiektami za pomocą ról w katalogu lub zasobów, które są zewnętrzne względem katalogu, na przykład aplikacji SaaS, usług platformy Azure i witryn programu SharePoint lub zasobów lokalnych. <br><br> Dostęp do Centrum programu Azure Active Directory w rozwiązaniu do zarządzania jest grupą zabezpieczeń. Właściciel zasobu (lub administratora katalogu) można przypisać grupę w celu zapewnienia niektóre uprawnienia dostępu do zasobów, w których są właścicielami. Członkowie grupy zapewnia dostęp i właściciel zasobu można delegować prawa do listy członków grupy do innej osoby — takie jak Menedżer działu lub administratora pomocy technicznej do zarządzania<br> <br> Zarządzanie grup w sekcji usługi Azure AD zamieszczono więcej informacji dotyczących zarządzania dostęp za pośrednictwem grup. |Rozszerzenie w chmurze za pośrednictwem synchronizacji i federacji tożsamości usługi Active Directory |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Dostępu opartej na rolach (RBAC) używa ról kontroli i inicjowania obsługi zasad do oceny, testowania i wymuszać procesy biznesowe, a zasady dotyczące przyznawania dostępu użytkownikom. Administratorzy klucza tworzyć zasady inicjowania obsługi i przypisywania użytkowników do ról i definiującą zestawów uprawnień do zasobów dla tych ról. RBAC rozszerza rozwiązanie do zarządzania tożsamością procesów opartych na oprogramowaniu i zmniejszyć ręczne interakcji z użytkownikiem w procesie inicjowania obsługi administracyjnej.
Azure AD RBAC umożliwia firmie, aby ograniczyć liczbę operacji, które osoby mogą wykonywać, gdy ma on dostęp do portalu Azure. Za pomocą RBAC kontrolować dostęp do portalu, Administratorzy IT urzędu certyfikacji delegować dostęp przy użyciu następujących metod zarządzania dostępu:

* **Przypisanie oparte na grupach roli**: może przypisać dostęp do grup usługi Azure AD, które można synchronizować z lokalnej usługi Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, wprowadzone w narzędzi i procesów do zarządzania grupami organizacji. Umożliwia także funkcję delegowanej grupy zarządzania programu Azure AD Premium.
* **Wykorzystywanie wbudowane role na platformie Azure**: można użyć trzech ról — właściciela, współautora i czytnika, aby upewnić się, że użytkownicy i grupy mają uprawnienia do wykonywania zadań, należy wykonać swoje zadania.
* **Szczegółowe dostęp do zasobów**: można przypisać role do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub pojedynczych zasobów platformy Azure, takich jak witryny sieci Web lub bazy danych. W ten sposób można upewnij się, że użytkownicy mają dostęp do wszystkich zasobów, które są im potrzebne i Brak dostępu do zasobów, które nie muszą zarządzać.

## <a name="provisioning-and-other-customization-options"></a>Inicjowanie obsługi i inne opcje dostosowywania
Zespół plany biznesowe i umożliwia wymagania ile zdecydować dostosować rozwiązania tożsamości. Na przykład dużego przedsiębiorstwa mogą wymagać planu wdrożenie etapowe dla przepływów pracy i kart niestandardowych, które jest oparte na osi czasu przyrostowo udostępniania aplikacji, które są powszechnie używane na różnych obszarach geograficznych. Inny plan dostosowania może zawierać dwóch lub więcej aplikacji na potrzeby aprowizacji w całej organizacji, po pomyślnym przeprowadzeniu testów. Interakcja użytkownika aplikacji mogą być dostosowywane i procedury dotyczące inicjowania obsługi administracyjnej zasobów może ulec zmianie w celu uwzględnienia automatyczne Inicjowanie obsługi.

Można anulowanie zastrzeżenia do usunięcia usługi lub składnika. Na przykład anulowania obsługi konta oznacza, że konto zostało usunięte z zasobu.

Model hybrydowego udostępniania zasobów łączy żądania i opartej na rolach metod, które są obsługiwane przez usługę Azure AD. Dla podzbioru pracowników lub zarządzanych systemach firma może być do automatyzowania dostępu z przypisania opartego na rolach. Firma może również obsługi wszystkich żądań dostępu lub wyjątki poprzez model na podstawie żądań. Niektóre firmy mogą rozpoczynać się od ręcznego przypisania i rozwijać kierunku modelu hybrydowych, zamiaru wdrożenia pełni opartej na rolach w przyszłości.

Innych firm mogą go znaleźć niepraktyczne powodów biznesowych do osiągnięcia pełnej opartej na rolach inicjowania obsługi, a celem podejście hybrydowego jako cel odpowiedniej. Nadal innych firm, które może być spełnione tylko na podstawie żądań Obsługa administracyjna i chcesz inwestycji dodatkowego nakładu pracy, zdefiniuj zasad i zarządzania nimi opartej na rolach, automatycznego inicjowania obsługi administracyjnej.

## <a name="license-management"></a>Zarządzanie licencjami
Zarządzanie licencjami na podstawie grupy w usłudze Azure AD umożliwia administratorom przypisywania użytkowników do grupy zabezpieczeń i usługi Azure AD przypisuje automatycznie licencji do wszystkich członków grupy. Jeśli użytkownik jest następnie dodane do lub usunięte z grupy, licencji zostaną automatycznie przypisane lub usunięte odpowiednio.

Możesz użyć grup synchronizowani z lokalnej usługi AD lub zarządzania nimi w usłudze Azure AD. Parowanie to z usługą Azure AD premium samoobsługowego zarządzania grupami można łatwo delegować przypisania licencji do odpowiednich inne osoby podejmujące decyzje. Można mieć pewność, automatycznie sortowane problemów, takich jak konflikty licencji i danych lokalizacji Brak.

## <a name="self-regulating-user-administration"></a>Samoregulujące Administracja użytkownikami
Po uruchomieniu organizacji do udostępniania zasobów między organizacjami wewnętrzny wszystkich zaimplementowaniem samoregulujące funkcji administracji użytkowników. Korzyści i zalet inicjowania obsługi administracyjnej użytkowników można uzyskać w granicach organizacji. W tym środowisku zmiany w stan użytkownika jest automatycznie odzwierciedlane w prawa dostępu między granicami organizacji i lokalizacji geograficznych. Można zmniejszyć koszty obsługi administracyjnej i usprawnić procesy dostępu i zatwierdzania. Implementacja realizacji pełne możliwości wykonywania kontroli dostępu opartej na rolach dla zarządzania dostępem end-to-end w Twojej organizacji. Można zmniejszyć koszty administracyjne za pomocą zautomatyzowanych procedur dotyczących Inicjowanie obsługi użytkowników. Można zwiększyć poziom bezpieczeństwa, automatyzując egzekwowanie zasad zabezpieczeń i usprawnić i scentralizowanie zarządzania cyklem życia użytkownika i udostępnianie zasobów dla dużej liczby użytkowników populacji.

> [!NOTE]
> Aby uzyskać więcej informacji zobacz Konfigurowanie usługi Azure AD w celu zarządzania dostępem do aplikacji Sklep internetowy
> 
> 

(W oparciu o uprawnieniach) na podstawie licencji usługi Azure AD usług pracy przez aktywowania subskrypcji w dzierżawie usługi katalogowej/usługi Azure AD. Gdy subskrypcja jest aktywna możliwości usługi można zarządzane przez administratorów usługi katalogowej/i używane przez licencjonowanych użytkowników. 

## <a name="integration-with-other-3rd-party-providers"></a>Integracja z innych dostawców 3

Usługa Azure Active Directory zapewnia jednokrotnego na i rozszerzone zabezpieczenia dostępu aplikacji do tysięcy aplikacji SaaS i lokalnej aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md)

## <a name="define-synchronization-management"></a>Definiowanie zarządzania synchronizacji
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów, zarówno lokalnych, jak i w chmurze. Dzięki tej integracji użytkownicy i organizacje uzyskują następujące korzyści:

* Organizacje mogą umożliwiać użytkownikom wspólną tożsamość hybrydowa między lokalnymi lub usług w chmurze korzystania z usługi Active Directory systemu Windows Server, a następnie łącząc do usługi Azure Active Directory.
* Administratorzy mogą udostępnić dostępu warunkowego na podstawie zasobów aplikacji, urządzenia i tożsamości użytkownika, lokalizacji sieciowej i uwierzytelniania wieloskładnikowego.
* Użytkownicy mogą korzystać z ich wspólną tożsamość za pomocą konta w usłudze Azure AD do usługi Office 365, Intune, aplikacji SaaS i aplikacje innych producentów.
* Deweloperzy mogą tworzyć aplikacje, które wykorzystują wspólnego modelu tożsamości, integrowanie aplikacje lokalne usługi Active Directory lub Azure dla aplikacji działających w chmurze

Poniższa ilustracja zawiera przykład ogólny widok procesu synchronizacji tożsamości.

![](./media/hybrid-id-design-considerations/identitysync.png)

Proces synchronizacji tożsamości

Przejrzyj poniższej tabeli, aby porównać opcji synchronizacji:

| Opcja zarządzania synchronizacji | Zalety | Wady |
| --- | --- | --- |
| Na podstawie synchronizacji (przy użyciu narzędzia DirSync lub AADConnect) |Użytkownicy i grupy synchronizowane z lokalnej i w chmurze <br>  **Zasady kontroli**: zasady konta można ustawić za pomocą usługi Active Directory, co daje możliwość zarządzania zasad haseł, stacji roboczej, ograniczenia, formanty blokady administrator, a więcej, bez konieczności wykonywania dodatkowych zadań w programie Chmura.  <br>  **Kontrola dostępu**: można ograniczyć dostęp do usługi w chmurze tak, aby usługi jest możliwy za pośrednictwem środowiska firmy za pośrednictwem serwerów online lub oba. <br>  Zmniejszona wywołań obsługi: Jeśli użytkownicy mają mniej haseł do zapamiętania, jest mniej prawdopodobne zapomnij je. <br>  Zabezpieczenia: Tożsamości użytkowników i informacje są chronione, ponieważ wszystkie serwery i usług używanych w rejestracji jednokrotnej, są zarządzane i kontrolowane lokalnymi. <br>  Obsługa silnego uwierzytelniania: można używać silnego uwierzytelniania (nazywanych również uwierzytelnianie dwuskładnikowe) z usługi w chmurze. Jednak użycie silnego uwierzytelniania, należy użyć rejestracji jednokrotnej. | |
| Na podstawie federacyjnych (za pośrednictwem usług AD FS) |Korzystając z usługi tokenu zabezpieczającego (STS). Po skonfigurowaniu usługi tokenu Zabezpieczającego w celu zapewnienia dostępu rejestracji jednokrotnej z usługą w chmurze firmy Microsoft, zostanie utworzony federacyjnej relacji zaufania między z lokalnej usługi STS i domeny federacyjnej, określonych przez Ciebie w dzierżawie usługi Azure AD. <br> Umożliwia użytkownikom końcowym używać tego samego zestawu poświadczeń do uzyskania dostępu do wielu zasobów <br>Użytkownicy końcowi nie jest konieczne Obsługa wielu zestawów poświadczeń. Jeszcze, użytkownicy muszą podać swoje poświadczenia do każdej z nich uwzględnionych zasobów., scenariusze B2B i B2C, które są obsługiwane. |Wymaga specjalnych personelu do wdrażania i konserwacji lokalnych dedykowanych serwerów usług AD FS. Jeśli planujesz używać usług AD FS dla Twojej usługi STS istnieją ograniczenia dotyczące stosowania silnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usług AD FS 2.0 zaawansowane opcje](http://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
> 
> 

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

