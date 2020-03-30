---
title: Usuwanie katalogu usługi Azure AD — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak przygotować katalog usługi Azure AD do usunięcia, w tym katalogi samoobsługowe
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
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961820"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Usuwanie katalogu w usłudze Azure Active Directory

Po usunięciu katalogu usługi Azure AD wszystkie zasoby, które są zawarte w katalogu są również usuwane. Przygotuj organizację, minimalizując skojarzone z nią zasoby przed usunięciem. Tylko administrator globalny usługi Azure Active Directory (Azure AD) może usunąć katalog usługi Azure AD z portalu.

## <a name="prepare-the-directory"></a>Przygotowywanie katalogu

Nie można usunąć katalogu w usłudze Azure AD, dopóki nie przejdzie kilka kontroli. Te kontrole zmniejszają ryzyko, że usunięcie katalogu usługi Azure AD negatywnie wpływa na dostęp użytkowników, na przykład możliwość logowania się do usługi Office 365 lub uzyskiwania dostępu do zasobów na platformie Azure. Na przykład jeśli katalog skojarzony z subskrypcją zostanie przypadkowo usunięty, użytkownicy nie będą mogli uzyskać dostępu do zasobów platformy Azure dla tej subskrypcji. Są sprawdzane następujące warunki:

* W katalogu nie może znajdować się żaden użytkownik, z wyjątkiem jednego administratora globalnego, który ma usunąć katalog. Wszyscy inni użytkownicy muszą zostać usunięci przed usunięciem katalogu. Jeśli użytkownicy są synchronizowane z lokalnego, następnie synchronizacji musi najpierw być wyłączone, a użytkownicy muszą zostać usunięte w katalogu w chmurze przy użyciu witryny azure portalu lub poleceń cmdlet programu Azure PowerShell.
* W katalogu nie może być aplikacji. Wszystkie aplikacje muszą zostać usunięte przed usunięciem katalogu.
* Nie może być żadnych dostawców uwierzytelniania wieloskładnikowego połączonych z katalogiem.
* Z katalogiem nie mogą być skojarzone żadne z usług Microsoft Online Services, takie jak platforma Microsoft Azure, usługa Office 365 lub usługa Azure AD w warstwie Premium. Na przykład jeśli domyślny katalog został utworzony na platformie Azure, nie możesz usunąć tego katalogu, jeśli subskrypcja platformy Azure wciąż korzysta z niego na potrzeby uwierzytelniania. Nie można także usunąć katalogu, jeśli inny użytkownik skojarzył z nim subskrypcję.

## <a name="delete-the-directory"></a>Usuwanie katalogu

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które jest administratorem globalnym w twojej organizacji.

2. Wybierz pozycję **Azure Active Directory**.

3. Przełącz się do katalogu, który chcesz usunąć.
  
   ![Potwierdź organizację przed usunięciem](./media/directory-delete-howto/delete-directory-command.png)

4. Wybierz **pozycję Usuń katalog**.
  
   ![wybierz polecenie, aby usunąć organizację](./media/directory-delete-howto/delete-directory-list.png)

5. Jeśli katalog nie przejdzie jednego lub więcej kontroli, otrzymasz łącze do więcej informacji na temat sposobu przekazywania. Po przejściu wszystkich kontroli wybierz pozycję **Usuń,** aby zakończyć proces.

## <a name="if-you-cant-delete-the-directory"></a>Jeśli nie możesz usunąć katalogu

Podczas konfigurowania katalogu usługi Azure AD być może aktywowano również subskrypcje oparte na licencji dla twojej organizacji, takie jak Azure AD Premium P2, Office 365 Business Premium lub Enterprise Mobility + Security E5. Aby uniknąć przypadkowej utraty danych, nie można usunąć katalogu, dopóki subskrypcje nie zostaną całkowicie usunięte. Subskrypcje muszą być w stanie **depropozycji,** aby umożliwić usuwanie katalogu. **Wygasła** lub **anulowana subskrypcja** przenosi się do stanu **Wyłączone,** a ostatnim etapem jest stan **Deprowidowane.**

Aby dostrzec, czego można się spodziewać po wygaśnięciu wersji próbnej subskrypcji usługi Office 365 (z wyłączeniem płatnego partnera/dostawców usług kryptograficznych, umowy Enterprise Agreement lub licencjonowania zbiorowego), zobacz poniższą tabelę. Aby uzyskać więcej informacji na temat przechowywania danych w usłudze Office 365 i cyklu życia subskrypcji, zobacz [Co się stanie z moimi danymi i dostępem po zakończeniu subskrypcji usługi Office 365 dla firm?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Stan subskrypcji | Dane | Dostęp do danych
----- | ----- | -----
Aktywny (30 dni w wersji próbnej) | Dane dostępne dla wszystkich | Użytkownicy mają normalny dostęp do plików lub aplikacji usługi Office 365<br>Administratorzy mają normalny dostęp do centrum administracyjnego i zasobów usługi Microsoft 365 
Upłynął (30 dni) | Dane dostępne dla wszystkich| Użytkownicy mają normalny dostęp do plików lub aplikacji usługi Office 365<br>Administratorzy mają normalny dostęp do centrum administracyjnego i zasobów usługi Microsoft 365
Wyłączone (30 dni) | Dane dostępne tylko dla administratora | Użytkownicy nie mogą uzyskiwać dostępu do plików lub aplikacji usługi Office 365<br>Administratorzy mogą uzyskiwać dostęp do centrum administracyjnego usługi Microsoft 365, ale nie mogą przypisywać licencji użytkownikom ani ich aktualizować
Anulowane (30 dni po wyłączono) | Dane usunięte (automatycznie usunięte, jeśli żadne inne usługi nie są używane) | Użytkownicy nie mogą uzyskiwać dostępu do plików lub aplikacji usługi Office 365<br>Administratorzy mogą uzyskiwać dostęp do centrum administracyjnego usługi Microsoft 365 w celu zakupu innych subskrypcji i zarządzania nimi

## <a name="delete-a-subscription"></a>Usuwanie subskrypcji

Subskrypcję można umieścić w stanie **Deprovisioned,** który ma zostać usunięty w ciągu trzech dni za pomocą centrum administracyjnego usługi Microsoft 365.

1. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com) przy za pomocą konta, które jest administratorem globalnym w organizacji. Jeśli próbujesz usunąć katalog "Contoso", który ma początkową domyślną domenę contoso.onmicrosoft.com, zaloguj admin@contoso.onmicrosoft.comsię przy za pomocą nazwy UPN, takiej jak .

2. Wyświetl podgląd nowego centrum administracyjnego usługi Microsoft 365, upewniając się, że przełącznik **Wypróbuj nowe centrum administracyjne** jest włączony.

   ![Podgląd nowego centrum administracyjnego M365](./media/directory-delete-howto/preview-toggle.png)

3. Po włączeniu nowego centrum administracyjnego musisz anulować subskrypcję, aby można było ją usunąć. Wybierz **pozycję Rozliczenia** i wybierz pozycję Produkty & **usług**, a następnie wybierz **pozycję Anuluj subskrypcję** dla subskrypcji, którą chcesz anulować. Zostaniesz przeniesiony na stronę opinii.

   ![Wybierz subskrypcję, aby anulować](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Wypełnij formularz opinii i wybierz **anuluj subskrypcję,** aby anulować subskrypcję.

   ![Polecenie Anuluj w podglądzie subskrypcji](./media/directory-delete-howto/cancel-command.png)

5. Teraz możesz usunąć subskrypcję. Wybierz **pozycję Usuń** dla subskrypcji, którą chcesz usunąć. Jeśli nie możesz znaleźć subskrypcji na stronie **Usługi & produkty,** upewnij się, że masz **stan Subskrypcja** ustawiony na **Wszystkie**.

   ![Usuń łącze do usuwania subskrypcji](./media/directory-delete-howto/delete-command.png)

6. Wybierz **pozycję Usuń subskrypcję,** aby usunąć subskrypcję i zaakceptować warunki. Wszystkie dane zostaną trwale usunięte w ciągu trzech dni. Możesz [ponownie aktywować subskrypcję w](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) ciągu trzech dni, jeśli zmienisz zdanie.
  
   ![uważnie przeczytać regulamin](./media/directory-delete-howto/delete-terms.png)

7. Teraz stan subskrypcji uległ zmianie, a subskrypcja jest oznaczona do usunięcia. Subskrypcja wchodzi w stan **Deprovisioned** 72 godzin później.

8. Po usunięciu subskrypcji w katalogu i upływie 72 godzin, można zalogować się ponownie do centrum administracyjnego usługi Azure AD i nie powinno być żadnych wymaganych działań i nie subskrypcji blokowania usunięcia katalogu. Powinno być możliwe pomyślne usunięcie katalogu usługi Azure AD.
  
   ![sprawdzanie subskrypcji na ekranie usuwania](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Mam subskrypcję próbną, która blokuje usuwanie

Istnieją [produkty do rejestracji samoobsługowej,](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) takie jak Microsoft Power BI, Rights Management Services, Microsoft Power Apps lub Dynamics 365, poszczególni użytkownicy mogą zarejestrować się za pośrednictwem usługi Office 365, która również tworzy użytkownika gościa do uwierzytelniania w katalogu usługi Azure AD. Te produkty samoobsługowe blokują usunięcia katalogów, dopóki nie zostaną całkowicie usunięte z katalogu, aby uniknąć utraty danych. Mogą one zostać usunięte tylko przez administratora usługi Azure AD, niezależnie od tego, czy użytkownik zarejestrował się indywidualnie, czy został przypisany do produktu.

Istnieją dwa rodzaje samoobsługowych produktów do rejestracji w sposobie ich przypisywania: 

* Przypisanie na poziomie organizacji: administrator usługi Azure AD przypisuje produkt do całej organizacji, a użytkownik może aktywnie korzystać z usługi z tym przypisaniem na poziomie organizacji, nawet jeśli nie jest licencjonowany indywidualnie.
* Przypisanie na poziomie użytkownika: Indywidualny użytkownik podczas samoobsługowej rejestracji zasadniczo przypisuje produkt do siebie bez administratora. Gdy organizacja stanie się zarządzana przez administratora (zobacz [Przejęcie niezarządzanego katalogu](domains-admin-takeover.md)przez administratora , administrator może bezpośrednio przypisać produkt do użytkowników bez samoobsługowej rejestracji.  

Po rozpoczęciu usuwania samoobsługowego produktu rejestracji akcja trwale usuwa dane i usuwa cały dostęp użytkownika do usługi. Każdy użytkownik, który został przypisany do oferty indywidualnie lub na poziomie organizacji, jest następnie zablokowany przed logowaniem się lub uzyskiwaniem dostępu do istniejących danych. Jeśli chcesz zapobiec utracie danych za pomocą samoobsługowego produktu rejestracji, takiego jak [pulpity nawigacyjne usługi Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) lub [konfiguracja zasad usługi Rights Management Services,](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)upewnij się, że kopia zapasowa danych jest archiwizacjona i zapisywana w innym miejscu.

Aby uzyskać więcej informacji na temat obecnie dostępnych produktów i usług samoobsługowych, zobacz [Dostępne programy samoobsługowe](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Aby dostrzec, czego można się spodziewać po wygaśnięciu wersji próbnej subskrypcji usługi Office 365 (z wyłączeniem płatnego partnera/dostawców usług kryptograficznych, umowy Enterprise Agreement lub licencjonowania zbiorowego), zobacz poniższą tabelę. Aby uzyskać więcej informacji na temat przechowywania danych w usłudze Office 365 i cyklu życia subskrypcji, zobacz [Co się stanie z moimi danymi i dostępem po zakończeniu subskrypcji usługi Office 365 dla firm?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Stan produktu | Dane | Dostęp do danych
------------- | ---- | --------------
Aktywny (30 dni w wersji próbnej) | Dane dostępne dla wszystkich | Użytkownicy mają normalny dostęp do samoobsługowego produktu rejestracji, plików lub aplikacji<br>Administratorzy mają normalny dostęp do centrum administracyjnego i zasobów usługi Microsoft 365
Usunięte | Dane usunięte | Użytkownicy nie mogą uzyskać dostępu do samoobsługowego produktu rejestracji, plików ani aplikacji<br>Administratorzy mogą uzyskiwać dostęp do centrum administracyjnego usługi Microsoft 365 w celu zakupu innych subskrypcji i zarządzania nimi

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Jak usunąć samoobsługowy produkt rejestracji w witrynie Azure portal?

Możesz umieścić samoobsługowy produkt rejestracji, taki jak Microsoft Power BI lub Usługi zarządzania prawami azure, do stanu **usuwania,** który ma zostać natychmiast usunięty w portalu usługi Azure AD.

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy za pomocą konta, które jest administratorem globalnym w organizacji. Jeśli próbujesz usunąć katalog "Contoso", który ma początkową domyślną domenę contoso.onmicrosoft.com, zaloguj admin@contoso.onmicrosoft.comsię za pomocą nazwy UPN, takiej jak .

2. Wybierz **pozycję Licencje**, a następnie wybierz **pozycję Produkty rejestracji samoobsługowej**. Możesz zobaczyć wszystkie produkty samoobsługowe rejestracji oddzielnie od subskrypcji opartych na siedzeniach. Wybierz produkt, który chcesz trwale usunąć. Oto przykład w usłudze Microsoft Power BI:

    ![nazwa użytkownika jest błędnie lub nie znaleziono](./media/directory-delete-howto/licenses-page.png)

3. Wybierz **usuń,** aby usunąć produkt i zaakceptować warunki, że dane są usuwane natychmiast i nieodwołalnie. Ta akcja usuwania usunie wszystkich użytkowników i usunie dostęp organizacji do produktu. Kliknij przycisk Tak, aby przejść do przodu z usunięciem.  

    ![nazwa użytkownika jest błędnie lub nie znaleziono](./media/directory-delete-howto/delete-product.png)

4. Po **wybraniu opcji Tak**zostanie zainicjowane usunięcie produktu samoobsługowego. Istnieje powiadomienie, które powie Ci o usunięciu w toku.  

    ![nazwa użytkownika jest błędnie lub nie znaleziono](./media/directory-delete-howto/progress-message.png)

5. Teraz stan samoobsługowego produktu rejestracji zmienił się na **Usunięte**. Po odświeżeniu strony produkt powinien zostać usunięty ze strony **Produkty do rejestracji samoobsługowej.**  

    ![nazwa użytkownika jest błędnie lub nie znaleziono](./media/directory-delete-howto/product-deleted.png)

6. Po usunięciu wszystkich produktów można ponownie zalogować się do centrum administracyjnego usługi Azure AD i nie powinno być żadnych wymaganych działań i żadnych produktów blokujących usunięcie katalogu. Powinno być możliwe pomyślne usunięcie katalogu usługi Azure AD.

    ![nazwa użytkownika jest błędnie lub nie znaleziono](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Następne kroki

[Dokumentacja usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
