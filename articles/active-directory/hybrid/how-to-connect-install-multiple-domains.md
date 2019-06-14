---
title: Azure AD Connect wielu domen
description: W tym dokumencie opisano instalowanie i konfigurowanie wiele domen najwyższego poziomu za pomocą usługi O365 i Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e822906a072ec8244c7108e98289482adebb5a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60245120"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Obsługa wielu domen do federowania w usłudze Azure AD
Poniższa dokumentacja zawiera wskazówki dotyczące sposobu używania wiele domen najwyższego poziomu i poddomeny, gdy federowania w usłudze Office 365 lub domeny usługi Azure AD.

## <a name="multiple-top-level-domain-support"></a>Obsługa wielu domen najwyższego poziomu
Federowanie wielu, domen najwyższego poziomu z usługą Azure AD wymaga dodatkowej konfiguracji, które nie są wymagane, gdy federowania z jednej domeny najwyższego poziomu.

Gdy domena jest Sfederowane z usługą Azure AD, kilka właściwości są ustawiane w domenie na platformie Azure.  Jeden z nich ważne jest IssuerUri.  Ta właściwość jest identyfikator URI, który jest używany przez usługę Azure AD do identyfikowania domeny skojarzonego z tokenem.  Identyfikator URI nie musi prowadzić do wszystko, ale musi to być prawidłowy identyfikator URI.  Domyślnie usługa Azure AD Ustawia identyfikator URI do wartości Identyfikator usługi federacyjnej w lokalnych usług AD FS konfiguracji.

> [!NOTE]
> Identyfikator usługi federacyjnej jest identyfikator URI, który unikatowo identyfikuje usługi federacyjnej.  Usługa federacyjna jest wystąpieniem usług AD FS, która działa jako usługa tokenu zabezpieczającego.
>
>

IssuerUri można wyświetlić za pomocą polecenia programu PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Problem pojawia się po dodaniu więcej niż jednej domeny najwyższego poziomu.  Na przykład, załóżmy, że po skonfigurowaniu federacji między usługami Azure AD i usługi w środowisku lokalnym.  Dla tego dokumentu, domeny, bmcontoso.com jest używana.  Teraz domeny drugi, najwyższego poziomu, bmfabrikam.com został dodany.

![Domeny](./media/how-to-connect-install-multiple-domains/domains.png)

Gdy użytkownik spróbuje przekonwertować bmfabrikam.com domenę do sfederowania, wystąpi błąd.  Przyczyną jest to, usługa Azure AD ma ograniczenie, które nie zezwala na właściwość IssuerUri mają taką samą wartość, aby uzyskać więcej niż jedną domenę.  

![Błąd Federacji](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain Parameter
Aby obejść to ograniczenie, należy dodać inny IssuerUri, co można zrobić za pomocą `-SupportMultipleDomain` parametru.  Ten parametr jest używany przy użyciu następujących poleceń cmdlet:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ten parametr sprawia, że usługa Azure AD, skonfiguruj IssuerUri opiera się na nazwę domeny.  IssuerUri jest unikatowa w katalogów w usłudze Azure AD.  Za pomocą parametru umożliwia polecenia programu PowerShell, która powinna zakończyć się powodzeniem.

![Błąd Federacji](./media/how-to-connect-install-multiple-domains/convert.png)

Patrząc ustawienia domeny bmfabrikam.com zobacz następujące tematy:

![Błąd Federacji](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` nie powoduje zmiany innych punktów końcowych, które nadal są skonfigurowane do punktu w usłudze federacyjnej na adfs.bmcontoso.com.

Inną rzeczą, `-SupportMultipleDomain` jest jest ona sprawdza, czy system usług AD FS zawiera poprawną wartość wystawcy w tokeny wystawione dla usługi Azure AD. Ta wartość jest ustawiana przez pobranie część nazwy UPN użytkowników domeny i ustawienie go jako domena w IssuerUri, czyli https://{upn sufiks} / adfs/services/zaufania.

Dlatego podczas uwierzytelniania w usłudze Azure AD lub Office 365, element IssuerUri token użytkownika jest używana do lokalizowania domeny w usłudze Azure AD.  Jeśli nie zostanie znalezione dopasowanie, uwierzytelnianie nie powiedzie się.

Na przykład, jeśli nazwy UPN użytkownika jest bsimon@bmcontoso.com, elementu IssuerUri w tokenie, problemy z usług AD FS, zostanie ustawiony na <http://bmcontoso.com/adfs/services/trust>. Ten element będzie zgodna z konfiguracją usługi Azure AD i uwierzytelnienie zakończy się powodzeniem.

Reguła oświadczenia niestandardowe, który implementuje tę logikę jest następująca:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Aby użyć przełącznika - SupportMultipleDomain, podczas próby, można dodać nowe lub przekonwertować istniejącej domeny, usługi federacyjnej relacji zaufania musi już skonfigurowano do ich obsługi.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Jak zaktualizować relację zaufania między usług AD FS a usługą Azure AD
Federacyjnych relacji zaufania między wystąpieniem usługi Azure AD i usług AD FS nie zostało skonfigurowane, może być konieczne odtworzenie tego zaufania.  Przyczyną jest początkowo jest skonfigurowana bez `-SupportMultipleDomain` , IssuerUri ustawionej wartości parametru z wartością domyślną.  Na poniższym zrzucie ekranu widać, IssuerUri jest ustawiona na https://adfs.bmcontoso.com/adfs/services/trust.

Jeśli pomyślnie dodano nową domenę w portalu usługi Azure AD, a następnie spróbuj przekonwertować ją za pomocą `Convert-MsolDomaintoFederated -DomainName <your domain>`, zostanie wyświetlony następujący błąd.

![Błąd Federacji](./media/how-to-connect-install-multiple-domains/trust1.png)

Jeśli użytkownik próbuje dodać `-SupportMultipleDomain` przełączyć się, zostanie wyświetlony następujący błąd:

![Błąd Federacji](./media/how-to-connect-install-multiple-domains/trust2.png)

Po prostu próby uruchomienia `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` na oryginalną domenę spowoduje wystąpienie błędu.

![Błąd Federacji](./media/how-to-connect-install-multiple-domains/trust3.png)

Użyj poniższe kroki, aby dodać dodatkowe domeny najwyższego poziomu.  Jeśli dodano już domeny i nie użyto `-SupportMultipleDomain` parametru, Rozpocznij proces usuwania i aktualizowania domenę oryginalnego.  Jeśli nie dodano jeszcze domeny najwyższego poziomu, można uruchomić za pomocą kroki, aby dodać domenę przy użyciu programu PowerShell programu Azure AD Connect.

Wykonaj następujące kroki, aby usunąć zaufanie Online firmy Microsoft i zaktualizować domenę oryginalnego.

1. Na serwerze federacyjnym usług AD FS Otwórz **zarządzania usługami AD FS.**
2. Po lewej stronie, rozwiń węzeł **relacje zaufania** i **zaufania jednostek uzależnionych**
3. Po prawej stronie, Usuń **platforma tożsamości usługi Microsoft Office 365** wpisu.
   ![Usuń Online firmy Microsoft](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Na komputerze, na którym [Azure Active Directory Module for Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) zainstalowanych na nim uruchom następujące polecenie: `$cred=Get-Credential`.  
5. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, które są federowania w usłudze.
6. W programie PowerShell wpisz: `Connect-MsolService -Credential $cred`
7. W programie PowerShell, należy wprowadzić `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Ta aktualizacja dotyczy oryginalną domenę.  Przy użyciu powyższej domeny, w których będzie:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Wykonaj następujące kroki, aby dodać nową domenę najwyższego poziomu przy użyciu programu PowerShell

1. Na komputerze, na którym [Azure Active Directory Module for Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) zainstalowanych na nim uruchom następujące polecenie: `$cred=Get-Credential`.  
2. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, które są federowania w usłudze
3. W programie PowerShell wpisz: `Connect-MsolService -Credential $cred`
4. W programie PowerShell wpisz: `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Wykonaj następujące kroki, aby dodać nową domenę najwyższego poziomu za pomocą usługi Azure AD Connect.

1. Uruchom program Azure AD Connect z pulpitu lub menu start
2. Wybieranie pozycji "Dodaj dodatkowe domenowych Azure AD" ![dodać kolejny domeny usługi Azure AD](./media/how-to-connect-install-multiple-domains/add1.png)
3. Wprowadź usługi Azure AD i poświadczeń usługi Active Directory
4. Wybierz drugą domenę, którą chcesz skonfigurować na potrzeby federacji.
   ![Dodaj dodatkowe domeny usługi Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kliknięcie pozycji Zainstaluj

### <a name="verify-the-new-top-level-domain"></a>Sprawdź nowe domeny najwyższego poziomu
Za pomocą polecenia programu PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`można wyświetlić zaktualizowane IssuerUri.  Poniższy zrzut ekranu przedstawia Federacji ustawienia zostały zaktualizowane na oryginalną domenę http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

I ustawiono IssuerUri dla nowej domeny https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Obsługa domen podrzędnych
Po dodaniu poddomeny, ze względu na domenach sposób usługa Azure AD obsługiwane odziedziczy ustawienia elementu nadrzędnego.  Dlatego IssuerUri, musi być zgodna elementów nadrzędnych.

Umożliwia tak Załóżmy na przykład, który mogę mieć bmcontoso.com, a następnie dodać corp.bmcontoso.com.  IssuerUri dla użytkownika z corp.bmcontoso.com będą musiały mieć  **http://bmcontoso.com/adfs/services/trust.**  Jednak reguły standard implementowane powyżej dla usługi Azure AD wygeneruje token z wystawcą jako  **http://corp.bmcontoso.com/adfs/services/trust.** nie będą zgodne domeny wymagane wartości, a uwierzytelnianie zakończy się niepowodzeniem.

### <a name="how-to-enable-support-for-subdomains"></a>Jak włączyć obsługę poddomeny
Aby obejść ten problem, do witryny Microsoft Online zaufanie jednostki uzależnionej usług AD FS musi zostać zaktualizowany.  Aby to zrobić, należy skonfigurować niestandardowej reguły oświadczeń, tak aby paski Wyłącz poddomen z sufiks nazwy UPN użytkownika podczas tworzenia niestandardowych wartości wystawcy.

Następujące oświadczenie będzie wykonaj następujące czynności:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Ostatnia liczba w zestawie wyrażenia regularnego jest ile domeny nadrzędnej istnieje w domenie katalogu głównego. W tym miejscu bmcontoso.com jest używana, więc konieczne są dwie domeny nadrzędnej. Jeśli trzy nadrzędnej domeny zostały ma być przechowywana (czyli: corp.bmcontoso.com), a następnie numer byłby trzy. Po pewnym czasie zakresu może być wskazane, zawsze będzie dopasowanie do dopasowania maksymalną liczbę domen. "{2,3}" będzie odpowiadał dwa lub trzy domeny (np: bmfabrikam.com i corp.bmcontoso.com).

Wykonaj następujące kroki, aby dodać niestandardowe oświadczenia do obsługi domen podrzędnych.

1. Otwórz AD FS zarządzania
2. Kliknij prawym przyciskiem myszy relację zaufania jednostki Uzależnionej usługi Online firmy Microsoft, a następnie wybierz reguły oświadczeń edycji
3. Wybierz trzecie reguła oświadczenia, a następnie zastąp ![Edycja oświadczeń](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Zastąp bieżący oświadczeń:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Zastąp oświadczeń](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Kliknij przycisk Ok.  Kliknij przycisk Zastosuj.  Kliknij przycisk Ok.  Zamknij przystawkę zarządzania usługami AD FS.

## <a name="next-steps"></a>Kolejne kroki
Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).

Dowiedz się więcej na temat funkcji, które zostały włączone w ramach instalacji: [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md), [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) i [Azure AD Connect Health](how-to-connect-health-sync.md).

Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
