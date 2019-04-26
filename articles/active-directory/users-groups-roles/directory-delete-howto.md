---
title: Usunąć katalog usługi Azure AD — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak przygotować katalogu usługi Azure AD do usunięcia, w tym katalogi samoobsługi
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473193"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Usunąć katalog usługi Azure Active Directory

Po usunięciu katalogu usługi Azure AD, również zostaną usunięte wszystkie zasoby, które są zawarte w katalogu. Przygotowywanie organizacji, minimalizując skojarzone z nią zasoby przed usunięciem. Tylko administrator globalny usługi Azure Active Directory (Azure AD) można usunąć katalog usługi Azure AD z poziomu portalu.

## <a name="prepare-the-directory"></a>Przygotowywanie katalogu

Nie można usunąć katalogu, w usłudze Azure AD, dopóki nie przekazuje kilka kontroli. Te sprawdzenia ograniczyć ryzyko związane z, jaki usunięcie katalogu usługi Azure AD negatywnie wpływa na dostęp użytkowników, takimi jak zalogować się do usługi Office 365 lub dostęp do zasobów na platformie Azure. Na przykład jeśli katalog skojarzony z subskrypcją zostanie przypadkowo usunięty, następnie użytkownicy nie mogą uzyskiwać dostęp do zasobów platformy Azure dla tej subskrypcji. Są sprawdzane następujące warunki:

* W katalogu, z wyjątkiem jednego administratora globalnego, który polega na usunięciu katalog może być żadnych użytkowników. Wszyscy inni użytkownicy muszą zostać usunięci przed usunięciem katalogu. Jeśli użytkownicy są synchronizowani z lokalnej, następnie synchronizacja muszą najpierw zostać wyłączone, a Użytkownicy usunięci z katalogu w chmurze przy użyciu witryny Azure portal lub poleceń cmdlet programu Azure PowerShell.
* W katalogu nie może być aplikacji. Wszystkie aplikacje muszą zostać usunięte przed usunięciem katalogu.
* Może to być żaden dostawca usługi Multi-Factor authentication, połączony z katalogiem.
* Z katalogiem nie mogą być skojarzone żadne z usług Microsoft Online Services, takie jak platforma Microsoft Azure, usługa Office 365 lub usługa Azure AD w warstwie Premium. Na przykład jeśli domyślny katalog został utworzony na platformie Azure, nie możesz usunąć tego katalogu, jeśli subskrypcja platformy Azure wciąż korzysta z niego na potrzeby uwierzytelniania. Nie można także usunąć katalogu, jeśli inny użytkownik skojarzył z nim subskrypcję.

## <a name="delete-the-directory"></a>Usuń katalog

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego Twojej organizacji.

2. Wybierz pozycję **Azure Active Directory**.

3. Przejdź do katalogu, który chcesz usunąć.
  
   ![Upewnij się, organizacji, przed usunięciem](./media/directory-delete-howto/delete-directory-command.png)

4. Wybierz **usunąć katalog**.
  
   ![Wybierz polecenie, aby usunąć organizacji](./media/directory-delete-howto/delete-directory-list.png)

5. Jeśli katalogu nie zakończy się pomyślnie co najmniej jeden, otrzymasz link, aby uzyskać więcej informacji na temat sposobu przekazywania. Po przejściu wszystkich kontroli wybierz **Usuń** aby ukończyć proces.

## <a name="if-you-cant-delete-the-directory"></a>Jeśli nie można usunąć katalogu

Podczas konfigurowania katalogu usługi Azure AD mogą również aktywowano subskrypcji opartej na licencji dla Twojej organizacji, takich jak Azure AD Premium P2, Office 365 Business Premium, lub pakietu Enterprise Mobility + Security E5. Aby uniknąć przypadkowej utraty danych, nie można usunąć katalogu, dopóki subskrypcje zostaną całkowicie usunięte. Subskrypcje muszą znajdować się w **cofnięcia aprowizacji** stanu, aby zezwolić na usunięcie katalogu. **Wygasłe** lub **anulowane** subskrypcja jest przenoszona do **wyłączone** stanu, a ostatnim krokiem jest **cofnięcia aprowizacji** stanu.

Do czego można oczekiwać po wygaśnięciu wersji próbnej subskrypcji usługi Office 365, (nie w tym płatnych partnera/programu CSP, umowy Enterprise Agreement lub licencjonowania zbiorowego) Zobacz poniższej tabeli. Aby uzyskać więcej informacji na temat cyklu przechowywania i subskrypcji danych usługi Office 365, zobacz [co się stanie z moimi danymi i dostępu, po zakończeniu mojej usługi Office 365 dla firm subskrypcji?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stan subskrypcji | Dane | Dostęp do danych
----- | ----- | -----
Aktywne (30 dni w wersji próbnej) | Dostęp do wszystkich danych | Użytkownicy mają dostęp do plików usługi Office 365 lub aplikacji<br>Administratorzy mają dostęp do Centrum administracyjnego usługi Microsoft 365 i zasobów 
Wygasłe (30 dni) | Dostęp do wszystkich danych| Użytkownicy mają dostęp do plików usługi Office 365 lub aplikacji<br>Administratorzy mają dostęp do Centrum administracyjnego usługi Microsoft 365 i zasobów
Wyłączone (30 dni) | Dostęp do administratora tylko do danych | Użytkownicy nie mogą uzyskiwać dostęp do plików usługi Office 365 lub aplikacji<br>Administratorzy mogą dostęp do Centrum administracyjnego usługi Microsoft 365, ale nie można przypisać licencje lub zaktualizować użytkowników
Anulowanie aprowizacji (30 dni po wyłączone) | Usunięte dane (automatycznie usuwane, jeśli brak innych usług, które są używane) | Użytkownicy nie mogą uzyskiwać dostęp do plików usługi Office 365 lub aplikacji<br>Administratorzy mogą uzyskiwać dostęp do Centrum administracyjnego usługi Microsoft 365, kupowanie i zarządzać innymi subskrypcjami

## <a name="delete-a-subscription"></a>Usuwanie subskrypcji

Subskrypcję można umieścić w stanie cofnięcia aprowizacji do usunięcia w ciągu trzech dni, za pomocą Centrum administracyjnego usługi Microsoft 365.

1. Zaloguj się do [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com) przy użyciu konta, które jest administratorem globalnym w Twojej organizacji. Jeśli próbujesz usunąć katalogu "Contoso", który ma contoso.onmicrosoft.com domeny początkowej domyślnej, zaloguj się przy użyciu nazwy UPN takich jak admin@contoso.onmicrosoft.com.

2. Wybierz **rozliczeń** i wybierz **subskrypcje**, następnie wybierz subskrypcję, którą chcesz anulować. Po kliknięciu **anulować**, Odśwież stronę.
  
   ![Usuń łącze do usuwania subskrypcji](./media/directory-delete-howto/delete-command.png)
  
3. Wybierz **Usuń** do usunięcia subskrypcji i zaakceptuj warunki i postanowienia. Wszystkie dane zostaną trwale usunięte w ciągu trzech dni. Jeśli zmienisz zdanie, można ponownie aktywować subskrypcję w ciągu trzech dni.
  
   ![dokładnie przeczytaj warunki i postanowienia](./media/directory-delete-howto/delete-terms.png)

4. Teraz zmieniła stan subskrypcji, subskrypcja jest oznaczona do usunięcia. Wprowadza subskrypcji **cofnięcia aprowizacji** stan później 72 godzin.

5. Po usunięciu subskrypcji w katalogu, a upłynęły 72 godziny, możesz zalogować się wstecz do Centrum administracyjnego usługi Azure AD ponownie i powinny być ulepszonymi wymagane i nie ma subskrypcji blokuje usunięcie usługi katalogu. Można pomyślnie usunąć katalog usługi Azure AD.
  
   ![Przekaż wyboru subskrypcji na ekranie usuwanie](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Mam subskrypcję wersji próbnej, która blokuje usuwanie

Istnieją [samoobsługowego tworzenia konta produktów](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) takich jak Microsoft Power BI, Rights Management Services, Microsoft Power Apps lub Dynamics 365 poszczególnych użytkowników mogą się zarejestrować za pomocą usługi Office 365, który również tworzy użytkownik-Gość, uwierzytelniania katalog usługi Azure AD. Te produkty samoobsługi Blokuj usuwania katalogu, dopóki nie zostaną one całkowicie usunięte z katalogu, aby uniknąć utraty danych. Mogą one usunięte tylko przez administratora usługi Azure AD, czy użytkownik utworzył konto indywidualnie lub przypisano produktu.

Istnieją dwa typy samoobsługowego tworzenia konta produktów, w jaki sposób są przypisane: 

* Przypisanie poziomu organizacji: Administrator usługi Azure AD przypisze produktu w całej organizacji i użytkownika mogą być aktywnie przy użyciu usługi z przypisaniem poziomie organizacji, nawet jeśli ich nie są licencjonowane osobno.
* Przypisanie poziomu użytkownika: Indywidualnego użytkownika w samoobsługowej zasadniczo przypisuje produktu do siebie bez administratorem. Gdy jest zarządzane przez administratora organizacji (zobacz [przejęcia przez administratora, niezarządzanego katalogu](domains-admin-takeover.md), administrator bezpośrednio przypisać do użytkowników bez samoobsługowej produktu.  

Po rozpoczęciu usuwania rejestracji produktu samoobsługi akcja trwale usuwa dane i spowoduje usunięcie wszystkich dostęp użytkowników do usługi. Każdy użytkownik, któremu przypisano oferty, pojedynczo lub na poziomie organizacji, następnie jest zablokowany z logowaniem lub uzyskiwaniem dostępu do istniejących danych. Jeśli chcesz zapobiec utracie danych z produktem rejestracji samoobsługi, takie jak [pulpitów nawigacyjnych w usłudze Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) lub [konfiguracji zasad usług zarządzania prawami dostępu](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), upewnij się, że dane kopia zapasowa jest tworzona i zapisane w innym miejscu.

Aby uzyskać więcej informacji na temat dostępnych samoobsługi rejestracji produktów i usług, zobacz [dostępnych programów samoobsługi](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Do czego można oczekiwać po wygaśnięciu wersji próbnej subskrypcji usługi Office 365, (nie w tym płatnych partnera/programu CSP, umowy Enterprise Agreement lub licencjonowania zbiorowego) Zobacz poniższej tabeli. Aby uzyskać więcej informacji na temat cyklu przechowywania i subskrypcji danych usługi Office 365, zobacz [co się stanie z moimi danymi i dostępu, po zakończeniu mojej usługi Office 365 dla firm subskrypcji?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stan produktu | Dane | Dostęp do danych
------------- | ---- | --------------
Aktywne (30 dni w wersji próbnej) | Dostęp do wszystkich danych | Użytkownicy mają normalny dostęp do samoobsługi rejestracji produktu, plików lub aplikacji<br>Administratorzy mają dostęp do Centrum administracyjnego usługi Microsoft 365 i zasobów
Usunięte | Usunięte dane | Użytkownicy nie mogą korzystać z samoobsługi rejestracji produktu, plików lub aplikacji<br>Administratorzy mogą uzyskiwać dostęp do Centrum administracyjnego usługi Microsoft 365, kupowanie i zarządzać innymi subskrypcjami

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Jak można usunąć produktu rejestracji samoobsługowego w witrynie Azure portal?

Możesz umieścić samoobsługowego tworzenia konta produktem takich jak Microsoft Power BI lub usługi Azure Rights Management do **Usuń** stan natychmiast usunięty z portalu usługi Azure AD.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta administratora globalnego w organizacji. Jeśli próbujesz usunąć katalogu "Contoso", który ma contoso.onmicrosoft.com domeny początkowej domyślnej, zaloguj się przy użyciu nazwy UPN takich jak admin@contoso.onmicrosoft.com.

2. Wybierz **licencji**, a następnie wybierz pozycję **samoobsługowego tworzenia konta produktów**. Możesz zobaczyć wszystkich samoobsługowego tworzenia konta produktów oddzielnie od subskrypcji opartej na stanowisko w warstwie. Wybierz produkt, który chcesz trwale usunąć. Oto przykład w usłudze Microsoft Power BI:

    ![Nazwa użytkownika jest błędnie wpisana lub nie można odnaleźć](./media/directory-delete-howto/licenses-page.png)

3. Wybierz **Usuń** można usunąć produktu i zaakceptuj warunki tego dane zostaną usunięte natychmiast i nieodwołalnie. Ta akcja usuwania spowoduje usunięcie wszystkich użytkowników i Usuń organizacji dostęp do produktu. Kliknij przycisk Tak, aby przejść do przodu o usunięcie.  

    ![Nazwa użytkownika jest błędnie wpisana lub nie można odnaleźć](./media/directory-delete-howto/delete-product.png)

4. Po wybraniu **tak**, usunięcie produktu samoobsługi zostanie zainicjowany. Istnieje powiadomienie, które zostanie powiadomiony o usuwanie w toku.  

    ![Nazwa użytkownika jest błędnie wpisana lub nie można odnaleźć](./media/directory-delete-howto/progress-message.png)

5. Teraz w stanie samoobsługi rejestracji produktu została zmieniona na **usunięte**. Po odświeżeniu strony produktu powinna zostać usunięta z **samoobsługowego tworzenia konta produktów** strony.  

    ![Nazwa użytkownika jest błędnie wpisana lub nie można odnaleźć](./media/directory-delete-howto/product-deleted.png)

6. Po usunięciu wszystkich produktów, możesz ponownie zarejestrować do Centrum administracyjnego usługi Azure AD i powinno być nie niezbędne czynności, a produkty, nie blokuje usunięcie usługi katalogu. Można pomyślnie usunąć katalog usługi Azure AD.

    ![Nazwa użytkownika jest błędnie wpisana lub nie można odnaleźć](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Kolejne kroki

[Dokumentacja usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
