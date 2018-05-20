---
title: Azure współpracy B2B usługi Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory B2B współpracy.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: bc63e320053ec4e8dbb9f166a9a69bb24d91fa02
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure współpracy B2B usługi Active Directory — często zadawane pytania

Te usługi Azure Active Directory (Azure AD) biznesowych między firmami (B2B) współpracy — często zadawane pytania (FAQ) są okresowo aktualizowane, aby uwzględnić nowe tematy.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Dlatego jest bardziej intuicyjne dla naszych gości współpracy B2B możemy dostosować stronę logowania?
Absolutnie! Zobacz nasze [wpis w blogu o tej funkcji](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Aby uzyskać więcej informacji na temat dostosowywania stronę logowania Twojej organizacji, zobacz [dodać znakowanie firmowe do Zaloguj się i strony panelu dostępu](../customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Można B2B współpracy użytkownicy uzyskują dostęp do usługi SharePoint Online i OneDrive?
Tak. Jednak jest możliwość wyszukiwania istniejących użytkowników Gość w usłudze SharePoint Online przy użyciu selektora osób **poza** domyślnie. Aby włączyć tę opcję, aby wyszukać istniejący użytkownicy gościa, należy ustawić **ShowPeoplePickerSuggestionsForGuestUsers** do **na**. Można włączyć to ustawienie na poziomie dzierżawy lub na poziomie zbioru witryn. To ustawienie można zmienić za pomocą polecenia cmdlet Set-SPOTenant i SPOSite zestawu. Z tych poleceń cmdlet elementy członkowskie umożliwia wyszukiwanie istniejących Goście w katalogu. Zmiany w zakresie dzierżawy nie wpływają na witryn usługi SharePoint Online, które zostały już zainicjowane.

### <a name="is-the-csv-upload-feature-still-supported"></a>Funkcja przekazywania CSV nadal jest obsługiwany?
Tak. Aby uzyskać więcej informacji dotyczących używania funkcji przekazywania pliku CSV, zobacz [w tym przykładzie programu PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak można dostosować wiadomości e-mail z zaproszeniem?
Można dostosować niemal wszystkie informacje o procesie zapraszającej przy użyciu [zaproszenia B2B interfejsów API](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Goście resetowania ich uwierzytelnianie wieloskładnikowe — metoda
Tak. Gości tak samo jak normalnych użytkowników czy zresetować ich metody uwierzytelniania wieloskładnikowego.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Której organizacji jest odpowiedzialny za licencje usługi Multi-Factor authentication?
Zapraszanie organizacja przeprowadza uwierzytelnianie wieloskładnikowe. Zapraszanie organizacji, należy się upewnić, czy organizacja ma wystarczającą liczbę licencji dla ich B2B użytkowników, którzy korzystają z usługi Multi-Factor authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co zrobić, jeśli już organizacji partnerskiej ma skonfigurować uwierzytelnianie wieloskładnikowe? Można możemy zaufania ich uwierzytelnianie wieloskładnikowe i nie używać własnej uwierzytelnianie wieloskładnikowe?
Ta funkcja jest planowane w przyszłości, które następnie można wybrać określonych partnerów do wykluczenia z Twojej uwierzytelnianie wieloskładnikowe (zaproszenia organizacji).

### <a name="how-can-i-use-delayed-invitations"></a>Jak używać opóźnione zaproszenia?
Aby dodać użytkowników współpracy B2B, w razie potrzeby udostępnić je do aplikacji, a następnie Wyślij zaproszenia mają organizacji. Zaproszenie współpracy B2B interfejsu API umożliwia dostosowywanie przepływu pracy przy dołączaniu.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Można utworzyć użytkownika gościa administratorem ograniczonym?
Naturalnie. Aby uzyskać więcej informacji, zobacz [Dodawanie gościa użytkowników do roli](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Współpraca B2B usługi Azure AD pozwala użytkownikom uzyskać dostęp do portalu Azure B2B?
Chyba że użytkownik przypisany do roli administratora ograniczony lub administratora globalnego, użytkownicy współpracy B2B nie wymagają dostępu do portalu Azure. Jednak użytkownicy współpracy B2B, którzy mają przypisaną rolę ograniczone administratora lub administratora globalnego dostępu do portalu. Ponadto jeśli użytkownika gościa, który nie jest przypisany jeden z tych ról Administrator uzyskuje dostęp do portalu, użytkownik może mieć możliwość dostępu niektórych części w środowisku. Rola użytkownika gościa ma niektóre uprawnienia w katalogu.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Czy można zablokować dostęp do portalu Azure dla gości
Tak! Podczas konfigurowania tych zasad, należy zachować ostrożność uniknąć przypadkowego blokowanie dostępu do elementów członkowskich i administratorów.
Aby zablokować dostęp użytkownika gościa do [portalu Azure](https://portal.azure.com), użyj zasad dostępu warunkowego w interfejsie API modelu klasycznym wdrażania systemu Windows Azure:
1. Modyfikowanie **wszyscy użytkownicy** grupy tak, aby zawierał tylko elementy członkowskie.
  ![Modyfikowanie grupy zrzut ekranu](media/faq/modify-all-users-group.png)
2. Utwórz grupy dynamicznej, która zawiera gości.
  ![Tworzenie grupy zrzut ekranu](media/faq/group-with-guest-users.png)
3. Skonfigurowanie zasad dostępu warunkowego do bloku gości z dostęp do portalu, jak pokazano w poniższym klipie wideo:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Współpraca B2B usługi Azure AD obsługuje uwierzytelnianie wieloskładnikowe i konta poczty e-mail użytkownika?
Tak. Wieloskładnikowego uwierzytelniania i konsumentów konta e-mail są obsługiwane dla współpracy B2B usługi Azure AD.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Czy firma planuje do obsługi resetowania haseł dla użytkowników współpracy B2B usługi Azure AD?
Tak. Poniżej przedstawiono istotne szczegóły dotyczące samoobsługowego resetowania hasła (SSPR) dla użytkownika B2B, który jest zaproszenie od organizacji będącej partnerem:
 
* SSPR występuje tylko w dzierżawie tożsamości użytkownika B2B.
* Jeśli jest dostępna dzierżawa tożsamość konta Microsoft, konto Microsoft mechanizm SSPR jest używane.
* Jeśli jest dostępna dzierżawa tożsamości just-in-time (JIT) lub "wirusa" dzierżawy, jest wysyłane e-mail resetowania hasła.
* W przypadku pozostałych dzierżaw w standardowym procesie SSPR nastąpią B2B użytkowników. Podobnie jak elementu członkowskiego SSPR B2B użytkowników, w kontekście zasobu dzierżawy jest zablokowany. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Jest resetowania hasła dostępne dla gości w just-in-time (JIT) lub "wirusa" dzierżawy, którzy zaakceptowali zaproszeń do skorzystania z pracą lub służbowego adresu e-mail, ale który nie ma istniejące konto usługi Azure AD?
Tak. Wiadomości resetowania hasła można wysyłać, która umożliwia użytkownikowi resetowania hasła w dzierżawy JIT.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365 oferuje pomocy online do współpracy B2B usługi Azure AD?
Tak, Dynamics 365 (online) zapewnia obsługę współpracy B2B usługi Azure AD. Aby uzyskać więcej informacji, zobacz artykuł Dynamics 365 [zaprosić użytkowników o współpracy B2B usługi Azure AD](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Co to jest okres istnienia hasła początkowego dla nowo utworzonego użytkownika współpracy B2B?
Usługa Azure AD ma ustalony zbiór znaków, siły hasła i konto, które konta użytkowników w chmurze blokady wymagania, które są stosowane jednakowo do wszystkich usługi Azure AD. Konta użytkowników w chmurze są kontami, które nie są Sfederowane przy użyciu innego dostawcy tożsamości, na przykład 
* Konto Microsoft
* Facebook
* Usługi federacyjne Active Directory
* Innej chmurze dzierżawy (Współpraca B2B)

Dla kont federacyjnych zasady haseł zależy od zasad, które są stosowane w dzierżawy lokalnych i ustawień konta Microsoft.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizacja może być mieć różnych środowiskach w aplikacjach dla użytkowników dzierżawy i gości. Czy istnieje standardowa wskazówki dotyczące to? To jest obecność dostawcy tożsamości oświadczeń poprawne modelu w celu wykorzystania?
 Użytkownik gościa może używać dowolnego dostawcy tożsamości do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [właściwości użytkownika współpracy B2B](user-properties.md). Użyj **UserType** właściwości w celu określenia użytkowników. **UserType** oświadczenia jest obecnie niedostępna w tokenie. Aplikacje powinny używać interfejsu API programu Graph do badania katalogu dla użytkownika, a także aby uzyskać UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Gdzie można znaleźć społeczność współpracy B2B udostępniać rozwiązania i przesłać pomysłów?
Firma Microsoft stale jest nasłuchiwanie swoją opinię, aby poprawić współpracy B2B. Zachęcamy do udziału użytkownika scenariuszy, najlepsze rozwiązania i co Ci się podoba współpracy B2B usługi Azure AD. Dołącz do dyskusji [społeczność techniczna Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Również zachęcamy do przesyłania swoich koncepcji i głosów dla przyszłych funkcje na [pomysły współpracy B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Tak, aby użytkownik po prostu "gotowe" mamy wysłać zaproszenie jest automatycznie zrealizowane? Czy użytkownik zawsze ma kliknij za pomocą adresu URL realizacji?
Zapraszającej zaprosić innych użytkowników w organizacji partnerskiej odpowiedzialnej za pomocą interfejsu użytkownika, skrypty programu PowerShell lub interfejsów API. Następnie zapraszającej może wysyłać użytkownika gościa bezpośredniego łącza do aplikacji udostępnionej. W większości przypadków nie ma trzeba otworzyć wiadomości e-mail z zaproszeniem, a następnie kliknij adres URL realizacji. Aby uzyskać więcej informacji, zobacz [realizacji zaproszenia współpracy usługi Azure Active Directory B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Współpraca B2B działanie Jeśli korzysta z zaproszonych partnera federacyjnego można dodać własne uwierzytelniania lokalnego?
Jeśli partner jest dzierżawa usługi Azure AD, która jest federacyjnego do lokalnej infrastruktury uwierzytelniania, lokalne logowanie jednokrotne (SSO) jest automatycznie osiągnięta. Jeśli partner nie jest dzierżawa usługi Azure AD, konto usługi Azure AD jest tworzone dla nowych użytkowników. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Po próbie B2B usługi Azure AD nie zaakceptował gmail.com i outlook.com adresy e-mail i że B2C był używany przez te typy kont?
Różnice między B2B i współpracę (B2C) biznesowych do konsumenta, zgodnie z którą są obsługiwane tożsamości zostaną usunięte. Tożsamość użyta nie jest potrzeba wybór między używaniem B2B przy użyciu usługi B2C. Informacje o wybieraniu opcjach współpracy, zobacz [współpracy B2B porównania i B2C w usłudze Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jakie aplikacje i usługi obsługują gości B2B usługi Azure?
Wszystkie aplikacje Azure zintegrowanej z usługą AD obsługuje gości B2B usługi Azure. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Naszych partnerów, braku uwierzytelniania wieloskładnikowego możemy wymusić uwierzytelnianie wieloskładnikowe dla gości B2B?
Tak. Aby uzyskać więcej informacji, zobacz [dostępu warunkowego dla użytkowników współpracy B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>W programie SharePoint można zdefiniować listę "Zezwalaj" lub "odmowa" dla użytkowników zewnętrznych. Firma Microsoft to zrobić na platformie Azure?
Tak. Azure obsługuje współpracy AD B2B listy zezwalania i odmowy dla. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jakie licencje musimy użyć B2B usługi Azure AD?
Aby dowiedzieć się, jak co licencje potrzeb organizacji do używania B2B usługi Azure AD, zobacz [współpracy usługi Azure Active Directory B2B licencjonowania wskazówki](licensing-guidance.md).

### <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)

