---
title: Azure AD Connect — dostosowywanie i zarządzanie usług AD FS | Dokumentacja firmy Microsoft
description: Zarządzanie usługami AD FS za pomocą usługi Azure AD Connect i dostosowywania środowiska użytkownika usługi AD FS logowanie przy użyciu programu PowerShell i Azure AD Connect.
keywords: Usługi AD FS, ADFS, usług AD FS zarządzania, program AAD Connect, Połącz, logowania, usług AD FS dostosowywania, napraw zaufanie, usługi O365, rosyjska, jednostki uzależnionej
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
ms.openlocfilehash: 021e13dafcc659337d4096a068e224312e69db1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353792"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Zarządzanie i dostosowywanie Active Directory Federation Services przy użyciu usługi Azure AD Connect
W tym artykule opisano, jak zarządzanie i dostosowywanie Active Directory Federation Services (AD FS) przy użyciu usługi Azure Active Directory (Azure AD) Connect. Zawiera on również innych typowych zadań usług AD FS, które może być konieczne na pełną konfigurację farmy usług AD FS.

| Temat | Co obejmuje |
|:--- |:--- |
| **Zarządzanie usługami AD FS** | |
| [Napraw zaufanie](#repairthetrust) |Jak naprawić relację zaufania federacji z usługą Office 365. |
| [Utworzyć Federację z usługą Azure AD przy użyciu Identyfikatora logowania alternatywnej](#alternateid) | Konfigurowanie Federacji przy użyciu Identyfikatora logowania alternatywnej  |
| [Dodawanie serwera usług AD FS](#addadfsserver) |Instrukcje, rozwiń węzeł farmy usług AD FS jest dodatkowy serwer usług AD FS. |
| [Dodawanie serwera Proxy aplikacji sieci Web usług AD FS](#addwapserver) |Instrukcje, rozwiń węzeł farmy usług AD FS jest dodatkowy serwer Proxy aplikacji sieci Web (WAP). |
| [Dodawanie domeny federacyjnej](#addfeddomain) |Jak dodać domeny federacyjnej. |
| [Aktualizuj certyfikat SSL](how-to-connect-fed-ssl-update.md)| Jak zaktualizować certyfikat SSL dla farmy usług AD FS. |
| **Dostosowywanie usług AD FS** | |
| [Dodawanie rysunku lub za pomocą logo firmy](#customlogo) |Jak dostosować stronę w logowania usług AD FS, za pomocą firmowego logo i ilustracja. |
| [Dodaj opis logowanie](#addsignindescription) |Jak dodać opis do strony logowania. |
| [Modyfikowanie reguł oświadczeń usług AD FS](#modclaims) |Jak zmodyfikować oświadczenia usług AD FS dla różnych scenariuszy federacji. |

## <a name="manage-ad-fs"></a>Zarządzanie usługami AD FS
Różne zadania związane z FS AD można wykonać w programie Azure AD Connect za pomocą użytkownika minimalnej interwencji przy użyciu Kreatora programu Azure AD Connect. Po zakończeniu instalacji program Azure AD Connect za pomocą kreatora można uruchomić kreatora ponownie, aby wykonać dodatkowe zadania.

## <a name="repairthetrust"></a>Napraw zaufanie 
Program Azure AD Connect można użyć, aby sprawdzić bieżącą kondycję AD FS i usługi Azure AD ufa i podjąć odpowiednie działania na napraw zaufanie. Wykonaj następujące kroki, aby naprawić usługi Azure AD i zaufania usług AD FS.

1. Wybierz **naprawy w usłudze AAD i zaufania usług AD FS** z listy dodatkowych zadań.
   ![Napraw usługi AAD i usług AD FS zaufania](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź swoje poświadczenia administratora globalnego dla usługi Azure AD, a następnie kliknij przycisk **dalej**.
   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na **poświadczenia dostępu zdalnego** strony, wprowadź poświadczenia administratora domeny.

   ![Poświadczenia dostępu zdalnego](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Po kliknięciu **dalej**, Azure AD Connect sprawdza, czy certyfikat kondycji i pokazuje wszelkie problemy.

    ![Stan certyfikatów](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    **Wszystko gotowe do skonfigurowania** strona zawiera listę działań wykonanych na napraw zaufanie.

    ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Kliknij przycisk **zainstalować** napraw zaufanie.

> [!NOTE]
> Usługa Azure AD Connect może tylko naprawy lub act związanych z certyfikatami, które są z podpisem własnym. Program Azure AD Connect nie może naprawić certyfikatów innych firm.

## <a name="alternateid"></a>Utworzyć Federację z usługą Azure AD przy użyciu AlternateID 
Zaleca się, że główna użytkownika w środowisku lokalnym i w chmurze główna nazwa użytkownika pozostają na takie same. Jeśli korzysta z lokalnej nazwy UPN domeny bez obsługi routingu (np.) Contoso.local) lub nie można zmienić z powodu zależności aplikacji lokalnych, zaleca się konfigurowania alternatywny identyfikator. Identyfikatora logowania alternatywnej pozwala na skonfigurowanie procesu logowania w którym użytkownicy mogą zarejestrować się przy użyciu atrybutu niż nazw UPN, taki jak adres e-mail. Wybór dla głównej nazwy użytkownika w usłudze Azure AD Connect Domyślnie atrybut userPrincipalName w usłudze Active Directory. Jeśli wybrać inne atrybuty dla głównej nazwy użytkownika i Federacji za pomocą usług AD FS, następnie program Azure AD Connect skonfigurować usług AD FS dla identyfikatora logowania alternatywnej. Poniżej przedstawiono przykład wybierając inny atrybut dla nazwy głównej nazwy użytkownika:

![Alternatywny identyfikator atrybutu zaznaczenia](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurowanie alternatywnego Identyfikatora logowania dla usług AD FS obejmuje dwa podstawowe kroki:
1. **Konfigurowanie odpowiedniego zestawu wystawiania oświadczeń**: Reguły wystawiania oświadczeń w usłudze Azure AD zaufania jednostki uzależnionej są modyfikowane w celu użycia wybranego atrybutu UserPrincipalName jako alternatywne identyfikator użytkownika.
2. **Włącz alternatywnego Identyfikatora logowania w konfiguracji usług AD FS**: Konfiguracja usług AD FS zostaną zmienione, tak aby usługi AD FS można wyszukać użytkowników w lasach odpowiednie, przy użyciu alternatywnego identyfikatora. Ta konfiguracja jest obsługiwana dla usług AD FS w systemie Windows Server 2012 R2 (z KB2919355) lub nowszym. Jeśli serwery usług AD FS 2012 R2, program Azure AD Connect sprawdza obecność wymagane KB. Jeśli nie zostanie wykryty KB, ostrzeżenie zostanie wyświetlony po zakończeniu konfiguracji, jak pokazano poniżej:

    ![Ostrzeżenie dotyczące brakuje KB na 2012 R2](./media/how-to-connect-fed-management/kbwarning.png)

    Aby rozwiązać konfiguracji w przypadku brakujących KB, zainstalować wymagane [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) i napraw za pomocą zaufania [naprawy usługi AAD i AD FS zaufania](#repairthetrust).

> [!NOTE]
> Aby uzyskać więcej informacji na temat alternateID i kroki, aby ręcznie skonfigurować, przeczytaj [Konfigurowanie alternatywnego Identyfikatora logowania](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Dodawanie serwera usług AD FS 

> [!NOTE]
> Aby dodać serwer usług AD FS, Azure AD Connect wymaga certyfikatu PFX. W związku z tym tę operację mogą wykonać tylko wtedy, gdy farmy usług AD FS jest skonfigurowany przy użyciu usługi Azure AD Connect.

1. Wybierz **Wdróż dodatkowy serwer federacyjny**i kliknij przycisk **dalej**.

   ![Serwera federacyjnego dodatkowego](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź swoje poświadczenia administratora globalnego dla usługi Azure AD, a następnie kliknij przycisk **dalej**.

   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Podaj domenę poświadczenia administratora.

   ![Poświadczenia administratora domeny](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Program Azure AD Connect poprosi o podanie hasła pliku PFX, podane podczas konfigurowania nowej farmie serwerów usług AD FS za pomocą usługi Azure AD Connect. Kliknij przycisk **wprowadź hasło** podać hasło dla pliku PFX.

   ![Hasło certyfikatu](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Określ certyfikat SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na **serwery usług AD FS** strony, wprowadź nazwę serwera lub adres IP, które ma zostać dodany do farmy usług AD FS.

   ![Serwery usług AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Kliknij przycisk **dalej**i przechodzą przez ostatni **Konfiguruj** strony. Po zakończeniu program Azure AD Connect podczas dodawania serwerów do farmy usług AD FS, użytkownik otrzyma opcję, aby sprawdzić łączność.

   ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Instalacja zakończona](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Dodawanie serwera proxy aplikacji sieci Web programu AD FS 

> [!NOTE]
> Aby dodać serwer proxy aplikacji sieci Web, program Azure AD Connect wymaga certyfikatu PFX. W związku z tym można wykonać tylko ta operacja Jeśli farmy usług AD FS jest skonfigurowany przy użyciu usługi Azure AD Connect.

1. Wybierz **wdrożyć serwer Proxy aplikacji internetowych** z listy dostępnych zadań.

   ![Wdrażanie serwera Proxy aplikacji sieci Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Podaj poświadczenia administratora globalnego usługi Azure.

   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na **certyfikat SSL określ** Podaj hasło dla pliku PFX, podane podczas konfigurowania farmy usług AD FS za pomocą usługi Azure AD Connect.
   ![Hasło certyfikatu](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Określ certyfikat SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Dodaj serwer do dodania jako serwer proxy aplikacji sieci Web. Ponieważ serwer proxy aplikacji sieci Web nie może być przyłączony do domeny, kreator poprosi o podanie poświadczeń administracyjnych do dodawany serwer.

   ![Poświadczenia administracyjne serwera](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na **poświadczenia relacji zaufania serwera Proxy** Podaj poświadczenia administracyjne, aby skonfigurować serwer proxy zaufania i uzyskać dostęp do podstawowego serwera w farmie usług AD FS.

   ![Poświadczenia relacji zaufania serwera proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na **wszystko gotowe do skonfigurowania** strony, Kreator pokazuje listę działań, które będą wykonywane.

   ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Kliknij przycisk **zainstalować** aby zakończyć konfigurację. Po zakończeniu konfiguracji kreatora zapewnia opcję, aby sprawdzić łączność z serwerami. Kliknij przycisk **Sprawdź** Aby sprawdzić łączność.

   ![Instalacja zakończona](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Dodawanie domeny federacyjnej 

To proste dodać domenę do sfederowania z usługą Azure AD za pomocą usługi Azure AD Connect. Program Azure AD Connect dodaje domeny na potrzeby federacji i modyfikuje reguł oświadczeń, aby odzwierciedlić wystawca poprawnie, w przypadku wielu domen Sfederowane za pomocą usługi Azure AD.

1. Aby dodać domeny federacyjnej, wybierz zadanie **dodać kolejny domeny usługi Azure AD**.

   ![Dodatkowe domeny usługi Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na następnej stronie kreatora należy podać poświadczenia administratora globalnego dla usługi Azure AD.

   ![Łączenie z usługą Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na **poświadczenia dostępu zdalnego** Podaj poświadczenia administratora domeny.

   ![Poświadczenia dostępu zdalnego](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na następnej stronie kreatora zawiera listę domen usługi Azure AD, które może tworzyć federacje z katalogu lokalnego. Wybierz domenę z listy.

   ![Domena usługi Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Po wybraniu domeny, Kreator udostępnia odpowiednie informacje na temat dalszych działań, przyjmujące kreatora i wpływu na konfigurację. W niektórych przypadkach po wybraniu domeny, która nie jest jeszcze zweryfikowane w usłudze Azure AD, Kreator przedstawiono informacje ułatwiające weryfikowanie domeny. Zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md) Aby uzyskać więcej informacji.

5. Kliknij przycisk **Dalej**. **Wszystko gotowe do skonfigurowania** strona zawiera listę akcji, które uruchomi program Azure AD Connect. Kliknij przycisk **zainstalować** aby zakończyć konfigurację.

   ![Wszystko gotowe do skonfigurowania](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Należy zsynchronizować użytkowników z domeny federacyjnej dodano, zanim będą mogli logować się do usługi Azure AD.

## <a name="ad-fs-customization"></a>Usługi AD FS dostosowywania
Poniższe sekcje zawierają szczegółowe informacje o niektórych typowych zadań, które trzeba wykonać, gdy możesz dostosować stronę logowania usług AD FS.

## <a name="customlogo"></a>Dodawanie rysunku lub za pomocą logo firmy 
Aby zmienić logo firmy, która jest wyświetlana na **logowania** strony, należy użyć następującego polecenia cmdlet programu Windows PowerShell i składni.

> [!NOTE]
> Zalecane wymiary logo to 260 x 35 \@ rozdzielczości 96 dpi w pliku o rozmiarze nie większym niż 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* parametr jest wymagany. Motyw domyślny publikowany z usługami AD FS nosi nazwę domyślną.

## <a name="addsignindescription"></a>Dodaj opis logowanie 
Aby dodać opis do strony logowania **strony logowania**, użyj następującego polecenia cmdlet programu Windows PowerShell i składni.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modyfikowanie reguł oświadczeń usług AD FS 
Usługi AD FS obsługuje język oświadczeń rozbudowane, który umożliwia tworzenie niestandardowych reguł oświadczeń. Aby uzyskać więcej informacji, zobacz [roli języka reguła oświadczenia](https://technet.microsoft.com/library/dd807118.aspx).

W poniższych sekcjach opisano, jak pisać reguły niestandardowe dla niektórych scenariuszy, które odnoszą się do usługi Azure AD i usług AD FS federacji.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Niemodyfikowalny identyfikator warunkowe na podstawie wartości w atrybucie
Program Azure AD Connect umożliwia określenie atrybutu ma być używany jako zakotwiczenia źródła, gdy obiekty są synchronizowane z usługą Azure AD. Jeśli wartość atrybutu niestandardowego nie jest pusty, można wystawiać oświadczenie Identyfikatora niezmienne.

Na przykład, możesz wybrać **ms-ds-consistencyguid** jako atrybutu zakotwiczenia źródła i wydawania **ImmutableID** jako **ms-ds-consistencyguid** w przypadku atrybutu ma wartość przed nim. Jeśli nie ma wartości tego atrybutu, wystawiać **objectGuid** jako niemodyfikowalny identyfikator. Można skonstruować zbiór niestandardowych reguł oświadczeń zgodnie z opisem w poniższej sekcji.

**Reguła 1: Atrybuty kwerendy**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

W tej regule zadajesz zapytanie do wartości **ms-ds-consistencyguid** i **objectGuid** dla użytkownika z usługi Active Directory. Zmień nazwę magazynu na nazwę odpowiedniego magazynu w ramach wdrożenia usług AD FS. Również zmiany odpowiedni typ oświadczenia oświadczeń typu usługi federacyjnej, zgodnie z definicją **objectGuid** i **ms-ds-consistencyguid**.

Ponadto za pomocą **Dodaj** i nie **problem**, unikać dodawania wychodzących problemu dla jednostki, a można użyć wartości jako wartości pośrednich. Będzie wystawiać oświadczenia w regule nowsze po ustanowieniu wartość, która do użycia jako niemodyfikowalny identyfikator.

**Reguła 2: Sprawdź, czy ms-ds-consistencyguid istnieje dla użytkownika**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Ta zasada definiuje flagę tymczasowy o nazwie **idflag** który jest skonfigurowany do **useguid** w przypadku nie **ms-ds-consistencyguid** wypełnione dla użytkownika. Logiki tego jest fakt, że usług AD FS nie zezwala na pusty oświadczeń. Tak, po dodaniu oświadczeń http://contoso.com/ws/2016/02/identity/claims/objectguid i http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid w zasadzie 1 na końcu **msdsconsistencyguid** oświadczenia tylko wtedy, jeśli wartość jest wypełniana dla użytkownika. Jeśli go nie jest wypełnione, usług AD FS widzi będzie zawierać wartości pustej i umieszcza go bezpośrednio. Wszystkie obiekty będą mieć **objectGuid**, więc to roszczenie zawsze będą dostępne po wykonaniu reguła 1.

**Reguła 3: Wystawiać ms-ds-consistencyguid jako niemodyfikowalny identyfikator, jeśli jest obecny**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Jest to bezwarunkowy **istnieje** Sprawdź. Jeśli istnieje wartość oświadczenia, następnie wydać, jako identyfikator niezmienne. W poprzednim przykładzie użyto **nameidentifier** oświadczenia. Należy zmienić typ oświadczenia właściwe dla niemodyfikowalny identyfikator w danym środowisku.

**Reguła 4: Wystawiać objectGuid jako niemodyfikowalny identyfikator, jeśli nie ma ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

W tej regule, po prostu sprawdzamy tymczasowe flagi **idflag**. Określenie, czy wystawiać oświadczenia na podstawie jej wartości.

> [!NOTE]
> Ważne jest sekwencja tych reguł.

### <a name="sso-with-a-subdomain-upn"></a>Usługa rejestracji Jednokrotnej z poddomeną nazwy UPN

Możesz dodać więcej niż jedną domenę do sfederowania przy użyciu usługi Azure AD Connect, zgodnie z opisem w [dodać nową domenę federacyjną](how-to-connect-fed-management.md#addfeddomain). Wersja usługi Azure AD Connect 1.1.553.0 i najnowsze reguły oświadczenia poprawna dla issuerID tworzy automatycznie. Jeśli nie można używać usługi Azure AD Connect w wersji 1.1.553.0 lub najnowsze, zalecane jest, [reguł oświadczeń w usłudze Azure AD RPT](https://aka.ms/aadrptclaimrules) narzędzie służy do generowania i zestawu reguł oświadczeń poprawne dla usługi Azure AD zaufania jednostki uzależnionej.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [opcje logowania użytkowników](plan-connect-user-signin.md).
