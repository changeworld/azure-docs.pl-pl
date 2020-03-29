---
title: Jak zaplanować implementację dołączania do usługi Azure Active Directory
description: W tym artykule wyjaśniono kroki, które są wymagane do zaimplementowania urządzeń przyłączonych do usługi Azure AD w twoim środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bbecf0e365ba7a8424da775245181fa64c21f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672694"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Jak: Planowanie implementacji dołączania usługi Azure AD

Sprzężenie usługi Azure AD umożliwia dołączanie urządzeń bezpośrednio do usługi Azure AD bez konieczności dołączania do lokalnej usługi Active Directory przy jednoczesnym zachowaniu wydajności i bezpieczeństwa użytkowników. Sprzężenie usługi Azure AD jest gotowe do pracy w przedsiębiorstwie zarówno dla wdrożeń na dużą skalę, jak i z zakresem.   

Ten artykuł zawiera informacje potrzebne do planowania implementacji dołączania usługi Azure AD.
 
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Zaplanuj swoją implementację

Aby zaplanować implementację dołączania usługi Azure AD, należy zapoznać się z:

|   |   |
|---|---|
|![Zaznacz][1]|Przejrzyj scenariusze|
|![Zaznacz][1]|Przejrzyj swoją infrastrukturę tożsamości|
|![Zaznacz][1]|Ocena zarządzania urządzeniami|
|![Zaznacz][1]|Opis zagadnień dotyczących aplikacji i zasobów|
|![Zaznacz][1]|Opis opcji inicjowania obsługi administracyjnej|
|![Zaznacz][1]|Konfigurowanie roamingu w stanie przedsiębiorstwa|
|![Zaznacz][1]|Konfigurowanie dostępu warunkowego|

## <a name="review-your-scenarios"></a>Przejrzyj scenariusze 

Podczas gdy sprzężenie usługi Azure AD może być preferowane w niektórych scenariuszach, sprzężenie usługi Azure AD umożliwia przejście do modelu opartego na chmurze z systemem Windows. Jeśli planujesz zmodernizować zarządzanie urządzeniami i zmniejszyć koszty it związane z urządzeniami, dołączenie usługi Azure AD stanowi doskonałą podstawę do osiągnięcia tych celów.  
 
Należy wziąć pod uwagę sprzężenie usługi Azure AD, jeśli cele są zgodne z następującymi kryteriami:

- Przyjmujesz usługę Microsoft 365 jako pakiet produktywności dla użytkowników.
- Chcesz zarządzać urządzeniami za pomocą rozwiązania do zarządzania urządzeniami w chmurze.
- Chcesz uprościć inicjowanie obsługi administracyjnej urządzeń dla użytkowników rozproszonych geograficznie.
- Planujesz zmodernizować infrastrukturę aplikacji.

## <a name="review-your-identity-infrastructure"></a>Przejrzyj swoją infrastrukturę tożsamości  

Sprzężenie usługi Azure AD działa zarówno ze środowiskami zarządzanymi, jak i federowanymi.  

### <a name="managed-environment"></a>Środowisko zarządzane

Środowisko zarządzane można wdrożyć za pomocą [synchronizacji skrótów haseł](/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) lub [przechodzić przez uwierzytelnianie](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) z bezproblemowym logowaniem jednokrotnym.

Te scenariusze nie wymagają konfigurowania serwera federacyjnego do uwierzytelniania.

### <a name="federated-environment"></a>Sfederowane środowisko

Środowisko federacyjne powinno mieć dostawcę tożsamości, który obsługuje zarówno protokoły WS-Trust, jak i WS-Fed:

- **WS-Fed:** Ten protokół jest wymagany do przyłączenia urządzenia do usługi Azure AD.
- **WS-Trust:** Ten protokół jest wymagany do logowania się do urządzenia przyłączone do usługi Azure AD.

Podczas korzystania z usług AD FS należy włączyć następujące punkty końcowe WS-Trust:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Jeśli dostawca tożsamości nie obsługuje tych protokołów, sprzężenie usługi Azure AD nie działa natywnie. 

>[!NOTE]
> Obecnie sprzężenie usługi Azure AD nie działa z [usługą AD FS 2019 skonfigurowanym z zewnętrznymi dostawcami uwierzytelniania jako podstawową metodą uwierzytelniania.](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary) Domyślne przyłączanie usługi Azure AD do uwierzytelniania haseł jako metody podstawowej, co powoduje błędy uwierzytelniania w tym scenariuszu


### <a name="smartcards-and-certificate-based-authentication"></a>Karty inteligentne i uwierzytelnianie oparte na certyfikatach

Nie można używać kart inteligentnych ani uwierzytelniania opartego na certyfikatach do łączenia urządzeń z usługą Azure AD. Jednak karty inteligentne mogą służyć do logowania się do urządzeń przyłączonych do usługi Azure AD, jeśli masz skonfigurowane usługi AD FS.

**Zalecenie:** Zaimplementuj system Windows Hello dla firm w celu silnego uwierzytelniania bez hasła na urządzeniach z systemem Windows 10.

### <a name="user-configuration"></a>Konfiguracja użytkownika

Jeśli utworzysz użytkowników w:

- **Lokalna usługa Active Directory**, należy zsynchronizować je z usługą Azure AD przy użyciu [usługi Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Usługa Azure AD**— nie jest wymagana żadna dodatkowa konfiguracja.

Lokalne sieci UPN, które różnią się od sieci UPN usługi Azure AD, nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Jeśli użytkownicy korzystają z lokalnej sieci UPN, należy zaplanować przejście do korzystania z ich podstawowej sieci UPN w usłudze Azure AD.

## <a name="assess-your-device-management"></a>Ocena zarządzania urządzeniami

### <a name="supported-devices"></a>Obsługiwane urządzenia

Dołączenie usługi Azure AD:

- Dotyczy tylko urządzeń z systemem Windows 10. 
- Nie ma zastosowania do poprzednich wersji systemu Windows lub innych systemów operacyjnych. Jeśli masz urządzenia z systemem Windows 7/8.1, musisz uaktualnić do systemu Windows 10, aby wdrożyć sprzężenie usługi Azure AD.
- Nie jest obsługiwany na urządzeniach z modułem TPM w trybie FIPS.
 
**Zalecenie:** Zawsze korzystaj z najnowszej wersji systemu Windows 10, aby korzystać ze zaktualizowanych funkcji.

### <a name="management-platform"></a>Platforma zarządzania

Zarządzanie urządzeniami dla urządzeń przyłączonych do usługi Azure AD opiera się na platformie MDM, takiej jak Intune i dostawców usług MDM. System Windows 10 ma wbudowany agent MDM, który współpracuje ze wszystkimi zgodnymi rozwiązaniami MDM.

> [!NOTE]
> Zasady grupy nie są obsługiwane w urządzeniach przyłączonych do usługi Azure AD, ponieważ nie są one połączone z lokalną usługą Active Directory. Zarządzanie urządzeniami przyłączanym do usługi Azure AD jest możliwe tylko za pośrednictwem usługi MDM

Istnieją dwa podejścia do zarządzania urządzeniami przyłączanym do usługi Azure AD:

- **Tylko mdm —** urządzenie jest zarządzane wyłącznie przez dostawcę mdm, takiego jak intune. Wszystkie zasady są dostarczane w ramach procesu rejestracji MDM. Dla klientów usługi Azure AD Premium lub EMS rejestracja mdm jest krokiem automatycznym, który jest częścią sprzężenia usługi Azure AD.
- **Współzarządzanie** — urządzenie jest zarządzane przez dostawcę mdm i SCCM. W tym podejściu agent SCCM jest zainstalowany na urządzeniu zarządzanym przez MDM do administrowania pewnymi aspektami.

Jeśli używasz zasad grupy, oceń parzystość zasad MDM za pomocą [narzędzia MMAT (Migration Analysis Tool) (MDM Migration Analysis Tool).](https://github.com/WindowsDeviceManagement/MMAT) 

Przejrzyj obsługiwane i nieobsługiwały zasady, aby ustalić, czy można użyć rozwiązania MDM zamiast zasad grupy. W przypadku nieobsługiconych zasad należy wziąć pod uwagę następujące kwestie:

- Czy nieobsługiwały zasady są niezbędne dla urządzeń lub użytkowników przyłączonych do usługi Azure AD?
- Czy nieobsługiwały zasady mają zastosowanie we wdrożeniu opartym na chmurze?

Jeśli rozwiązanie MDM nie jest dostępne za pośrednictwem galerii aplikacji usługi Azure AD, można je dodać zgodnie z procesem opisanym w [integracji usługi Azure Active Directory z usługą MDM.](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) 

Dzięki współzarządzania można używać SCCM do zarządzania niektórymi aspektami urządzeń, podczas gdy zasady są dostarczane za pośrednictwem platformy MDM. Usługa Microsoft Intune umożliwia współzarządzanie sccm. Aby uzyskać więcej informacji na temat współzarządzania urządzeniami z systemem Windows 10, zobacz [Co to jest współzarządzanie?](/configmgr/core/clients/manage/co-management-overview). Jeśli używasz produktu MDM innego niż Intune, skontaktuj się z dostawcą mdm w odpowiednich scenariuszach współzarządzania.

**Zalecenie:** Należy wziąć pod uwagę zarządzanie tylko MDM dla urządzeń przyłączonych do usługi Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Opis zagadnień dotyczących aplikacji i zasobów

Zalecamy migrację aplikacji z lokalnego do chmury, aby uzyskać lepsze środowisko użytkownika i kontrolę dostępu. Jednak urządzenia przyłączone do usługi Azure AD mogą bezproblemowo zapewniać dostęp do aplikacji lokalnych i chmurowych. Aby uzyskać więcej informacji, zobacz [Jak logowania przed usż do zasobów lokalnych działa na urządzeniach przyłączonych do usługi Azure AD.](azuread-join-sso.md)

W poniższych sekcjach przedstawiono zagadnienia dotyczące różnych typów aplikacji i zasobów.

### <a name="cloud-based-applications"></a>Aplikacje oparte na chmurze

Jeśli aplikacja zostanie dodana do galerii aplikacji usługi Azure AD, użytkownicy otrzymują wpisola sytego za pośrednictwem urządzeń przyłączonych do usługi Azure AD. Nie jest wymagana żadna dodatkowa konfiguracja. Użytkownicy otrzymują sso zarówno w przeglądarkach Microsoft Edge, jak i Chrome. W chrome musisz wdrożyć [rozszerzenie Konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Wszystkie aplikacje Win32, które:

- Polegaj na Menedżerze konta sieci Web (WAM) dla żądań tokenów również uzyskać jednostkę SSO na urządzeniach przyłączonych do usługi Azure AD. 
- Nie polegaj na WAM może monitować użytkowników o uwierzytelnienie. 

### <a name="on-premises-web-applications"></a>Lokalne aplikacje sieci Web

Jeśli aplikacje są tworzone na zamówienie i/lub hostowane lokalnie, musisz dodać je do zaufanych witryn przeglądarki, aby:

- Włączanie uwierzytelniania zintegrowanego systemu Windows do pracy 
- Zapewnij użytkownikom bezproblemowe środowisko. 

Jeśli używasz usług AD FS, zobacz [Weryfikowanie logowania jednokrotnego za pomocą usług AD FS i zarządzanie nim](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Zalecenie:** Rozważ hosting w chmurze (na przykład Azure) i integrację z usługą Azure AD, aby uzyskać lepsze środowisko.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Aplikacje lokalne korzystające ze starszych protokołów

Użytkownicy uzyskują identyfikatory SSO z urządzeń przyłączonych do usługi Azure AD, jeśli urządzenie ma dostęp do kontrolera domeny. 

**Zalecenie:** Wdrażanie [serwera proxy aplikacji usługi Azure AD,](/azure/active-directory/manage-apps/application-proxy) aby włączyć bezpieczny dostęp dla tych aplikacji.

### <a name="on-premises-network-shares"></a>Lokalne udziały sieciowe

Użytkownicy mają identyfikator sytowy z urządzeń przyłączonych do usługi Azure AD, gdy urządzenie ma dostęp do lokalnego kontrolera domeny.

### <a name="printers"></a>Drukarki

W przypadku drukarek należy wdrożyć [wydruk chmury hybrydowej](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) do odnajdowania drukarek na urządzeniach przyłączonych do usługi Azure AD. 

Chociaż drukarki nie mogą być automatycznie odnajdowane w środowisku tylko w chmurze, użytkownicy mogą również użyć ścieżki UNC drukarek, aby bezpośrednio je dodać. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplikacje lokalne korzystające z uwierzytelniania maszynowego

Urządzenia przyłączone do usługi Azure AD nie obsługują aplikacji lokalnych korzystających z uwierzytelniania maszynowego. 

**Zalecenie:** Rozważ wycofanie tych aplikacji i przejście do ich nowoczesnych alternatyw.

### <a name="remote-desktop-services"></a>Usługi pulpitu zdalnego

Połączenie pulpitu zdalnego z urządzeniami przyłączanym do usługi Azure AD wymaga, aby komputer-host był przyłączony do usługi Azure AD lub przyłączony do usługi Hybrid Azure AD. Pulpit zdalny z urządzenia nieprzyłączonego lub niezwiązanego z systemem Windows nie jest obsługiwany. Aby uzyskać więcej informacji, zobacz [Łączenie się ze zdalnym komputerem przyłączonym do usługi Azure AD](/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Opis opcji inicjowania obsługi administracyjnej

Można aprowizować sprzężenie usługi Azure AD przy użyciu następujących metod:

- **Samoobsługa w ustawieniach OOBE/—** w trybie samoobsługowym użytkownicy przechodzą przez proces dołączania usługi Azure AD podczas środowiska OOBE (Windows Out of Box Experience) lub w ustawieniach systemu Windows. Aby uzyskać więcej informacji, zobacz [Dołączanie urządzenia służbowego do sieci organizacji](/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot** — Windows Autopilot umożliwia wstępną konfigurację urządzeń, aby uzyskać płynniejsze środowisko w OOBE do wykonywania sprzężenia usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie programu Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Rejestracja zbiorcza** — rejestracja zbiorcza umożliwia dołączanie usługi Azure AD sterowane przez administratora przy użyciu narzędzia do inicjowania obsługi administracyjnej zbiorczego w celu skonfigurowania urządzeń. Aby uzyskać więcej informacji, zobacz [Rejestracja zbiorcza dla urządzeń z systemem Windows](/intune/windows-bulk-enroll).
 
Oto porównanie tych trzech podejść 
 
|   | Konfiguracja samoobsługowa | Windows Autopilot | Rejestrowanie zbiorcze |
| --- | --- | --- | --- |
| Wymaganie interakcji z użytkownikiem do skonfigurowania | Tak | Tak | Nie |
| Wymagaj nakładu pracy w it | Nie | Tak | Tak |
| Odpowiednie przepływy | Ustawienia & OOBE | Tylko OOBE | Tylko OOBE |
| Prawa administratora lokalnego do użytkownika podstawowego | Tak, domyślnie | Możliwość konfiguracji | Nie |
| Wymagaj obsługi oem oem urządzenia | Nie | Tak | Nie |
| Obsługiwane wersje | 1511+ | 1709+ | 1703+ |
 
Wybierz podejście do wdrażania lub podejścia, przeglądając powyższą tabelę i przeglądając następujące zagadnienia dotyczące przyjęcia obu podejść:  

- Czy twoi użytkownicy są doświadczeni technologicznie, aby przejść przez samą konfigurację? 
   - Samoobsługa może działać najlepiej dla tych użytkowników. Należy wziąć pod uwagę Program Windows Autopilot, aby poprawić komfort użytkowania.  
- Czy użytkownicy są zdalni lub w pomieszczeniach firmowych? 
   - Samoobsługowe lub Autopilot działają najlepiej dla użytkowników zdalnych dla bezproblemowej konfiguracji. 
- Czy wolisz konfigurację opartą na użytkownikach lub zarządzaną przez administratora? 
   - Rejestracja zbiorcza działa lepiej w przypadku wdrażania sterowanego przez administratora w celu skonfigurowania urządzeń przed przekazaniem ich użytkownikom.     
- Czy kupujesz urządzenia od 1-2 OEMS, czy masz szeroką dystrybucję urządzeń OEM?  
   - Jeśli kupujesz od ograniczonych OEM, którzy również obsługują autopilota, możesz skorzystać z ściślejszej integracji z autopilotem. 

## <a name="configure-your-device-settings"></a>Konfigurowanie ustawień urządzenia

Portal Azure umożliwia kontrolowanie wdrażania urządzeń przyłączonych do usługi Azure AD w organizacji. Aby skonfigurować powiązane ustawienia, na stronie Usługi `Devices > Device settings`Azure Active **Directory**wybierz pozycję .

### <a name="users-may-join-devices-to-azure-ad"></a>Użytkownicy mogą dołączać urządzenia do usługi Azure AD

Ustaw tę opcję na **Wszystkie** lub **Wybrane** na podstawie zakresu wdrożenia i kto chcesz zezwolić na konfigurację urządzenia przyłączone do usługi Azure AD. 

![Użytkownicy mogą dołączać urządzenia do usługi Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Dodatkowi administratorzy lokalni na urządzeniach przyłączonych do usługi Azure AD

Wybierz **pozycję Zaznaczone** i wybierz użytkowników, których chcesz dodać do grupy administratorów lokalnych na wszystkich urządzeniach przyłączonych do usługi Azure AD. 

![Dodatkowi administratorzy lokalni na urządzeniach przyłączonych do usługi Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Wymaganie wieloczynnikowego auth do łączenia urządzeń

Wybierz **opcję "Tak,** jeśli potrzebujesz od użytkowników wykonywania usługi MFA podczas łączenia urządzeń z usługą Azure AD. Dla użytkowników łączących urządzenia z usługą Azure AD przy użyciu usługi MFA samo urządzenie staje się drugim czynnikiem.

![Wymaganie wieloczynnikowego auth do łączenia urządzeń](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Konfigurowanie ustawień mobilności

Zanim będzie można skonfigurować ustawienia mobilności, może być najpierw trzeba dodać dostawcę mdm, najpierw.

**Aby dodać dostawcę mdm:**

1. Na **stronie Usługi Azure Active Directory**w `Mobility (MDM and MAM)`sekcji **Zarządzanie** kliknij pozycję . 
1. Kliknij **pozycję Dodaj aplikację**.
1. Wybierz dostawcę mdm z listy.

   ![Dodawanie aplikacji](./media/azureadjoin-plan/04.png)

Wybierz dostawcę mdm, aby skonfigurować powiązane ustawienia. 

### <a name="mdm-user-scope"></a>Zakres użytkownika MDM

Wybierz **niektóre** lub **wszystkie** na podstawie zakresu wdrożenia. 

![Zakres użytkownika MDM](./media/azureadjoin-plan/05.png)

Na podstawie zakresu, jeden z następujących dzieje się: 

- **Użytkownik jest w zakresie MDM:** Jeśli masz subskrypcję usługi Azure AD Premium, rejestracja MDM jest zautomatyzowana wraz z przyłączem usługi Azure AD. Wszyscy użytkownicy o określonym zakresie muszą mieć odpowiednią licencję dla urządzenia MDM. Jeśli rejestracja MDM nie powiedzie się w tym scenariuszu, sprzężenie usługi Azure AD również zostanie wycofane.
- **Użytkownik nie jest w zakresie MDM:** Jeśli użytkownicy nie są w zakresie MDM, sprzężenia usługi Azure AD kończy się bez rejestracji MDM. Powoduje to, że urządzenie niezarządzane.

### <a name="mdm-urls"></a>Adresy URL mdm

Istnieją trzy adresy URL związane z konfiguracją mdm:

- Adres URL warunków użytkowania zarządzania urządzeniami przenośnymi
- Adres URL odnajdywania zarządzania urządzeniami przenośnymi 
- Adres URL zgodności zarządzania urządzeniami przenośnymi

![Dodawanie aplikacji](./media/azureadjoin-plan/06.png)

Każdy adres URL ma wstępnie zdefiniowaną wartość domyślną. Jeśli te pola są puste, skontaktuj się z dostawcą mdm, aby uzyskać więcej informacji.

### <a name="mam-settings"></a>Ustawienia mam

Mam nie ma zastosowania do sprzężenia usługi Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurowanie roamingu w stanie przedsiębiorstwa

Jeśli chcesz włączyć roaming stanu do usługi Azure AD, aby użytkownicy mogli synchronizować ustawienia między urządzeniami, zobacz [Włączanie roamingu w stanie przedsiębiorstwa w usłudze Azure Active Directory](enterprise-state-roaming-enable.md). 

**Zalecenie:** Włącz to ustawienie nawet dla hybrydowych urządzeń przyłączonych do usługi Azure AD.

## <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

Jeśli masz dostawcę MDM skonfigurowany dla urządzeń przyłączonych do usługi Azure AD, dostawca flagi urządzenia jako zgodne, jak tylko urządzenie jest w zarządzaniu. 

![Zgodne urządzenie](./media/azureadjoin-plan/46.png)

Za pomocą tej implementacji [można wymagać zarządzanych urządzeń dostępu do aplikacji w chmurze za pomocą programu Dostęp warunkowy](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dołączanie nowego urządzenia z systemem Windows 10 do usługi Azure AD podczas pierwszego uruchomienia](azuread-joined-devices-frx.md)
> [Dołączanie urządzenia służbowego do sieci organizacji](/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
