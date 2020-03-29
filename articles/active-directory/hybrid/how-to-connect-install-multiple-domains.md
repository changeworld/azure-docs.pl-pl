---
title: Połączenie wielu domen usługi Azure AD
description: W tym dokumencie opisano konfigurowanie i konfigurowanie wielu domen najwyższego poziomu za pomocą usług O365 i usługi Azure AD.
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
ms.openlocfilehash: 18b5f19e3e994aa05fa99caf360d0c1be69ec7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049785"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Obsługa wielu domen do federowania w usłudze Azure AD
Poniższa dokumentacja zawiera wskazówki dotyczące używania wielu domen i poddomen najwyższego poziomu podczas federowania za pomocą domen usługi Office 365 lub usługi Azure AD.

## <a name="multiple-top-level-domain-support"></a>Obsługa wielu domen najwyższego poziomu
Federacja wielu domen najwyższego poziomu za pomocą usługi Azure AD wymaga dodatkowej konfiguracji, która nie jest wymagana podczas federowania za pomocą jednej domeny najwyższego poziomu.

Gdy domena jest sfederowana z usługą Azure AD, kilka właściwości są ustawiane w domenie na platformie Azure.  Jednym z ważnych jest IssuerUri.  Ta właściwość jest identyfikatorem URI, który jest używany przez usługę Azure AD do identyfikowania domeny, z którą jest skojarzony token.  Identyfikator URI nie musi rozwiązać do niczego, ale musi być prawidłowy identyfikator URI.  Domyślnie usługa Azure AD ustawia identyfikator URI na wartość identyfikatora usługi federacyjnej w lokalnej konfiguracji usług AD FS.

> [!NOTE]
> Identyfikator usługi federacyjnej jest identyfikatorem URI, który jednoznacznie identyfikuje usługę federacji.  Usługa federacji jest wystąpieniem usług AD FS, które działa jako usługa tokenu zabezpieczającego.
>
>

Identyfikator IssuerUri można wyświetlić za pomocą `Get-MsolDomainFederationSettings -DomainName <your domain>`polecenia Programu PowerShell .

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Problem pojawia się po dodaniu więcej niż jednej domeny najwyższego poziomu.  Załóżmy na przykład, że skonfigurowałeś federację między usługą Azure AD a środowiskiem lokalnym.  W przypadku tego dokumentu używana jest domena bmcontoso.com.  Teraz dodano drugą domenę najwyższego poziomu, bmfabrikam.com.

![Domeny](./media/how-to-connect-install-multiple-domains/domains.png)

Podczas próby konwersji domeny bmfabrikam.com do federacji występuje błąd.  Powodem jest, że usługa Azure AD ma ograniczenie, które nie zezwala na właściwość IssuerUri mieć taką samą wartość dla więcej niż jednej domeny.  

![Błąd federacyjnego](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parametr SupportMultipleDomain
Aby obejść to ograniczenie, należy dodać inny IssuerUri, które `-SupportMultipleDomain` można wykonać przy użyciu parametru.  Ten parametr jest używany z następującymi poleceniami cmdlet:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ten parametr sprawia, że usługa Azure AD konfiguruje identyfikator IssuerUri tak, aby był oparty na nazwie domeny.  IssuerUri będzie unikatowy w katalogach w usłudze Azure AD.  Użycie tego parametru umożliwia pomyślne ukończenie polecenia programu PowerShell.

![Błąd federacyjnego](./media/how-to-connect-install-multiple-domains/convert.png)

Patrząc na ustawienia domeny bmfabrikam.com możesz zobaczyć następujące informacje:

![Błąd federacyjnego](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain`nie zmienia innych punktów końcowych, które są nadal skonfigurowane do wskazywały usługę federacji w adfs.bmcontoso.com.

Inną rzeczą, która `-SupportMultipleDomain` nie jest to, że zapewnia, że system usług AD FS zawiera właściwą wartość wystawcy w tokenach wystawionych dla usługi Azure AD. Wartość ta jest ustawiana przez przyjęcie części domeny nazwy UPN użytkowników i ustawienie jej jako domeny w issueruri, czyli https://{upn suffix}/adfs/services/trust.

W związku z tym podczas uwierzytelniania w usłudze Azure AD lub Office 365 element IssuerUri w tokenie użytkownika jest używany do lokalizowania domeny w usłudze Azure AD.  Jeśli nie można odnaleźć dopasowania, uwierzytelnianie zakończy się niepowodzeniem.

Na przykład, jeśli nazwa UPN bsimon@bmcontoso.comużytkownika jest , Element IssuerUri w tokenie, `http://bmcontoso.com/adfs/services/trust`ad fs problemy, zostanie ustawiona na . Ten element będzie zgodny z konfiguracją usługi Azure AD, a uwierzytelnianie zakończy się pomyślnie.

Poniżej znajduje się reguła oświadczeń dostosowanych, która implementuje tę logikę:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Aby użyć przełącznika -SupportMultipleDomain podczas próby dodania nowych lub konwersji już istniejących domen, zaufanie federacyjne musi być już skonfigurowane do ich obsługi.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Jak zaktualizować zaufanie między usługami AD FS i usługą Azure AD
Jeśli nie skonfigurowałeś zaufania federacyjnego między usługami AD FS a wystąpieniem usługi Azure AD, może być konieczne ponowne utworzenie tego zaufania.  Powodem jest, gdy jest pierwotnie skonfigurowany bez parametru, `-SupportMultipleDomain` IssuerUri jest ustawiony z wartością domyślną.  Na poniższym zrzucie ekranu widać, `https://adfs.bmcontoso.com/adfs/services/trust`że Najem jest ustawiony na .

Jeśli pomyślnie dodano nową domenę w portalu usługi Azure AD, a następnie spróbuj przekonwertować ją przy użyciu `Convert-MsolDomaintoFederated -DomainName <your domain>`, zostanie wyświetlony następujący błąd.

![Błąd federacyjnego](./media/how-to-connect-install-multiple-domains/trust1.png)

Jeśli spróbujesz `-SupportMultipleDomain` dodać przełącznik, pojawi się następujący błąd:

![Błąd federacyjnego](./media/how-to-connect-install-multiple-domains/trust2.png)

Po prostu `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` próbuje uruchomić w oryginalnej domenie spowoduje również błąd.

![Błąd federacyjnego](./media/how-to-connect-install-multiple-domains/trust3.png)

Skorzystaj z poniższych czynności, aby dodać dodatkową domenę najwyższego poziomu.  Jeśli domena została już dodana i `-SupportMultipleDomain` nie została użyta, należy rozpocząć od kroków usuwania i aktualizowania oryginalnej domeny.  Jeśli domena najwyższego poziomu nie została jeszcze dodana, możesz rozpocząć od kroków dotyczących dodawania domeny przy użyciu programu PowerShell usługi Azure AD Connect.

Aby usunąć zaufanie usługi Microsoft Online i zaktualizować oryginalną domenę, należy wykonać następujące czynności.

1. Na serwerze federacyjnym usług AD FS otwórz **zarządzanie usługami AD FS.**
2. Po lewej stronie rozwiń **relacje zaufania** i relacje **zaufania jednostki uzależniającej**
3. Po prawej stronie usuń wpis **platformy tożsamości usługi Microsoft Office 365.**
   ![Usuń usługę Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Na komputerze z [zainstalowanym modułem usługi Azure Active Directory dla systemu Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) uruchom następujące czynności: `$cred=Get-Credential`.  
5. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, z którą się federujesz.
6. W programie PowerShell wprowadź`Connect-MsolService -Credential $cred`
7. W programie PowerShell wprowadź plik `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Ta aktualizacja dotyczy oryginalnej domeny.  Więc za pomocą powyższych domen byłoby:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Aby dodać nową domenę najwyższego poziomu za pomocą programu PowerShell, należy wykonać następujące czynności

1. Na komputerze z [zainstalowanym modułem usługi Azure Active Directory dla systemu Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) uruchom następujące czynności: `$cred=Get-Credential`.  
2. Wprowadź nazwę użytkownika i hasło administratora globalnego dla domeny usługi Azure AD, z którą się
3. W programie PowerShell wprowadź`Connect-MsolService -Credential $cred`
4. W programie PowerShell wprowadź`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Aby dodać nową domenę najwyższego poziomu przy użyciu usługi Azure AD Connect, należy wykonać następujące kroki.

1. Uruchamianie usługi Azure AD Connect z menu pulpitu lub startu
2. Wybierz opcję "Dodaj dodatkową ![domenę usługi Azure AD" Dodaj dodatkową domenę usługi Azure AD](./media/how-to-connect-install-multiple-domains/add1.png)
3. Wprowadzanie poświadczeń usługi Azure AD i usługi Active Directory
4. Wybierz drugą domenę, którą chcesz skonfigurować dla federacji.
   ![Dodawanie dodatkowej domeny usługi Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kliknięcie pozycji Zainstaluj

### <a name="verify-the-new-top-level-domain"></a>Weryfikowanie nowej domeny najwyższego poziomu
Za pomocą polecenia `Get-MsolDomainFederationSettings -DomainName <your domain>`programu PowerShell można wyświetlić zaktualizowane IssuerUri.  Poniższy zrzut ekranu pokazuje ustawienia federacji zostały zaktualizowane w oryginalnej domenie`http://bmcontoso.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

A IssuerUri w nowej domenie został ustawiony na`https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Wsparcie dla subdomen
Po dodaniu poddomeny, ze względu na sposób usługi Azure AD obsługiwane domeny, odziedziczy ustawienia nadrzędnego.  Tak więc, IssuerUri, musi dopasować rodziców.

Powiedzmy na przykład, że mam bmcontoso.com, a następnie dodajmy corp.bmcontoso.com.  Najem dla użytkownika z corp.bmcontoso.com będzie musiał być ** http://bmcontoso.com/adfs/services/trust.**  Jednak standardowa reguła zaimplementowana powyżej dla usługi Azure AD wygeneruje token z wystawcą jako ** http://corp.bmcontoso.com/adfs/services/trust.** który nie będzie zgodny z wymaganą wartością domeny, a uwierzytelnianie zakończy się niepowodzeniem.

### <a name="how-to-enable-support-for-subdomains"></a>Jak włączyć obsługę poddomen
Aby obejść to zachowanie, należy zaktualizować zaufanie jednostki uzależniającej usługi AD FS dla usługi Microsoft Online.  Aby to zrobić, należy skonfigurować niestandardową regułę oświadczeń, tak aby usuwała wszelkie poddomeny z sufiksu nazwy UPN użytkownika podczas konstruowania niestandardowej wartości wystawcy.

Następujące oświadczenie spowoduje następujące oświadczenie:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Ostatnim numerem w zestawie wyrażeń regularnych jest liczba domen nadrzędnych w domenie głównej. W tym miejscu bmcontoso.com jest używany, więc dwie domeny nadrzędne są niezbędne. Gdyby trzy domeny nadrzędne były przechowywane (tj. corp.bmcontoso.com), liczba ta byłaby trzy. Ostatecznie można wskazać zakres, mecz będzie zawsze dopasowywany do maksymalnej liczby domen. "{2,3}" będzie pasować do dwóch do trzech domen (tj. bmfabrikam.com i corp.bmcontoso.com).

Poniższe kroki można wykonać, aby dodać oświadczenie niestandardowe do obsługi poddomen.

1. Zarządzanie otwartymi systemami ad fs
2. Kliknij prawym przyciskiem myszy zaufanie usługi Microsoft Online RP i wybierz polecenie Edytuj reguły oświadczeń
3. Wybierz trzecią regułę oświadczeń ![i zastąp Edytuj oświadczenie](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Zastąp bieżące oświadczenie:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Zamień oświadczenie](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Kliknij przycisk Ok.  Kliknij przycisk Zastosuj.  Kliknij przycisk Ok.  Zamknij przystawkę zarządzania usługami AD FS.

## <a name="next-steps"></a>Następne kroki
Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).

Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md), [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](how-to-connect-health-sync.md).

Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
