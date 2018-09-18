---
title: Zarządzanie dzierżawą usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat dzierżawy usługi Azure AD jest i jak nią zarządzać za pomocą usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.reviewer: jeffsta
custom: it-pro
ms.openlocfilehash: 2a548a16a6b896ae1c1297badcef6154b64977f1
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45737179"
---
# <a name="manage-your-azure-active-directory-tenant"></a>Zarządzanie dzierżawą usługi Azure Active Directory

## <a name="what-is-an-azure-ad-tenant"></a>Co to jest dzierżawa usługi Azure AD?
W usłudze Azure Active Directory (Azure AD) dzierżawa to dedykowane wystąpienie katalogu usługi Azure AD, które otrzymuje organizacja po zarejestrowaniu w usłudze firmy Microsoft w chmurze, takiej jak Azure lub Office 365. Każdy katalog usługi Azure AD jest odrębny i oddzielony od innych katalogów usługi Azure AD. Podobnie jak budynek biurowy, który jest zabezpieczonym zasobem przeznaczonym tylko dla Twojej organizacji, katalog Azure AD został zaprojektowany jako zabezpieczony zasób do użytku tylko Twojej organizacji. Architektura usługi Azure AD izoluje dane klienta i informacje o tożsamości, więc użytkownicy i administratorzy jednego katalogu usługi Azure AD nie mogą przypadkowo ani złośliwie uzyskać dostępu do danych w innym katalogu.

![Zarządzanie usługą Azure Active Directory](./media/active-directory-administer/aad_portals.png)

## <a name="how-can-i-get-an-azure-ad-directory"></a>Jak mogę uzyskać katalog usługi Azure AD?
Usługa Azure AD zapewnia podstawowe możliwości zarządzania katalogiem i tożsamościami, na których bazuje większość usług w chmurze firmy Microsoft, w tym:

* Azure
* Usługa Microsoft Office 365
* Usługa Microsoft Dynamics CRM Online
* Microsoft Intune

Katalog usługi Azure AD uzyskujesz, gdy tworzysz konto w ramach dowolnej z tych usług w chmurze firmy Microsoft. W razie potrzeby możesz utworzyć dodatkowe katalogi. Na przykład możesz zachować pierwszy katalog jako katalog produkcyjny i utworzyć kolejny na potrzeby testowania lub wdrażania przejściowego.

### <a name="using-the-azure-ad-directory-that-comes-with-a-new-azure-subscription"></a>Używanie katalogu usługi Azure AD, który jest udostępniany wraz z nową subskrypcją platformy Azure

Zaleca się korzystanie z konta administratora używanego dla pierwszej usługi po zarejestrowaniu w innych usługach firmy Microsoft. Informacje, które podasz podczas pierwszego rejestrowania się w usłudze Microsoft, służą do tworzenia nowego wystąpienia katalogu usługi Azure AD dla Twojej organizacji. Jeśli używasz tego katalogu do uwierzytelniania prób logowania po zasubskrybowaniu innych usług firmy Microsoft, możesz użyć istniejących kont użytkownika, zasad, ustawień lub integracji katalogu lokalnego skonfigurowanych w Twoim katalogu domyślnym.

Jeśli na przykład zarejestrujesz się w celu uzyskania subskrypcji usługi Microsoft Intune, a następnie dalej będziesz synchronizować lokalną usługę Active Directory z katalogiem usługi Azure AD, możesz zarejestrować się w innej usłudze firmy Microsoft, takiej jak Office 365, i łatwo uzyskać takie same korzyści z integracji katalogu, które były dostępne w usłudze Microsoft Intune.

Aby uzyskać więcej informacji na temat integracji Twojego lokalnego katalogu z usługą Azure AD, zobacz [Directory integration with Azure AD Connect](../connect/active-directory-aadconnect.md) (Integracja katalogu z usługą Azure AD Connect).

### <a name="associate-an-existing-azure-ad-directory-with-a-new-azure-subscription"></a>Kojarzenie istniejącego katalogu usługi Azure AD z nową subskrypcją platformy Azure
Możesz skojarzyć nową subskrypcję platformy Azure z tym samym katalogiem, który uwierzytelnia logowanie dla istniejącej subskrypcji usługi Office 365 lub Microsoft Intune. Aby uzyskać więcej informacji dotyczących tego scenariusza, zobacz [Transfer ownership of an Azure subscription to another account](../../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto)

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Tworzenie katalogu usługi Azure AD przez utworzenie konta w usłudze w chmurze firmy Microsoft jako organizacja
Jeśli jeszcze nie masz subskrypcji usługi w chmurze firmy Microsoft, możesz użyć następujących linków, aby zarejestrować się. Rejestracja w pierwszej usłudze spowoduje automatyczne utworzenie katalogu usługi Azure AD.

* [Microsoft Azure](https://account.azure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://portal.office.com/Signup/Signup.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&dl=INTUNE_A&ali=1#0%20)

### <a name="how-to-change-the-default-directory-for-a-subscription"></a>Jak zmienić domyślny katalog dla subskrypcji

1. Zaloguj się do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions) przy użyciu konta należącego do administratora konta dla subskrypcji, aby przenieść własność subskrypcji.
2. Upewnij się, że użytkownik, który ma być właścicielem subskrypcji, znajduje się w katalogu docelowym.
3. Kliknij pozycję **Przenieś subskrypcję**.
4. Określ adresata. Adresat automatycznie otrzymuje wiadomość e-mail z linkiem umożliwiającym akceptację.
5. Adresat klika link i postępuje zgodnie z instrukcjami, co obejmuje również wprowadzenie informacji dotyczących płatności. Jeśli czynności wykonywane przez adresata zakończą się pomyślnie, subskrypcja zostanie przeniesiona. 
6. Jeśli przenoszenie własności subskrypcji powiedzie się, domyślny katalog subskrypcji zostanie zmieniony na katalog, w którym znajduje się użytkownik.

Aby dowiedzieć się więcej, zobacz [Transfer Azure subscription ownership to another account (Przenoszenie własności subskrypcji platformy Azure na inne konto)](../../billing/billing-subscription-transfer.md).

### <a name="manage-the-default-directory-in-azure"></a>Zarządzanie domyślnym katalogiem na platformie Azure
Kiedy rejestrujesz się na platformie Azure, domyślny katalog usługi Azure AD jest kojarzony z Twoją subskrypcją. Korzystanie z usługi Azure AD nie wiąże się z żadnymi kosztami, a Twoje katalogi są bezpłatnym zasobem. Istnieją płatne usługi Azure AD, które są licencjonowane osobno i udostępniają dodatkowe funkcje, takie jak oznaczanie marką firmy podczas logowania i samoobsługowe resetowanie hasła. Możesz również utworzyć niestandardową domenę przy użyciu własnej nazwy DNS zamiast domyślnej domeny *.onmicrosoft.com.

## <a name="how-can-i-manage-directory-data"></a>Jak mogę zarządzać danymi w katalogu?
Aby administrować co najmniej jedną subskrypcją usługi w chmurze firmy Microsoft, możesz użyć [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com), portalu konta usługi Microsoft Intune lub [Centrum administracyjnego usługi Office 365](https://portal.office.com/) do zarządzania danymi katalogu Twojej organizacji. Możesz również użyć [poleceń cmdlet programu PowerShell usługi Azure Active Directory](https://docs.microsoft.com/powershell/azure/active-directory), które ułatwiają zarządzanie danymi zapisanymi w usłudze Azure AD.

Przy użyciu dowolnego z tych portali (lub poleceń cmdlet) możesz:

* Tworzyć konta użytkowników oraz grup i zarządzać nimi
* Zarządzanie powiązanymi usługami w chmurze dla subskrypcji Twojej organizacji
* Konfigurowanie lokalnej integracji z usługami tożsamości i uwierzytelniania usługi Azure AD

Centrum administracyjne usługi Azure AD, centrum administracyjne usługi Office 365, portal konta usługi Microsoft Intune i polecenia cmdlet usługi Azure AD odczytują i zapisują dane w ramach pojedynczego, współdzielonego wystąpienia usługi Azure AD, które jest skojarzone z katalogiem organizacji. Każde z tych narzędzi działa jako interfejs frontonu, który ściąga lub zmienia dane katalogu.
Kiedy wprowadzisz zmiany w danych organizacji przy użyciu dowolnego z portali lub poleceń cmdlet, będąc zalogowanym w kontekście jednej z tych usług, to jednocześnie zmiany są także wyświetlane w innych portalach po następnym zalogowaniu. Dane są współużytkowane przez usługi firmy Microsoft w chmurze, które subskrybujesz.

Jeśli na przykład użyjesz centrum administracyjnego usługi Office 365, aby uniemożliwić użytkownikowi logowanie, ta akcja zablokuje także logowanie użytkownika do wszystkich innych usług, które obecnie subskrybuje Twoja organizacja. Jeśli wyświetlisz to samo konto użytkownika w portalu konta usługi Microsoft Intune, możesz również zobaczyć, że użytkownik jest zablokowany.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Jak mogę dodać wiele katalogów i zarządzać nimi?
Możesz [dodać katalog usługi Azure AD w witrynie Azure Portal](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Podaj informacje i wybierz pozycję **Utwórz**.

Możesz zarządzać każdym katalogiem jako w pełni niezależnym zasobem: każdy katalog jest równorzędny, obsługuje wszystkie funkcje i jest logicznie niezależny od innych katalogów zarządzanych przez Ciebie. Między katalogami nie ma relacji nadrzędny-podrzędny. Ta niezależność między katalogami obejmuje niezależność zasobów, niezależność administracyjną i niezależność synchronizacji.

* **Niezależność zasobów**. Jeśli utworzysz lub usuniesz zasób w jednym katalogu, nie ma to wpływu na żaden zasób w innym katalogu (z częściowym wyjątkiem dotyczącym zewnętrznych użytkowników). Jeśli używasz niestandardowej domeny „contoso.com” w jednym katalogu, nie można jej użyć w żadnym innym katalogu.
* **Niezależność administracyjna**.  Jeśli użytkownik, który nie jest administratorem katalogu „Contoso”, utworzy katalog testowy „Test”, wtedy:
  
  * Administratorzy katalogu „Contoso” nie mają bezpośrednich uprawnień administracyjnych do katalogu „Test”, chyba że administrator tego katalogu jawnie nada im odpowiednie uprawnienia. Administratorzy katalogu „Contoso” mogą kontrolować dostęp do katalogu „Test”, korzystając ze swoich możliwości kontrolowania konta użytkownika, za pomocą którego utworzono katalog „Test”.
    
  * Jeśli dodasz lub usuniesz rolę administratora dla użytkownika w jednym katalogu, zmiana ta nie wpłynie na żadną rolę administracyjną, którą użytkownik może mieć w innym katalogu.
* **Niezależność synchronizacji**. Możesz niezależnie skonfigurować każdą dzierżawę usługi Azure AD w celu synchronizacji danych z pojedynczego wystąpienia narzędzia do synchronizacji katalogów usługi Azure AD Connect.

W przeciwieństwie do innych zasobów platformy Azure, Twoje katalogi nie są zasobami podrzędnymi subskrypcji platformy Azure. Więc jeśli anulujesz subskrypcję platformy Azure lub zezwolisz na jej wygaśnięcie, dane katalogu będą w dalszym ciągu dostępne przy użyciu programu PowerShell usługi Azure AD, interfejsu API Graph platformy Azure lub innych interfejsów, takich jak interfejs centrum administracyjnego usługi Office 365. Możesz także skojarzyć inną subskrypcję z katalogiem.

## <a name="how-to-prepare-to-delete-an-azure-ad-directory"></a>Jak przygotować usunięcie katalogu usługi Azure AD
Administrator globalny może usunąć katalog usługi Azure AD z portalu. Wraz z katalogiem zostaną również usunięte wszystkie zasoby w nim zawarte. Sprawdź, czy katalog nie jest potrzebny, zanim go usuniesz.

> [!NOTE]
> Jeśli użytkownik jest zalogowany za pomocą konta służbowego, nie może próbować usunąć własnego katalogu macierzystego. Jeśli na przykład użytkownik jest zalogowany za pomocą konta joe@contoso.onmicrosoft.com, to nie może usunąć katalogu, którego domyślna domena to contoso.onmicrosoft.com.

Usługa Azure AD wymaga spełnienia pewnych warunków przed usunięciem katalogu. Zmniejszają one ryzyko negatywnego wpływu, jaki usunięcie katalogu będzie mieć na użytkowników lub aplikacje, na przykład na możliwość zalogowania się do usługi Office 365 lub dostęp do zasobów platformy Azure. Jeśli na przykład katalog subskrypcji zostanie przypadkowo usunięty, użytkownicy nie będą mieli dostępu do zasobów platformy Azure dla tej subskrypcji.

Są sprawdzane następujące warunki:

* Jedynym użytkownikiem katalogu powinien być administrator globalny, który powinien usunąć katalog. Wszyscy inni użytkownicy muszą zostać usunięci przed usunięciem katalogu. Jeśli użytkownicy są synchronizowani z lokalnego źródła, synchronizacja musi zostać wyłączona, a użytkownicy usunięci z katalogu w chmurze za pomocą witryny Azure Portal lub poleceń cmdlet programu PowerShell. Nie jest wymagane usunięcie grup ani kontaktów, takich jak kontakty dodane za pomocą centrum administracyjnego usługi Office 365.
* W katalogu nie może być aplikacji. Wszystkie aplikacje muszą zostać usunięte przed usunięciem katalogu.
* Żaden dostawca uwierzytelniania wieloskładnikowego nie może być połączony z katalogiem.
* Z katalogiem nie mogą być skojarzone żadne z usług Microsoft Online Services, takie jak platforma Microsoft Azure, usługa Office 365 lub usługa Azure AD w warstwie Premium. Na przykład jeśli domyślny katalog został utworzony na platformie Azure, nie możesz usunąć tego katalogu, jeśli subskrypcja platformy Azure wciąż korzysta z niego na potrzeby uwierzytelniania. Nie można także usunąć katalogu, jeśli inny użytkownik skojarzył z nim subskrypcję. 


## <a name="next-steps"></a>Kolejne kroki
* [Forum usługi Azure AD](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazuread)
* [Forum usługi Azure Multi-Factor Authentication](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazureactiveauthentication)
* [Witryna StackOverflow z pytaniami dotyczącymi platformy Azure](https://stackoverflow.com/questions/tagged/azure-active-directory)
* [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory)
* [Przypisywanie ról administratorów w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
