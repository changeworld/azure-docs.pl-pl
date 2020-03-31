---
title: Przejęcie przez administratora katalogu niezarządzanego — Usługa Azure AD | Dokumenty firmy Microsoft
description: Jak przejąć nazwę domeny DNS w niezarządzanej organizacji usługi Azure AD (dzierżawy w tle).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834184"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Przejęcie niezarządzanego katalogu jako administrator w usłudze Azure Active Directory

W tym artykule opisano dwa sposoby przejęcia nazwy domeny DNS w katalogu niezarządzanym w usłudze Azure Active Directory (Azure AD). Gdy użytkownik samoobsługi rejestruje się w usłudze w chmurze, która korzysta z usługi Azure AD, jest dodawany do niezarządzanego katalogu usługi Azure AD na podstawie swojej domeny poczty e-mail. Aby uzyskać więcej informacji na temat samoobsługi lub "wirusowe" rejestracji dla usługi, zobacz [Co to jest samoobsługowe rejestracji dla usługi Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Zdecyduj, w jaki sposób chcesz przejąć katalog niezarządzany
Podczas procesu przejęcia przez administratora możesz udowodnić własność w sposób opisany w artykule [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md). W kolejnych sekcjach objaśniono środowisko pracy administratora bardziej szczegółowo, ale w tym miejscu znajduje się podsumowanie:

* W przypadku wykonania [„wewnętrznego” przejęcia przez administratora](#internal-admin-takeover) niezarządzanego katalogu platformy Azure użytkownik jest dodawany jako administrator globalny katalogu niezarządzanego. Żadni inni użytkownicy, domeny ani plany usługi nie są migrowane do żadnego innego katalogu, którym administruje.

* W przypadku wykonania [„zewnętrznego” przejęcia przez administratora](#external-admin-takeover) niezarządzanego katalogu platformy Azure dodajesz nazwę domeny DNS katalogu niezarządzanego do swojego zarządzanego katalogu platformy Azure. W przypadku dodania nazwy domeny mapowanie użytkowników do zasobów jest tworzone w Twoim zarządzanym katalogu usługi Azure, dzięki czemu użytkownicy mogą nadal bez przeszkód uzyskiwać dostęp do usług. 

## <a name="internal-admin-takeover"></a>Przejęcie przez administratora wewnętrznego

Niektóre produkty, które zawierają program SharePoint i OneDrive, takie jak Office 365, nie obsługują zewnętrznego przejęcia. Jeśli jest to twój scenariusz lub jeśli jesteś administratorem i chcesz przejąć niezarządzaną lub "cień" dzierżawy utworzyć przez użytkowników, którzy korzystali z samoobsługi rejestracji, można to zrobić za pomocą przejęcia administratora wewnętrznego.

1. Tworzenie kontekstu użytkownika w dzierżawie niezarządzanej przez zarejestrowanie się w usłudze Power BI. Dla wygody przykład te kroki zakładają, że ścieżka.

2. Otwórz [witrynę usługi Power BI](https://powerbi.com) i wybierz **pozycję Rozpocznij bezpłatne**. Wprowadź konto użytkownika, które używa nazwy domeny dla organizacji; na przykład `admin@fourthcoffee.xyz`. Po wprowadzeniu kodu weryfikacyjnego sprawdź kod potwierdzenia.

3. W wiadomości e-mail z potwierdzeniem z usługi Power BI wybierz pozycję **Tak, to ja**.

4. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://portal.office.com/admintakeover) przy za pomocą konta użytkownika usługi Power BI. Zostanie wyświetlony komunikat z poleceniem **zostania administratorem** nazwy domeny, która została już zweryfikowana w dzierżawie niezarządzanej. wybierz **Tak, chcę być administratorem**.
  
   ![pierwszy zrzut ekranu dla Zostań administratorem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Dodaj rekord TXT, aby udowodnić, że jesteś właścicielem nazwy domeny **fourthcoffee.xyz** u rejestratora nazw domen. W tym przykładzie jest GoDaddy.com.
  
   ![Dodawanie rekordu txt dla nazwy domeny](./media/domains-admin-takeover/become-admin-txt-record.png)

Po zweryfikowaniu rekordów DNS TXT u rejestratora nazw domen można zarządzać dzierżawą usługi Azure AD.

Po wykonaniu powyższych kroków jesteś teraz globalnym administratorem dzierżawy czwartej kawy w usłudze Office 365. Aby zintegrować nazwę domeny z innymi usługami platformy Azure, można ją usunąć z usługi Office 365 i dodać do innej dzierżawy zarządzanej na platformie Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Dodawanie nazwy domeny do dzierżawy zarządzanej w usłudze Azure AD

1. Otwórz [centrum administracyjne usługi Microsoft 365](https://admin.microsoft.com).
2. Wybierz kartę **Użytkownicy** i utwórz nowe konto użytkownika o nazwie takiej jak *fourthcoffeexyz.onmicrosoft.com\@użytkownika,* która nie używa niestandardowej nazwy domeny. 
3. Upewnij się, że nowe konto użytkownika ma uprawnienia administratora globalnego dla dzierżawy usługi Azure AD.
4. Karta **Otwórz domeny** w centrum administracyjnym usługi Microsoft 365, wybierz nazwę domeny i wybierz pozycję **Usuń**. 
  
   ![usuwanie nazwy domeny z usługi Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Jeśli w usłudze Office 365 są użytkownicy lub grupy, które odwołują się do usuniętej nazwy domeny, należy zmienić ich nazwę na domenę .onmicrosoft.com. Jeśli wymusisz usunięcie nazwy domeny, wszyscy użytkownicy zostaną automatycznie zmienieni, w tym przykładzie *użytkownik\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy za pomocą konta, które jest administratorem globalnym dzierżawy usługi Azure AD.
  
7. Wybierz **pozycję Niestandardowe nazwy domen**, a następnie dodaj nazwę domeny. Aby zweryfikować własność nazwy domeny, musisz wprowadzić rekordy DNS TXT. 
  
   ![domena zweryfikowana jako dodana do usługi Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Użytkownicy usługi Power BI lub Usługi Azure Rights Management, którzy mają licencje przypisane do dzierżawy usługi Office 365, muszą zapisać swoje pulpity nawigacyjne, jeśli nazwa domeny zostanie usunięta. Muszą zalogować się przy przy fourthcoffeexyz.onmicrosoft.com, *\@a* nie *\@użytkownik fourthcoffee.xyz.*

## <a name="external-admin-takeover"></a>Przejęcie zewnętrznego administratora

Jeśli zarządzasz już dzierżawą za pomocą usług platformy Azure lub usługi Office 365, nie można dodać niestandardowej nazwy domeny, jeśli jest już zweryfikowana w innej dzierżawie usługi Azure AD. Jednak z dzierżawy zarządzanej w usłudze Azure AD można przejąć dzierżawy niezarządzanej jako przejęcie administratora zewnętrznego. Ogólna procedura jest następująca poniżej artykułu [Dodawanie domeny niestandardowej do usługi Azure AD](../fundamentals/add-custom-domain.md).

Po zweryfikowaniu własności nazwy domeny usługa Azure AD usuwa nazwę domeny z dzierżawy niezarządzanej i przenosi ją do istniejącej dzierżawy. Przejęcie katalogu niezarządzanego przez administratora zewnętrznego wymaga tego samego procesu sprawdzania poprawności DNS TXT, co wewnętrzne przejęcie administratora. Różnica polega na tym, że następujące są również przenoszone z nazwą domeny:

- Użytkownicy
- Subskrypcje
- Przypisania licencji

### <a name="support-for-external-admin-takeover"></a>Wsparcie dla przejęcia zewnętrznego administratora
Przejęcie przez administratora zewnętrznego jest obsługiwane przez następujące usługi online:

- Azure Rights Management
- Exchange Online

Obsługiwane plany usług obejmują:

- Usługa PowerApps za darmo
- Bez przepływu energii
- Usługi RMS dla użytkowników indywidualnych
- Usługa Microsoft Stream
- Bezpłatna wersja próbna Dynamics 365

Przejęcie administratora zewnętrznego nie jest obsługiwane w przypadku żadnej usługi, która ma plany usług obejmujące program SharePoint, OneDrive lub Skype dla firm; na przykład za pośrednictwem bezpłatnej subskrypcji pakietu Office. 

Opcjonalnie można użyć [ **opcji ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) do usuwania nazwy domeny z dzierżawy niezarządzanej i weryfikowania jej w żądanej dzierżawie. 

#### <a name="more-information-about-rms-for-individuals"></a>Więcej informacji o RMS dla osób fizycznych

W przypadku [programu RMS dla osób fizycznych,](/azure/information-protection/rms-for-individuals)gdy dzierżawa niezarządzana znajduje się w tym samym regionie co dzierżawa, której jesteś właścicielem, automatycznie utworzony [klucz dzierżawy usługi Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) i [domyślne szablony ochrony](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) są dodatkowo przenoszone z nazwą domeny.

Klucz i szablony nie są przenoszone, gdy dzierżawa niezarządzana znajduje się w innym regionie. Na przykład jeśli dzierżawa niezarządzana znajduje się w Europie, a organizacja, której jesteś właścicielem, znajduje się w Ameryce Północnej.

Chociaż program RMS dla osób fizycznych jest przeznaczony do obsługi uwierzytelniania usługi Azure AD w celu otwarcia chronionej zawartości, nie uniemożliwia użytkownikom również ochrony zawartości. Jeśli użytkownicy chronili zawartość za pomocą usługi RMS dla osób prywatnych, a klucz i szablony nie zostały przeniesione, ta zawartość nie jest dostępna po przejęciu domeny.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Polecenia cmdlet programu Azure AD PowerShell dla opcji ForceTakeover
Te polecenia cmdlet używane w [przykładzie programu PowerShell](#powershell-example)są widoczne.

cmdlet | Sposób użycia
------- | -------
`connect-msolservice` | Po wyświetleniu monitu zaloguj się do zarządzanej dzierżawy.
`get-msoldomain` | Pokazuje nazwy domen skojarzone z bieżącą dzierżawą.
`new-msoldomain –name <domainname>` | Dodaje nazwę domeny do dzierżawy jako niezweryfikowaną (nie przeprowadzono jeszcze weryfikacji DNS).
`get-msoldomain` | Nazwa domeny znajduje się teraz na liście nazw domen skojarzonych z zarządzana dzierżawą, ale jest wyświetlana jako **Niezweryfikowana**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Zawiera informacje, które należy umieścić w nowym rekordzie DNS TXT dla domeny (MS=xxxxx). Weryfikacja może nie nastąpić natychmiast, ponieważ propagowanie rekordu TXT zajmuje trochę czasu, więc poczekaj kilka minut, zanim rozważymy opcję **-ForceTakeover.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Jeśli nazwa domeny nadal nie jest zweryfikowana, możesz przejść do opcji **-ForceTakeover.** Sprawdza, czy rekord TXT został utworzony i rozpoczyna proces przejęcia.<li>Opcja **-ForceTakeover** powinna zostać dodana do polecenia cmdlet tylko wtedy, gdy wymusza przejęcie zewnętrznego administratora, na przykład gdy dzierżawa niezarządzana ma usługi Office 365 blokujące przejęcie.
`get-msoldomain` | Lista domen pokazuje teraz nazwę domeny jako **Zweryfikowana**.

> [!NOTE]
> Niezarządzana organizacja usługi Azure AD jest usuwana 10 dni po wykonaniu opcji zewnętrznych sił przejęcia.

### <a name="powershell-example"></a>Przykład programu PowerShell

1. Połącz się z usługą Azure AD przy użyciu poświadczeń, które były używane do odpowiadania na ofertę samoobsługi:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Pobierz listę domen:
  
   ```powershell
   Get-MsolDomain
   ```
3. Uruchom polecenie cmdlet Get-MsolDomainVerificationDns, aby utworzyć wyzwanie:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Przykład:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Skopiuj wartość (wyzwanie), która jest zwracana z tego polecenia. Przykład:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. W publicznej przestrzeni nazw DNS utwórz rekord DNS txt zawierający wartość skopiowaną w poprzednim kroku. Nazwa tego rekordu jest nazwą domeny nadrzędnej, więc jeśli rekord tego zasobu zostanie utworzony przy użyciu roli DNS z systemu Windows Server, pozostaw nazwę rekordu pustą i po prostu wklej tę wartość do pola Tekstowa.
6. Uruchom polecenie cmdlet Confirm-MsolDomain, aby zweryfikować wyzwanie:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Przykład:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Pomyślne wyzwanie powraca do monitu bez błędu.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
