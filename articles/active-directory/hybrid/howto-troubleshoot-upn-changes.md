---
title: Planowanie i rozwiązywanie problemów ze zmianami nazwy zasad użytkownika platformy Azure (UPN)
description: Opis znanych problemów i czynników ograniczających zagrożenie dla zmian w upn
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11be1d971922095d4a1ace1c81c763134b4e58c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743323"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planowanie i rozwiązywanie problemów ze zmianami nazwy głównej użytkownika w usłudze Azure Active Directory

Nazwa główna użytkownika (UPN) jest atrybutem, który jest standardem komunikacji internetowej dla kont użytkowników. Nazwa UPN składa się z prefiksu nazwy UŻYTKOWNIKA (nazwa konta użytkownika) i sufiksu nazwy UPN (nazwa domeny DNS). Prefiks łączy sufiks przy użyciu symbolu "@". Na przykład someone@example.com. Numer UPN musi być unikatowy dla wszystkich obiektów głównych zabezpieczeń w lesie katalogu. 

> [!NOTE]
> Dla deweloperów zaleca się użycie identyfikatora obiektu użytkownika jako identyfikatora niezmiennego, a nie nazwy UPN. Jeśli aplikacje korzystają obecnie z nazwy UPN, zalecamy ustawienie nazwy UPN w celu dopasowania do podstawowego adresu e-mail użytkownika w celu poprawy ich środowiska.<br> **W środowisku hybrydowym ważne jest, aby sieć UPN dla użytkownika była identyczna w katalogu lokalnym i w usłudze Azure Active Directory.**

**W tym artykule założono, że używasz nazwy UPN jako identyfikatora użytkownika. Dotyczy planowania zmian w upn i odzyskiwania z problemów, które mogą wynikać ze zmian w upn.**

## <a name="learn-about-upns-and-upn-changes"></a>Dowiedz się więcej o sieciach UPN i zmianach w sieciach UPN
Strony logowania często monitują użytkowników o wprowadzenie adresu e-mail, gdy wymaganą wartością jest ich nazwa UPN. W związku z tym należy pamiętać, aby zmienić upn użytkowników w każdej chwili ich podstawowy adres e-mail zmiany.

Podstawowe adresy e-mail użytkowników mogą ulec zmianie z wielu powodów:

* rebranding firmy

* pracownicy przenoszący się do różnych działów firmy 

* fuzje i przejęcia

* Zmiany nazwiska pracownika

### <a name="types-of-upn-changes"></a>Typy zmian w upn

Można zmienić upn, zmieniając prefiks, sufiks lub oba te elementy.

* **Zmiana prefiksu**.

   *  Jeśli na przykład imię i nazwisko osoby uległo zmianie, możesz zmienić jej nazwę konta:  
BSimon@contoso.com doBJohnson@contoso.com

   * Można również zmienić standard firmowy dla prefiksów:  
Bsimon@contoso.com doBritta.Simon@contoso.com

* **Zmiana sufiksu**. <br>

    Jeśli na przykład dana osoba zmieniła podziały, możesz zmienić jej domenę: 

   * Britta.Simon@contoso.comdoBritta.Simon@contosolabs.com <br>
     Lub<br>
    * Britta.Simon@corp.contoso.comdoBritta.Simon@labs.contoso.com 

Zmieniaj główną główną liczecę użytkownika za każdym razem, gdy podstawowy adres e-mail użytkownika jest aktualizowany. Bez względu na przyczynę zmiany adresu e-mail, numer UPN musi być zawsze aktualizowany w celu dopasowania.

Podczas początkowej synchronizacji z usługi Active Directory do usługi Azure AD upewnij się, że wiadomości e-mail użytkowników są identyczne z ich sieciami UPN.

### <a name="upns-in-active-directory"></a>Sieci UPN w usłudze Active Directory

W usłudze Active Directory domyślnym sufiksem nazwy UPN jest nazwa DNS domeny, w której utworzono konto użytkownika. W większości przypadków jest to nazwa domeny zarejestrowana jako domena przedsiębiorstwa w Internecie. Jeśli utworzysz konto użytkownika w domenie contoso.com, domyślna nazwa UPN

username@contoso.com

 Można jednak [dodać więcej sufiksów nazwy UPN](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) przy użyciu domen i relacji zaufania usługi Active Directory. 

Na przykład możesz dodać labs.contoso.com i mieć wiadomości UPN i e-mail użytkowników. Następnie staliby się

username@labs.contoso.com.

>[!IMPORTANT]
> Jeśli sieci UPN w usłudze Active Directory i usłudze Azure Active Directory nie są zgodne, pojawią się problemy. W [przypadku zmiany sufiksu w usłudze Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)należy upewnić się, że dopasowana niestandardowa nazwa domeny została [dodana i zweryfikowana w usłudze Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) 

![Zrzut ekranu zweryfikowanych domen](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Sieci UPN w usłudze Azure Active Directory

Użytkownicy logują się do usługi Azure AD przy obliczu wartości w atrybucie userPrincipalName. 

Podczas korzystania z usługi Azure AD w połączeniu z lokalną usługą Active Directory konta użytkowników są synchronizowane przy użyciu usługi Azure AD Connect. Domyślnie kreator usługi Azure AD Connect używa atrybutu userPrincipalName z lokalnej usługi Active Directory jako nazwy UPN w usłudze Azure AD. Można go zmienić na inny atrybut w instalacji niestandardowej.

Ważne jest, aby podczas aktualizowania głównej nazwy użytkownika (UPN) jednego użytkownika lub całej organizacji był on zdefiniowany. 

Zobacz znane problemy i obejścia w tym dokumencie.

Podczas synchronizowania kont użytkowników z usługi Active Directory do usługi Azure AD upewnij się, że sieci UPN w usłudze Active Directory są mapowane do zweryfikowanych domen w usłudze Azure AD.

![Zrzut ekranu przedstawiający zweryfikowane domeny](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Jeśli wartość atrybutu userPrincipalName nie odpowiada zweryfikowanej domenie w usłudze Azure AD, proces synchronizacji zastępuje sufiks domyślną wartością onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Wprowadzanie zbiorczych zmian w upn

Postępuj zgodnie z [najlepszymi praktykami pilotażowymi](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) w zakresie zbiorczych zmian w un. Masz również przetestowany plan wycofywania sieci UPN, jeśli znajdziesz problemy, których nie można szybko rozwiązać. Po uruchomieniu pilotażu możesz zacząć kierować reklamy na małe grupy użytkowników z różnymi rolami organizacyjnymi i określonymi zestawami aplikacji lub urządzeń.

Przechodzenie przez ten pierwszy podzbiór użytkowników daje dobry pomysł, co użytkownicy powinni oczekiwać w ramach zmiany. Dołącz te informacje do komunikacji z użytkownikami.

Utwórz zdefiniowaną procedurę zmiany sieci UPN dla poszczególnych użytkowników w ramach normalnych operacji. Zaleca się posiadanie przetestowanej procedury, która zawiera dokumentację dotyczącą znanych problemów i obejść te rozwiązania.

W poniższych sekcjach opisano potencjalne znane problemy i obejścia problemów po zmianie sieci UPN.

## <a name="apps-known-issues-and-workarounds"></a>Znane problemy i obejścia aplikacji

[Aplikacje Software as a service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) i Line of Business (LoB) często polegają na sieciach UPN w celu znajdowania użytkowników i przechowywania informacji o profilu użytkownika, w tym ról. Aplikacje, które używają [just in Time inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) do tworzenia profilu użytkownika, gdy użytkownicy logują się do aplikacji po raz pierwszy mogą mieć wpływ na zmiany nazwy UPN.

**Znany problem**<br>
Zmiana nazwy UPN użytkownika może spowodować przerwanie relacji między użytkownikiem usługi Azure AD a profilem użytkownika utworzonym w aplikacji. Jeśli aplikacja używa [just in Time inicjowania obsługi administracyjnej,](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)może utworzyć nowy profil użytkownika. Będzie to wymagało od administratora aplikacji ręcznego wprowadzania zmian w celu naprawienia tej relacji.

**Obejście**<br>
[Automatyczne inicjowanie obsługi administracyjnej usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) umożliwia automatyczne tworzenie, obsługa i usuwanie tożsamości użytkowników w obsługiwanych aplikacjach w chmurze. Konfigurowanie automatycznego inicjowania obsługi administracyjnej użytkowników w aplikacjach automatycznie aktualizuje nazwy UPN w aplikacjach. Przetestuj aplikacje w ramach stopniowego wdrażania, aby sprawdzić, czy zmiany w numerze UPN nie mają na nie wpływu.
Jeśli jesteś deweloperem, należy rozważyć [dodanie obsługi scim do aplikacji,](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) aby włączyć automatyczne inicjowanie obsługi administracyjnej użytkowników z usługi Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Znane problemy i obejścia problemów z zarządzanymi urządzeniami

Dzięki [wprowadzeniu urządzeń do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview)można zmaksymalizować produktywność użytkowników za pomocą logowania jednokrotnego (Logowanie jednokrotne) w chmurze i zasobach lokalnych.

### <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Urządzenia [przyłączone do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) są dołączane bezpośrednio do usługi Azure AD i umożliwiają użytkownikom logowanie się do urządzenia przy użyciu tożsamości organizacji.

**Znane problemy** <br>
Użytkownicy mogą wystąpić problemy z logowaniem jednokrotnym z aplikacjami, które zależą od usługi Azure AD do uwierzytelniania.

**Obejście** <br>
Pozwól wystarczająco dużo czasu na zsynchronizowanie zmiany upn z usługą Azure AD. Po sprawdzeniu, czy nowa nazwa UPN jest odzwierciedlona w portalu usługi Azure AD Portal, poproś użytkownika o wybranie kafelka "Inny użytkownik", aby zalogować się przy nowym usn. Można również sprawdzić za pośrednictwem [programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Po zalogowaniu się przy nowej nowej numerze UPN odwołania do starej umowy UPN mogą nadal pojawiać się w ustawieniu systemu Windows "Dostęp do pracy lub szkoły".

![Zrzut ekranu przedstawiający zweryfikowane domeny](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

[Hybrydowe urządzenia przyłączone do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) są przyłączane do usługi Active Directory i usługi Azure AD. Można zaimplementować hybrydowe sprzężenie usługi Azure AD, jeśli twoje środowisko ma lokalną usługę Active Directory i chcesz również korzystać z możliwości oferowanych przez usługę Azure AD.

**Znane problemy** 

Urządzenia połączone z usługą Azure AD z systemem Windows 10 mogą wystąpić nieoczekiwane ponowne uruchomienie i problemy z dostępem.

Jeśli użytkownicy logują się do systemu Windows przed zsynchronizowaniem nowej nazwy UPN z usługą Azure AD lub nadal korzystają z istniejącej sesji systemu Windows, mogą wystąpić problemy z logowaniem jednokrotnym z aplikacjami korzystającymi z usługi Azure AD do uwierzytelniania, jeśli dostęp warunkowy został skonfigurowany do wymuszania użycia urządzeń sprzężonych z użyciem hybrydowych urządzeń w celu uzyskania dostępu do zasobów. 

Ponadto pojawi się następujący komunikat, wymuszając ponowne uruchomienie po jednej minucie. 

"Komputer zostanie automatycznie uruchomiony ponownie w ciągu jednej minuty. System Windows napotkał problem i musi zostać ponownie uruchomiony. Powinieneś teraz zamknąć tę wiadomość i zapisać swoją pracę".

**Obejście** 

Urządzenie musi być odłączone od usługi Azure AD i ponownie uruchomione. Po ponownym uruchomieniu urządzenie automatycznie dołączy do usługi Azure AD ponownie, a użytkownik musi zalogować się przy użyciu nowej nazwy UPN, wybierając kafelek "Inny użytkownik". Aby odłączyć urządzenie z usługi Azure AD, uruchom następujące polecenie w wierszu polecenia:

**dsregcmd /urlop**

Użytkownik będzie musiał [ponownie zarejestrować się](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) w usłudze Windows Hello dla firm, jeśli jest używany. Ten problem nie dotyczy urządzeń z systemem Windows 7 i 8.1 po zmianach w sieci UPN.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Znane problemy i obejścia znanych rozwiązań i obejścia problemu z programem Microsoft Authenticator

Organizacja może wymagać użycia [aplikacji Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) do logowania się i uzyskiwania dostępu do aplikacji i danych organizacji. Chociaż nazwa użytkownika może pojawić się w aplikacji, konto nie jest skonfigurowane do działania jako metoda weryfikacji, dopóki użytkownik nie zakończy procesu rejestracji.

[Aplikacja Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) ma cztery główne funkcje:

* Uwierzytelnianie wieloskładnikowe za pomocą powiadomienia wypychanego lub kodu weryfikacyjnego

* Działa jako broker uwierzytelniania na urządzeniach z systemami iOS i Android, aby zapewnić logowanie jednokrotne dla aplikacji korzystających z [uwierzytelniania brokerskiego](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Rejestracja urządzenia (znana również jako Dołączanie do miejsca pracy) w usłudze Azure AD, która jest wymagana dla innych funkcji, takich jak ochrona aplikacji usługi Intune i rejestracja/zarządzanie urządzeniami,

* Logowanie telefoniczne, które wymaga rejestracji usługi MFA i urządzenia.

### <a name="multi-factor-authentication-with-android-devices"></a>Uwierzytelnianie wieloskładnikowe na urządzeniach z androidem

Aplikacja Microsoft Authenticator oferuje opcję weryfikacji poza pasmem. Zamiast umieszczać automatyczną rozmowę telefoniczną lub WIADOMOŚĆ SMS do użytkownika podczas logowania, [uwierzytelnianie wieloskładnikowe (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) wypycha powiadomienie do aplikacji Microsoft Authenticator na smartfonie lub tablecie użytkownika. Użytkownik po prostu naciska Przycisk Zatwierdź (lub wprowadza kod PIN lub dane biometryczne i naciska "Uwierzytelnij") w aplikacji, aby zakończyć logowanie.

**Znane problemy** 

Po zmianie nazwy UPN użytkownika stare nazwy UPN są nadal wyświetlane na koncie użytkownika, a powiadomienie może nie zostać odebrane. [Kody weryfikacyjne](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) nadal działają.

**Obejście**

Jeśli zostanie odebrane powiadomienie, poinstruuj użytkownika, aby odrzucił powiadomienie, otwórz aplikację Authenticator, naciśnij opcję "Sprawdź powiadomienia" i zatwierdź monit usługi MFA. Następnie numer UPN wyświetlany na koncie zostanie zaktualizowany. Należy zauważyć, że zaktualizowana nazwa UPN może być wyświetlana jako nowe konto, wynika to z innych funkcji authenticatora. Aby uzyskać więcej informacji, zapoznaj się z dodatkowymi znanymi problemami w tym artykule.

### <a name="brokered-authentication"></a>Uwierzytelnianie brokerskie

Na Android i iOS brokerów, takich jak Microsoft Authenticator włączyć:

* Logowanie jednokrotne (Logowanie jednokrotne) — użytkownicy nie będą musieli logować się do każdej aplikacji.

* Identyfikacja urządzenia — broker uzyskuje dostęp do certyfikatu urządzenia utworzonego na urządzeniu, gdy zostało ono połączone w miejscu pracy.

* Weryfikacja identyfikacji aplikacji — gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania, a broker weryfikuje go.

Ponadto umożliwia aplikacjom uczestnictwo w bardziej zaawansowanych funkcjach, takich jak [dostęp warunkowy,](https://docs.microsoft.com/azure/active-directory/conditional-access/)i obsługuje [scenariusze usługi Microsoft Intune.](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)

**Znane problemy**<br>
Użytkownik otrzymuje bardziej interaktywne monity o uwierzytelnienie w nowych aplikacjach, które używają logowania wspomaganego przez brokera z powodu niezgodności między login_hint przekazywanych przez aplikację a siecią UPN przechowywaną w brokerze.

**Obejście** <br> Użytkownik musi ręcznie usunąć konto z programu Microsoft Authenticator i rozpocząć nowe logowanie z aplikacji wspomaganej przez brokera. Konto zostanie automatycznie dodane po początkowym uwierzytelnieniu.

### <a name="device-registration"></a>Rejestracja urządzenia

Aplikacja Microsoft Authenticator jest odpowiedzialna za zarejestrowanie urządzenia w usłudze Azure AD. Rejestracja urządzenia umożliwia urządzeniu uwierzytelnienie w usłudze Azure AD i jest wymagana dla następujących scenariuszy:

* Ochrona aplikacji usługi Intune

* Rejestracja urządzeń usługi Intune

* Logowanie telefoniczne

**Znane problemy**<br>
Po zmianie upn, nowe konto z nową emiką UPN pojawia się na liście w aplikacji Microsoft Authenticator, podczas gdy konto ze starą emimią OWANĄ jest nadal na liście. Ponadto stare numery UPN są wyświetlane w sekcji Rejestracja urządzeń w ustawieniach aplikacji. Nie ma żadnych zmian w normalnej funkcjonalności rejestracji urządzenia lub scenariuszy zależnych.

**Obejście** <br> Aby usunąć wszystkie odwołania do starej nazwy UPN w aplikacji Microsoft Authenticator, poinstruuj użytkownika, aby ręcznie usunął stare i nowe konta z usługi Microsoft Authenticator, ponownie zarejestrował się w usłudze MFA i ponownie dołączył do urządzenia.

### <a name="phone-sign-in"></a>Logowanie telefoniczne

Logowanie telefoniczne umożliwia użytkownikom logowanie się do usługi Azure AD bez hasła. Aby włączyć logowanie telefoniczne, użytkownik musi zarejestrować się w urzędzie obsługi uwierzytelnienia przy użyciu aplikacji Authenticator, a następnie włączyć logowanie telefoniczne bezpośrednio w authenticatorze. W ramach konfiguracji urządzenie rejestruje się w usłudze Azure AD.

**Znane problemy** <br>
Użytkownicy nie mogą korzystać z logowania telefonicznego, ponieważ nie otrzymują żadnych powiadomień. Jeśli użytkownik dotknie opcji Sprawdź powiadomienia, zostanie wyświetlony błąd.

**Obejście**<br>
Użytkownik musi wybrać menu rozwijane na koncie włączonym dla logowania telefonu i wybrać Wyłącz logowanie telefoniczne. W razie potrzeby można ponownie włączyć logowanie do telefonu.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Znane problemy i obejścia dotyczące klucza bezpieczeństwa (FIDO2)

**Znane problemy** <br>
Gdy wielu użytkowników jest zarejestrowanych w tym samym kluczu, na ekranie logowania jest wyświetlana strona wyboru konta, na której wyświetlana jest stara owana liczba adresów UPN. Zmiany numerów UPN nie mają wpływu na logowania przy użyciu kluczy zabezpieczeń.  

**Obejście**<br>
Aby usunąć odwołania do starych sieci UPN, użytkownicy muszą [zresetować klucz zabezpieczeń i ponownie zarejestrować](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Znane problemy i obejścia dotyczące usługi OneDrive

Użytkownicy usługi OneDrive są znani z problemów po zmianach w upn. Aby uzyskać więcej informacji, zobacz [Jak zmiany w liczeń 100 000 000 000 000 000 000 000 000 000 000 0](https://docs.microsoft.com/onedrive/upn-changes)

## <a name="next-steps"></a>Następne kroki

Zobacz następujące zasoby:
* [Usługa Azure AD Connect: koncepcje projektu](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Wypełnianie wartości UserPrincipalName w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Tokeny identyfikatorów platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
