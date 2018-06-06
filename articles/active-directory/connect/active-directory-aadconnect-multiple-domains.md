---
title: Azure AD Connect wielu domen
description: W tym dokumencie opisano instalowanie i konfigurowanie wielu domen najwyższego poziomu z usługi Office 365 i Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2f596f64041b3d429b99db482cd635ab441bf468
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801511"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Obsługa wielu domen do federowania w usłudze Azure AD
Poniższa dokumentacja zawiera wskazówki dotyczące sposobu używania wielu najwyższego poziomu domen i poddomen podczas federowania w usłudze Office 365 lub domen usługi Azure AD.

## <a name="multiple-top-level-domain-support"></a>Obsługa wielu domen najwyższego poziomu
Federowanie wielu, domeny najwyższego poziomu z usługą Azure AD wymaga dodatkowego skonfigurowania, który nie jest wymagany, gdy federowania z jednej domeny najwyższego poziomu.

Gdy domeny jest Sfederowane przy użyciu usługi Azure AD, kilka właściwości są ustawiane w domenie na platformie Azure.  Jeden z nich ważne jest IssuerUri.  Ta właściwość jest identyfikatorem URI, który jest używany przez usługę Azure AD, aby zidentyfikować domenę, w której token jest skojarzony z.  Identyfikator URI nie musi rozpoznać cokolwiek, ale musi być prawidłowym identyfikatorem URI.  Domyślnie usługi Azure AD Ustawia identyfikator URI do wartości Identyfikator usługi federacyjnej w lokalnej usługi AD FS konfiguracji.

> [!NOTE]
> Identyfikator usługi federacyjnej jest identyfikatorem URI, który unikatowo identyfikuje usługi federacyjnej.  Usługi federacyjnej jest wystąpieniem programu AD FS, która działa jako usługę tokenu zabezpieczającego.
>
>

IssuerUri można wyświetlić za pomocą polecenia programu PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Pojawi się problem podczas dodawania więcej niż jednej domeny najwyższego poziomu.  Na przykład, załóżmy, że po skonfigurowaniu federacji między usługą Azure AD oraz w lokalnym środowisku.  Dla tego dokumentu, domeny, bmcontoso.com jest używany.  Teraz domeny drugiego, najwyższego poziomu, bmfabrikam.com został dodany.

![Domeny](./media/active-directory-multiple-domains/domains.png)

Podczas próby konwersji domeny bmfabrikam.com federacyjną, występuje błąd.  Dzieje się tak, usługa Azure AD ma ograniczenie, które nie zezwala na właściwość IssuerUri mają taką samą wartość dla więcej niż jedną domenę.  

![Błąd federacyjnego](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parametr SupportMultipleDomain
Aby obejść to ograniczenie, należy dodać inny IssuerUri, można to zrobić za pomocą `-SupportMultipleDomain` parametru.  Ten parametr jest używany z następujących poleceń cmdlet:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ten parametr umożliwia usługi Azure AD, skonfiguruj IssuerUri, dzięki czemu jest on oparty na nazwę domeny.  IssuerUri będzie unikatowy dla katalogów w usłudze Azure AD.  Za pomocą parametru umożliwia polecenia programu PowerShell zakończyć się pomyślnie.

![Błąd federacyjnego](./media/active-directory-multiple-domains/convert.png)

Patrzeć ustawienia domeny bmfabrikam.com zobacz następujące tematy:

![Błąd federacyjnego](./media/active-directory-multiple-domains/settings.png)

`-SupportMultipleDomain` nie powoduje zmiany innych punktów końcowych, które są nadal skonfigurowane do punktu w usłudze federacyjnej na adfs.bmcontoso.com.

Innym czynnikiem który `-SupportMultipleDomain` jest jest ona sprawdza, czy system usług AD FS zawiera poprawną wartość wystawca w tokenów wystawionych dla usługi Azure AD. Ta wartość jest ustawiana przez pobranie część nazwy UPN użytkowników domeny i ustawienie go jako domeny w IssuerUri, tj. sufiks https://{upn} / adfs/services/zaufania.

W związku z tym podczas uwierzytelniania do usługi Azure AD lub usługi Office 365, element IssuerUri token użytkownika jest używana do lokalizowania domeny w usłudze Azure AD.  Jeśli nie można odnaleźć dopasowania, uwierzytelnianie nie powiedzie się.

Na przykład, jeśli nazwy UPN użytkownika jest bsimon@bmcontoso.com, element IssuerUri w tokenie, problemy z usług AD FS, zostanie ustawiona do http://bmcontoso.com/adfs/services/trust. Ten element będzie odpowiadała konfiguracji usługi Azure AD i uwierzytelniania powiedzie się.

Poniżej przedstawiono reguły niestandardowe oświadczeń, który implementuje tę logikę:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Aby użyć przełącznika - SupportMultipleDomain podczas próby umożliwiają dodanie nowych lub przekonwertować istniejącej domeny, z zaufaniem federacyjnym musi zostały już skonfigurowane do ich obsługi.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Jak zaktualizować relacji zaufania między usługami AD FS a usługą Azure AD
Federacyjnych relacji zaufania między usługami AD FS a wystąpieniem usługi Azure AD nie zostało skonfigurowane, może być konieczne ponowne tworzenie tego zaufania.  Przyczyną jest początkowo jest skonfigurowana bez `-SupportMultipleDomain` parametru IssuerUri została skonfigurowana z wartością domyślną.  Na poniższym zrzucie ekranu, można zobaczyć IssuerUri ma ustawioną wartość https://adfs.bmcontoso.com/adfs/services/trust.

Jeśli pomyślnie dodano nową domenę w portalu usługi Azure AD, a następnie spróbuj przekonwertować ją przy użyciu `Convert-MsolDomaintoFederated -DomainName <your domain>`, wystąpi następujący błąd.

![Błąd federacyjnego](./media/active-directory-multiple-domains/trust1.png)

Jeśli próbujesz dodać `-SupportMultipleDomain` przełącznika, zostanie wyświetlony następujący błąd:

![Błąd federacyjnego](./media/active-directory-multiple-domains/trust2.png)

Po prostu próby uruchomienia `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` w domenie oryginalnego również spowoduje błąd.

![Błąd federacyjnego](./media/active-directory-multiple-domains/trust3.png)

Skorzystaj z poniższych czynności, aby dodać dodatkowe domeny najwyższego poziomu.  Jeśli dodano już domeny, a nie użyto `-SupportMultipleDomain` parametru start prostych kroków, usuwania i aktualizowania domenę oryginalnego.  Jeśli nie dodano jeszcze domeny najwyższego poziomu, można uruchomić z poziomu procedury dodawania domeny przy użyciu programu PowerShell Azure AD Connect.

Wykonaj następujące kroki, aby usunąć zaufanie Online firmy Microsoft i zaktualizować domenę oryginalnego.

1. Na serwerze federacyjnym usług AD FS Otwórz **Zarządzanie usługami AD FS.**
2. Po lewej stronie rozwiń **relacje zaufania** i **zaufania jednostek uzależnionych**
3. Po prawej stronie, należy usunąć **Microsoft Office 365 tożsamość platformy** wpisu.
   ![Usuń Online firmy Microsoft](./media/active-directory-multiple-domains/trust4.png)
4. Na komputerze, na którym ma [Azure Active Directory modułu dla środowiska Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) zainstalowanym uruchom następujące polecenie: `$cred=Get-Credential`.  
5. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, które są federowania w usłudze.
6. W programie PowerShell wpisz: `Connect-MsolService -Credential $cred`
7. W programie PowerShell, wprowadź `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Ta aktualizacja jest oryginalnym domeny.  Przy użyciu powyższej domeny, które będzie:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Wykonaj następujące kroki, aby dodać nową domenę najwyższego poziomu przy użyciu programu PowerShell

1. Na komputerze, na którym ma [Azure Active Directory modułu dla środowiska Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) zainstalowanym uruchom następujące polecenie: `$cred=Get-Credential`.  
2. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, które są federowania w usłudze
3. W programie PowerShell wpisz: `Connect-MsolService -Credential $cred`
4. W programie PowerShell wpisz: `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Wykonaj następujące kroki, aby dodać nową domenę najwyższego poziomu za pomocą usługi Azure AD Connect.

1. Uruchamianie usługi Azure AD Connect z pulpitu lub menu start
2. Wybierz opcję "Dodaj dodatkowe domenowych Azure AD" ![Dodawanie kolejnej domeny usługi Azure AD](./media/active-directory-multiple-domains/add1.png)
3. Wprowadź usługi Azure AD i poświadczeń usługi Active Directory
4. Wybierz drugiej domeny, który chcesz skonfigurować na potrzeby federacji.
   ![Dodawanie kolejnej domeny usługi Azure AD](./media/active-directory-multiple-domains/add2.png)
5. Kliknięcie pozycji Zainstaluj

### <a name="verify-the-new-top-level-domain"></a>Sprawdź nowej domeny najwyższego poziomu
Za pomocą polecenia programu PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`można wyświetlić zaktualizowane IssuerUri.  Poniższy zrzut ekranu przedstawia Federacji ustawienia zostały zaktualizowane w pierwotnej domeny http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Ustawiono IssuerUri dla nowej domeny https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Obsługa poddomeny
Po dodaniu poddomeny, ze względu na sposób Azure AD obsługi domen odziedziczy ustawienia elementu nadrzędnego.  Tak IssuerUri, musi być zgodna elementów nadrzędnych.

Umożliwia tak, że, na przykład, I mieć bmcontoso.com i następnie dodać corp.bmcontoso.com.  Trzeba będzie IssuerUri dla użytkownika z corp.bmcontoso.com  **http://bmcontoso.com/adfs/services/trust.**  Jednak standardowa zasada zaimplementowana powyżej dla usługi Azure AD wygeneruje token z wystawcą jako  **http://corp.bmcontoso.com/adfs/services/trust.** nie będzie odpowiadała domeny wymagane wartości, a uwierzytelnianie zakończy się niepowodzeniem.

### <a name="how-to-enable-support-for-subdomains"></a>Jak włączyć obsługę poddomeny
Aby obejść ten problem, usług AD FS dla witryny Microsoft Online zaufanie jednostki uzależnionej musi zostać zaktualizowany.  Aby to zrobić, należy skonfigurować niestandardowej reguły oświadczeń tak, aby taśmy poza poddomen z sufiks nazwy UPN użytkownika podczas tworzenia niestandardowej wartości wystawcy.

Następujące oświadczenie będzie wykonaj następujące czynności:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Ostatni numer w zestawie wyrażenie regularne jest ile domeny nadrzędnej są w domenie katalogu głównego. W tym miejscu bmcontoso.com jest używana, więc niezbędne są dwie domeny nadrzędnej. Jeśli trzy nadrzędnego domeny zostały mają być przechowywane (tj.: corp.bmcontoso.com), następnie numer byłby trzy. Po pewnym czasie zakresu mogą być wskazywane, zawsze będą dopasowania do dopasowania maksymalną liczbę domen. "{2,3}" będzie odpowiadała dwóch do trzech domenach (np.: bmfabrikam.com i corp.bmcontoso.com).

Wykonaj następujące kroki, aby dodać niestandardowe oświadczenia do obsługi poddomeny.

1. Zarządzanie usługami Otwórz AD FS
2. Kliknij prawym przyciskiem myszy zaufania Microsoft Online planu odzyskiwania i wybierz polecenie reguły Edycja oświadczeń
3. Wybierz trzecie reguły oświadczeń i Zastąp ![Edycja oświadczeń](./media/active-directory-multiple-domains/sub1.png)
4. Zastąp bieżący oświadczeń:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Zastąp oświadczeń](./media/active-directory-multiple-domains/sub2.png)

5. Kliknij przycisk Ok.  Kliknij przycisk Zastosuj.  Kliknij przycisk Ok.  Zamknij przystawkę zarządzania usługami AD FS.

## <a name="next-steps"></a>Kolejne kroki
Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](active-directory-aadconnect-whats-next.md).

Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md), [Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](active-directory-aadconnectsync-feature-scheduler.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).