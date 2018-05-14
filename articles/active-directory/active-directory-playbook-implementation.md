---
title: Implementacja fazy weryfikacji koncepcji podręcznika dotyczącego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Eksploruj i szybkie rozpoczęcie scenariusze Zarządzanie tożsamościami i dostępem
services: active-directory
keywords: Usługa Azure active directory, podręcznika dotyczącego koncepcji, aby zapewnić
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 2f01bdee5ffcc9e2f48278c7209da40ef64cc030
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Dowód koncepcji podręcznika dotyczącego usługi Azure Active Directory: implementacja

## <a name="foundation---syncing-ad-to-azure-ad"></a>Foundation — synchronizowanie usługi AD do usługi Azure AD 

Tożsamość hybrydowa jest podstawą dla większości klientów organizacji, którzy mają już katalog lokalny. Celem jest celowo poświęcać mniej czasu, w tym miejscu możliwie najbliżej do wartości rzeczywistych scenariuszy tożsamościami i dostępem. 

| Scenariusz | Bloki konstrukcyjne| 
| --- | --- |  
| [Rozszerzanie tożsamości lokalnych z chmurą](#extending-your-on-premises-identity-to-the-cloud) | [Synchronizacja katalogów — synchronizacja skrótów haseł](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Uwaga**: Jeśli masz już DirSync/ADSync i jego wcześniejsze wersje programu Azure AD Connect, ten krok jest opcjonalny. Sytuacje, w tym przewodniku może wymagać nowszej wersji programu Azure AD Connect.  <br/>[Znakowania.](active-directory-playbook-building-blocks.md#branding) | 
| [Przypisywanie licencji usługi Azure AD przy użyciu grup](#assigning-azure-ad-licenses-using-groups) | [Oparta na grupy licencji](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Rozszerzanie tożsamości lokalnych z chmurą 

1. Tomasz jest administratorem usługi Active Directory w firmie Contoso. Pobiera on wymagania umożliwiające tożsamości jako usługa dla zestawu użytkowników. Po wykonaniu Kreator Azure AD Connect, tożsamość użytkowników docelowych dostępna w chmurze. 
2. Robert zapyta Susie, jeden z użytkowników docelowych, aby otworzyć panel dostępu usługi Azure Active Directory i upewnij się, że użytkownik może uwierzytelnić. Susie widzi strony marką logowania i panelu dostępu puste, który jest gotowy do włączania dostępu do aplikacji w przyszłości.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Przypisywanie licencji usługi Azure AD przy użyciu grup 

1. Robert jest administrator globalny usługi Azure AD i chce przydzielić licencje usługi Azure AD do określonych użytkowników w ramach początkowego rozmieszczania usługi Azure AD.
2. Robert tworzy grupę dla użytkowników pilotażowych. 
3. Robert przypisuje licencje do grupy
4. Susie, jeden pracownicy przetwarzający informacje, jest dodawane do grupy zabezpieczeń w ramach swojego zadania
5. Po pewnym czasie Susie ma dostęp do usługi Azure AD premium licencji. Spowoduje to włączenie więcej scenariusze weryfikacji Koncepcji później.

## <a name="theme---lots-of-apps-one-identity"></a>Motyw — części aplikacji, co tożsamości

| Scenariusz | Bloki konstrukcyjne| 
| --- | --- |  
| [Integracja aplikacji SaaS - federacyjną rejestracją Jednokrotną](#integrate-saas-applications---federated-sso) | [Konfiguracja federacyjną rejestracją Jednokrotną w modelu SaaS](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Grupy - delegować prawa własności](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integracja aplikacji SaaS — hasło logowania jednokrotnego](#integrate-saas-applications---password-sso) | [Konfiguracja rejestracji Jednokrotnej SaaS hasła](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [Usługa rejestracji Jednokrotnej i zdarzenia cyklu życia tożsamości](#sso-and-identity-lifecycle-events) | [SaaS i cyklem życia tożsamości](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Bezpieczny dostęp do udostępnionego konta](#secure-access-to-shared-accounts) | [SaaS kont konfiguracji udostępnionej](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Bezpieczny dostęp zdalny do aplikacji lokalnych](#secure-remote-access-to-on-premises-applications) | [Konfiguracja serwera Proxy aplikacji](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Synchronizuj tożsamości LDAP do usługi Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Ogólny łącznik LDAP konfiguracji](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integracja aplikacji SaaS - federacyjną rejestracją Jednokrotną 

1. Robert jest administrator globalny usługi Azure AD i otrzymuje żądanie z działu marketingu, aby umożliwić dostęp do swojego wystąpienia usługi ServiceNow. Robert znajduje samouczek krok po kroku w dokumentacji usługi Azure AD i następuje i deleguje przypisywania użytkowników do aplikacji do Kevina, head zespołu marketingu. 
2. Jan zaloguje się jako właściciel usługi ServiceNow uprawnień i przypisuje Susie do aplikacji. Jan powiadomienia, że jego Susie profil został utworzony w usługi ServiceNow automatycznie
3. Susie jest Pracownik przetwarzający informacje w dziale marketingu. Użytkownik loguje się do usługi azure AD i klient znajdzie użytkownik jest przypisany do wszystkich aplikacji SaaS w portalu myapps. Z tego miejsca klika bezproblemowo uzyskuje dostęp do usługi ServiceNow.
4. Działu marketingu chce inspekcji, który uzyskał dostęp do usługi ServiceNow. Robert pobiera raport aktywności i udostępnia ją Kevina za pośrednictwem poczty e-mail.  

### <a name="sso-and-identity-lifecycle-events"></a>Usługa rejestracji Jednokrotnej i zdarzenia cyklu życia tożsamości

1. Susie przyjmuje urlop i przez zasady firmowe lokalną AD Konto wyłączone tymczasowego. Susie nie może zalogować się do usługi Azure AD i dlatego nie można uzyskać dostępu do usługi ServiceNow. 
2. Susie powoduje przeniesienie penetracji z działu marketingu sprzedaży. Jan usuwa jej dostępu usługi ServiceNow. Susie zaloguje myapps usługi azure ad, a nie widzi na kafelku usługi ServiceNow. Po 10 minutach Kevina potwierdza, że konto Susie zostało wyłączone z konsoli zarządzania usługi ServiceNow.

### <a name="integrate-saas-applications---password-sso"></a>Integracja aplikacji SaaS — hasło logowania jednokrotnego

1. Robert konfiguruje dostęp do Atlassian HipChat. HipChat ma integracji hasło logowania jednokrotnego i udzielanie dostępu do Susie
2. Susie loguje się do portalu myapps i widzi łącze do pobrania rozszerzenie przeglądarki IE usługi Azure AD, która pobiera ona
3. Po kliknięciu, klika pobiera monitowani o podanie poświadczeń jej HipChat nazwy użytkownika i hasła. Jest to jednorazowa operacja i po jej zakończeniu użytkownik ma dostęp do HipChat
4. Później za kilka dni Susie otwiera myapps portalu i klika HipChat ponownie. Go pobiera ona bezproblemowy dostęp
5. Jan, właściciel aplikacji HipChat chce inspekcji, kto uzyskiwał dostęp do aplikacji. Robert pobiera raport dotyczący inspekcji i udostępnia ją Kevina za pośrednictwem poczty e-mail. 

### <a name="secure-access-to-shared-accounts"></a>Bezpieczny dostęp do udostępnionego konta 

1. Robert jest zlecił do zabezpieczania udostępnionego dojścia usługi Twitter dla członków zespołu sprzedaży. On dodaje Twitter jako aplikację rejestracji Jednokrotnej i przypisuje go do grupy zabezpieczeń zespół ds. sprzedaży. On podano poświadczenia do udostępnionego konta i jego dostarcza w systemie. 
2. Udostępnianie Twitter poświadczeń nie jest już zaufany ze względu na wiele osób, które wiedząc o tym. Robert umożliwia automatyczne Przerzucanie hasła usługi Twitter.
3. Susie, członek zespołu sprzedaży loguje się do portalu myapps i widzi łącze do pobrania rozszerzenie przeglądarki IE usługi Azure AD. Użytkownik instaluje go.
4. Po kliknięciu ona korzystać bezpośrednio z usługi Twitter. Użytkownik nie zna hasło.
5. Arnold jest również częścią zespołu sprzedaży. Ma to samo środowisko korzystania jako Susie w krokach 3 — 4
6. Pracownicy działu sprzedaży chce inspekcji, który uzyskał dostęp do usługi Twitter. Robert pobiera raport aktywności i udostępnia ją Kevina za pośrednictwem poczty e-mail. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Bezpieczny dostęp zdalny do aplikacji lokalnych

1. Robert, Azure AD administratora globalnego, uzyskał wiele żądań, aby umożliwić pracownikom dostęp do kilku zasobów przydatne lokalnymi, takie jak aplikacja kosztów, podczas pracy zdalnej. Wykonuje [dokumentacji serwera Proxy aplikacji](manage-apps/application-proxy-enable.md) do zainstalowania łącznika i opublikować kosztów jako aplikację serwera Proxy aplikacji. 
2. Robert udostępniać zewnętrzny adres URL aplikacji kosztów Susie, jeden pracowników, którzy wymaga dostępu zdalnego. Użytkownik uzyskuje dostęp do łącza, a po uwierzytelniany w usłudze AAD, jest ona dostęp do aplikacji koszty i wydajność w dalszym ciągu podczas pracy zdalnej. 
3. Robert nadal publikowania aplikacji dodatkowych lokalnych przy użyciu tego samego procesu i zapewniające dostęp do użytkowników, zgodnie z potrzebami. Dodaje dostępu warunkowego i uwierzytelniania wieloskładnikowego dla bardziej poufnego aplikacji, które on publikuje, aby zapewnić dodatkową ochronę.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Synchronizuj tożsamości LDAP do usługi Azure AD

1. Roberta firma ma infrastrukturę złożone tożsamości. Większość użytkowników są obsługiwane wewnątrz systemu Windows Server Active Directory domeny usługi (DODAJE). Niektóre z nich, są zarządzane przez system HR wewnątrz Active Directory Lightweight Directory Services (ADLDS).
2. Robert jest zlecił mu Włączanie dostępu do aplikacji SaaS dla wszystkich użytkowników (także tych nie występują w DODAJE).
3. Robert konfiguruje ogólny łącznik LDAP do pobierania danych z ADLDS w programie Azure AD Connect.
4. Robert tworzy reguły synchronizacji, a więc LDAP użytkowników zostaną wypełnione do środowiska Metaverse, a także do usługi Azure AD
5. Tożsamości synchronizowane Susie, która LDAP użytkownik uzyskuje dostęp do jej SaaS aplikacji w usłudze



> [!IMPORTANT] 
> Jest to Konfiguracja zaawansowana wymagające masz pewną znajomość programu FIM/MIM. Jeśli używane w środowisku produkcyjnym, zaleca się pytania dotyczące tej konfiguracji przejść przez [pomocy technicznej Premium](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Motyw — zwiększenie bezpieczeństwa 

| Scenariusz | Bloki konstrukcyjne| 
| --- | --- |  
| [Administrator bezpiecznego dostępu do konta](#secure-administrator-account-access) | [Usługa Azure MFA z połączeń telefonicznych](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Bezpieczny dostęp do aplikacji](#secure-access-to-applications) | [Dostęp warunkowy dla aplikacji SaaS](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Włącz tylko w czasie administracji](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Ochrona tożsamości oparte na ryzyko](#protect-identities-based-on-risk) | [Wykrywanie zdarzenia ryzyka](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Wdrażanie zasad logowania ryzyka](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [Uwierzytelnianie bez hasła przy użyciu uwierzytelniania opartego na certyfikatach](#authenticate-without-passwords-using-certificate-based-authentication) | [Konfigurowanie uwierzytelniania opartego na certyfikatach](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Administrator bezpiecznego dostępu do konta

1. Robert jest Administrator globalny usługi Azure AD. Zidentyfikował on Stuart jako współadministrator usługi. 
2. Robert konfiguruje Stuart tego konta, aby zawsze wymagają usługi MFA poprawić stan zabezpieczeń
3. Stuart loguje się do portalu Azure i powiadomienia, które musi zarejestrować jego numeru telefonu, aby kontynuować logowanie
4. Kolejnych logowań z Stuart są teraz chronione przy użyciu uwierzytelniania wieloskładnikowego i pobiera on teraz rozmowę telefoniczną w celu zweryfikowania jego tożsamości.

### <a name="secure-access-to-applications"></a>Bezpieczny dostęp do aplikacji

1. Tomasz jest właścicielem business usługi ServiceNow. Firma chce teraz tych użytkowników, aby zalogować się za pomocą usługi MFA podczas uzyskiwania dostępu do spoza sieci firmowej.
2. Bob naszej usługi Azure AD administratora globalnego dodaje zasady dostępu warunkowego do aplikacji usługi ServiceNow, aby włączyć uwierzytelnianie wieloskładnikowe dla dostępu poza
3. Susie, naszych Pracownik przetwarzający informacje logowania w portalu Moje aplikacje i klika pozycję na kafelku usługi ServiceNow. Teraz jest ona wezwał za pomocą usługi MFA.

### <a name="enable-just-in-time-jit-administration"></a>Włącz tylko w witrynie Administracja time (JIT)

1. Robert i Stuart są Administratorzy globalni usługi Azure AD. Chcą włączyć JIT dostęp do zarządzania rolami i rejestrować użycia ról uprzywilejowanych.
2. Robert włącza usługi PIM w dzierżawie usługi Azure AD i staje się administratorem zabezpieczeń. On zmienia się i członkostwo w roli administratora globalnego firmy Stuart stałe do kwalifikujących się.
3. Robert i Stuart wymaga aktywowania ich roli za pomocą portalu Azure przed wykonaniem wszelkie zmiany w konfiguracji usługi Azure AD. 

### <a name="protect-identities-based-on-risk"></a>Ochrona tożsamości oparte na ryzyko 

1. Susie, Pracownik przetwarzający informacje próbuje zalogować się przy użyciu przeglądarki tor. 
2. Robert sprawdza pulpit nawigacyjny ochrony tożsamości usługi Azure AD i widzi na Susie logowania z anonimowych adresów IP. Zespół ds chce żądanie uzyskuje dostęp do użytkowników za pomocą usługi MFA
3. Robert umożliwia zasady ochrony usługi Azure AD Identity zażąda MFA dla zdarzeń o podwyższonym ryzyku średnia lub nowszej
4. Czas przechodzi i Susie dzienniki w przeglądarce Tor ponownie. Tym razem, użytkownik zostanie wyświetlony żądanie uwierzytelniania MFA

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>Uwierzytelnianie bez hasła przy użyciu uwierzytelniania opartego na certyfikatach

1. Tomasz jest administratorem globalnym dla instytucji finansowej, który zabrania używania haseł jako składnik uwierzytelniania dla aplikacji.
2. Robert umożliwia i wymusza certyfikatów uwierzytelniania w usługach AD FS a usługą Azure AD
3. Susie podczas uzyskiwania dostępu do aplikacji jest monit o uwierzytelnienie przy użyciu certyfikatu

## <a name="theme---scale-with-self-service"></a>Motyw — skali z samoobsługi

| Scenariusz | Bloki konstrukcyjne| 
| --- | --- |  
| [Samodzielne resetowanie haseł](#self-service-password-reset) | [Samodzielne resetowanie haseł](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Samoobsługowego dostęp do aplikacji](#self-service-access-to-applications) | [Samoobsługowego dostęp do aplikacji](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Samodzielne resetowanie hasła 

1. Robert jest administrator globalny usługi Azure AD i umożliwia zarządzanie hasłami usługi samoobsługowego do podzbioru użytkowników, w tym Susie. 
2. Dzienniki Susie w portalu myapps i zobacz wiadomość w celu zarejestrowania swoich informacji o zabezpieczeniach dla przyszłych hasła zresetować zdarzenia.
3. Szybko przewiń do przodu za kilka dni Susie zapomni swoje hasło, a ponadto resetuje go za pomocą portalu usługi Azure AD

### <a name="self-service-access-to-applications"></a>Samoobsługowego dostęp do aplikacji 

1. Tomasz jest właścicielem business usługi ServiceNow. Maciej chce użytkownikom "Załóż" ją na żądanie, zamiast dodawania ich jednocześnie
2. Aplikację usługi ServiceNow, aby umożliwiała żądań samoobsługi modyfikuje Bob naszych administratora globalnego usługi Azure AD
3. Susie, naszych Pracownik przetwarzający informacje logowania w portalu Moje aplikacje i kliknie przycisk "Dodaj więcej aplikacji" i zobacz usługi ServiceNow jako jedną z zalecanych aplikacji. Następnie użytkownik nawiguje wstecz do mojej aplikacji portalu i zobaczyć aplikację usługi ServiceNow.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]