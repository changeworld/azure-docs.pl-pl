---
title: Wybierz rozwiązania z tożsamością hybrydową Azure | Dokumentacja firmy Microsoft
description: Uzyskaj podstawową wiedzę na temat dostępnych hybrydowych rozwiązań tożsamości i zalecenia dotyczące najlepszych decyzji ładu tożsamości dla Twojej organizacji.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 03/02/2018
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 9f9099c0ebd65ba84e171314e6f04d858648a805
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft hybrydowych rozwiązań tożsamości
[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hybrydowych rozwiązań tożsamości umożliwiają do synchronizowania obiektów katalogu lokalnego z usługą Azure AD nadal użytkowników lokalnej i zarządzania nimi. Pierwsza decyzja, aby podczas planowania do synchronizowania lokalnej systemu Windows serwer usługi Active Directory z usługą Azure AD jest Określa, czy ma być używany do synchronizacji tożsamości lub tożsamości federacyjnych. Tożsamości synchronizowane i opcjonalnie skrótów haseł, użytkownicy będą używać tego samego hasła dostępu do zarówno lokalnie, jak i oparte na chmurze zasobów organizacji. Bardziej zaawansowane wymagań scenariusza, takich jak-jednokrotnej (SSO) lub lokalnej usługi MFA należy wdrożyć usługi Active Directory Federation Services (AD FS), możliwości utworzenia federacji tożsamości. 

Dostępnych jest kilka opcji dostępne w celu konfigurowania tożsamości hybrydowej. Ten artykuł zawiera informacje ułatwiające wybór najlepszy dla organizacji, łatwość wdrażania w oparciu i wymaga określonych zarządzania tożsamościami i dostępem. Podczas planowania model tożsamości, który najlepiej odpowiada potrzebom organizacji, należy zastanowić, czas, istniejącej infrastruktury, złożoność i kosztów. Czynniki te są różne dla każdej organizacji i może ulec zmianie. Jednak w przypadku zmiany wymagań, masz również elastyczności, aby przełączyć się do innej tożsamości modelu.

> [!TIP]
> Te rozwiązania są dostarczane przez [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Tożsamości synchronizowane 
Tożsamości synchronizowane jest najprostszym sposobem synchronizowania obiektów katalogu lokalnego (użytkowników i grup) z usługą Azure AD. 

![Tożsamość hybrydowa zsynchronizowane](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Tożsamości synchronizowane jest metoda Najłatwiejszym i najszybszym, użytkownicy nadal należy obsługiwać oddzielnych haseł dla zasobów w chmurze. Aby tego uniknąć, można także (opcjonalnie) [synchronizacji skrótów haseł użytkowników](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) do katalogu usługi Azure AD. Synchronizowanie skrótów haseł pozwala użytkownikom na logowanie się do organizacji zasobów w chmurze przy użyciu tej samej nazwy użytkownika i hasła użycia lokalnego. Azure AD Connect okresowo sprawdza zmiany i przechowuje zsynchronizowany katalog usługi Azure AD w katalogu lokalnym. Atrybut użytkownika lub hasło jest zmienione w lokalnej usłudze Active Directory, jest automatycznie aktualizowany w usłudze Azure AD. 

W przypadku większości organizacji, potrzebują tylko umożliwienie użytkownikom zalogować się do usługi Office 365, aplikacji SaaS i innych zasobów platformy Azure na podstawie usługi AD zaleca się domyślną opcją synchronizacji haseł. Jeśli to nie pomoże Ci, konieczne będzie wybór między uwierzytelniania przekazywanego i usług AD FS.

> [!TIP]
> Hasła użytkowników są przechowywane w lokalnych Windows Server Active Directory w formie wartość skrótu, która reprezentuje rzeczywiste hasło. Wartość skrótu jest wynikiem jednokierunkowej funkcji matematycznych (algorytmu wyznaczania wartości skrótu). Nie istnieje metoda można przywrócić wynik jednokierunkowa funkcja wersji tekstowego hasła. Nie można użyć skrótu hasła do logowania się w sieci lokalnej. Jeśli wybierzesz opcję synchronizacji haseł, Azure AD Connect wyodrębnia skrótów haseł z lokalnej usługi Active Directory i zastosowanie dodatkowych zabezpieczeń przetwarzania do wyznaczania wartości skrótu hasła przed jest zsynchronizowany z usługą Azure AD. Synchronizację haseł można również wraz z zapisywania zwrotnego haseł włączyć własnym resetowania haseł w usłudze Azure AD. Ponadto umożliwia logowanie jednokrotne (SSO) dla użytkowników na komputerach przyłączonych do domeny, które są podłączone do sieci firmowej. Z logowania jednokrotnego włączonych użytkowników tylko trzeba wprowadzić nazwę użytkownika do bezpiecznego dostępu do zasobów w chmurze. 

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane
[Uwierzytelnianie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) zapewnia rozwiązanie weryfikacji proste hasło do usług platformy Azure na podstawie usługi AD przy użyciu lokalnej usługi Active Directory. Jeśli zasady zabezpieczeń i zgodności w firmie nie zezwalają na wysyłanie hasła użytkowników, nawet w formie skrótu i wymaganych do obsługi pulpitów logowania jednokrotnego dla urządzeń przyłączonych do domeny, zaleca się przy użyciu uwierzytelniania przekazywanego oceny. Uwierzytelniania przekazywanego nie wymaga żadnych wdrożenia w sieci obwodowej, co upraszcza infrastruktura wdrażania w porównaniu z usługami AD FS. Podczas logowania przy użyciu usługi Azure AD, ta metoda uwierzytelniania sprawdza poprawność haseł użytkowników bezpośrednio z lokalnej usługi Active Directory.

![Uwierzytelnianie przekazywane](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Przy użyciu przekazywanego uwierzytelniania nie istnieje potrzeba złożoną siecią infrastruktury, a nie ma potrzeby przechowywania haseł lokalnych w chmurze. Połączone z logowania jednokrotnego, uwierzytelnianie przekazywane zapewnia integrację naprawdę podczas logowania do usługi Azure AD lub innych usług w chmurze.

Przekazywanego uwierzytelniania jest skonfigurowane z programem Azure AD Connect, używający agenta proste lokalnymi, która nasłuchuje żądań sprawdzania poprawności hasła. Agenta można łatwo wdrożyć wiele komputerów, aby zapewnić wysoką dostępność i równoważenie obciążenia. Ponieważ cała komunikacja jest tylko ruchu wychodzącego, nie jest wymagane dla łącznika do zainstalowania w sieci obwodowej. Wymagania dotyczące komputera serwera dla łącznika są następujące:

- Windows Server 2012 R2 lub nowszy
- Przyłączony do domeny w lesie, za pomocą którego użytkownicy są weryfikowane

## <a name="federated-identity-ad-fs"></a>Tożsamości federacyjnych (AD FS)
Aby uzyskać większą kontrolę nad dostęp użytkowników usługi Office 365 i innych usług w chmurze, można skonfigurować synchronizacji katalogów z logowaniem jednokrotnym (SSO) przy użyciu [Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). Federowania logowania użytkownika z usługami AD FS deleguje uwierzytelniania do serwera lokalnego, która weryfikuje poświadczenia użytkownika. W tym modelu lokalnej usługi Active Directory poświadczenia nigdy nie są przekazywane do usługi Azure AD.

![Tożsamości federacyjnych](./media/choose-hybrid-identity-solution/federated-identity.png)

Zwanej również Federacją tożsamości, ta metoda logowania gwarantuje, że wszystkie uwierzytelnianie użytkownika jest kontrolowany lokalnymi i pozwala administratorom na wdrożenie bardziej rygorystyczne poziomy kontroli dostępu. Federacji tożsamości z usługami AD FS jest najbardziej skomplikowanych opcji i wymaga wdrażania dodatkowych serwerów w środowisku lokalnym. Federacji tożsamości również zatwierdza należy do zapewnienia pomocy technicznej 24 x 7 dla infrastruktury usługi Active Directory i usług AD FS. Tak wysokiego poziomu obsługi jest konieczne, ponieważ jeśli internetowy lokalnymi, kontroler domeny lub serwery usług AD FS są niedostępne, użytkownicy nie mogą się zalogować w usług w chmurze.

> [!TIP]
> Jeśli zdecydujesz się używać federacyjnego z usługi Active Directory Federation Services (AD FS), można opcjonalnie skonfigurować synchronizacji haseł jako zapasowy na wypadek awarii infrastruktury usług AD FS.


## <a name="common-scenarios-and-recommendations"></a>Typowe scenariusze i zalecenia
Poniżej przedstawiono kilka typowych hybrydowego tożsamościami i dostępem zarządzania scenariuszy wraz z zaleceniami klienckiemu, który hybrydowego opcja tożsamości (lub opcji) mogą być odpowiednie dla każdego.

|Należy:|PWS i logowania jednokrotnego<sup>1</sup>| PTA i logowania jednokrotnego<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synchronizacja nowych, skontaktuj się z pomocą, konta użytkowników i grup tworzone automatycznie w mojej lokalnej usługi Active Directory do chmury.|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Skonfiguruj moje dzierżawy w scenariuszach hybrydowych usługi Office 365|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Moje użytkownikom logować się i uzyskać dostępu do usługi w chmurze przy użyciu swoich haseł lokalnych|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementowanie logowania jednokrotnego przy użyciu poświadczeń firmowych|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Upewnij się, że hasła nie są przechowywane w chmurze| |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Włącz uwierzytelnianie wieloskładnikowe rozwiązań lokalnymi| | |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Obsługa uwierzytelniania za pomocą kart inteligentnych dla moich użytkowników<sup>4</sup>| | |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Wyświetlane powiadomienia o wygaśnięciu hasła w portalu usługi Office i na pulpicie systemu Windows 10| | |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> synchronizacji haseł z logowania jednokrotnego. 

> <sup>2</sup> przekazywanego uwierzytelniania i logowania jednokrotnego. 

> <sup>3</sup> federacyjne logowanie jednokrotne z usługami AD FS.

> <sup>4</sup> usług AD FS można zintegrować z przedsiębiorstwa infrastruktury kluczy publicznych, aby umożliwić logowanie za pomocą certyfikatów. Te certyfikaty mogą być elastyczne certyfikatów wdrożonych za pośrednictwem zaufanego inicjowania obsługi administracyjnej kanałów, takich jak certyfikaty zarządzania urządzeniami Przenośnymi lub zasad grupy lub za pomocą kart inteligentnych (w tym karty PIV/CAC) lub Hello dla firm (relacji zaufania certyfikatów). Aby uzyskać więcej informacji na temat obsługi uwierzytelniania za pomocą kart inteligentnych, zobacz [ten blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej w środowisku Azure koncepcji](https://aka.ms/aad-poc)

[Instalowanie programu Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Monitor synchronizacji tożsamości hybrydowej](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

