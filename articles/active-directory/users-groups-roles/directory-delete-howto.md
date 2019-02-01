---
title: Usunąć katalog dzierżawy — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak przygotować katalogu dzierżawy usługi Azure AD do usunięcia
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 2fa8b6573952220b214294cfd0e89e978f6d00b5
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509358"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Usunąć dzierżawę usługi Azure Active Directory

Po usunięciu dzierżawy również zostaną usunięte wszystkie zasoby, które są zawarte w dzierżawie. Należy przygotować, minimalizując skojarzone z nią zasoby przed usunięciem dzierżawy. Tylko administrator globalny usługi Azure Active Directory (Azure AD) można usunąć dzierżawę usługi Azure AD z poziomu portalu.

## <a name="prepare-the-tenant-for-deletion"></a>Przygotowanie dzierżawy do usunięcia

Nie można usunąć dzierżawy w usłudze Azure AD, dopóki nie przekazuje kilka kontroli. Te sprawdzenia zmniejszyć ryzyko, że usunięcie dzierżawy negatywnie wpływa na dostęp użytkowników, takimi jak zalogować się do usługi Office 365 lub dostęp do zasobów na platformie Azure. Na przykład jeśli dzierżawca skojarzony z subskrypcją zostanie przypadkowo usunięty, następnie użytkownicy nie mogą uzyskiwać dostęp do zasobów platformy Azure dla tej subskrypcji. Poniżej wyjaśniono warunki, które są sprawdzane:

* W ramach dzierżawy, z wyjątkiem jednego administratora globalnego, która polega na usunięciu dzierżawy może być żadnych użytkowników. Wszyscy inni użytkownicy muszą zostać usunięte przed usunięciem dzierżawy. Jeśli użytkownicy są synchronizowani z lokalnej, następnie synchronizacja musi być wyłączona, a użytkownicy muszą zostać usunięte w dzierżawie chmury przy użyciu witryny Azure portal lub poleceń cmdlet programu Azure PowerShell. 
* Może to być żadnych aplikacji w dzierżawie. Wszystkie aplikacje muszą zostać usunięte przed usunięciem dzierżawy.
* Może to być żaden dostawca usługi Multi-Factor authentication, połączony z dzierżawą.
* Może to być żadnych subskrypcji dla dowolnego Microsoft Online Services, takich jak Microsoft Azure, usługi Office 365 lub Azure AD Premium skojarzone z dzierżawcą. Na przykład, jeśli dzierżawcy domyślne został utworzony na platformie Azure, nie można usunąć tej dzierżawy Jeśli Twoja subskrypcja platformy Azure wciąż korzysta z niego dla tej dzierżawy na potrzeby uwierzytelniania. Podobnie nie można usunąć dzierżawę, jeśli inny użytkownik skojarzył z nim subskrypcję. 

## <a name="delete-an-azure-ad-tenant"></a>Usunąć dzierżawę usługi Azure AD

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego dla dzierżawy.

2. Wybierz pozycję **Azure Active Directory**.

3. Przełącz się do dzierżawy chcesz usunąć.
  
  ![Usuwanie przycisku katalogu](./media/directory-delete-howto/delete-directory-command.png)

4. Wybierz **usunąć katalog**.
  
  ![Usuwanie przycisku katalogu](./media/directory-delete-howto/delete-directory-list.png)

5. Dzierżawy nie zakończy się pomyślnie co najmniej jeden, otrzymasz Link, aby uzyskać więcej informacji na temat sposobu przekazywania. Po przejściu wszystkich kontroli wybierz **Usuń** aby ukończyć proces.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Mam wygasłej subskrypcji, ale nie mogę usunąć dzierżawy

Podczas konfigurowania dzierżawy usługi Azure Active Directory mogą również aktywowano subskrypcji opartej na licencji dla Twojej organizacji, takich jak Azure Active Directory Premium P2, Office 365 Business Premium, lub pakietu Enterprise Mobility + Security E5. Te subskrypcje blokuje usunięcie katalogu, dopóki nie zostaną całkowicie usunięte, aby uniknąć przypadkowej utraty danych. Subskrypcje muszą znajdować się w **cofnięcia aprowizacji** stanu, aby zezwolić na usunięcie dzierżawy. **Wygasłe** lub **anulowane** subskrypcja jest przenoszona do **wyłączone** stanu, a ostatnim krokiem jest **Deprovisoned** stanu. 

Do czego można oczekiwać po wygaśnięciu wersji próbnej subskrypcji usługi Office 365, (nie w tym płatnych partnera/programu CSP, umowy Enterprise Agreement lub licencjonowania zbiorowego) Zobacz poniższej tabeli. Aby uzyskać więcej informacji na temat cyklu przechowywania i subskrypcji danych usługi Office 365, zobacz [co się stanie z moimi danymi i dostępu, po zakończeniu mojej usługi Office 365 dla firm subskrypcji?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stan subskrypcji | Dane | Dostęp do danych
----- | ----- | -----
Aktywne (30 dni w wersji próbnej)  | Dostęp do wszystkich danych    | <li>Użytkownicy mają dostęp do plików usługi Office 365 lub aplikacji<li>Administratorzy mają dostęp do Centrum administracyjnego usługi Office 365 i zasobów 
Wygasłe (30 dni)   | Dostęp do wszystkich danych    | <li>Użytkownicy mają dostęp do plików usługi Office 365 lub aplikacji<li>Administratorzy mają dostęp do Centrum administracyjnego usługi Office 365 i zasobów
Wyłączone (30 dni) | Dostęp do administratora tylko do danych  | <li>Użytkownicy nie mogą uzyskiwać dostęp do plików usługi Office 365 lub aplikacji<li>Administratorzy mogą dostęp do Centrum administracyjnego usługi Office 365, ale nie można przypisać licencje lub zaktualizować użytkowników
Anulowanie aprowizacji (30 dni po wyłączone) | Usunięte dane (automatycznie usuwane, jeśli brak innych usług, które są używane) | <li>Użytkownicy nie mogą uzyskiwać dostęp do plików usługi Office 365 lub aplikacji<li>Administratorzy mogą uzyskiwać dostęp do Centrum administracyjnego usługi Office 365, aby kupić i zarządzać innymi subskrypcjami

Możesz umieścić subskrypcji do **Deprovisoned** stanu do usunięcia w ciągu 3 dni, przy użyciu Microsoft Store dla firm Centrum administracyjnego. Ta funkcja będzie wkrótce dostępna do Centrum administracyjnego usługi Office 365.

1. Zaloguj się do [Microsoft Store dla firm Centrum administracyjnego](https://businessstore.microsoft.com/manage/) przy użyciu konta, które jest administratorem globalnym w dzierżawie. Jeśli próbujesz usunąć dzierżawę "Contoso", który ma contoso.onmicrosoft.com domeny początkowej domyślnej, zaloguj się przy użyciu nazwy UPN takich jak admin@contoso.onmicrosoft.com.

2. Przejdź do **Zarządzaj** kartę, a następnie wybierz pozycję **produktów i usług**, następnie wybierz subskrypcję, którą chcesz anulować i wybierz **Usuń**.
  
  ![Usuń łącze do usuwania subskrypcji](./media/directory-delete-howto/delete-command.png)
  
3. Wybierz **Usuń subskrypcję** Zaakceptuj warunki i postanowienia i Usuń subskrypcję. Wszystkie dane zostaną trwale usunięte w ciągu trzech dni. Jeśli zmienisz zdanie, może ponownie aktywować subskrypcję w ciągu następnych trzech dni.
  
  ![warunki i postanowienia](./media/directory-delete-howto/delete-terms.png)

4. Teraz zmieniła stan subskrypcji, subskrypcja jest oznaczona do usunięcia. Wprowadza subskrypcji **cofnięcia aprowizacji** stan później 72 godzin.

5. Po usunięciu subskrypcji w dzierżawie usługi i upłynęły 72 godziny, możesz zarejestrować się wstecz do Centrum administracyjnego usługi Azure AD ponownie i powinny być ulepszonymi wymagane i nie ma subskrypcji blokuje usunięcie Twojej dzierżawy. Można pomyślnie usunąć dzierżawę usługi Azure AD.
  
  ![Przekaż wyboru subskrypcji na ekranie usuwanie](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>Kolejne kroki
[Dokumentacja usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
