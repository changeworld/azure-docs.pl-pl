---
title: Usługa Azure AD Connect — zarządzanie usługą AD FS i dostosowywanie | Dokumenty firmy Microsoft
description: Zarządzanie usługami AD FS za pomocą usługi Azure AD Connect i dostosowywanie środowiska logowania usług AD FS za pomocą usługi Azure AD Connect i programu PowerShell.
keywords: Usługi AD FS, zarządzanie usługami ADFS, usługi AAD Connect, Connect, logowanie, dostosowywanie usług AD FS, zaufanie do naprawy, O365, federacja, jednostka uzależniona
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcbeedddc65a916f869a778616779917a9571181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331978"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Zarządzanie usługami federacyjnymi active directory i dostosowywanie ich przy użyciu usługi Azure AD Connect
W tym artykule opisano sposób zarządzania usługami federacyjnymi Active Directory (AD FS) przy użyciu usługi Azure Active Directory (Azure AD) Connect i dostosowywania ich. Zawiera również inne typowe zadania usług AD FS, które mogą być konieczne w celu pełnej konfiguracji farmy usług AD FS.

| Temat | Co obejmuje |
|:--- |:--- |
| **Zarządzanie usługami AD FS** | |
| [Naprawianie zaufania](#repairthetrust) |Jak naprawić zaufanie federacji za pomocą usługi Office 365. |
| [Sedanowanie z usługą Azure AD przy użyciu alternatywnego identyfikatora logowania](#alternateid) | Konfigurowanie federacji przy użyciu alternatywnego identyfikatora logowania  |
| [Dodawanie serwera usług AD FS](#addadfsserver) |Jak rozwinąć farmę usług AD FS za pomocą dodatkowego serwera usług AD FS. |
| [Dodawanie serwera serwera proxy aplikacji sieci Web usług AD FS](#addwapserver) |Jak rozwinąć farmę usług AD FS za pomocą dodatkowego serwera proxy aplikacji sieci Web (WAP). |
| [Dodawanie domeny federacyjnej](#addfeddomain) |Jak dodać domenę federacyjnej. |
| [Aktualizowanie certyfikatu TLS/SSL](how-to-connect-fed-ssl-update.md)| Jak zaktualizować certyfikat TLS/SSL dla farmy usług AD FS. |
| **Dostosowywanie usług AD FS** | |
| [Dodawanie niestandardowego logo firmy lub ilustracji](#customlogo) |Jak dostosować stronę logowania usług AD FS z logo firmy i ilustracją. |
| [Dodawanie opisu logowania](#addsignindescription) |Jak dodać opis strony logowania. |
| [Modyfikowanie reguł oświadczeń usług AD FS](#modclaims) |Jak zmodyfikować oświadczenia usług AD FS dla różnych scenariuszy federacji. |

## <a name="manage-ad-fs"></a>Zarządzanie usługami AD FS
Za pomocą kreatora usługi Azure AD Connect można wykonywać różne zadania związane z usługami AD FS w usłudze Azure AD Connect przy minimalnej interwencji użytkownika. Po zakończeniu instalowania usługi Azure AD Connect przez uruchomienie kreatora można ponownie uruchomić kreatora, aby wykonać dodatkowe zadania.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Naprawianie zaufania 
Za pomocą usługi Azure AD Connect można sprawdzić bieżącą kondycję zaufania usług AD FS i usługi Azure AD i podjąć odpowiednie akcje w celu naprawy zaufania. Wykonaj następujące kroki, aby naprawić zaufanie usługi Azure AD i usług AD FS.

1. Z listy dodatkowych zadań wybierz **pozycję Napraw aad i zaufanie usługi ADFS.**
   ![Naprawa AAD i ADFS Trust](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na stronie **Połącz z usługą Azure AD** podaj poświadczenia administratora globalnego dla usługi Azure AD i kliknij przycisk **Dalej**.
   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na stronie **Poświadczenia dostępu zdalnego** wprowadź poświadczenia administratora domeny.

   ![Poświadczenia dostępu zdalnego](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Po **kliknięciu przycisku Dalej**usługa Azure AD Connect sprawdza stan certyfikatu i wyświetla wszelkie problemy.

    ![Stan świadectw](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Na stronie **Gotowe do skonfigurowania** jest wyświetlana lista akcji, które zostaną wykonane w celu naprawy zaufania.

    ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Kliknij **przycisk Zainstaluj,** aby naprawić zaufanie.

> [!NOTE]
> Usługa Azure AD Connect może naprawiać lub działać tylko na certyfikatach, które są podpisane samodzielnie. Usługa Azure AD Connect nie może naprawić certyfikatów innych firm.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Sedanuj z usługą Azure AD przy użyciu funkcji AlternateID 
Zaleca się, aby lokalna nazwa główna użytkownika (UPN) i główna nazwa użytkownika w chmurze były takie same. Jeśli korzysta z lokalnej nazwy UPN domeny bez obsługi routingu (np. Contoso.local) lub nie można zmienić ze względu na zależności aplikacji lokalnych, zaleca się skonfigurowanie alternatywnego identyfikatora logowania. Alternatywny identyfikator logowania umożliwia skonfigurowanie środowiska logowania, w którym użytkownicy mogą logować się przy pomocą atrybutu innego niż ich nazwa UPN, takiego jak poczta. Wybór głównej nazwy użytkownika w usłudze Azure AD Connect domyślnie atrybutu userPrincipalName w usłudze Active Directory. Jeśli wybierzesz inny atrybut dla głównej nazwy użytkownika i są federating przy użyciu usług AD FS, a następnie usługi Azure AD Connect skonfiguruje ad fs dla alternatywnego identyfikatora logowania. Poniżej przedstawiono przykład wyboru innego atrybutu dla głównej nazwy użytkownika:

![Wybór atrybutu identyfikatora alternatywnego](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurowanie alternatywnego identyfikatora logowania dla usług AD FS składa się z dwóch głównych kroków:
1. **Skonfiguruj odpowiedni zestaw oświadczeń o wydawaniu:** reguły oświadczeń o wydawaniu w zaufaniu jednostki uzależnionej usługi Azure AD są modyfikowane w celu użycia wybranego atrybutu UserPrincipalName jako alternatywnego identyfikatora użytkownika.
2. **Włącz alternatywny identyfikator logowania w konfiguracji usług AD FS:** Konfiguracja usług AD FS jest aktualizowana, dzięki czemu usługi AD FS mogą wyszukać użytkowników w odpowiednich lasach przy użyciu alternatywnego identyfikatora. Ta konfiguracja jest obsługiwana dla usług AD FS w systemie Windows Server 2012 R2 (z KB2919355) lub nowszym. Jeśli serwery usług AD FS są 2012 R2, usługa Azure AD Connect sprawdza obecność wymaganej bazy wiedzy. Jeśli kb nie zostanie wykryty, ostrzeżenie zostanie wyświetlone po zakończeniu konfiguracji, jak pokazano poniżej:

    ![Ostrzeżenie dotyczące brakujących KB na 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Aby poprawić konfigurację w przypadku braku KB, zainstaluj wymaganą [kb2919355,](https://go.microsoft.com/fwlink/?LinkID=396590) a następnie napraw zaufanie za pomocą [naprawy AAD i ad fs trust](#repairthetrust).

> [!NOTE]
> Aby uzyskać więcej informacji na temat alternateID i kroków do ręcznego konfigurowania, przeczytaj [konfigurowanie alternatywnego identyfikatora logowania](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Dodawanie serwera usług AD FS 

> [!NOTE]
> Aby dodać serwer usług AD FS, usługa Azure AD Connect wymaga certyfikatu PFX. W związku z tym tę operację można wykonać tylko wtedy, gdy skonfigurowano farmę usług AD FS przy użyciu usługi Azure AD Connect.

1. Wybierz **pozycję Wdrażanie dodatkowego serwera federacyjnego**i kliknij przycisk **Dalej**.

   ![Dodatkowy serwer federacyjny](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla usługi Azure AD i kliknij przycisk **Dalej**.

   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Podaj poświadczenia administratora domeny.

   ![Poświadczenia administratora domeny](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Usługa Azure AD Connect prosi o hasło pliku PFX, które zostały dostarczone podczas konfigurowania nowej farmy usług AD FS za pomocą usługi Azure AD Connect. Kliknij **przycisk Wprowadź hasło,** aby podać hasło do pliku PFX.

   ![Hasło certyfikatu](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Określanie certyfikatu TLS/SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na stronie **Serwery usług AD FS** wprowadź nazwę serwera lub adres IP, który ma zostać dodany do farmy usług AD FS.

   ![Serwery usług AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Kliknij **przycisk Dalej**i przejdź przez ostatnią stronę **Konfiguruj.** Po zakończeniu dodawania serwerów do farmy usług AD FS usługa Azure AD Connect będzie mieć możliwość zweryfikowania łączności.

   ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Instalacja zakończona](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Dodawanie serwera WAP usług AD FS 

> [!NOTE]
> Aby dodać serwer WAP, usługa Azure AD Connect wymaga certyfikatu PFX. W związku z tym tę operację można wykonać tylko wtedy, gdy skonfigurowano farmę usług AD FS przy użyciu usługi Azure AD Connect.

1. Z listy dostępnych zadań wybierz **pozycję Wdrażanie serwera proxy aplikacji sieci Web.**

   ![Wdrażanie serwera proxy aplikacji sieci Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Podaj poświadczenia administratora globalnego platformy Azure.

   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na stronie **Określanie certyfikatu SSL podaj** hasło do pliku PFX podanego podczas konfigurowania farmy usług AD FS za pomocą usługi Azure AD Connect.
   ![Hasło certyfikatu](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Określanie certyfikatu TLS/SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Dodaj serwer, który ma zostać dodany jako serwer WAP. Ponieważ serwer WAP może nie zostać przyłączony do domeny, kreator prosi o podanie poświadczeń administracyjnych do dodawanych serwerów.

   ![Poświadczenia serwera administracyjnego](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na stronie **Poświadczenia zaufania serwera proxy** podaj poświadczenia administracyjne w celu skonfigurowania zaufania serwera proxy i uzyskania dostępu do serwera podstawowego w farmie usług AD FS.

   ![Poświadczenia zaufania serwera proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na stronie **Gotowe do skonfigurowania** kreator pokazuje listę akcji, które zostaną wykonane.

   ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Kliknij **przycisk Zainstaluj,** aby zakończyć konfigurację. Po zakończeniu konfiguracji kreator umożliwia sprawdzenie łączności z serwerami. Kliknij **przycisk Sprawdź,** aby sprawdzić łączność.

   ![Instalacja zakończona](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Dodawanie domeny federacyjnej 

Łatwo jest dodać domenę, która ma być sfederowana z usługą Azure AD przy użyciu usługi Azure AD Connect. Usługa Azure AD Connect dodaje domenę dla federacji i modyfikuje reguły oświadczeń, aby poprawnie odzwierciedlały wystawcę, gdy masz wiele domen sfederowanych z usługą Azure AD.

1. Aby dodać domenę federacjną, wybierz zadanie **Dodaj dodatkową domenę usługi Azure AD**.

   ![Dodatkowa domena usługi Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na następnej stronie kreatora podaj poświadczenia administratora globalnego dla usługi Azure AD.

   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na stronie **Poświadczenia dostępu zdalnego** podaj poświadczenia administratora domeny.

   ![Poświadczenia dostępu zdalnego](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na następnej stronie kreator udostępnia listę domen usługi Azure AD, z którymi można skonserwować katalog lokalny. Wybierz domenę z listy.

   ![Domena usługi Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Po wybraniu domeny kreator udostępnia odpowiednie informacje o dalszych działaniach, które kreator podejmie, oraz o wpływie konfiguracji. W niektórych przypadkach jeśli wybierzesz domenę, która nie jest jeszcze zweryfikowana w usłudze Azure AD, kreator udostępnia informacje ułatwiające weryfikację domeny. Aby uzyskać więcej [informacji, zobacz Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory.](../active-directory-domains-add-azure-portal.md)

5. Kliknij przycisk **alej**. Strona **Gotowe do skonfigurowania** zawiera listę akcji, które będą wykonywane usługi Azure AD Connect. Kliknij **przycisk Zainstaluj,** aby zakończyć konfigurację.

   ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Użytkownicy z dodanej domeny federacyjnej muszą być synchronizowani, zanim będą mogli zalogować się do usługi Azure AD.

## <a name="ad-fs-customization"></a>Usługi AD FS dostosowywania
W poniższych sekcjach podano szczegółowe informacje na temat niektórych typowych zadań, które mogą być wykonywane podczas dostosowywania strony logowania do usług AD FS.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Dodawanie niestandardowego logo firmy lub ilustracji 
Aby zmienić logo firmy wyświetlane na stronie **Logowania,** użyj następującego polecenia cmdlet i składni programu Windows PowerShell.

> [!NOTE]
> Zalecane wymiary logo to 260 x \@ 35 96 dpi o rozmiarze pliku nie większym niż 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Wymagany jest parametr *TargetName.* Domyślny motyw wydany z usługą AD FS nosi nazwę Domyślny.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Dodawanie opisu logowania 
Aby dodać opis strony logowania do **strony logowania,** użyj następującej linii cmdlet i składni programu Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Modyfikowanie reguł oświadczeń usług AD FS 
Usługi AD FS obsługują język oświadczeń rozszerzonych, którego można użyć do tworzenia niestandardowych reguł oświadczeń. Aby uzyskać więcej informacji, zobacz [Rola języka reguły oświadczeń](https://technet.microsoft.com/library/dd807118.aspx).

W poniższych sekcjach opisano, jak można napisać reguły niestandardowe dla niektórych scenariuszy, które odnoszą się do federacji usług Ad AD i AD FS platformy Azure.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Niezmienny identyfikator zależny od wartości obecnej w atrybucie
Usługa Azure AD Connect umożliwia określenie atrybutu, który ma być używany jako kotwica źródłowa, gdy obiekty są synchronizowane z usługą Azure AD. Jeśli wartość w atrybucie niestandardowym nie jest pusta, można wystawić niezmienne oświadczenie o identyfikatorze.

Na przykład można wybrać **ms-ds-consistencyguid** jako atrybut zakotwiczenia źródłowego i wydać **ImmutableID** jako **ms-ds-consistencyguid** w przypadku, gdy atrybut ma wartość przeciwko niemu. Jeśli nie ma żadnej wartości względem atrybutu, problem **objectGuid** jako niezmienny identyfikator. Można skonstruować zestaw reguł oświadczeń niestandardowych, zgodnie z opisem w poniższej sekcji.

**Reguła 1: Atrybuty kwerendy**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

W tej regule kwerendy wartości **ms-ds-consistencyguid** i **objectGuid** dla użytkownika z usługi Active Directory. Zmień nazwę sklepu na odpowiednią nazwę sklepu we wdrożeniu usług AD FS. Należy również zmienić typ oświadczeń na odpowiedni typ oświadczeń dla federacji, zgodnie z definicją dla **objectGuid** i **ms-ds-consistencyguid**.

Ponadto za pomocą **dodaj** i nie **rozchodzą**się , można uniknąć dodawania problemu wychodzącego dla encji i można użyć wartości jako wartości pośrednich. Oświadczenie zostanie wydane w późniejszej regule po ustaleniu, która wartość ma być używana jako niezmienny identyfikator.

**Reguła 2: Sprawdź, czy ms-ds-consistencyguid istnieje dla użytkownika**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Ta reguła definiuje tymczasową flagę o nazwie **idflag,** która jest ustawiona na **useguid,** jeśli nie ma **ms-ds-consistencyguid** wypełnione dla użytkownika. Logika za to jest fakt, że usługi AD FS nie zezwala pustych oświadczeń. Tak więc po `http://contoso.com/ws/2016/02/identity/claims/objectguid` `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` dodaniu oświadczeń i w regule 1, kończy się z **msdsconsistencyguid** oświadczenia tylko wtedy, gdy wartość jest wypełniona dla użytkownika. Jeśli nie jest wypełniona, ad fs widzi, że będzie miał pustą wartość i spada go natychmiast. Wszystkie obiekty będą miały **objectGuid**, tak aby oświadczenie zawsze będzie tam po reguły 1 jest wykonywana.

**Reguła 3: Problem ms-ds-consistencyguid jako niezmienny identyfikator, jeśli jest obecny**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Jest to niejawne **sprawdzanie istnieje.** Jeśli istnieje wartość oświadczenia, a następnie wydać, że jako identyfikator niezmienne. W poprzednim przykładzie użyto oświadczenia **nameidentifier.** Należy zmienić to na odpowiedni typ oświadczenia dla niezmiennego identyfikatora w twoim środowisku.

**Reguła 4: Obiekt wydaniaGuid jako niezmienny identyfikator, jeśli ms-ds-consistencyGuid nie jest obecny**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

W tej zasadzie, jesteś po prostu sprawdzanie tymczasowej flagi **idflag**. Użytkownik decyduje, czy wystawi roszczenie na podstawie jego wartości.

> [!NOTE]
> Kolejność tych reguł jest ważna.

### <a name="sso-with-a-subdomain-upn"></a>SSO z subdomeną UPN

Możesz dodać więcej niż jedną domenę, która ma być sfederowana przy użyciu usługi Azure AD Connect, zgodnie z opisem w [obszarze Dodaj nową domenę federacyjnej](how-to-connect-fed-management.md#addfeddomain). Usługa Azure AD Connect w wersji 1.1.553.0 i najnowsza automatycznie tworzy regułę poprawnej oświadczeń dla identyfikatora wystawców. Jeśli nie można użyć usługi Azure AD Connect w wersji 1.1.553.0 lub najnowszej, zaleca się, że narzędzie [reguł oświadczeń usługi Azure AD RPT](https://aka.ms/aadrptclaimrules) służy do generowania i ustawiania reguł oświadczeń poprawne dla zaufania jednostki uzależniającej usługi Azure AD.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [opcjach logowania użytkownika](plan-connect-user-signin.md).
