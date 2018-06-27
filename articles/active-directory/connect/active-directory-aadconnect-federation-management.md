---
title: Azure AD Connect - zarządzania usług AD FS i dostosowywania | Dokumentacja firmy Microsoft
description: Zarządzanie usługami AD FS z usługi Azure AD Connect i dostosowywania AD FS logowania użytkowników z usługi Azure AD Connect i programu PowerShell.
keywords: Usługi AD FS, usługi AD FS, usługi AD FS zarządzania AAD Connect, Connect, logowania, usługi AD FS dostosowanie, napraw federacyjnej relacji zaufania, usługi O365, jednostki uzależnionej
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 719506e35e6abe5ac573c7ceedc1668fd2704bd4
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2018
ms.locfileid: "36961693"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Zarządzanie i dostosowania usług federacyjnych Active Directory przy użyciu usługi Azure AD Connect
W tym artykule opisano sposób zarządzania i dostosowywania Active Directory Federation Services (AD FS) przy użyciu połączenia usługi Azure Active Directory (Azure AD). Zawiera również innych typowych zadań usług AD FS, które może być konieczne przeprowadzenie pełnej konfiguracji farmy usług AD FS.

| Temat | Co obejmuje |
|:--- |:--- |
| **Zarządzanie usługami AD FS** | |
| [Napraw zaufania](#repairthetrust) |Jak naprawić relacja zaufania federacji z usługą Office 365. |
| [Utworzenie federacji z usługą Azure AD przy użyciu alternatywnego Identyfikatora logowania ](#alternateid) | Konfigurowanie Federacji przy użyciu alternatywnego Identyfikatora logowania  |
| [Dodawanie serwera usług AD FS](#addadfsserver) |Jak rozszerzyć farmy usług AD FS jest dodatkowy serwer usług AD FS. |
| [Dodaj serwer Proxy aplikacji sieci Web usług AD FS](#addwapserver) |Jak rozszerzyć farmy usług AD FS jest dodatkowy serwer Proxy aplikacji sieci Web (WAP). |
| [Dodawanie domeny federacyjnej](#addfeddomain) |Jak dodać domeny federacyjnej. |
| [Aktualizuj certyfikat protokołu SSL](active-directory-aadconnectfed-ssl-update.md)| Jak zaktualizować certyfikat SSL do farmy usług AD FS. |
| **Dostosowywanie usług AD FS** | |
| [Dodać logo firmy lub ilustracji](#customlogo) |Sposób dostosowania strony w logowania usług AD FS, z firmowego logo i ilustracja. |
| [Dodaj opis logowania](#addsignindescription) |Jak dodać opis do strony logowania. |
| [Modyfikowanie reguł oświadczeń usług AD FS](#modclaims) |Jak zmodyfikować oświadczenia usług AD FS dla różnych scenariuszach federacji. |

## <a name="manage-ad-fs"></a>Zarządzanie usługami AD FS
Różne zadania związane z FS AD można wykonać w programie Azure AD Connect z użytkownika minimalnej interwencji przy użyciu Kreatora programu Azure AD Connect. Po zakończeniu instalowania Azure AD Connect za pomocą kreatora można uruchomić kreatora ponownie w celu wykonywania dodatkowych zadań.

## <a name="repairthetrust"></a>Napraw zaufania 
Azure AD Connect umożliwia Sprawdź bieżącą kondycję usług AD FS i usługi Azure AD ufa i podjąć odpowiednie działania naprawić relacji zaufania. Wykonaj następujące kroki, aby naprawić usługi Azure AD i zaufania usług AD FS.

1. Wybierz **AAD naprawy i zaufania usług AD FS** z listy dodatkowe zadania.
   ![Napraw AAD i usług AD FS zaufania](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. Na **nawiązywanie połączenia z usługą Azure AD** , podaj poświadczenia administratora globalnego dla usługi Azure AD i kliknij przycisk **dalej**.
   ![Łączenie z usługą Azure AD](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. Na **poświadczeń dostępu zdalnego** strony, wprowadź poświadczenia administratora domeny.

   ![Poświadczenia dostępu zdalnego](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Po kliknięciu **dalej**, Azure AD Connect sprawdza, czy certyfikat kondycji i zawiera wszystkie problemy.

    ![Stan certyfikatów](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    **Wszystko gotowe do skonfigurowania** znajduje się lista akcji, które zostanie wykonane do naprawy relacji zaufania.

    ![Wszystko gotowe do skonfigurowania](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Kliknij przycisk **zainstalować** naprawić relacji zaufania.

> [!NOTE]
> Azure AD Connect może tylko naprawy lub ustawy o certyfikaty z podpisem własnym. Azure AD Connect nie może naprawić certyfikatów innych firm.

## <a name="alternateid"></a>Utworzenie federacji z usługą Azure AD przy użyciu AlternateID 
Zaleca się, że Name(UPN) podmiot zabezpieczeń użytkownika lokalnie i w chmurze główna nazwa użytkownika pozostają na takie same. Jeśli korzysta z lokalną nazwą UPN domeny bez obsługi routingu (np. Contoso.local) lub nie można zmienić z powodu zależności aplikacji lokalnych, zaleca się konfigurowanie alternatywnego identyfikatora. Alternatywnego Identyfikatora logowania umożliwia skonfigurowanie środowiska logowania z której użytkownicy mogą logować się przy użyciu atrybutu innego niż ich nazwy UPN, taki jak adres e-mail. Wybór dla główna nazwa użytkownika usługi Azure AD Connect Domyślnie atrybut userPrincipalName w usłudze Active Directory. Jeśli możesz wybrać inny atrybut główna nazwa użytkownika i Federacji przy użyciu usług AD FS, następnie Azure AD Connect konfiguracji usług AD FS dla alternatywny identyfikator. Poniżej przedstawiono przykład wybranie innego atrybutu główna nazwa użytkownika:

![Alternatywny identyfikator atrybutu zaznaczenia](media/active-directory-aadconnect-federation-management/attributeselection.png)

Konfigurowanie alternatywnego Identyfikatora logowania dla usług AD FS składa się z dwóch głównych czynności:
1. **Konfigurowanie odpowiedniego zestawu wystawiania oświadczeń**: reguły wystawiania oświadczeń w usłudze Azure AD uzależnionej zaufania są modyfikacji w celu użycia wybranego atrybutu UserPrincipalName jako alternatywnego Identyfikatora użytkownika.
2. **Włącz alternatywnego Identyfikatora logowania w konfiguracji usług AD FS**: Zaktualizowano konfigurację usług AD FS, aby usługi AD FS można wyszukiwać użytkowników w lasach odpowiednie przy użyciu alternatywnego identyfikatora. Ta konfiguracja jest obsługiwana dla usług AD FS w systemie Windows Server 2012 R2 (z KB2919355) lub nowszym. Jeśli serwery usług AD FS 2012 R2, Azure AD Connect sprawdza obecność wymagane KB. Jeśli nie wykryto KB, będzie wyświetlane ostrzeżenie po zakończeniu konfiguracji, jak pokazano poniżej:

    ![Ostrzeżenie dotyczące Brak KB na 2012 R2](media/active-directory-aadconnect-federation-management/kbwarning.png)

    Aby rozwiązać konfiguracji w przypadku brakujących KB, należy zainstalować wymagane [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) , a następnie napraw za pomocą zaufania [napraw AAD i AD FS zaufania](#repairthetrust).

> [!NOTE]
> Aby uzyskać więcej informacji na alternateID i kroki, aby ręcznie skonfigurować odczytu [Konfigurowanie alternatywnego Identyfikatora logowania](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Dodawanie serwera usług AD FS 

> [!NOTE]
> Aby dodać serwer usług AD FS, Azure AD Connect wymaga certyfikatu PFX. W związku z tym tę operację mogą wykonać tylko w przypadku skonfigurowania farmy usług AD FS przy użyciu usługi Azure AD Connect.

1. Wybierz **wdrażanie dodatkowego serwera federacyjnego**i kliknij przycisk **dalej**.

   ![Serwer federacyjny dodatkowe](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. Na **nawiązywanie połączenia z usługą Azure AD** strony, wprowadź swoje poświadczenia administratora globalnego dla usługi Azure AD, a następnie kliknij przycisk **dalej**.

   ![Łączenie z usługą Azure AD](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Podaj poświadczenia administratora domeny.

   ![Podane poświadczenia administratora domeny](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Azure AD Connect pytanie o hasło pliku PFX podany podczas konfigurowania nowej farmie serwerów usług AD FS z programem Azure AD Connect. Kliknij przycisk **wprowadź hasło** o podanie hasła dla pliku PFX.

   ![Hasło certyfikatu](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Określ certyfikat SSL](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. Na **serwery usług AD FS** strony, wprowadź nazwę serwera lub adres IP ma zostać dodany do farmy usług AD FS.

   ![Serwery usług AD FS](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Kliknij przycisk **dalej**i przejść ostatecznych **Konfiguruj** strony. Po zakończeniu Azure AD Connect Dodawanie serwerów do farmy usług AD FS, użytkownik otrzyma opcję, aby sprawdzić łączność.

   ![Wszystko gotowe do skonfigurowania](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Zakończenie instalacji](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Dodaj serwer AD FS WAP 

> [!NOTE]
> Aby dodać serwer proxy, Azure AD Connect wymaga certyfikatu PFX. W związku z tym można wykonać tylko tej operacji po skonfigurowaniu farmy usług AD FS przy użyciu usługi Azure AD Connect.

1. Wybierz **wdrażanie Proxy aplikacji sieci Web** z listy dostępnych zadań.

   ![Wdrożenie serwera Proxy aplikacji sieci Web](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Podaj poświadczenia administratora globalnego usługi Azure.

   ![Łączenie z usługą Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. Na **certyfikat SSL określ** Podaj hasło dla pliku PFX podany podczas konfigurowania farmy usług AD FS z programem Azure AD Connect.
   ![Hasło certyfikatu](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Określ certyfikat SSL](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Dodaj serwer, który ma zostać dodany jako serwer proxy. Ponieważ serwerowi proxy aplikacji nie może być przyłączony do domeny, Kreator zapyta o podanie poświadczeń administracyjnych dodawany serwer.

   ![Poświadczenia administracyjne serwera](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. Na **poświadczeń zaufania serwera Proxy** Podaj poświadczenia administracyjne, aby skonfigurować serwer proxy zaufania i uzyskać dostęp serwer główny w farmie usług AD FS.

   ![Poświadczenia relacji zaufania serwera proxy](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. Na **wszystko gotowe do skonfigurowania** strony, Kreator z listą akcje, które będą wykonywane.

   ![Wszystko gotowe do skonfigurowania](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Kliknij przycisk **zainstalować** aby zakończyć konfigurację. Po zakończeniu konfiguracji kreatora udostępnia opcję, aby sprawdzić łączność z serwerami. Kliknij przycisk **Sprawdź** Aby sprawdzić łączność.

   ![Zakończenie instalacji](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Dodawanie domeny federacyjnej 

To proste dodać domenę federacyjną z usługą Azure AD za pomocą usługi Azure AD Connect. Azure AD Connect dodaje domeny na potrzeby federowania i modyfikuje reguł oświadczeń w celu odzwierciedlenia wystawca poprawnie, jeśli masz wiele domen federacyjnych z usługą Azure AD.

1. Aby dodać domenę federacyjną, wybierz zadanie **Dodawanie kolejnej domeny usługi Azure AD**.

   ![Dodatkowe domeny usługi Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. Na następnej stronie kreatora należy podać poświadczenia administratora globalnego dla usługi Azure AD.

   ![Łączenie z usługą Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. Na **poświadczeń dostępu zdalnego** Podaj poświadczenia administratora domeny.

   ![Poświadczenia dostępu zdalnego](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. Na następnej stronie kreatora zawiera listę domen usługi Azure AD, które można było wykonać Federację z katalogu lokalnego. Wybierz domenę z listy.

   ![Azure AD domain](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Po wybraniu domeny Kreator udostępnia odpowiednie informacje o dalsze akcje, które podejmie kreatora i ich wpływ na konfigurację. W niektórych przypadkach w przypadku wybrania domeny, która nie jest jeszcze zweryfikowana w usłudze Azure AD, Kreator dostarcza informacje ułatwiające zweryfikować domenę. Zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md) więcej szczegółów.

5. Kliknij przycisk **Dalej**. **Wszystko gotowe do skonfigurowania** strona zawiera listę działań, które wykona Azure AD Connect. Kliknij przycisk **zainstalować** aby zakończyć konfigurację.

   ![Wszystko gotowe do skonfigurowania](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> Musi być synchronizowany użytkowników dodanych domeny federacyjnej, zanim będą mogli logować się do usługi Azure AD.

## <a name="ad-fs-customization"></a>Dostosowania usług AD FS
Poniższe sekcje zawierają szczegółowe informacje o niektórych typowych zadań, które należy wykonać podczas dostosowywania strony logowania usług AD FS.

## <a name="customlogo"></a>Dodać logo firmy lub ilustracji 
Aby zmienić logo firmy, który jest wyświetlany na **logowania** strony, należy użyć następującego polecenia cmdlet programu Windows PowerShell i składni.

> [!NOTE]
> Zalecane wymiary logo to 260 x 35 przy rozdzielczości 96 dpi w pliku o rozmiarze nie większym niż 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* parametr jest wymagany. Motyw domyślny publikowany z usługami AD FS nosi nazwę domyślny.

## <a name="addsignindescription"></a>Dodaj opis logowania 
Aby dodać opis do strony logowania **strony logowania**, użyj następującego polecenia cmdlet programu Windows PowerShell i składni.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modyfikowanie reguł oświadczeń usług AD FS 
Usługi AD FS obsługuje język sformatowanego oświadczenia, którego można używać do tworzenia niestandardowych reguł oświadczeń. Aby uzyskać więcej informacji, zobacz [rola języka reguł oświadczeń](https://technet.microsoft.com/library/dd807118.aspx).

W poniższych sekcjach opisano, jak można zapisać reguły niestandardowe dla niektórych scenariuszy, które odnoszą się do usługi Azure AD i federacji usług AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Warunkowe na wartość jest obecny w atrybucie niezmienialnego Identyfikatora
Azure AD Connect umożliwia określenie atrybutu ma być używany jako zakotwiczenie źródła, gdy obiekty są synchronizowane z usługą Azure AD. Jeśli wartość atrybutu niestandardowego nie jest pusta, można wystawić oświadczenie niezmienne identyfikator.

Na przykład możesz wybrać pozycję **ms-ds-consistencyguid** jako atrybut zakotwiczenia źródła i problem **nazwę ImmutableID** jako **consistencyguid-ms-ds** w przypadku atrybutu ma wartość na nim. Jeśli nie ma żadnej wartości tego atrybutu, **objectGuid** jako niezmienialny identyfikatora. Można utworzyć zestaw reguł oświadczeń, zgodnie z opisem w poniższej sekcji.

**Reguła 1: Atrybuty zapytania**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

W tej regule, w przypadku badania wartości **ms-ds-consistencyguid** i **objectGuid** dla użytkownika z usługi Active Directory. Zmień nazwę magazynu do magazynu odpowiednie nazwy we wdrożeniu usług AD FS. Zmień typ oświadczenia do odpowiedniego oświadczeń również typ federacyjnym, zgodnie z definicją **objectGuid** i **consistencyguid-ms-ds**.

Ponadto za pomocą **dodać** i nie **problem**, unikać dodawania problemu wychodzących dla obiektu, a można użyć wartości jako wartości pośrednich. Po ustaleniu wartość, która ma być używana jako niezmienialny identyfikatora wystawia oświadczenia w regule nowsze

**Reguła 2: Sprawdź, czy consistencyguid-ms-ds istnieje dla użytkownika**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Ta zasada definiuje tymczasowe flagę o nazwie **idflag** który ustawiono **useguid** w przypadku nie **consistencyguid-ms-ds** wypełnione dla użytkownika. Logiki tego jest fakt, że usługi AD FS nie zezwala na puste oświadczeń. Dlatego podczas dodawania oświadczeń http://contoso.com/ws/2016/02/identity/claims/objectguid i http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid w zasadzie 1 na końcu **msdsconsistencyguid** oświadczeń tylko, jeśli wartość jest wypełniana dla użytkownika. Jeśli go nie jest wypełnione, będzie mieć wartość pustą i odrzuca pochodzące od razu będzie widział usług AD FS. Wszystkie obiekty będą mieć **objectGuid**, więc roszczenie zawsze będą dostępne po wykonaniu reguła 1.

**Reguła 3: Wystawiać ms-ds-consistencyguid jako niezmienialnego Identyfikatora, jeśli jest obecny**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

To jest niejawnie **Exist** Sprawdź. Jeśli istnieje wartość oświadczenia, następnie wystawia, które jako niezmienialny identyfikatora. W poprzednim przykładzie użyto **nameidentifier** oświadczeń. Należy zmienić to typ oświadczenia właściwe dla niezmienialnego Identyfikatora w danym środowisku.

**Reguła 4: Jeśli consistencyGuid-ms-ds nie jest obecny Wystaw objectGuid jako niezmienialnego Identyfikatora**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

W tej regule, sprawdzamy po prostu flagę tymczasowego **idflag**. Określenie, czy wystawiać oświadczenia na podstawie jego wartości.

> [!NOTE]
> Ważne jest sekwencji tych zasad.

### <a name="sso-with-a-subdomain-upn"></a>Usługa rejestracji Jednokrotnej z poddomeny nazwy UPN

Można dodać więcej niż jedną domenę federacyjną za pomocą usługi Azure AD Connect, zgodnie z opisem w [Dodaj nową domenę federacyjną](active-directory-aadconnect-federation-management.md#addfeddomain). A najnowszej wersji programu Azure AD Connect 1.1.553.0 automatycznie tworzy dla reguły oświadczeń poprawne issuerID. Jeśli nie można użyć usługi Azure AD Connect wersji 1.1.553.0 lub najnowszej, zalecane jest [reguł oświadczeń, Azure AD RPT](https://aka.ms/aadrptclaimrules) narzędzie jest używane do generowania i ustawić reguły oświadczeń poprawne dla usługi Azure AD zaufania jednostki uzależnionej.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [opcje logowania użytkowników](active-directory-aadconnect-user-signin.md).
