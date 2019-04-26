---
title: Współpraca B2B — często zadawane pytania — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące współpracy B2B usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celested
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d361a40e2efa80a677255c02c7e9cf155af09ffb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60413655"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Współpraca B2B usługi Active Directory Azure — często zadawane pytania

Te często zadawane pytania (FAQ) dotyczące współpracy w usłudze Azure Active Directory (Azure AD) business-to-business (B2B) są okresowo aktualizowane, aby uwzględnić nowe tematy.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Dlatego jest bardziej intuicyjne dla naszych użytkowników gości współpracy B2B możemy dostosować naszą stronę logowania?
Oczywiście! Zobacz nasze [wpis w blogu o tej funkcji](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Aby uzyskać więcej informacji o tym, jak dostosować stronę logowania Twojej organizacji, zobacz [dodać znakowanie firmowe do Zaloguj się i strony panelu dostępu](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Użytkowników we współpracy B2B dostęp do usługi SharePoint Online i OneDrive?
Tak. Jednak jest możliwość wyszukiwania dla istniejących użytkowników-gości w usłudze SharePoint Online przy użyciu selektora osób **poza** domyślnie. Aby włączyć opcję, aby wyszukać istniejących użytkowników-gości, ustaw **ShowPeoplePickerSuggestionsForGuestUsers** do **na**. Możesz włączyć następujące ustawienie na poziomie dzierżawy lub na poziomie zbioru witryn. To ustawienie można zmienić za pomocą polecenia cmdlet Set-SPOTenant i SPOSite zestawu. Z tych poleceń cmdlet elementy członkowskie można wyszukiwać wszystkich istniejących użytkowników-gości w katalogu. Zmiany w zakresie dzierżawy nie wpływają na witryn usługi SharePoint Online, które już zostały udostępnione.

### <a name="is-the-csv-upload-feature-still-supported"></a>Jest funkcja przekazywania pliku CSV są nadal obsługiwane?
Tak. Aby uzyskać więcej informacji o korzystaniu z funkcji przekazywania plików CSV, zobacz [tego przykładu z programu PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak dostosować wiadomości e-mail z zaproszeniem
Prawie wszystko, co o procesie zapraszającej można dostosować za pomocą [zaproszenie B2B interfejsów API](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Użytkownicy-goście, można zresetować swoje metody uwierzytelniania wieloskładnikowego?
Tak. Użytkownicy-goście mogą resetować swoje metody uwierzytelniania wieloskładnikowego tak samo jak regularne użytkowników.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Której organizacji jest odpowiedzialny za licencje na usługę uwierzytelnianie wieloskładnikowe?
Organizacji zapraszającej jest przeprowadzane uwierzytelnianie wieloskładnikowe. Organizacji zapraszającej musisz upewnić się, że organizacja charakteryzuje się wystarczającą liczbę licencji dla użytkowników B2B, którzy korzystają z uwierzytelniania Multi-Factor Authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co zrobić, jeśli w organizacji partnera, który jest już ma skonfigurować uwierzytelnianie wieloskładnikowe? Można możemy zaufania uwierzytelniania wieloskładnikowego i nie używać własną usługę uwierzytelnianie wieloskładnikowe?
Ta funkcja jest planowana w przyszłej wersji, tak, po czym można wybrać określone partnerów do wykluczenia z usługi uwierzytelnianie wieloskładnikowe (zapraszający organizacji).

### <a name="how-can-i-use-delayed-invitations"></a>Jak używać opóźnione zaproszenia?
Organizacja chcieć dodają użytkowników we współpracy B2B, udostępnić je do aplikacji, zgodnie z potrzebami, a następnie Wyślij zaproszenia. Zaproszenie współpracy B2B interfejs API umożliwia dostosowywanie przepływu pracy przy dołączaniu.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Czy mogę utworzyć użytkowników-gości widoczne w globalnej listy adresowej Exchange?
Tak. Gość obiekty nie są widoczne w Twojej organizacji globalnej liście adresowej (GAL) domyślnie, ale można użyć programu PowerShell usługi Azure Active Directory, aby stały się widoczne. Zobacz [mogę sprawdzić, że obiekty gościa widoczna na globalnej liście adresowej?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#can-i-make-guest-objects-visible-in-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Można utworzyć użytkownika-gościa ograniczony administrator?
Naturalnie. Aby uzyskać więcej informacji, zobacz [dodawania użytkowników-gości do roli](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Współpraca B2B w usłudze Azure AD pozwala użytkownikom B2B dostępu witryny Azure portal?
Chyba że użytkownik przypisany do roli administratora ograniczone, użytkowników we współpracy B2B nie wymagają dostępu do witryny Azure portal. Jednak użytkowników we współpracy B2B, którzy mają przypisaną rolę ograniczony administrator może uzyskać dostęp do portalu. Ponadto jeśli użytkownika gościa, który nie jest przypisany jeden z tych ról Administrator uzyskuje dostęp do portalu, użytkownik może być mogli korzystać z niektórych części środowiska. Rola użytkownika gościa ma niektóre uprawnienia w katalogu.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Czy można zablokować dostęp do witryny Azure portal dla użytkowników-gości?
Tak! Po skonfigurowaniu tych zasad, należy zachować ostrożność uniknąć przypadkowego blokowanie dostępu do członków i administratorów.
Aby zablokować dostęp użytkownika-gościa do [witryny Azure portal](https://portal.azure.com), użyj zasad dostępu warunkowego w interfejsie API modelu klasycznym wdrożeniu platformy Windows Azure:
1. Modyfikowanie **wszyscy użytkownicy** grupy tak, aby zawierała tylko elementy członkowskie.
   ![Zrzut ekranu przedstawiający grupa Wszyscy użytkownicy, których UserType nie jest równy gościa](media/faq/modify-all-users-group.png)
2. Utwórz grupę dynamiczną, która zawiera użytkowników-gości.
   ![Zrzut ekranu przedstawiający nową grupę Wszyscy użytkownicy-goście](media/faq/group-with-guest-users.png)
3. Skonfigurowanie zasad dostępu warunkowego do Blokuj użytkowników-gości z dostęp do portalu, jak pokazano w poniższym klipie wideo:
  
   > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Współpraca B2B w usłudze Azure AD obsługuje uwierzytelnianie wieloskładnikowe i konta poczty e-mail odbiorcy?
Tak. Oba konta e-mail uwierzytelniania i konsumentów usługi Multi-Factor Authentication są obsługiwane dla współpracy B2B usługi Azure AD.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Resetowanie haseł dla użytkowników współpracy B2B usługi Azure AD są obsługiwane?
Jeśli dzierżawa usługi Azure AD jest katalogu macierzystego użytkownika, możesz [resetowania hasła użytkownika](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) w witrynie Azure portal. Jednak bezpośrednio nie można zresetować hasła dla użytkownika gościa, który zaloguje się za pomocą konta, które jest zarządzane przez innego dostawcę tożsamości katalogu lub zewnętrznej usługi Azure AD. Tylko użytkownik-Gość lub administratora w katalogu macierzystym użytkownika może zresetować hasło. Poniżej przedstawiono kilka przykładów, jak działa dla użytkowników-gości Resetowanie hasła:
 
* Użytkowników-gości, którzy Zaloguj się przy użyciu konta Microsoft (na przykład guestuser@live.com) mogą resetować swoje hasła przy użyciu konta Microsoft, Samoobsługowe resetowanie haseł (SSPR). Zobacz [jak zresetować hasło do konta Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Użytkowników-gości, którzy zarejestrują się przy użyciu konta Google lub innego zewnętrznego dostawcy tożsamości mogą resetować swoje hasła przy użyciu metody funkcji samoobsługowego resetowania HASEŁ jego dostawcy tożsamości. Na przykład gościa za pomocą konta Google guestuser@gmail.com mogą zresetować swoje hasło, postępując zgodnie z instrukcjami wyświetlanymi w [zmiany lub resetowania hasła](https://support.google.com/accounts/answer/41078).
* Jeśli dzierżawa tożsamości jest just-in-time (JIT) lub "wirusowego" dzierżawy (co oznacza, że jest oddzielnym niezarządzanej dzierżawy platformy Azure), tylko użytkownik-Gość mogą zresetować swoje hasło. Czasami organizacji będzie [przejęcie zarządzania wirusowe dzierżaw](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) które są tworzone, gdy pracownicy korzystają ich służbowe adresy e-mail, aby się zarejestrować dla usług. Po organizacji ma utworzyć niezbędnej dzierżawy, tylko przez administratorów w organizacji można zresetować hasła użytkownika lub włączyć funkcji samoobsługowego resetowania HASEŁ. W razie potrzeby jako organizacji zapraszającej można usunąć konta gościa z katalogu i ponownie wysłać zaproszenia.
* Jeśli w katalogu macierzystym użytkownika gościa jest dzierżawą usługi Azure AD, możesz zresetować hasła użytkownika. Na przykład możesz być może został utworzony przez użytkownika lub zsynchronizować użytkownika z usługi Active Directory w środowisku lokalnym i ustawić ich UserType do gościa. Ponieważ ten użytkownik jest umieszczone w katalogu, możesz zresetować swoje hasło w witrynie Azure portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365 oferuje pomocy online do współpracy B2B usługi Azure AD?
Tak, Dynamics 365 (online) obsługuje współpracy B2B usługi Azure AD. Aby uzyskać więcej informacji, zobacz artykuł Dynamics 365 [zaprosić użytkowników przy użyciu funkcji współpracy B2B usługi Azure AD](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Co to jest okres istnienia początkowe hasło dla nowo utworzonego użytkownika współpracy B2B?
Usługa Azure AD ma stały zestaw znaków, siły hasła i konta wymagania blokady, które stosuje się jednakowo do wszystkich usługi Azure AD w chmurze kont użytkowników. Konta użytkowników w chmurze są kontami, które nie są Sfederowane z użyciem innego dostawcy tożsamości, takich jak 
* Konto Microsoft
* Facebook
* Usługi Active Directory Federation Services
* Innej dzierżawy w chmurze (na potrzeby współpracy B2B)

W przypadku kont federacyjnych zasady haseł zależy od zasad, które są stosowane w dzierżawy w środowisku lokalnym i ustawienia konta Microsoft użytkownika.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizacja może być wskazane różnych doświadczeń w swoich aplikacjach dla użytkowników dzierżawy i użytkowników-gości. Czy istnieje standardowa wskazówki dotyczące to? Jest obecność dostawcy tożsamości oświadczeń poprawny model używany?
Użytkownik-Gość można użyć dowolnego dostawcy tożsamości do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [właściwości użytkownika współpracy B2B](user-properties.md). Użyj **UserType** właściwości w celu określenia środowisko użytkownika. **UserType** oświadczenia nie jest obecnie uwzględnionym w tokenie. Aplikacje powinny używać interfejsu API programu Graph do wysyłania zapytań o katalog dla użytkownika i UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Gdzie mogę znaleźć udostępnianie rozwiązań i przesyłaj pomysły społeczność współpracy B2B?
Stale słuchamy opinii, aby poprawić współpracę B2B. Udostępnij użytkownikowi scenariuszy, najlepsze rozwiązania i co Ci się podoba współpracy B2B usługi Azure AD. Dołącz do dyskusji w [społeczności technicznej firmy Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Zachęcamy także do przesyłania Twoje pomysły i głosuj na przyszłych funkcji w [pomysły współpracy B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Tak, aby użytkownik po prostu "gotowe" możemy wysłać zaproszenie, który jest automatycznie zrealizowany? Lub użytkownik zawsze należy kliknąć na adres URL realizacji?
Może zapraszać innych użytkowników w organizacji partnera przy użyciu interfejsu użytkownika, skryptów programu PowerShell lub interfejsów API. Następnie możesz wysłać użytkownik-Gość bezpośredni link do udostępnionej aplikacji. W większości przypadków jest już trzeba otworzyć wiadomości e-mail z zaproszeniem i kliknij adres URL realizacji. Zobacz [realizacja zaproszenia współpracy B2B usługi Azure Active Directory](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Współpraca B2B działanie partnerów zaproszonych korzysta federacyjnych można dodać własny mechanizm uwierzytelniania w środowisku lokalnym?
Jeśli partner ma dzierżawę usługi Azure AD, sfederowaną infrastruktury uwierzytelniania w środowisku lokalnym, lokalne logowanie jednokrotne (SSO) automatycznie uzyskuje się. Jeśli partner nie ma dzierżawę usługi Azure AD, zostanie utworzone konto usługi Azure AD dla nowych użytkowników. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Po próbie B2B usługi Azure AD nie zaakceptował gmail.com i outlook.com adresy e-mail i że B2C został użyty podczas tych rodzajów kont?
Zostaną usunięte z różnicami B2B i firma klient (B2C) współpracy, zgodnie z którą tożsamości są obsługiwane. Tożsamość używana jest powód, dla wybór między używaniem B2B przy użyciu usługi B2C. Aby dowiedzieć się, jak wybranie opcji usługi współpracy, zobacz [współpracy porównania B2B i B2C w usłudze Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jakie aplikacje i usługi obsługują użytkowników-gości B2B w usłudze Azure?
Wszystkimi aplikacjami platformy Azure zintegrowanych z usługą AD może obsługiwać użytkowników-gości B2B w usłudze Azure, ale punkt końcowy skonfigurowany jako dzierżawca musi zostać użyty do uwierzytelniania użytkowników-gości. Być może trzeba będzie również [Dostosowywanie oświadczeń](claims-mapping.md) w tokenie SAML, wydawanego po użytkownik-Gość przeprowadza uwierzytelnianie do aplikacji. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Nasi partnerzy braku uwierzytelniania wieloskładnikowego możemy wymusić uwierzytelnianie wieloskładnikowe dla użytkowników-gości B2B?
Tak. Aby uzyskać więcej informacji, zobacz [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>W programie SharePoint można zdefiniować listę "Zezwalaj" lub "odmowa" dla użytkowników zewnętrznych. Możemy to zrobić na platformie Azure?
Tak. Platforma Azure obsługuje współpracy B2B usługi AD list zezwalania i odmowy dla. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jakie licencje musimy użyć usługi Azure AD B2B?
Aby uzyskać informacje o licencjach, aby Twoja organizacja musi korzystać z usługi Azure AD B2B, zobacz [współpracy B2B usługi Azure Active Directory, wskazówki dotyczące licencjonowania](licensing-guidance.md).

### <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)

