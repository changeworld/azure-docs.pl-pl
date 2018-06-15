---
title: Konfigurowanie lokalnego dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Przewodnik krok po kroku do włączania dostępu warunkowego do aplikacji lokalnych przy użyciu usługi Active Directory Federation Services (AD FS) w systemie Windows Server 2012 R2.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 0ce4497a8bebf9078363509c1f962728ab4189f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764210"
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Konfigurowanie lokalnego dostępu warunkowego przy użyciu rejestracji urządzeń usługi Azure Active Directory
Jeśli wymagane jest użytkownikom Dołącz do miejsca pracy swoje urządzenia osobiste do usługi rejestracji urządzeń usługi Azure Active Directory (Azure AD), urządzeń może być oznaczony znane w organizacji. Poniżej przedstawiono krok po kroku dotyczące włączania dostępu warunkowego do aplikacji lokalnych przy użyciu usługi Active Directory Federation Services (AD FS) w systemie Windows Server 2012 R2.

> [!NOTE]
> Licencja usługi Office 365 lub Azure AD Premium licencji jest wymagana podczas używania urządzenia, które są zarejestrowane w zasad dostępu warunkowego usługi rejestracji urządzeń usługi Azure Active Directory. Obejmują one zasady, których przestrzeganie jest wymuszane przez usługę AD FS w lokalnych zasobów.
> 
> Aby uzyskać więcej informacji na temat scenariuszy dostępu warunkowego dla zasobów lokalnych, zobacz [dołączanie do miejsca pracy z dowolnego urządzenia na potrzeby logowania jednokrotnego i łatwego uwierzytelniania dwuskładnikowego w aplikacjach firmy](https://technet.microsoft.com/library/dn280945.aspx).

Te funkcje są dostępne dla klientów, którzy kupili licencji usługi Azure Active Directory Premium.

## <a name="supported-devices"></a>Obsługiwane urządzenia
* Urządzenia przyłączone do domeny systemu Windows 7
* Windows 8.1 urządzeń osobistych i przyłączonych do domeny
* iOS 6 i nowsze przeglądarki Safari
* System android 4.0 lub nowszy, Samsung GS3 lub telefony nowsze, Samsung Galaxy Uwaga 2 lub nowsze tabletów

## <a name="scenario-prerequisites"></a>Warunki wstępne scenariusza
* Subskrypcja usługi Office 365 lub Azure Active Directory — wersja Premium
* Dzierżawy usługi Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 lub nowszy)
* Zaktualizowany schemat w systemie Windows Server 2012 R2
* Licencja usługi Azure Active Directory — wersja Premium
* Windows Server 2012 R2 usług federacyjnych, skonfigurowany na potrzeby logowania jednokrotnego do usługi Azure AD
* Serwer Proxy aplikacji sieci Web systemu Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(Pobieranie programu Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Zweryfikowanej domeny

## <a name="known-issues-in-this-release"></a>Znane problemy w tej wersji
* Zasady dostępu warunkowego opartego na urządzenia wymagają zapisywanie zwrotne obiektów urządzeń do usługi Active Directory z usługą Azure Active Directory. Może potrwać do trzech godzin obiekty urządzeń być zapisywane z powrotem do usługi Active Directory.
* urządzenia z systemem iOS 7 zawsze monituj użytkownika o wybranie certyfikatu podczas uwierzytelniania certyfikatu klienta.
* Niektóre wersje systemu iOS 8 przed iOS 8.3 nie działają.

## <a name="scenario-assumptions"></a>Założenia scenariusza
W tym scenariuszu założono, że środowisku hybrydowym, składające się z dzierżawy usługi Azure AD i lokalnej usługi Active Directory. Te dzierżaw powinny być połączone z programem Azure AD Connect, zweryfikowanej domeny i usług AD FS dla logowania jednokrotnego. Użyj poniższej listy kontrolnej ułatwiają konfigurowanie środowiska zgodnie z wymaganiami.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista kontrolna: Wymagania wstępne dotyczące dla scenariuszy dostępu warunkowego
Połącz dzierżawy usługi Azure AD z lokalnym wystąpieniem usługi Active Directory.

## <a name="configure-azure-active-directory-device-registration-service"></a>Konfigurowanie usługi rejestracji urządzeń usługi Azure Active Directory
Użyj tego podręcznika wdrażania i konfigurowania usługi rejestracji urządzeń usługi Azure Active Directory dla Twojej organizacji.

W tym przewodniku założono, że skonfigurowano usługi Active Directory systemu Windows Server i ma subskrypcję usługi Microsoft Azure Active Directory. Zobacz wymagania wstępne opisane wcześniej.

Wdrożenie usługi rejestracji urządzeń usługi Azure Active Directory z dzierżawą usługi Azure Active Directory, należy wykonać zadania z poniższej listy kontrolnej w kolejności. Opis łącza wprowadzającymi w koncepcje, wróć do tej listy kontrolnej później, tak, aby móc kontynuować wykonywanie kolejnych zadań. Niektóre zadania obejmują krok weryfikacji scenariusz, który ułatwia upewnij się, czy krok została ukończona pomyślnie.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Część 1: Rejestracja urządzeń Włączanie usługi Azure Active Directory
Wykonaj kroki opisane na liście kontrolnej, aby włączyć i skonfigurować usługę rejestracji urządzeń usługi Azure Active Directory.

| Zadanie | Informacje ogólne | 
| --- | --- |
| Włączanie rejestracji urządzeń w Twojej dzierżawie usługi Azure Active Directory, aby zezwolić urządzeniom na dołączenia w miejscu pracy. Domyślnie usługa Azure Multi-Factor Authentication nie jest włączone dla usługi. Jednak zaleca się korzystanie z uwierzytelniania wieloskładnikowego podczas rejestrowania urządzenia. Przed włączeniem uwierzytelniania wieloskładnikowego w usłudze rejestracji w usłudze Active Directory, upewnij się, że usługi AD FS jest skonfigurowany dla dostawcy uwierzytelniania wieloskładnikowego. |[Włączanie rejestracji urządzeń usługi Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Wykrywa urządzenia z usługi rejestracji urządzeń usługi Azure Active Directory przez wyszukiwanie dobrze znanych rekordów systemu DNS. System DNS firmy należy skonfigurować tak, aby urządzenia odnajdywania usługi rejestracji urządzeń usługi Azure Active Directory. |[Konfigurowanie odnajdywania rejestracji urządzeń usługi Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Część 2: Wdróż i skonfiguruj Active Directory Federation Services do systemu Windows Server 2012 R2 i skonfiguruj relację federacji z usługą Azure AD

| Zadanie | Informacje ogólne |
| --- | --- |
| Wdrażanie usług domenowych w usłudze Active Directory z rozszerzeniami schematu systemu Windows Server 2012 R2. Jest konieczne uaktualnienie wszystkich kontrolerów domeny do systemu Windows Server 2012 R2. Jedynym wymaganiem jest uaktualnienie schematu. |[Uaktualnienia schematu do usług domenowych w usłudze Active Directory](#upgrade-your-active-directory-domain-services-schema) |
| Wykrywa urządzenia z usługi rejestracji urządzeń usługi Azure Active Directory przez wyszukiwanie dobrze znanych rekordów systemu DNS. System DNS firmy należy skonfigurować tak, aby urządzenia odnajdywania usługi rejestracji urządzeń usługi Azure Active Directory. |[Przygotowywanie urządzenia pomocy technicznej usługi Active Directory](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Część 3: Zapisywanie zwrotne urządzeń Włącz w usłudze Azure AD
| Zadanie | Informacje ogólne |
| --- | --- |
| Zakończenie części "Włączanie zapisywania zwrotnego urządzeń w programie Azure AD Connect." Po zakończeniu pracy, wróć do tego przewodnika. |[Włączanie zapisywania zwrotnego urządzeń w programie Azure AD Connect](./connect/active-directory-aadconnect-feature-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcjonalnie] Część 4: Włączanie uwierzytelniania wieloskładnikowego
Zdecydowanie zalecane jest skonfigurowanie jednego z kilku opcji uwierzytelnianie wieloskładnikowe. Jeśli chcesz wymagać uwierzytelniania wieloskładnikowego, zobacz [wybierz rozwiązanie bezpieczeństwa uwierzytelniania wieloskładnikowego](authentication/concept-mfa-whichversion.md). Zawiera opis każdego rozwiązania i łączy ułatwiają konfigurowanie rozwiązania wybranych przez użytkownika.

## <a name="part-5-verification"></a>Część 5: Weryfikacja
Wdrażanie zostało ukończone i można wypróbować niektóre scenariusze. Użyj następujących łączy do eksperymentu w usłudze i zapoznać się z jego funkcji.

| Zadanie | Informacje ogólne |
| --- | --- |
| Dołączanie niektóre urządzenia do miejsca pracy przy użyciu usługi rejestracji urządzeń usługi Azure Active Directory. Możesz także dołączyć do systemu iOS, Windows i urządzeń z systemem Android. |[Dołączenie urządzenia do miejsca pracy przy użyciu usługi rejestracji urządzeń usługi Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Wyświetl i włączyć lub wyłączyć zarejestrowanych urządzeń za pomocą portalu administratora. To zadanie możesz wyświetlić niektórych urządzeń zarejestrowanych za pomocą portalu administratora. |[Omówienie usługi rejestracji urządzeń usługi Azure Active Directory](active-directory-device-registration-get-started.md) |
| Sprawdź, czy obiekty urządzeń są zapisywane z usługi Azure Active Directory systemu Windows Server Active Directory. |[Sprawdź, czy zarejestrowanych urządzeń są zapisywane z powrotem do usługi Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Teraz, gdy użytkownicy mogą zarejestrować swoje urządzenia, aplikację można utworzyć zasady dostępu w usługach AD FS, który zezwala tylko zarejestrowane urządzenia. To zadanie służy do tworzenia reguł dostępu do aplikacji i niestandardowy komunikat odmowy dostępu. |[Tworzenie zasad dostępu do aplikacji i niestandardowego komunikatu o odmowie dostępu](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrowanie usługi Azure Active Directory z lokalną usługą Active Directory

**Zobacz:**

- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](./connect/active-directory-aadconnect.md) — Aby przejrzeć informacje o pojęciach.

- [Instalacja niestandardowa programu Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) — instrukcje dotyczące instalacji.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Uaktualnienia schematu do usług domenowych w usłudze Active Directory
> [!NOTE]
> Po uaktualnieniu schemat usługi Active Directory, proces nie można cofnąć. Firma Microsoft zaleca, należy najpierw przeprowadzić uaktualnienie w środowisku testowym.
> 

1. Zaloguj się do kontrolera domeny przy użyciu konta, które ma uprawnienia administratora schematu i administratora przedsiębiorstwa.
2. Kopiuj **\support\adprep [media]** katalogu i jego podkatalogach do jednego z kontrolerów domeny usługi Active Directory (gdzie **[media]** to ścieżka do nośnika instalacyjnego systemu Windows Server 2012 R2).
4. W wierszu polecenia przejdź do **adprep** katalogu i uruchom **adprep.exe/forestprep**. Postępuj zgodnie z wyświetlanymi instrukcjami, aby dokończyć uaktualnianie schematu.

## <a name="prepare-your-active-directory-to-support-devices"></a>Przygotowanie usługi Active Directory do obsługi urządzeń
> [!NOTE]
> Jest to jednorazowa operacja, który należy uruchomić, aby przygotować las usługi Active Directory do obsługi urządzeń. Aby wykonać tę procedurę, użytkownik musi zalogować się przy użyciu uprawnień administratora przedsiębiorstwa i lasu usługi Active Directory musi mieć schematu systemu Windows Server 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Przygotowanie lasu usługi Active Directory
1. Na serwerze federacyjnym Otwórz okno poleceń programu Windows PowerShell, a następnie wpisz **ADDeviceRegistration zainicjować**. 
2. Po wyświetleniu monitu o **ServiceAccountName**, wprowadź nazwę konta usługi, wybrać jako konto usługi dla usług AD FS. Jeśli konto usługi zarządzane przez grupę, wprowadź konto w **domain\accountname$** format. Dla konta domeny, użyj formatu **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Włącz uwierzytelnianie urządzeń w usługach AD FS
1. Na serwerze federacyjnym, otwórz konsolę zarządzania usług AD FS i przejdź do **usług AD FS** > **zasady uwierzytelniania**.
2. Na **akcje** okienku wybierz **Edytuj globalne uwierzytelnianie podstawowe**.
3. Sprawdź **Włącz uwierzytelnianie urządzeń**, a następnie wybierz **OK**.
4. Domyślnie usługi AD FS okresowo usuwa nieużywane urządzenia z usługi Active Directory. To zadanie należy wyłączyć, gdy używasz usługi rejestracji urządzeń usługi Azure Active Directory, dzięki czemu będzie można zarządzać urządzeniami w systemie Azure.

### <a name="disable-unused-device-cleanup"></a>Wyłącz oczyszczania nieużywanych urządzeń
Na serwerze federacyjnym Otwórz okno poleceń programu Windows PowerShell, a następnie wpisz **Set AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Przygotowanie Azure AD Connect dla zapisu zwrotnego urządzeń
Wypełnia część 1: przygotowanie Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Dołączanie urządzenia do miejsca pracy przy użyciu usługi rejestracji urządzeń usługi Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Dołączanie urządzenia z systemem iOS przy użyciu rejestracji urządzeń usługi Azure Active Directory
Rejestracja urządzenia w usłudze Azure Active Directory korzysta z profilu bezprzewodowego procesu rejestracji dla urządzeń z systemem iOS. Ten proces rozpoczyna się, gdy użytkownik łączy się adres URL profilu rejestracji, z przeglądarki Safari. Format adresu URL jest następujący:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

W takim przypadku `yourdomainname` jest nazwą domeny skonfigurowaną w usłudze Azure Active Directory. Na przykład jeśli nazwa domeny to contoso.com, adres URL wygląda następująco:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Istnieje wiele różnych sposobów komunikacji ten adres URL dla użytkowników. Na przykład jedną metodę, zalecane jest publikowanie tego adresu URL w aplikacji niestandardowej komunikatu o odmowie dostępu w usługach AD FS. Informacje te są objęte w następnej sekcji [Tworzenie zasad dostępu do aplikacji i niestandardowego komunikatu o odmowie dostępu](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Dołączanie urządzenia Windows 8.1 przy użyciu rejestracji urządzeń usługi Azure Active Directory
1. Na urządzeniu Windows 8.1, wybierz **ustawienia komputera** > **sieci** > **pracy**.
2. Wprowadź nazwę użytkownika w formacie nazwy UPN. na przykład **dan@contoso.com**.
3. Wybierz **Join**.
4. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń. Urządzenie zostało przyłączone.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Dołączanie urządzenia z systemem Windows 7 przy użyciu rejestracji urządzeń usługi Azure Active Directory
Aby zarejestrować urządzenia przyłączone do domeny systemu Windows 7, należy wdrożyć [pakiet oprogramowania rejestracji urządzenia](https://www.microsoft.com/download/details.aspx?id=53554).

Aby uzyskać instrukcje dotyczące używania pakietu, zobacz [pakietów Instalatora Windows na komputerach z systemem innym niż Windows 10](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Sprawdź, czy zarejestrowanych urządzeń są zapisywane z powrotem do usługi Active Directory
Możesz wyświetlić i sprawdzić, czy obiekty urządzeń zapisano wróć do usługi Active Directory przy użyciu LDP.exe lub ADSI Edit. Oba są dostępne przy użyciu narzędzi administratora usługi Active Directory.

Domyślnie obiekty urządzeń są zapisywane z usługi Azure Active Directory są umieszczane w tej samej domenie co farmę usług AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Tworzenie zasad dostępu do aplikacji i niestandardowego komunikatu o odmowie dostępu
Rozważmy następujący scenariusz: tworzenie aplikacji zaufania jednostki uzależnionej w usługach AD FS i skonfigurować regułę autoryzacji wystawiania, który umożliwia tylko zarejestrowane urządzenia. Teraz tylko w przypadku urządzeń, które są zarejestrowane mogą uzyskać dostęp do aplikacji. 

Aby ułatwić użytkownikom uzyskać dostęp do aplikacji, należy skonfigurować niestandardowy komunikat odmowy dostępu, który zawiera instrukcje dotyczące sposobu przyłączania urządzeń. Teraz użytkownicy mają możliwość łatwego zarejestrować swoje urządzenia, tak aby uzyskać dostęp do aplikacji.

Poniższe kroki pokazują, jak wdrożyć ten scenariusz.

> [!NOTE]
> W tej sekcji założono, że została już skonfigurowana zaufania jednostki uzależnionej dla aplikacji w usługach AD FS.
> 

1. Otwórz narzędzie AD FS MMC, a następnie wybierz **usług AD FS** > **relacje zaufania** > **zaufania jednostek uzależnionych**.
2. Zlokalizuj aplikacji, którego dotyczy ta nowa reguła dostępu. Kliknij prawym przyciskiem myszy aplikację, a następnie wybierz **Edycja reguł oświadczeń**.
3. Wybierz **reguł autoryzacji wystawiania** , a następnie wybierz **Dodaj regułę**.
4. Z **reguły oświadczeń** szablonu listy rozwijanej wybierz pozycję **akceptowanie lub odrzucanie użytkowników na podstawie oświadczenia przychodzącego**. Następnie wybierz przycisk **Dalej**.
5. W **nazwy reguły oświadczeń** wpisz **zezwolenie na dostęp z urządzeń zarejestrowanych**.
6. Z **typ oświadczenia przychodzącego** listy rozwijanej wybierz **użytkownik jest zarejestrowany**.
7. W **wartość oświadczenia przychodzącego** wpisz **true**.
8. Wybierz **zezwolenie na dostęp użytkowników na podstawie tego oświadczenia przychodzącego** przycisk radiowy.
9. Wybierz **Zakończ**, a następnie wybierz **Zastosuj**.
10. Usuń wszystkie reguły, które są mniej ograniczająca niż reguły, który został utworzony. Na przykład usunąć domyślną regułę **zezwolenie na dostęp do wszystkich użytkowników**.

Aplikacja jest skonfigurowany tak, aby zezwolić na dostęp tylko wtedy, gdy użytkownik pochodzi z urządzenia zarejestrowane i dołączony w miejscu pracy. Aby dostęp do bardziej zaawansowanych zasad, zobacz [zarządzanie ryzykiem przy użyciu dodatkowego uwierzytelniania wieloskładnikowego dla aplikacji zawierających poufne dane](https://technet.microsoft.com/library/dn280949.aspx).

Następnie należy skonfigurować niestandardowy komunikat o błędzie dla aplikacji. Komunikat o błędzie powiadamia użytkowników, że ich musi zostać dołączony do swoich urządzeń w miejscu pracy przed uzyskaniem dostępu do aplikacji. Przy użyciu niestandardowego kodu HTML i programu PowerShell, można utworzyć niestandardową aplikację komunikat odmowy dostępu.

Na serwerze federacyjnym Otwórz okno poleceń programu PowerShell, a następnie wpisz następujące polecenie. Część polecenia należy zastąpić elementy, które są specyficzne dla systemu:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Należy zarejestrować urządzenie, aby korzystać z tej aplikacji.

**Jeśli używasz urządzenia z systemem iOS, wybierz ten link, aby dołączyć urządzenie**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Dołącz to urządzenie z systemem iOS do miejsca pracy.

Jeśli używasz urządzenia Windows 8.1, można dołączyć urządzenie, wybierając **ustawienia komputera**> **sieci** > **pracy**.

W powyższych poleceniach **jednostki uzależnionej Nazwa zaufania jednostki** to nazwa obiektu zaufania jednostki uzależnionej aplikacji w usługach AD FS.
I **twoja_domena.com** jest nazwą domeny, które zostały skonfigurowane w usłudze Azure Active Directory (na przykład contoso.com).
Należy usunąć wszystkie podziały wiersza (jeśli istnieją) z zawartość HTML, który jest przekazywany do **AdfsRelyingPartyWebContent zestaw** polecenia cmdlet.

Teraz, gdy użytkownicy uzyskują dostęp do aplikacji z urządzenia, które nie jest zarejestrowane w usłudze rejestracji urządzeń usługi Azure Active Directory, zostanie wyświetlona wystąpił błąd.

