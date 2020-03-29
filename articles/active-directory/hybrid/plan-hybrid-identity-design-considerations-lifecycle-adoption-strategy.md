---
title: Hybrydowy projekt tożsamości — strategia wdrażania cyklu życia Azure | Dokumenty firmy Microsoft
description: Pomaga zdefiniować zadania zarządzania tożsamością hybrydową zgodnie z opcjami dostępnymi dla każdej fazy cyklu życia.
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
ms.openlocfilehash: 85f600c8bd46e699e80bf7b596574dc01467ef79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109319"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Określanie strategii wdrażania cyklu życia tożsamości hybrydowej
W tym zadaniu zdefiniujesz strategię zarządzania tożsamościami dla rozwiązania tożsamości hybrydowej, aby spełnić wymagania biznesowe zdefiniowane w [obszarze Określanie zadań zarządzania tożsamościami hybrydowymi.](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

Aby zdefiniować zadania zarządzania tożsamością hybrydową zgodnie z cyklem życia tożsamości end-to-end przedstawionym wcześniej w tym kroku, należy wziąć pod uwagę opcje dostępne dla każdej fazy cyklu życia.

## <a name="access-management-and-provisioning"></a>Zarządzanie dostępem i inicjowanie obsługi administracyjnej
Dzięki dobremu rozwiązaniu do zarządzania dostępem do konta twoja organizacja może dokładnie śledzić, kto ma dostęp do jakich informacji w całej organizacji.

Kontrola dostępu jest krytyczną funkcją scentralizowanego, jednopunktowego systemu inicjowania obsługi administracyjnej. Oprócz ochrony poufnych informacji, kontroli dostępu uwidaczniać istniejących kont, które mają niezatwierdzone autoryzacje lub nie są już konieczne. Aby kontrolować przestarzałe konta, system inicjowania obsługi administracyjnej łączy informacje o koncie z wiarygodnymi informacjami o użytkownikach, którzy są właścicielami kont. Autorytatywne informacje o tożsamości użytkownika są zazwyczaj przechowywane w bazach danych i katalogach zasobów ludzkich.

Konta w zaawansowanych przedsiębiorstwach IT zawierają setki parametrów definiujących władze, a te szczegóły mogą być kontrolowane przez system inicjowania obsługi administracyjnej. Nowi użytkownicy mogą być identyfikowani za pomocą danych podanych z autorytatywnego źródła. Możliwość zatwierdzania żądań dostępu inicjuje procesy, które zatwierdzają (lub odrzucają) inicjowanie obsługi administracyjnej zasobów dla nich.

| Faza zarządzania cyklem życia | W lokalu | Chmura | Połączenie hybrydowe |
| --- | --- | --- | --- |
| Zarządzanie kontem i inicjowanie obsługi administracyjnej |Korzystając z roli serwera usług domenowych Active Directory® (AD DS), można utworzyć skalowalną, bezpieczną i łatwą w obsłudze infrastrukturę do zarządzania użytkownikami i zasobami oraz zapewnić wsparcie aplikacji obsługujących katalogi, takich jak Microsoft® Exchange Server. <br><br> [Grupy w usługach AD DS można aprowizować za pośrednictwem Menedżera tożsamości](https://technet.microsoft.com/library/ff686261.aspx) <br>[Można aprowizować użytkowników w usługach AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Administratorzy mogą korzystać z kontroli dostępu do zarządzania dostępem użytkowników do zasobów udostępnionych ze względów bezpieczeństwa. W usłudze Active Directory kontrola dostępu jest administrowana na poziomie obiektu przez ustawienie różnych poziomów dostępu lub uprawnień do obiektów, takich jak Pełna kontrola, Zapis, Odczyt lub Brak dostępu. Kontrola dostępu w usłudze Active Directory określa, w jaki sposób różni użytkownicy mogą używać obiektów usługi Active Directory. Domyślnie uprawnienia do obiektów w usłudze Active Directory są ustawione na najbezpieczniejsze ustawienie. |Musisz utworzyć konto dla każdego użytkownika, który będzie miał dostęp do usługi w chmurze firmy Microsoft. Możesz też zmienić konta użytkowników lub je usunąć, gdy nie są już potrzebne. Domyślnie użytkownicy nie mają uprawnień administratora, ale opcjonalnie można je przypisać. <br><br> W usłudze Azure Active Directory jedną z głównych funkcji jest możliwość zarządzania dostępem do zasobów. Te zasoby mogą być częścią katalogu, jak w przypadku uprawnień do zarządzania obiektami za pośrednictwem ról w katalogu, lub znajdować się poza katalogiem, jak w przypadku aplikacji SaaS, usług platformy Azure, witryn programu SharePoint lub zasobów lokalnych. <br><br> W centrum rozwiązania do zarządzania dostępem usługi Azure Active Directory znajduje się grupa zabezpieczeń. Właściciel zasobu (lub administrator katalogu) może przypisać grupę, aby udzielić określonych uprawnień dostępu do jego zasobów. Członkowie grupy otrzymają dostęp, a właściciel zasobu może delegować prawo do zarządzania listą członków grupy innej osobie — na przykład kierownika działu lub administratora działu pomocy technicznej.<br> <br> Zarządzanie grupami w usłudze Azure AD, zawiera więcej informacji na temat zarządzania dostępem za pośrednictwem grup. |Rozszerzanie tożsamości usługi Active Directory do chmury poprzez synchronizację i federację |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontrola dostępu oparta na rolach (RBAC) używa ról i zasad inicjowania obsługi administracyjnej do oceny, testowania i wymuszania procesów biznesowych i reguł udzielania dostępu użytkownikom. Kluczowi administratorzy tworzą zasady inicjowania obsługi administracyjnej i przypisują użytkowników do ról i definiują zestawy uprawnień do zasobów dla tych ról. RBAC rozszerza rozwiązanie do zarządzania tożsamościami do korzystania z procesów opartych na oprogramowaniu i zmniejszyć interakcję ręczną użytkownika w procesie inicjowania obsługi administracyjnej.
Usługa Azure AD RBAC umożliwia firmie ograniczenie liczby operacji, które dana osoba może wykonać, gdy mają dostęp do witryny Azure portal. Za pomocą RBAC kontrolować dostęp do portalu, administratorzy IT ca delegowania dostępu przy użyciu następujących metod zarządzania dostępem:

* **Przypisanie ról oparte na grupach:** można przypisać dostęp do grup usługi Azure AD, które można synchronizować z lokalnej usługi Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, które organizacja poczyniła w narzędzia i procesy zarządzania grupami. Można również użyć funkcji zarządzania delegowanymi grupami usługi Azure AD Premium.
* **Wykorzystanie wbudowane role na platformie Azure:** można użyć trzech ról — właściciel, współautor i czytelnik, aby upewnić się, że użytkownicy i grupy mają uprawnienia do wykonywania tylko zadań, których potrzebują do wykonywania swoich zadań.
* **Szczegółowy dostęp do zasobów:** Można przypisać role do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub pojedynczego zasobu platformy Azure, takiego jak witryna sieci Web lub baza danych. W ten sposób można zapewnić, że użytkownicy mają dostęp do wszystkich zasobów, których potrzebują i nie ma dostępu do zasobów, które nie muszą zarządzać.

## <a name="provisioning-and-other-customization-options"></a>Inicjowanie obsługi administracyjnej i inne opcje dostosowywania
Twój zespół może korzystać z planów biznesowych i wymagań, aby zdecydować, ile dostosować rozwiązanie tożsamości. Na przykład duże przedsiębiorstwo może wymagać stopniowego planu wdrażania przepływów pracy i niestandardowych kart, który jest oparty na wierszu czasu dla aplikacji inicjowania obsługi administracyjnej przyrostowych, które są szeroko stosowane w różnych regionach geograficznych. Inny plan dostosowywania może zapewnić co najmniej dwie aplikacje, które mają być aprowizacji w całej organizacji, po pomyślnym przetestowaniu. Interakcja między użytkownikiem a aplikacją może być dostosowana, a procedury inicjowania obsługi administracyjnej zasobów mogą zostać zmienione w celu uwzględnienia automatycznego inicjowania obsługi administracyjnej.

Można anulować aprowizję, aby usunąć usługę lub składnik. Na przykład anulowanie obsługi administracyjnej konta oznacza, że konto jest usuwane z zasobu.

Hybrydowy model inicjowania obsługi administracyjnej zasobów łączy podejścia oparte na żądaniu i rolach, które są obsługiwane przez usługę Azure AD. W przypadku podzbioru pracowników lub systemów zarządzanych firma może chcieć zautomatyzować dostęp za pomocą przypisania opartego na rolach. Firma może również obsługiwać wszystkie inne żądania dostępu lub wyjątki za pośrednictwem modelu opartego na żądaniu. Niektóre firmy mogą rozpocząć od ręcznego przypisywania i ewoluować w kierunku modelu hybrydowego, z zamiarem wdrożenia w pełni opartego na rolach w przyszłości.

Inne firmy mogą uznać za niepraktyczne ze względów biznesowych, aby osiągnąć pełną aprowizacji opartej na rolach i kierować podejście hybrydowe jako cel poszukiwany. Jeszcze inne firmy mogą być zadowolone tylko z inicjowania obsługi administracyjnej opartej na żądaniach i nie chcą inwestować dodatkowych wysiłków w celu definiowania i zarządzania zautomatyzowanymi zasadami inicjowania obsługi administracyjnej opartymi na rolach.

## <a name="license-management"></a>Zarządzanie licencjami
Zarządzanie licencjami grupowymi w usłudze Azure AD umożliwia administratorom przypisywanie użytkowników do grupy zabezpieczeń, a usługa Azure AD automatycznie przypisuje licencje wszystkim członkom grupy. Jeśli użytkownik zostanie następnie dodany lub usunięty z grupy, licencja zostanie automatycznie przypisana lub usunięta odpowiednio.

Można użyć grup, które można synchronizować z lokalnej usługi AD lub zarządzać w usłudze Azure AD. Powiązanie tej usługi z usługą Azure AD premium Self-Service Group Management można łatwo delegować przypisanie licencji do odpowiednich decydentów. Możesz mieć pewność, że problemy, takie jak konflikty licencji i brakujące dane lokalizacji, są automatycznie rozwiązywane.

## <a name="self-regulating-user-administration"></a>Samoregulujące się administrowanie użytkownikami
Gdy organizacja zacznie aprowizować zasoby we wszystkich organizacjach wewnętrznych, można zaimplementować samoregulujące się możliwości administrowania użytkownikami. Można zrealizować zalety i korzyści z inicjowania obsługi administracyjnej użytkowników w granicach organizacji. W tym środowisku zmiana stanu użytkownika jest automatycznie odzwierciedlana w prawach dostępu w granicach organizacji i regionach geograficznych. Można zmniejszyć koszty inicjowania obsługi administracyjnej i usprawnić procesy dostępu i zatwierdzania. Implementacja realizuje pełny potencjał wdrażania kontroli dostępu opartej na rolach do zarządzania dostępem end-to-end w organizacji. Można zmniejszyć koszty administracyjne za pomocą zautomatyzowanych procedur zarządzania inicjowania obsługi administracyjnej użytkowników. Można poprawić bezpieczeństwo, automatyzując wymuszanie zasad zabezpieczeń oraz usprawnić i scentralizować zarządzanie cyklem życia użytkowników i inicjowanie obsługi administracyjnej zasobów dla dużych populacji użytkowników.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz Konfigurowanie usługi Azure AD do zarządzania dostępem do aplikacji samoobsługowych
> 
> 

Usługi Azure AD oparte na licencji (oparte na uprawnieniach) działają, aktywując subskrypcję w dzierżawie katalogów/usług usługi Azure AD. Gdy subskrypcja jest aktywna, możliwościami usługi mogą zarządzać administratorzy katalogów/usług i używać ich licencjonowani użytkownicy. 

## <a name="integration-with-other-3rd-party-providers"></a>Integracja z innymi dostawcami usług zewnętrznych

Usługa Azure Active Directory zapewnia logowanie jednokrotne i rozszerzone zabezpieczenia dostępu do aplikacji dla tysięcy aplikacji SaaS i lokalnych aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definiowanie zarządzania synchronizacją
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów, zarówno lokalnych, jak i w chmurze. Dzięki tej integracji użytkownicy i organizacje mogą korzystać z następujących elementów:

* Organizacje mogą zapewnić użytkownikom wspólną tożsamość hybrydową w usługach lokalnych lub opartych na chmurze, korzystając z usługi Windows Server Active Directory, a następnie łącząc się z usługą Azure Active Directory.
* Administratorzy mogą zapewnić dostęp warunkowy na podstawie zasobów aplikacji, tożsamości urządzenia i użytkownika, lokalizacji sieciowej i uwierzytelniania wieloskładnikowego.
* Użytkownicy mogą korzystać ze swojej wspólnej tożsamości za pośrednictwem kont w usłudze Azure AD do usługi Office 365, usługi Intune, aplikacji SaaS i aplikacji innych firm.
* Deweloperzy mogą tworzyć aplikacje, które wykorzystują wspólny model tożsamości, integrując aplikacje z usługą Active Directory lokalnie lub platformą Azure dla aplikacji opartych na chmurze

Poniższy rysunek ma przykład wysokiego poziomu widoku procesu synchronizacji tożsamości.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Proces synchronizacji tożsamości

Przejrzyj następującą tabelę, aby porównać opcje synchronizacji:

| Opcja zarządzania synchronizacją | Zalety | Wady |
| --- | --- | --- |
| Oparte na synchronizacji (za pośrednictwem DirSync lub AADConnect) |Użytkownicy i grupy zsynchronizowane z lokalnych i chmur <br>  **Kontrola zasad:** Zasady konta można ustawić za pomocą usługi Active Directory, która daje administratorowi możliwość zarządzania zasadami haseł, stacjami roboczymi, ograniczeniami, formantami blokady i innymi elementami, bez konieczności wykonywania dodatkowych zadań w chmurze.  <br>  **Kontrola dostępu:** Można ograniczyć dostęp do usługi w chmurze, dzięki czemu usługi są dostępne za pośrednictwem środowiska firmowego, za pośrednictwem serwerów online lub obu. <br>  Zredukowane połączenia z pomocą techniczną: jeśli użytkownicy mają mniej haseł do zapamiętania, rzadziej o nich zapominają. <br>  Zabezpieczenia: Tożsamości użytkowników i informacje są chronione, ponieważ wszystkie serwery i usługi używane w logowanie jednokrotne są opanowane i kontrolowane lokalnie. <br>  Obsługa silnego uwierzytelniania: można użyć silnego uwierzytelniania (nazywanego również uwierzytelnianiem dwuskładnikowym) z usługą w chmurze. Jeśli jednak używasz silnego uwierzytelniania, musisz użyć logowania jednokrotnego. | |
| Oparte na federacji (za pośrednictwem usług AD FS) |Włączone przez usługę tokenu zabezpieczającego (STS). Po skonfigurowaniu usługi STS w celu zapewnienia dostępu do logowania jednokrotnego z usługą w chmurze firmy Microsoft, zostaniesz utworzyć federacyjne zaufanie między lokalnym sts i federacyjnej domeny, które zostały określone w dzierżawie usługi Azure AD. <br> Umożliwia użytkownikom końcowym używanie tego samego zestawu poświadczeń w celu uzyskania dostępu do wielu zasobów <br>użytkownicy końcowi nie muszą obsługiwać wielu zestawów poświadczeń. Mimo to użytkownicy muszą podać swoje poświadczenia do każdego z uczestniczących zasobów.,B2B i B2C scenariusze obsługiwane. |Wymaga wyspecjalizowanego personelu do wdrażania i konserwacji dedykowanych na lokalnych serwerach AD FS. Istnieją ograniczenia dotyczące używania silnego uwierzytelniania, jeśli planujesz używać usług AD FS dla sts. Aby uzyskać więcej informacji, zobacz [Konfigurowanie opcji zaawansowanych dla usług AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

