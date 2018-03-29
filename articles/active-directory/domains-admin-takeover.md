---
title: Przejęcia administratora dzierżawy cienia w usłudze Azure Active Directory lub katalogu niezarządzane | Dokumentacja firmy Microsoft
description: Jak przejąć nazwy domeny DNS w katalogu niezarządzane (tle dzierżawcy) w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 16f5c515231f486e3576b95a0d103d2fa34842ff
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Przejąć niezarządzane katalogu jako administrator w usłudze Azure Active Directory
W tym artykule opisano dwa sposoby przejąć nazwy domeny DNS w niezarządzanych katalogu w usłudze Azure Active Directory (Azure AD). Gdy użytkownik samoobsługi tworzy konto dla usługi w chmurze, która używa usługi Azure AD, są dodawane do niezarządzanego Azure AD directory oparte na ich domenę poczty e-mail. Aby uzyskać więcej informacji o samoobsługi lub signup "wirusa" dla usługi, zobacz [co to jest subskrypcji samoobsługowej usługi Azure Active Directory?]()

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Zdecyduj, jak chcesz przejąć niezarządzane katalogu
W trakcie przejęcia administratora mogą udowodnić własność, zgodnie z opisem w [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](add-custom-domain.md). W kolejnych sekcjach wyjaśniono środowisko pracy administratora bardziej szczegółowo, ale w tym miejscu znajduje się podsumowanie:

* Po wykonaniu [przejęcia admin "internal"](#internal-admin-takeover) niezarządzane katalogu platformy Azure, są dodawane jako administrator globalny katalogu niezarządzane. Nie użytkowników, domeny i planów usługi są migrowane do innego katalogu, którą możesz administrować.

* Po wykonaniu [przejęcia admin "external"](#external-admin-takeover) niezarządzane katalogu platformy Azure, Dodaj nazwę domeny DNS niezarządzane katalogu do katalogu Azure zarządzanych. Po dodaniu nazwy domeny, mapowanie użytkowników do zasobów jest tworzony w katalogu Azure zarządzanych, dzięki czemu użytkownicy mogą nadal uzyskiwać dostęp do usług bez przeszkód. 

## <a name="internal-admin-takeover"></a>Wewnętrznie administrator przejęcia

Niektórych produktów, które obejmują OneDrive, takich jak Office 365 i SharePoint nie obsługują przejęcia zewnętrznych. Czy jest scenariusz, czy są administratorem i chcesz przejąć niezarządzanych lub utworzyć "cienia" dzierżawy przez użytkowników, którzy korzystali samoobsługowego tworzenia konta, można to zrobić z przejęcia wewnętrznego administratora.

1. Tworzenie kontekstu użytkownika w niezarządzanej dzierżawy za pośrednictwem rejestracji z takich jak usługi Power BI. Dla wygody przykładowy tych krokach przyjęto założenie tej ścieżki.

2. Otwórz [witryny usługi Power BI](https://powerbi.com) i wybierz **Start wolnego**. Wprowadź konto użytkownika, który używa nazwy domeny dla organizacji; na przykład `admin@fourthcoffee.xyz`. Po wprowadzeniu w kod weryfikacyjny, Sprawdź pocztę na kod potwierdzenia.

3. W wiadomości e-mail z usługi Power BI, wybierz **tak, to ja**.

4. Zaloguj się do [Centrum administracyjne usługi Office 365](https://portal.office.com/adminportal/Home) przy użyciu konta użytkownika usługi Power BI. Zostanie wyświetlony komunikat zalecający **stają się administrator** nazwy domeny, która została już zweryfikowana w niezarządzanej dzierżawy. Wybierz **tak, chcę mieć administrator**.
  
  ![pierwszy zrzut ekranu przedstawiający Become administratora](./media/domains-admin-takeover/become-admin-first.png)
  
5. Dodaj rekord TXT w celu potwierdzenia, że własnej nazwy domeny **fourthcoffee.xyz** u rejestratora nazw domen. W tym przykładzie jest GoDaddy.com.
  
  ![Dodaj rekord txt dla nazwy domeny](./media/domains-admin-takeover/become-admin-txt-record.png)

Po zweryfikowaniu rekordów TXT usługi DNS u rejestratora nazw domen można zarządzać dzierżawy usługi Azure AD.

Po ukończeniu powyższych kroków jesteś teraz administratorem globalnym dzierżawcy Fourth Coffee w usłudze Office 365. Aby zintegrować nazwy domeny z innymi usługami Azure, możesz usunąć go z usługi Office 365 i dodaj go do innej dzierżawy zarządzanego na platformie Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Dodawanie nazwy domeny do zarządzanego dzierżawcy w usłudze Azure AD 

1. Otwórz [Centrum administracyjne usługi Office 365](https://portal.office.com/adminportal/Home).
2. Wybierz **użytkowników** , a następnie utwórz nowe konto użytkownika z nazwą jak *user@fourthcoffeexyz.onmicrosoft.com* nie używa nazwy domeny niestandardowej. 
3. Upewnij się, że nowe konto użytkownika ma uprawnienia administratora globalnego dla dzierżawy usługi Azure AD.
4. Otwórz **domen** w Centrum administracyjne usługi Office 365, wybierz nazwę domeny i wybierz **Usuń**. 
  
  ![Usuń nazwę domeny z usługi Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Jeśli masz jakichkolwiek użytkowników lub grup w usłudze Office 365, odwołujące się do nazwy domeny usunięty, musi zostać zmieniona na. onmicrosoft.com domeny. Jeśli wymusisz usunąć nazwy domeny, wszyscy użytkownicy są automatycznie zmieniona jego nazwa, w tym przykładzie do *user@fourthcoffeexyz.onmicrosoft.com*.
  
6. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, które jest administratorem globalnym dla dzierżawy usługi Azure AD.
  
7. Wybierz **niestandardowych nazw domen**, następnie dodać nazwy domeny. Będzie konieczne podanie rekordów TXT usługi DNS, aby zweryfikować własność nazwy domeny. 
  
  ![domeny dodane do usługi Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Wszyscy użytkownicy usługi Power BI lub Azure Rights Management, którzy mają licencje przypisane w dzierżawie usługi Office 365 należy zapisać swoje pulpity nawigacyjne, jeśli nazwa domeny została usunięta. Muszą one Zaloguj się przy użyciu nazwy użytkownika, takich jak *user@fourthcoffeexyz.onmicrosoft.com* zamiast *user@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Przejęcia zewnętrznego administratora

Jeśli zarządzasz już dzierżawy z usług Azure lub Office 365, nie można dodać niestandardową nazwę domeny, jeśli jest on już zweryfikowana w innej dzierżawy usługi Azure AD. Jednak dzierżawy zarządzane w usłudze Azure AD można wykonywać za pośrednictwem niezarządzanej dzierżawy jako przejęcia zewnętrznego administratora. Ogólna procedura następuje artykuł [dodać niestandardową domenę do usługi Azure AD](add-custom-domain.md).

Podczas weryfikowania własności nazwy domeny usługi Azure AD usuwa nazwy domen z niezarządzanej dzierżawy i przenosi ją do istniejącej dzierżawy. Przejęcia zewnętrznego administratora katalogu niezarządzane wymaga tego samego procesu weryfikacji DNS TXT jako przejęcia wewnętrznego administratora. Różnica polega na tym, że następujące są również przenoszony z nazwą domeny:

- Użytkownicy
- Subskrypcje
- Przypisań licencji
 
[ **ForceTakeover** opcji](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) dla zewnętrznego administratora nazwa domeny przejęcia jest obsługiwana w przypadku tylko dwie usługi Power BI i Azure RMS.

### <a name="support-for-external-admin-takeover"></a>Obsługa przejęcia zewnętrznego administratora
Przejęcia zewnętrznego administratora jest obsługiwany przez następujących usług online:

- Power BI
- Usługa Azure Rights Management (RMS)
- Exchange Online

Plany usługi obsługiwane obejmują:

- Bezpłatne usługi Power BI
- Power BI Pro
- Rozwiązanie PowerApps w warstwie bezpłatna
- PowerFlow bezpłatna
- Usługi Azure Rights Management Service Basic (RMS)
- Przedsiębiorstwa usługi Azure Rights Management (RMS)
- Microsoft Stream
- Dynamics 365 bezpłatnej wersji próbnej

Exernal przejęcia admin nie jest obsługiwane dla żadnej usługi, która ma planów usług, które obejmują programu SharePoint, usłudze OneDrive lub Skype dla firm; na przykład za pomocą bezpłatnej subskrypcji pakietu Office lub podstawowy SKU pakietu Office.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Polecenia cmdlet systemu Azure AD PowerShell opcji ForceTakeover
Można wyświetlić te polecenia cmdlet używane w [przykładzie programu PowerShell](#powershell-example).


Polecenia cmdlet | Sposób użycia 
------- | -------
`connect-msolservice` | Po wyświetleniu monitu zaloguj się do dzierżawy zarządzanego.
`get-msoldomain` | Pokazuje nazwy domeny skojarzony z bieżącą dzierżawą.
`new-msoldomain –name <domainname>` | Dodaje nazwę domeny do dzierżawy jako Unverified (nie DNS weryfikacji ma zostać wykonane jeszcze).
`get-msoldomain` | Nazwa domeny jest teraz zawarta w listę nazw domeny skojarzony z dzierżawą zarządzanych, ale jest wymienione jako **Unverified**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Informacje w celu uruchomienia nowego rekordu TXT usługi DNS dla domeny (MS = xxxxx). Weryfikacja może nie wystąpić natychmiast ponieważ dopiero po pewnym czasie dla rekordu TXT propagację, więc czekać kilka minut przed uwzględnieniu **- ForceTakeover** opcji. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Jeśli nadal nazwy domeny nie została zweryfikowana, możesz przejść z **- ForceTakeover** opcji. Sprawdza, czy rekord TXT został utworzony i rozpoczyna proces przejęcia.<li>**- ForceTakeover** opcji powinny zostać dodane do polecenia cmdlet, tylko wtedy, gdy wymuszania przejęcia zewnętrznego administratora, na przykład w przypadku niezarządzanej dzierżawy usługi Office 365 blokuje przejęcia.
`get-msoldomain` | Pojawi się na liście domeny nazwę domeny, jak **zweryfikowano**.

### <a name="powershell-example"></a>Przykład środowiska PowerShell

1. Połączenie z usługą Azure AD za pomocą poświadczeń użytych do odpowiedzi z ofertą samoobsługi:
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Pobierz listę domen:
  
  ````
    Get-MsolDomain
  ````
3. Uruchom polecenie cmdlet Get-MsolDomainVerificationDns, aby utworzyć żądanie:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Skopiuj wartość (żądanie), która jest zwracana z tego polecenia. Na przykład:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. W sieci publicznej przestrzeni nazw DNS należy utworzyć rekord txt DNS, zawierający wartość skopiowanego w poprzednim kroku. Nazwa dla tego rekordu jest nazwa domeny nadrzędnej, więc jeśli utworzysz ten rekord zasobu za pomocą roli DNS z systemem Windows Server, pozostaw Wklej puste, a tylko nazwy rekordu wartość w polu tekstowym.
6. Uruchom polecenie cmdlet Potwierdź MsolDomain można zweryfikować żądania:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Na przykład:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

Żądanie powiodło się zwraca do wiersza polecenia bez błędów.

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](add-custom-domain.md)
* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
