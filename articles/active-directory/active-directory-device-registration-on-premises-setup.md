---
title: Konfigurowanie lokalnego dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Przewodnik krok po kroku, aby włączyć dostęp warunkowy do aplikacji lokalnych przy użyciu usługi Active Directory Federation Services (AD FS) w systemie Windows Server 2012 R2.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: b1711b86042c74eba47fe1cfa41bbbd36ceaf127
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248762"
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Konfigurowanie lokalnego dostępu warunkowego przy użyciu usługi Azure Active Directory rejestracji urządzenia

Gdy możesz wymagać od użytkowników dołączenie do miejsca pracy swoje urządzenia osobiste do usługi rejestracji urządzeń usługi Azure Active Directory (Azure AD), ich urządzeń można oznaczyć jako znane w organizacji. Oto przewodnik krok po kroku dotyczące włączania dostępu warunkowego do aplikacji lokalnych przy użyciu usługi Active Directory Federation Services (AD FS) w systemie Windows Server 2012 R2.

> [!NOTE]
> Licencja usługi Office 365 lub licencji usługi Azure AD Premium jest wymagana podczas korzystania z urządzeń, które są zarejestrowane w usłudze Azure Active Directory — zasady dostępu warunkowego usługi rejestracji urządzeń. Obejmują one zasad, które są wymuszane przez usługi AD FS w zasoby lokalne.
> 
> Aby uzyskać więcej informacji na temat scenariuszy dostępu warunkowego dla zasobów lokalnych, zobacz [dołączanie do miejsca pracy z dowolnego urządzenia logowania jednokrotnego i łatwego uwierzytelniania dwuskładnikowego w aplikacjach firmy](https://technet.microsoft.com/library/dn280945.aspx).

Te możliwości są dostępne dla klientów, którzy zakupią licencji usługi Azure Active Directory Premium systemu Azure.

## <a name="supported-devices"></a>Obsługiwane urządzenia

* Windows 7 przyłączonych do domeny urządzenia
* Windows 8.1 urządzeń osobistych, jak i przyłączone do domeny
* iOS 6 i nowsze w przeglądarce Safari
* System android 4.0 lub nowszy, Samsung GS3 lub nowsze telefonów, Samsung Galaxy Uwaga 2 lub nowszej tabletów

## <a name="scenario-prerequisites"></a>Warunki wstępne scenariusza

* Subskrypcja usługi Office 365 lub Azure Active Directory Premium
* Dzierżawa usługi Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 lub nowszy)
* Zaktualizowano schemat w systemie Windows Server 2012 R2
* Licencja usługi Azure Active Directory — wersja Premium
* Windows Server 2012 R2 Federation Services, skonfigurowane na potrzeby logowania jednokrotnego do usługi Azure AD
* Serwer Proxy aplikacji sieci Web systemu Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(Pobieranie programu Azure AD Connect)](https://www.microsoft.com/download/details.aspx?id=47594)
* Zweryfikowana domena

## <a name="known-issues-in-this-release"></a>Znane problemy w tej wersji

* Zasady dostępu warunkowego opartego na urządzeniach wymagają zapisywanie zwrotne obiektów urządzeń do usługi Active Directory z usługą Azure Active Directory. Może potrwać do trzech godzin obiekty urządzeń być zapisywane z powrotem do usługi Active Directory.
* urządzenia z systemem iOS 7 zawsze monituj użytkownika o wybranie certyfikatu podczas uwierzytelniania certyfikatu klienta.
* Niektóre wersje systemu IOS jest 8 przed 8.3 systemu iOS nie będą działać.

## <a name="scenario-assumptions"></a>Założenia scenariusza

W tym scenariuszu przyjęto założenie, iż w środowisku hybrydowym, składające się z dzierżawy usługi Azure AD i usługi Active Directory w środowisku lokalnym. Te dzierżawy powinny być połączone za pomocą usługi Azure AD Connect, zweryfikowanej domeny i usług AD FS dla logowania jednokrotnego. Aby skonfigurować środowisko zgodnie z wymaganiami, należy użyć poniższej listy kontrolnej.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista kontrolna: Warunki wstępne scenariusza dostępu warunkowego

Łączenie dzierżawy usługi Azure AD z wystąpieniem usługi Active Directory w środowisku lokalnym.

## <a name="configure-azure-active-directory-device-registration-service"></a>Konfigurowanie usługi rejestracji urządzeń w usłudze Azure Active Directory

Użyj tego przewodnika wdrażania i konfigurowania usługi rejestracji urządzeń usługi Azure Active Directory w Twojej organizacji.

W przewodniku założono, że skonfigurowano usługi Active Directory systemu Windows Server i subskrybuje usługi Microsoft Azure Active Directory. Zobacz wymagania wstępne opisane wcześniej.

Aby wdrożyć usługi Azure Active Directory urządzenia rejestracji z dzierżawą usługi Azure Active Directory, należy wykonać zadania z poniższej listy kontrolnej w kolejności. Gdy łącze odwołania przejście do tematu pojęciowego, wróć do tej listy kontrolnej w efekcie tak, aby móc kontynuować wykonywanie kolejnych zadań. Niektóre zadania obejmują kroku weryfikacji scenariusza, który pomoże Ci upewnij się, czy krok zakończyła się pomyślnie.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Część 1: Włączanie usługi Azure Active Directory urządzenia rejestracji

Wykonaj kroki z listy kontrolnej, aby włączyć i skonfigurować usługę rejestracji urządzeń usługi Azure Active Directory.

| Zadanie | Informacje ogólne | 
| --- | --- |
| Włączanie rejestracji urządzeń w Twojej dzierżawie usługi Azure Active Directory, aby umożliwić urządzeniom do dołączenia do miejsca pracy. Domyślnie usługi Azure Multi-Factor Authentication nie jest włączona dla usługi. Jednak zaleca się, że używasz uwierzytelniania wieloskładnikowego podczas rejestrowania urządzenia z systemem. Przed włączeniem uwierzytelniania wieloskładnikowego w usłudze rejestracji usługi Active Directory, upewnij się, że usług AD FS jest skonfigurowany dla dostawcy uwierzytelniania wieloskładnikowego. |[Włączanie rejestracji urządzeń w usłudze Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Urządzenia odnajdywać usługi rejestracji urządzeń usługi Azure Active Directory przez wyszukiwanie dobrze znanych rekordów systemu DNS. System DNS firmy należy skonfigurować tak, aby urządzenia mogły odnajdywać usługi rejestracji urządzeń usługi Azure Active Directory. |[Konfigurowanie odnajdywania usługi rejestracji urządzeń usługi Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Część 2: Wdrażanie i konfigurowanie systemu Windows Server 2012 R2 usług federacyjnych Active Directory oraz skonfigurować relację federacji z usługą Azure AD

| Zadanie | Informacje ogólne |
| --- | --- |
| Wdrażanie usług Active Directory Domain Services przy użyciu rozszerzeń schematu systemu Windows Server 2012 R2. Jest konieczne uaktualnienie wszystkich kontrolerów domeny do systemu Windows Server 2012 R2. Uaktualnienie schematu jest jedynym wymaganiem. |[Uaktualnienia schematu do usług domenowych Active Directory](#upgrade-your-active-directory-domain-services-schema) |
| Urządzenia odnajdywać usługi rejestracji urządzeń usługi Azure Active Directory przez wyszukiwanie dobrze znanych rekordów systemu DNS. System DNS firmy należy skonfigurować tak, aby urządzenia mogły odnajdywać usługi rejestracji urządzeń usługi Azure Active Directory. |[Przygotuj urządzenie pomocy technicznej usługi Active Directory](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Część 3: Włącz urządzenia zapisu w usłudze Azure AD

| Zadanie | Informacje ogólne |
| --- | --- |
| Wykonaj drugiej części "Włączanie zapisywania zwrotnego urządzeń w usłudze Azure AD Connect." Po zakończeniu pracy, wróć do tego przewodnika. |[Włączanie zapisywania zwrotnego urządzeń w programie Azure AD Connect](hybrid/how-to-connect-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcjonalnie] Część 4: Włącz uwierzytelnianie wieloskładnikowe

Zdecydowanie zaleca się skonfigurować jeden z kilku opcji uwierzytelniania Multi-Factor Authentication. Jeśli chcesz wymagać uwierzytelniania wieloskładnikowego, zobacz [wybierz rozwiązania zabezpieczeń uwierzytelniania wieloskładnikowego](authentication/concept-mfa-whichversion.md). Zawiera opis każdego rozwiązania i linki do pomocne w konfigurowaniu wybranego rozwiązania.

## <a name="part-5-verification"></a>Część 5: Weryfikacja

Wdrożenie jest teraz ukończona i możesz wypróbować niektóre scenariusze. Użyj następujących linków, aby eksperymentować z usługą i zapoznanie się z jej funkcjami.

| Zadanie | Informacje ogólne |
| --- | --- |
| Dołącz do niektórych urządzeń do miejsca pracy przy użyciu usługi rejestracji urządzeń w usłudze Azure Active Directory. Możesz dołączyć systemów iOS, Windows i urządzeń z systemem Android. |[Dołączać urządzenia do miejsca pracy przy użyciu usługi rejestracji urządzeń w usłudze Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Wyświetlanie i włączyć lub wyłączyć zarejestrowanych urządzeń, korzystając z portalu administratora. W ramach tego zadania możesz wyświetlić niektórych urządzeń zarejestrowanych za pomocą portalu administratora. |[Usługa Azure Active Directory urządzenia rejestracji Omówienie usługi](active-directory-device-registration-get-started.md) |
| Sprawdź, czy obiekty urządzeń są zapisywane z usługi Azure Active Directory do usługi Active Directory systemu Windows Server. |[Upewnij się, że zarejestrowane urządzenia będą zwrotnie zapisywane w usłudze Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Teraz, gdy użytkownicy mogą rejestrować swoje urządzenia, aplikację można utworzyć zasady dostępu w usługach AD FS, dzięki czemu tylko zarejestrowanym urządzeniom. To zadanie służy do tworzenia reguł dostępu do aplikacji i niestandardowy komunikat Odmowa dostępu. |[Tworzenie zasad dostępu do aplikacji i niestandardowy komunikat odmowy dostępu](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrowanie usługi Azure Active Directory z usługą Active Directory w środowisku lokalnym

**Zobacz:**

- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](hybrid/whatis-hybrid-identity.md) — Aby przejrzeć informacje o pojęciach.

- [Instalację niestandardową programu Azure AD Connect](hybrid/how-to-connect-install-custom.md) — Aby uzyskać instrukcje instalacji.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Uaktualnienia schematu do usług domenowych Active Directory

> [!NOTE]
> Po uaktualnieniu schemat usługi Active Directory nie można cofnąć ten proces. Zaleca się najpierw przeprowadzić uaktualnienie w środowisku testowym.
> 

1. Zaloguj się do kontrolera domeny za pomocą konta mającego uprawnienia administratora schematu i administratora przedsiębiorstwa.
1. Kopiuj **\support\adprep [media]** katalogu i podkatalogach na jeden z kontrolerów domeny usługi Active Directory (gdzie **[media]** jest ścieżką do nośnika instalacyjnego systemu Windows Server 2012 R2).
1. W wierszu polecenia przejdź do **adprep** katalogu i uruchom **adprep.exe/forestprep**. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby ukończyć uaktualnienie schematu.

## <a name="prepare-your-active-directory-to-support-devices"></a>Przygotowanie usługi Active Directory do obsługi urządzeń

> [!NOTE]
> Jest to jednorazowa operacja, który należy uruchomić, aby przygotować las usługi Active Directory do obsługi urządzeń. Aby wykonać tę procedurę, użytkownik musi być zalogowany przy użyciu uprawnień administratora przedsiębiorstwa, a las usługi Active Directory musi mieć schematu systemu Windows Server 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Przygotowanie lasu usługi Active Directory

1. Na serwerze federacyjnym, Otwórz okno poleceń programu Windows PowerShell, a następnie wpisz **Initialize ADDeviceRegistration**. 
1. Po wyświetleniu monitu o **ServiceAccountName**, wprowadź nazwę konta usługi, wybrana jako konto usługi dla usług AD FS. Jeśli jest konta gMSA, wprowadź konto w **domain\accountname$** formatu. Dla konta domeny, użyj formatu **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Włącz uwierzytelnianie urządzeń w usługach AD FS

1. Na serwerze federacyjnym, otwórz konsolę zarządzania usług AD FS, a następnie przejdź do **usług AD FS** > **zasady uwierzytelniania**.
1. Na **akcje** okienku wybierz **Edytuj globalne uwierzytelniania podstawowego**.
1. Sprawdź **Włącz uwierzytelnianie urządzeń**, a następnie wybierz pozycję **OK**.
1. Domyślnie usług AD FS okresowo usuwa nieużywane urządzenia z usługi Active Directory. To zadanie należy wyłączyć, gdy używasz usługi rejestracji urządzeń w usłudze Azure Active Directory, dzięki czemu będzie można zarządzać urządzeniami w systemie Azure.

### <a name="disable-unused-device-cleanup"></a>Wyłącz czyszczenie nieużywanych urządzeń

Na serwerze federacyjnym, Otwórz okno poleceń programu Windows PowerShell, a następnie wpisz **Set AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Przygotowywanie usługi Azure AD Connect do zapisywania zwrotnego urządzeń

Wypełnij część 1: Przygotowanie usługi Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Dołączanie urządzenia do miejsca pracy przy użyciu usługi rejestracji urządzeń w usłudze Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Dołączanie urządzenia z systemem iOS przy użyciu usługi Azure Active Directory rejestracji urządzenia

Rejestracja urządzenia w usłudze Azure Active Directory korzysta z profilu bezprzewodowego procesu rejestracji dla urządzeń z systemem iOS. Ten proces rozpoczyna się, gdy użytkownik podłącza się do profilu rejestracji adresu URL w przeglądarce Safari. Format adresu URL jest następujący:

`https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/<yourdomainname>`

W tym przypadku `yourdomainname` jest nazwą domeny, który został skonfigurowany w usłudze Azure Active Directory. Na przykład jeśli nazwa domeny to contoso.com, adres URL następująco:

`https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com`

Istnieje wiele różnych sposobów informację o adresie URL dla użytkowników. Na przykład jedną metodę, którą firma Microsoft zaleca jest publikowanie tego adresu URL w aplikacji niestandardowej komunikat odmowy dostępu w usługach AD FS. Te informacje są omówione w następnej sekcji [Tworzenie zasad dostępu do aplikacji i niestandardowy komunikat odmowy dostępu](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Przyłącz urządzenie Windows 8.1 za pomocą usługi Azure Active Directory rejestracji urządzenia

1. Na urządzeniu z systemem Windows 8.1, wybierz **ustawienia komputera** > **sieci** > **pracy**.
1. Wprowadź swoją nazwę użytkownika w formacie UPN. na przykład **dan@contoso.com**.
1. Wybierz **Dołącz**.
1. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń. Urządzenie zostało przyłączone.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Przyłącz urządzenie Windows 7 przy użyciu usługi Azure Active Directory rejestracji urządzenia

Aby zarejestrować urządzenia przyłączone do domeny Windows 7, należy wdrożyć [pakiet oprogramowania rejestracji urządzenia](https://www.microsoft.com/download/details.aspx?id=53554).

Aby uzyskać instrukcje dotyczące sposobu korzystania z pakietu, zobacz [pakietów Instalatora Windows na komputerach bez systemu Windows 10](devices/hybrid-azuread-join-control.md#control-windows-down-level-devices).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Sprawdź, że zarejestrowane urządzenia będą zwrotnie zapisywane w usłudze Active Directory

Można wyświetlić i sprawdzić, czy obiekty urządzeń zapisanych powrót do usługi Active Directory przy użyciu LDP.exe lub ADSI Edit. Obie są dostępne przy użyciu narzędzia administratora usługi Active Directory.

Domyślnie obiekty urządzeń, które są zapisywane z usługi Azure Active Directory są umieszczane w tej samej domenie co farmie usług AD FS.

`CN=RegisteredDevices,defaultNamingContext`

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Tworzenie zasad dostępu do aplikacji i niestandardowy komunikat odmowy dostępu

Rozważmy następujący scenariusz: tworzenie aplikacji jednostki uzależnionej relacja zaufania w usługach AD FS i skonfigurować regułę autoryzacji wystawiania, który umożliwia tylko zarejestrowanym urządzeniom. Teraz tylko te urządzenia, które są zarejestrowane mogą uzyskiwać dostęp do aplikacji. 

Aby ułatwić użytkownikom w celu uzyskania dostępu do aplikacji, należy skonfigurować niestandardowy komunikat odmowy dostępu, który zawiera instrukcje dotyczące sposobu przyłączania urządzenia. Teraz użytkownicy mają swobodne rejestrowanie swoich urządzeń, dzięki czemu mogą uzyskać dostęp do aplikacji.

Poniższe kroki pokazują, jak wdrożyć ten scenariusz.

> [!NOTE]
> W tej sekcji założono, że już skonfigurowano relację zaufania jednostki uzależnionej dla aplikacji w usługach AD FS.
> 

1. Otwórz narzędzie AD FS w programie MMC, a następnie wybierz **usług AD FS** > **relacje zaufania** > **zaufania jednostek uzależnionych**.
1. Odszukaj aplikację, do którego ma zastosowanie tej nowej reguły dostępu. Kliknij prawym przyciskiem myszy aplikację, a następnie wybierz **Edycja reguł oświadczeń**.
1. Wybierz **reguły autoryzacji wystawiania** , a następnie wybierz pozycję **Dodaj regułę**.
1. Z **reguła oświadczenia** szablonu listy rozwijanej wybierz pozycję **zezwolenia lub odrzucanie użytkowników oparte na oświadczenia przychodzącego**. Następnie wybierz przycisk **Dalej**.
1. W **Nazwa reguły oświadczeń** wpisz **zezwolenie na dostęp z urządzeń zarejestrowanych**.
1. Z **typ oświadczenia przychodzące** listy rozwijanej wybierz **użytkownik jest zarejestrowany**.
1. W **wartości oświadczenia przychodzące** wpisz **true**.
1. Wybierz **zezwolić na dostęp do użytkowników na podstawie tego oświadczenia przychodzącego** przycisku radiowego.
1. Wybierz **Zakończ**, a następnie wybierz pozycję **Zastosuj**.
1. Usuń wszystkie reguły, które są mniej ograniczająca niż reguły, który został utworzony. Na przykład usunąć domyślną regułę **zezwolić na dostęp do wszystkich użytkowników**.

Aplikacja jest skonfigurowany tak, aby zezwolić na dostęp tylko wtedy, gdy użytkownik pochodzi z urządzenia, które są zarejestrowane i przyłączone do miejsca pracy. Aby uzyskać dostęp do bardziej zaawansowanych zasad, zobacz [zarządzanie ryzykiem przy użyciu dodatkowego uwierzytelniania wieloskładnikowego w przypadku aplikacji poufnych](https://technet.microsoft.com/library/dn280949.aspx).

Następnie należy skonfigurować niestandardowy komunikat o błędzie dla danej aplikacji. Komunikat o błędzie informuje użytkowników o tym, że dołączenie do swojego urządzenia w miejscu pracy przed uzyskaniem dostępu do aplikacji. Stosowanie niestandardowy komunikat odmowy dostępu można utworzyć przy użyciu niestandardowego kodu HTML i programu PowerShell.

Na serwerze federacyjnym Otwórz okno poleceń programu PowerShell, a następnie wpisz następujące polecenie. Zastąp fragmenty polecenia elementy, które są specyficzne dla systemu:

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage`

Należy zarejestrować urządzenie, aby korzystać z tej aplikacji.

**Jeśli używasz urządzenia z systemem iOS, wybierz ten link, aby dołączyć urządzenie**:

`https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com`

Dołącz to urządzenie z systemem iOS do miejsca pracy.

Jeśli używasz urządzenia Windows 8.1 można dołączyć urządzenie, wybierając **ustawienia komputera**> **sieci** > **pracy**.

W poprzednich poleceniach **Nazwa zaufania jednostki uzależnionej** jest nazwą obiektu jednostki uzależnionej zaufanie Twojej aplikacji w usługach AD FS.
I **yourdomain.com** jest nazwą domeny skonfigurowaną w usłudze Azure Active Directory (np. contoso.com).
Pamiętaj usunąć wszystkie podziały wiersza (jeśli istnieje) z zawartość HTML, który jest przekazywany do **AdfsRelyingPartyWebContent zestaw** polecenia cmdlet.

Teraz gdy użytkownicy uzyskują dostęp aplikacji z urządzenia, która nie jest zarejestrowana przy użyciu usługi rejestracji urządzeń w usłudze Azure Active Directory, zobaczą błąd.
