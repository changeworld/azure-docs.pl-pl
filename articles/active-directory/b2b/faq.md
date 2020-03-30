---
title: Często zadawane pytania dotyczące współpracy b2b — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące współpracy b2b usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92c3e0d77a26db406e24d6d2fa07e96349613634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050819"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Często zadawane pytania dotyczące współpracy usługi Azure Active Directory B2B

Te często zadawane pytania (często zadawane pytania) dotyczące współpracy między firmami usługi Azure Active Directory (Azure AD) (B2B) są okresowo aktualizowane w celu uwzględnienia nowych tematów.

   > [!IMPORTANT]
   > **Począwszy od 31 marca 2021**r. firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc niezarządzane konta usługi Azure AD i dzierżawy scenariuszy współpracy B2B. W ramach przygotowań zachęcamy klientów do wyrażenia zgody na [jednorazowe uwierzytelnianie kodem dostępu pocztą e-mail.](one-time-passcode.md) Cieszymy się z waszych opinii na temat tej publicznej funkcji podglądu i cieszymy się, że możemy stworzyć jeszcze więcej sposobów współpracy.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Czy możemy dostosować naszą stronę logowania, aby była bardziej intuicyjna dla naszych użytkowników-gości współpracujących z B2B?
Jak najbardziej! Zobacz nasz [wpis na blogu o tej funkcji](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Aby uzyskać więcej informacji na temat dostosowywania strony logowania w organizacji, zobacz [Dodawanie znakowania firmy do logowania i strony Panelu dostępu](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Czy użytkownicy współpracujący b2b mogą uzyskiwać dostęp do usług SharePoint Online i OneDrive?
Tak. Jednak możliwość wyszukiwania istniejących użytkowników-gości w usłudze SharePoint Online przy użyciu selektora osób jest domyślnie **wyłączona.** Aby włączyć opcję wyszukiwania istniejących użytkowników-gości, ustaw **opcję ShowPeoplePickerSuggestionsForGuestUsers** **na On**. To ustawienie można włączyć na poziomie dzierżawy lub na poziomie zbioru witryn. To ustawienie można zmienić za pomocą poleceń cmdlet Set-SPOTenant i Set-SPOSite. Dzięki tym poleceń cmdlet członkowie mogą przeszukiwać wszystkich istniejących użytkowników-gości w katalogu. Zmiany w zakresie dzierżawy nie mają wpływu na witryny usługi SharePoint Online, które zostały już zainicjowane.

### <a name="is-the-csv-upload-feature-still-supported"></a>Czy funkcja przekazywania csv jest nadal obsługiwana?
Tak. Aby uzyskać więcej informacji na temat korzystania z funkcji przekazywania plików csv, zobacz [przykład programu PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak mogę dostosować e-maile z zaproszeniami?
Możesz dostosować prawie wszystko o procesie zapraszania za pomocą [interfejsów API zaproszenia B2B](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Czy użytkownicy-goście mogą zresetować metodę uwierzytelniania wieloskładnikowego?
Tak. Użytkownicy-goście mogą zresetować metodę uwierzytelniania wieloskładnikowego w taki sam sposób, jak zwykli użytkownicy.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Która organizacja jest odpowiedzialna za licencje uwierzytelniania wieloskładnikowego?
Zapraszająca organizacja wykonuje uwierzytelnianie wieloskładnikowe. Zapraszająca organizacja musi upewnić się, że organizacja ma wystarczającą liczbę licencji dla swoich użytkowników B2B, którzy korzystają z uwierzytelniania wieloskładnikowego.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co zrobić, jeśli organizacja partnerska ma już skonfigurowane uwierzytelnianie wieloskładnikowe? Czy możemy zaufać ich uwierzytelnianiu wieloskładnikowego, a nie korzystać z własnego uwierzytelniania wieloskładnikowego?
Ta funkcja nie jest obecnie obsługiwana. Jeśli dostęp do zasobów organizacji wymaga uwierzytelniania wieloskładnikowego, organizacja partnerska będzie musiała zarejestrować się w celu uwierzytelniania wieloskładnikowego w organizacji (zapraszającej).

### <a name="how-can-i-use-delayed-invitations"></a>Jak mogę skorzystać z opóźnionych zaproszeń?
Organizacja może chcieć dodać użytkowników współpracy B2B, aprowizować je do aplikacji w razie potrzeby, a następnie wysyłać zaproszenia. Za pomocą interfejsu API zaproszenia do współpracy B2B można dostosować przepływ pracy dołączania.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Czy mogę uwidocznić użytkowników-gości na globalnej liście adresów programu Exchange?
Tak. Obiekty gościa nie są domyślnie widoczne na globalnej liście adresów organizacji, ale można użyć programu Azure Active Directory PowerShell, aby były widoczne. Zobacz [Czy obiekty gościa mogą być widoczne na globalnej liście adresów?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Czy mogę uczynić użytkownika-gościa ograniczonym administratorem?
Naturalnie. Aby uzyskać więcej informacji, zobacz [Dodawanie użytkowników-gości do roli](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Czy współpraca usługi Azure AD B2B umożliwia użytkownikom B2B dostęp do witryny Azure portal?
Jeśli użytkownik nie zostanie przypisany do roli ograniczonego administratora, użytkownicy współpracy B2B nie będą wymagać dostępu do witryny Azure portal. Jednak użytkownicy współpracy B2B, którym przypisano rolę ograniczonego administratora, mogą uzyskać dostęp do portalu. Ponadto jeśli użytkownik-gość, który nie jest przypisany do jednej z tych ról administratora uzyskuje dostęp do portalu, użytkownik może mieć dostęp do niektórych części środowiska. Rola użytkownika gościa ma pewne uprawnienia w katalogu.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Czy mogę zablokować dostęp do witryny Azure portal dla użytkowników-gości?

Tak! Można utworzyć zasadę dostępu warunkowego, która blokuje wszystkim użytkownikom-gościom i użytkownikom zewnętrznym dostęp do witryny Azure portal. Podczas konfigurowania tej zasady należy uważać, aby uniknąć przypadkowego zablokowania dostępu do członków i administratorów.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator zabezpieczeń lub administrator dostępu warunkowego.
2. W witrynie Azure Portal wybierz pozycję **Azure Active Directory**. 
3. W obszarze **Zarządzanie**wybierz pozycję **Zabezpieczenia**.
4. W obszarze **Chroń**pozycję Dostęp warunkowy wybierz pozycję **Dostęp warunkowy**. Wybierz **pozycję Nowa zasada**.
5. Na stronie **Nowy** w polu tekstowym **Nazwa** wprowadź nazwę zasad (na przykład "Zablokuj gościom dostęp do portalu").
6. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
7. Na karcie **Dołączanie** wybierz pozycję **Wybierz użytkowników i grupy**, a następnie wybierz pozycję Wszyscy **użytkownicy-goście i użytkownicy zewnętrzni (Podgląd)**.
9. Wybierz pozycję **Done** (Gotowe).
10. Na stronie **Nowe** w sekcji **Przydziały** wybierz pozycję **Aplikacje lub akcje w chmurze**.
11. Na stronie **Aplikacje lub akcje w chmurze** wybierz pozycję **Wybierz aplikacje**, a następnie wybierz pozycję **Wybierz**.
12. Na stronie **Wybieranie** wybierz pozycję **Microsoft Azure Management**, a następnie wybierz pozycję **Wybierz**.
13. Na stronie **Aplikacje lub akcje w chmurze** wybierz pozycję **Gotowe**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Czy współpraca usługi Azure AD B2B obsługuje uwierzytelnianie wieloskładnikowe i konsumenckie konta e-mail?
Tak. Uwierzytelnianie wieloskładnikowe i konsumenckie konta e-mail są obsługiwane dla współpracy usługi Azure AD B2B.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Czy obsługujesz resetowanie haseł dla użytkowników współpracujących usługi Azure AD B2B?
Jeśli dzierżawa usługi Azure AD jest katalogiem macierzystym dla użytkownika, możesz [zresetować hasło użytkownika](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) z witryny Azure portal. Nie można jednak bezpośrednio zresetować hasła dla użytkownika-gościa, który loguje się za pomocą konta zarządzanego przez inny katalog usługi Azure AD lub zewnętrznego dostawcę tożsamości. Tylko użytkownik-gość lub administrator w katalogu domowym użytkownika może zresetować hasło. Oto kilka przykładów działania resetowania hasła dla użytkowników-gości:
 
* Użytkownicy-goście, którzy logują się guestuser@live.comza pomocą konta Microsoft (na przykład) mogą resetować własne hasła przy użyciu samoobsługowego resetowania haseł (SSPR). Zobacz [Jak zresetować hasło do konta Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Użytkownicy-goście, którzy logują się za pomocą konta Google lub innego zewnętrznego dostawcy tożsamości, mogą resetować własne hasła przy użyciu metody SSPR dostawcy tożsamości. Na przykład użytkownik-gość z guestuser@gmail.com kontem Google może zresetować swoje hasło, postępując zgodnie z instrukcjami w [obszarze Zmień lub zresetować hasło](https://support.google.com/accounts/answer/41078).
* Jeśli dzierżawa tożsamości jest dzierżawą just-in-time (JIT) lub "wirusową" (co oznacza, że jest oddzielną, niezarządzaną dzierżawą platformy Azure), tylko użytkownik-gość może zresetować swoje hasło. Czasami organizacja [przejmie zarządzanie wirusowymi najemcami,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) które są tworzone, gdy pracownicy używają swoich służbowych adresów e-mail do rejestracji w usługach. Po przejęcie przez organizację dzierżawy wirusowej tylko administrator w tej organizacji może zresetować hasło użytkownika lub włączyć wiele łatW. W razie potrzeby jako organizacja zapraszająca można usunąć konto użytkownika gościa z katalogu i ponownie wysuń zaproszenie.

* Jeśli katalog domowy użytkownika-gościa jest dzierżawą usługi Azure AD, możesz zresetować hasło użytkownika. Na przykład użytkownik został utworzony lub zsynchronizowany z lokalnej usługi Active Directory i ustawiony na UserType na Gość. Ponieważ ten użytkownik jest w katalogu, można zresetować swoje hasło z witryny Azure portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Czy system Microsoft Dynamics 365 zapewnia obsługę online współpracy usługi Azure AD B2B?
Tak, Dynamics 365 (online) obsługuje współpracę usługi Azure AD B2B. Aby uzyskać więcej informacji, zobacz artykuł Dynamics 365 [Zapraszanie użytkowników do współpracy usługi Azure AD B2B](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Jaki jest okres istnienia początkowego hasła dla nowo utworzonego użytkownika współpracy B2B?
Usługa Azure AD ma stały zestaw znaków, siły hasła i wymagania dotyczące blokady konta, które mają zastosowanie równo do wszystkich kont użytkowników w chmurze usługi Azure AD. Konta użytkowników w chmurze to konta, które nie są sfederowane z innym dostawcą tożsamości, na przykład 
* Konto Microsoft
* Facebook
* Usługi Active Directory Federation Services
* Kolejna dzierżawa chmury (dla współpracy B2B)

W przypadku kont federacyjnych zasady haseł zależą od zasad stosowanych w lokalnym najem i ustawieniach konta Microsoft użytkownika.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizacja może chcieć mieć różne środowiska w swoich aplikacjach dla użytkowników dzierżawy i użytkowników-gości. Czy istnieją standardowe wytyczne w tym zakresie? Czy obecność dostawcy tożsamości twierdzi, że jest to poprawny model do użycia?
Użytkownik-gość może użyć dowolnego dostawcy tożsamości do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [właściwości użytkownika współpracy B2B](user-properties.md). Użyj **UserType** właściwość, aby określić środowisko użytkownika. **UserType** oświadczenie nie jest obecnie uwzględniony w tokenie. Aplikacje powinny używać interfejsu API programu Microsoft Graph do wykonywania zapytań o katalog użytkownika i uzyskania typu użytkownika.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Gdzie mogę znaleźć społeczność współpracy B2B, aby dzielić się rozwiązaniami i zgłaszać pomysły?
Nieustannie słuchamy Twoich opinii, aby poprawić współpracę b2b. Podziel się swoimi scenariuszami użytkowników, najlepszymi rozwiązaniami i tym, co lubisz w zakresie współpracy usługi Azure AD B2B. Dołącz do dyskusji w społeczności [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Zapraszamy również do zgłaszania swoich pomysłów i głosowania na przyszłe funkcje w [B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Czy możemy wysłać zaproszenie, które zostanie automatycznie zrealizowane, aby użytkownik był po prostu "gotowy do pracy"? A może użytkownik zawsze musi kliknąć adres URL realizacji?
Można zaprosić innych użytkowników w organizacji partnerskiej przy użyciu interfejsu użytkownika, skryptów programu PowerShell lub interfejsów API. Następnie można wysłać użytkownikowi-gościowi bezpośrednie łącze do udostępnionej aplikacji. W większości przypadków nie ma już potrzeby otwierania zaproszenia e-mail i klikania adresu URL realizacji. Zobacz, [jak realizacja zaproszenia do współpracy usługi Azure Active Directory B2B](redemption-experience.md)została

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Jak działa współpraca B2B, gdy zaproszony partner używa federacji do dodawania własnego uwierzytelniania lokalnego?
Jeśli partner ma dzierżawę usługi Azure AD, która jest sfederowana z lokalną infrastrukturą uwierzytelniania, lokalne logowanie jednokrotne (Logowanie jednokrotne) jest automatycznie osiągane. Jeśli partner nie ma dzierżawy usługi Azure AD, konto usługi Azure AD jest tworzone dla nowych użytkowników. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Myślałem, że usługa Azure AD B2B nie akceptuje gmail.com i outlook.com adresów e-mail i że B2C był używany dla tego rodzaju kont?
Usuwamy różnice między B2B a współpracą między przedsiębiorstwami a konsumentami (B2C), pod względem której obsługiwane są tożsamości. Używana tożsamość nie jest dobrym powodem, aby wybrać między użyciem B2B lub przy użyciu B2C. Aby uzyskać informacje dotyczące wybierania opcji współpracy, zobacz [Porównanie współpracy B2B i B2C w usłudze Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jakie aplikacje i usługi obsługują użytkowników-gościa platformy Azure B2B?
Wszystkie aplikacje zintegrowane z usługą Azure AD mogą obsługiwać użytkowników-gościa usługi Azure B2B, ale muszą używać punktu końcowego skonfigurowanego jako dzierżawa do uwierzytelniania użytkowników-gości. Może być również konieczne [dostosowanie oświadczeń](claims-mapping.md) w tokenie SAML, który jest wystawiany, gdy użytkownik-gość uwierzytelnia się w aplikacji. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Czy możemy wymusić uwierzytelnianie wieloskładnikowe dla użytkowników-gości B2B, jeśli nasi partnerzy nie mają uwierzytelniania wieloskładnikowego?
Tak. Aby uzyskać więcej informacji, zobacz [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>W programie SharePoint można zdefiniować listę "zezwalaj" lub "odmów" dla użytkowników zewnętrznych. Czy możemy to zrobić na platformie Azure?
Tak. Współpraca usługi Azure AD B2B obsługuje listy zezwalają na listy i listy odmów. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jakich licencji potrzebujemy do korzystania z usługi Azure AD B2B?
Aby uzyskać informacje o licencjach, których organizacja potrzebuje do korzystania z usługi Azure AD B2B, zobacz [Wskazówki dotyczące licencjonowania współpracy usługi Azure Active Directory B2B](licensing-guidance.md).

### <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)

