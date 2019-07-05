---
title: Jak zaplanować wdrożenie przyłączanie do usługi Azure Active Directory (Azure AD) | Dokumentacja firmy Microsoft
description: Wyjaśniono, że urządzenia przyłączone do kroków, które są wymagane do wdrożenia usługi Azure AD w środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36429feed99c421984ed55d4e506954aa30f0040
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482126"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Instrukcje: Planowanie implementacji dołączania do usługi Azure AD

Przyłączanie do usługi Azure AD można dołączać urządzenia bezpośrednio do usługi Azure AD bez konieczności dołączania do usługi Active Directory w środowisku lokalnym przy jednoczesnym zachowaniu użytkownikom w wydajnej i bezpiecznej pracy. Azure AD join jest gotowa do użycia w przedsiębiorstwie zarówno na dużą skalę, jak i wdrożeń o określonym zakresie.   

Ten artykuł zawiera informacje potrzebne do planowania implementacji usługi Azure AD join.
 
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że czytelnik zna [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować wdrożenie usługi Azure AD join, należy się zapoznać z:

|   |   |
|---|---|
|![Sprawdź][1]|Przegląd scenariuszy|
|![Sprawdź][1]|Przejrzyj swoją infrastrukturę tożsamości|
|![Sprawdź][1]|Ocena zarządzania urządzeniami|
|![Sprawdź][1]|Omówienie zagadnień dotyczących aplikacji i zasobów|
|![Sprawdź][1]|Opis opcji inicjowania obsługi administracyjnej|
|![Sprawdź][1]|Konfigurowanie roaming stanu dla przedsiębiorstw|
|![Sprawdź][1]|Konfigurowanie dostępu warunkowego|

## <a name="review-your-scenarios"></a>Przegląd scenariuszy 

Gdy hybrydowe przyłączanie do usługi Azure AD może być preferowane w przypadku niektórych scenariuszy, dołączania do usługi Azure AD umożliwia przejście do modelu chmury z Windows. Jeśli planowane jest modernizuj zarządzania urządzeniami i koszty związane z urządzeniami, dołączania do usługi Azure AD zapewnia doskonałą podstawę na osiągnięcie tych celów.  
 
Należy wziąć pod uwagę dołączania do usługi Azure AD, jeśli Twoje cele wyrównane z następującymi kryteriami:

- Microsoft 365 jako pakiet do zarządzania produktywnością adaptują dla użytkowników.
- Chcesz zarządzać urządzeniami za pomocą rozwiązania do zarządzania urządzeniami chmury.
- Chcesz uprościć Inicjowanie obsługi administracyjnej urządzeń dla użytkowników geograficznie rozproszonych.
- Zaplanuj modernizowanie infrastruktury aplikacji.

## <a name="review-your-identity-infrastructure"></a>Przejrzyj swoją infrastrukturę tożsamości  

Przyłączanie do usługi Azure AD działa w środowiskach, zarządzane i federacyjnego.  

### <a name="managed-environment"></a>Zarządzane środowisko

Zarządzane środowisko może być wdrożone za pośrednictwem [synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) lub [przekazać za pośrednictwem uwierzytelniania](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) za pomocą bezproblemowego logowania jednokrotnego.

Te scenariusze nie wymagają skonfigurowania serwera federacyjnego uwierzytelniania.

### <a name="federated-environment"></a>Środowisku federacyjnym

Środowisku federacyjnym powinna mieć dostawcę tożsamości, który obsługuje zarówno protokołu WS-Trust i WS-Fed:

- **WS-Fed:** Ten protokół jest wymagany do przyłączania urządzenia do usługi Azure AD.
- **WS-Trust:** Ten protokół jest wymagana do logowania się na urządzeniu dołączonym do usługi Azure AD. 

Jeśli dostawca tożsamości nie obsługuje tych protokołów, dołączania do usługi Azure AD nie działa natywnie. Począwszy od systemu Windows 10 1809 użytkownicy zalogować się do urządzenia połączonego z usługi Azure AD za pomocą dostawcy tożsamości opartej na SAML za pośrednictwem [sieci web logowania w systemie Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Obecnie logowanie w sieci web jest tylko do wersji zapoznawczej funkcji.

### <a name="smartcards-and-certificate-based-authentication"></a>Karty inteligentne i uwierzytelniania opartego na certyfikatach

Nie można używać kart inteligentnych lub uwierzytelniania opartego na certyfikatach, dołączać urządzenia do usługi Azure AD. Jednak kart inteligentnych może służyć do logowania się na urządzeniach przyłączonych do usługi Azure AD, w przypadku usług AD FS skonfigurowane.

**Zalecenie:** Implementowanie Windows Hello dla firm dla uwierzytelniania silne, bez hasła na urządzeniach z systemem Windows 10.

### <a name="user-configuration"></a>Konfiguracja użytkownika

Jeśli tworzysz użytkowników w sieci:

- **Lokalnej usługi Active Directory**, należy zsynchronizować je z usługą Azure AD przy użyciu [program Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Usługa Azure AD**, żadna dodatkowa konfiguracja jest wymagana.

Lokalnej nazwy UPN, które różnią się od usługi Azure AD nazwy UPN nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Jeśli użytkownicy korzystają lokalnymi nazwami UPN, należy zaplanować przełączyć się do korzystania z adresu podstawowego nazwy UPN w usłudze Azure AD.

## <a name="assess-your-device-management"></a>Ocena zarządzania urządzeniami

### <a name="supported-devices"></a>Obsługiwane urządzenia

Funkcja Azure AD join:

- Dotyczy tylko urządzeń z systemem Windows 10. 
- Nie ma zastosowania do poprzednich wersji systemu Windows lub innych systemów operacyjnych. Jeśli masz urządzenia z systemem Windows 7/8.1, należy uaktualnić do systemu Windows 10 do wdrożenia usługi Azure AD join.
- Nie jest obsługiwana na urządzeniach przy użyciu modułu TPM w trybie FIPS.
 
**Zalecenie:** Zawsze używaj najnowszej wersji systemu Windows 10, aby korzystać ze zaktualizowanych funkcji.

### <a name="management-platform"></a>Platforma zarządzania

Zarządzanie urządzeniami dla urządzeń przyłączonych do usługi Azure AD opiera się na platformie zarządzania urządzeniami Przenośnymi, takich jak usługa Intune i zarządzania urządzeniami Przenośnymi dostawców usług kryptograficznych. Windows 10 ma wbudowanego agenta MDM, który działa we wszystkich rozwiązaniach MDM zgodne.

> [!NOTE]
> Zasady grupy nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD, ponieważ nie są one połączone z usługi Active Directory w środowisku lokalnym. Zarządzanie urządzeniami dołączonymi do usługi Azure AD jest możliwe tylko w za pomocą oprogramowania MDM

Na urządzeniach przyłączonych do zarządzania usługą Azure AD, dostępne są dwie opcje:

- **Tylko do zarządzania urządzeniami Przenośnymi** — urządzenie wyłącznie jest zarządzana przez dostawcę zarządzania urządzeniami Przenośnymi, takiej jak Intune. Wszystkie zasady są dostarczane jako część procesu rejestracji zarządzania urządzeniami Przenośnymi. W przypadku klientów usługi Azure AD Premium lub pakietu EMS rejestracji w rozwiązaniu MDM jest zautomatyzowane krok, który jest częścią usługi Azure AD join.
- **Współzarządzanie** — urządzenie jest zarządzane przez dostawcy usług MDM i SCCM. W tym podejściu na urządzenia zarządzane przez system MDM do administrowania niektóre aspekty zainstalowano agenta programu SCCM.

Jeśli używasz zasad grupy, ocenić z parzystością zasad zarządzania urządzeniami Przenośnymi za pomocą [MDM migracji analizy narzędzie (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Przejrzyj obsługiwane i nieobsługiwane zasady, aby ustalić, czy rozwiązanie do zarządzania urządzeniami Przenośnymi można użyć zamiast zasad grupy. Nieobsługiwana zasad należy rozważyć następujące kwestie:

- Nieobsługiwana zasad są niezbędne, dla dołączonych do usługi Azure AD, urządzeń lub użytkowników?
- Nieobsługiwana zasady mają zastosowanie w chmurze oparte na wdrożenia?

Jeśli rozwiązania MDM nie jest dostępna za pośrednictwem galerii aplikacji Azure AD, możesz dodać go zgodnie z procedurą opisaną w [integracji usługi Azure Active Directory z zarządzaniem urządzeniami Przenośnymi](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Za pomocą współzarządzania programu SCCM służy również do zarządzania niektórymi aspektami urządzeń, podczas gdy zasady są dostarczane za pośrednictwem platformy zarządzania urządzeniami Przenośnymi. Microsoft Intune umożliwia wspólne zarządzanie za pomocą programu SCCM. Aby uzyskać więcej informacji, zobacz [współzarządzania dla systemu Windows 10 urządzenia](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Jeśli używasz produktu zarządzania urządzeniami Przenośnymi innego niż usługi Intune, skontaktuj się z dostawcą zarządzania urządzeniami Przenośnymi na scenariuszach stosowanych współzarządzania.

**Zalecenie:** Tylko urządzenia przyłączone do zarządzania dla usługi Azure AD, należy wziąć pod uwagę zarządzania urządzeniami Przenośnymi.

## <a name="understand-considerations-for-applications-and-resources"></a>Omówienie zagadnień dotyczących aplikacji i zasobów

Zalecamy przeprowadzenie migracji aplikacji ze środowiska lokalnego do chmury dla użytkownika lepsze środowiska i kontroli dostępu. Jednak do usługi Azure AD urządzenia bezproblemowo można zapewnić dostęp do, zarówno w lokalnych, jak i w chmurze aplikacji. Aby uzyskać więcej informacji, zobacz [jak logowanie Jednokrotne do lokalnych zasobów działa w usłudze Azure AD urządzenia przyłączone do](azuread-join-sso.md).

Poniższe sekcje zawierają informacje dotyczące różnych typów aplikacji i zasobów.

### <a name="cloud-based-applications"></a>Aplikacje oparte na chmurze

Jeśli aplikacja zostanie dodany do galerii aplikacji Azure AD, użytkownicy otrzymują logowania jednokrotnego za pośrednictwem urządzeń przyłączonych do usługi Azure AD. Dodatkowa konfiguracja nie jest wymagana. Użytkownicy otrzymują logowania jednokrotnego w, Microsoft Edge i przeglądarki Chrome. Dla programu Chrome, musisz wdrożyć [rozszerzenie systemu Windows 10 kont](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Wszystkie aplikacje Win32 który:

- Dla żądania tokenu również uzyskać logowanie Jednokrotne na urządzeniach przyłączonych do usługi Azure AD są oparte na sieci Web Menedżera konta (WAM). 
- Nie należy polegać na WAM może monitować użytkowników na potrzeby uwierzytelniania. 

### <a name="on-premises-web-applications"></a>Aplikacje sieci web w środowisku lokalnym

Jeśli Twoje aplikacje są niestandardowe skompilowane i/lub hostowanych lokalnie, należy dodać je do zaufanych witryn w przeglądarce do:

- Włącz zintegrowane uwierzytelnianie Windows do pracy 
- Udostępnia środowisko logowania jednokrotnego nie wiersza dla użytkowników. 

Jeśli używasz usług AD FS, zobacz [Sprawdź i zarządzanie nimi logowanie jednokrotne za pomocą usług AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Zalecenie:** Należy wziąć pod uwagę hostingu w chmurze (na przykład Azure) oraz integracji z usługą Azure AD, aby uzyskać lepsze efekty.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Opierając się na starszych protokołów aplikacji lokalnych

Użytkownicy pobierają logowania jednokrotnego z usługi Azure AD przyłączone do urządzenia, jeśli urządzenie ma dostęp do kontrolera domeny. 

**Zalecenie:** Wdrażanie [serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Aby włączyć bezpieczny dostęp do tych aplikacji.

### <a name="on-premises-network-shares"></a>Udziały sieciowe w środowisku lokalnym

Użytkownicy mają rejestracji Jednokrotnej z urządzeń przyłączonych do usługi Azure AD, jeśli urządzenie ma dostęp do kontrolera domeny w środowisku lokalnym.

### <a name="printers"></a>Drukarki

W przypadku drukarek, zajdzie potrzeba wdrożenia [chmura hybrydowa wydruku](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) na urządzeniach przyłączonych do wykrywania drukarek w usłudze Azure AD. 

Gdy drukarki nie może automatycznie odnalezione w środowisku tylko na chmurze, użytkownicy umożliwia również ścieżkę UNC drukarki dodać bezpośrednio. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplikacje lokalne, opierając się na uwierzytelnianie komputera

Urządzenia usługi Azure AD przyłączone nie obsługują aplikacje lokalne, opierając się na uwierzytelnianie komputera. 

**Zalecenie:** Należy wziąć pod uwagę wycofania tych aplikacji i przeniesienie do ich nowoczesnych rozwiązań alternatywnych.

### <a name="remote-desktop-services"></a>Usługi pulpitu zdalnego

Podłączanie pulpitu zdalnego na urządzeniach przyłączonych do usługi Azure AD wymaga komputer hosta musi być albo dołączone do usługi Azure AD lub hybrydowe przyłączone do usługi Azure AD. Pulpit zdalny z urządzenia z systemem odłączony lub inne niż Windows nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [komputera przyłączonego do Connect do zdalnej usługi Azure AD](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Opis opcji inicjowania obsługi administracyjnej

Możesz aprowizować usługi Azure AD join, korzystając z następujących metod:

- **Samoobsługowe w trybie OOBE/ustawienia** — w trybie samoobsługi użytkowników z rzeczywistym użyciem za pośrednictwem usługi Azure AD join albo w ramach Windows poza pole środowiska (OOBE) lub z Windows ustawienia procesów. Aby uzyskać więcej informacji, zobacz [Dołączanie urządzenia pracy z siecią organizacji](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Rozwiązania Windows Autopilot** — rozwiązania Windows Autopilot umożliwia wstępnej konfiguracji urządzeń płynne w OOBE do wykonania sprzężenia Azure AD. Aby uzyskać więcej informacji, zobacz [Przegląd rozwiązania Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Rejestracja zbiorcza** — rejestracja zbiorcza umożliwia sprzężenie administratora oparte na usłudze Azure AD za pomocą zbiorczego inicjowania obsługi administracyjnej narzędzia do konfigurowania urządzeń. Aby uzyskać więcej informacji, zobacz [rejestracji zbiorczej dla urządzeń Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
Poniżej znajduje się porównanie tych trzech metod. 
 
|   | Instalator samoobsługi | Rozwiązania Windows Autopilot | Rejestrowanie zbiorcze |
| --- | --- | --- | --- |
| Wymagaj interakcji użytkownika, aby skonfigurować | Tak | Yes | Nie |
| Wymaga IT | Nie | Yes | Tak |
| Zastosowanie przepływów | Ustawienia & OOBE | Tylko w trybie OOBE | Tylko w trybie OOBE |
| Prawa administratora lokalnego do podstawowego użytkownika | Tak, domyślnie | Można konfigurować | Nie |
| Wymagana jest obsługa urządzeń OEM | Nie | Yes | Nie |
| Obsługiwane wersje | 1511+ | 1709+ | 1703+ |
 
Wybierz swoje metody wdrożenia lub metody recenzowania powyższej tabeli i przeglądając następujące zagadnienia dotyczące wdrażania każda z tych metod:  

- Twoje tech użytkownicy są pomysłowy przechodzić przez Instalatora samodzielnie? 
   - Samoobsługa można najlepszej metody dla tych użytkowników. Należy wziąć pod uwagę rozwiązania Windows Autopilot, aby ulepszyć środowisko użytkownika.  
- Czy użytkownicy zdalnego lub w ramach lokalnej firmy? 
   - Samoobsługa lub najlepszym rozwiązaniem dla użytkowników zdalnych, w przypadku instalacji Bezproblemowa praca rozwiązania Autopilot. 
- Wolisz konfiguracji usługi zarządzane przez administratora lub użytkownika na podstawie? 
   - Rejestracja zbiorcza sprawdzi się najlepiej w administratora oparte na wdrożenia, aby skonfigurować urządzenia przed przekazaniem użytkowników.     
- Czy kupują urządzenia od producentów OEM 1-2, czy masz szeroką dystrybucję OEM urządzenia?  
   - Zakup od producentów OEM ograniczone, którzy obsługują także rozwiązania Autopilot, możesz korzystać z ściślejszą integrację z rozwiązaniem Autopilot. 

## <a name="configure-your-device-settings"></a>Skonfiguruj ustawienia urządzenia

Witryna Azure portal umożliwia kontrolę wdrożenia usługi Azure AD urządzenia przyłączone do Twojej organizacji. Aby skonfigurować powiązane ustawienia na **strony usługi Azure Active Directory**, wybierz opcję `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Użytkownicy mogą dołączać urządzania do usługi Azure AD

Ustaw tę opcję na **wszystkich** lub **wybrane** na podstawie zakresu wdrożenia i który ma otrzymać zezwolenie na konfigurowanie usługi Azure AD połączonych urządzeń. 

![Użytkownicy mogą dołączać urządzania do usługi Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Urządzenia przyłączone do dodatkowych administratorów lokalnych w usłudze Azure AD

Wybierz **wybrane** i wybiera użytkowników, które chcesz dodać do grupy Administratorzy lokalni na urządzeniach przyłączonych do wszystkich Azure AD. 

![Urządzenia przyłączone do dodatkowych administratorów lokalnych w usłudze Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń

Wybierz **"tak** w razie potrzeby użytkowników do wykonywania uwierzytelniania Wieloskładnikowego podczas przyłączania urządzenia do usługi Azure AD. Dla użytkowników urządzeń łączących się z usługą Azure AD przy użyciu usługi MFA samo urządzenie staje się 2nd factor.

![Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Konfiguruj ustawienia usługi mobilności

Aby można było skonfigurować ustawienia usługi mobilności, może być konieczne najpierw Dodaj dostawcę zarządzania urządzeniami Przenośnymi.

**Aby dodać dostawcę zarządzania urządzeniami Przenośnymi**:

1. Na **strony usługi Azure Active Directory**w **Zarządzaj** kliknij `Mobility (MDM and MAM)`. 
1. Kliknij przycisk **Dodaj aplikację**.
1. Wybierz dostawcę zarządzania urządzeniami Przenośnymi z listy.

   ![Dodawanie aplikacji](./media/azureadjoin-plan/04.png)

Wybierz dostawcę zarządzania urządzeniami Przenośnymi, skonfigurować powiązane ustawienia. 

### <a name="mdm-user-scope"></a>Zakres użytkownika oprogramowania MDM

Wybierz **niektóre** lub **wszystkich** na podstawie zakresu wdrożenia. 

![Zakres użytkownika oprogramowania MDM](./media/azureadjoin-plan/05.png)

Oparte na zakresie Twoich obowiązków, jedną z następujących sytuacji: 

- **Użytkownik, który znajduje się w zakresie zarządzania urządzeniami Przenośnymi**: Jeśli masz subskrypcję usługi Azure AD Premium, rejestracji w rozwiązaniu MDM jest zautomatyzowany wraz z usługi Azure AD join. Wszyscy użytkownicy o określonym zakresie muszą mieć odpowiednią licencję dla usługi zarządzania urządzeniami przenośnymi. W przypadku niepowodzenia rejestracji w rozwiązaniu MDM w tym scenariuszu dołączania do usługi Azure AD zostanie również wycofana.
- **Użytkownik nie jest w zakresie zarządzania urządzeniami Przenośnymi**: Jeśli użytkownicy nie są w zakresie zarządzania urządzeniami Przenośnymi, usługi Azure AD join kończy się bez rejestracji w rozwiązaniu MDM. Skutkuje to niezarządzanego urządzenia.

### <a name="mdm-urls"></a>Adresy URL zarządzania urządzeniami Przenośnymi

Istnieją trzy adresy URL, które są powiązane z konfiguracji zarządzania urządzeniami Przenośnymi:

- Zarządzania urządzeniami Przenośnymi adres URL warunków użytkowania
- Adres URL odnajdywania zarządzania urządzeniami Przenośnymi 
- Adres URL zgodności oprogramowania MDM

![Dodawanie aplikacji](./media/azureadjoin-plan/06.png)

Każdy adres URL ma wartość domyślną wstępnie zdefiniowane. Jeśli te pola są puste, skontaktuj się z dostawcą zarządzania urządzeniami Przenośnymi, aby uzyskać więcej informacji.

### <a name="mam-settings"></a>Ustawienia zarządzania aplikacjami Mobilnymi

Zarządzanie aplikacjami Mobilnymi nie ma zastosowania do dołączania do usługi Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurowanie roaming stanu dla przedsiębiorstw

Jeśli chcesz włączyć roaming stanu do usługi Azure AD, dzięki czemu użytkownicy mogą synchronizować swoje ustawienia między urządzeniami, zobacz [Włącz Enterprise stanu mobilnych w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable). 

**Zalecenie**: Włącz to ustawienie, nawet w przypadku urządzeń przyłączonych do usługi Azure AD hybrydowych.

## <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

W przypadku urządzeń przyłączonych do dostawcy usług MDM skonfigurowany dla usługi Azure AD, dostawca flagi urządzenie jako zgodne, jak urządzenie znajduje się w obszarze Zarządzanie. 

![Zgodne urządzenie](./media/azureadjoin-plan/46.png)

Można użyć tej implementacji, aby [wymagają zarządzanych urządzeń do uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dołączanie urządzenia z systemem Windows 10 nowe za pomocą usługi Azure AD podczas pierwszego uruchomienia](azuread-joined-devices-frx.md)
> [Dołącz swoje urządzenie pracy do sieci organizacji](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
