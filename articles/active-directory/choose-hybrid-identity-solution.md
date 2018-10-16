---
title: Wybieranie rozwiązania tożsamości hybrydowej platformy Azure | Dokumentacja firmy Microsoft
description: Uzyskaj podstawową wiedzę na temat rozwiązania tożsamości hybrydowej dostępne i zalecenia dotyczące najlepszych decyzji nadzoru tożsamości dla Twojej organizacji.
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
ms.openlocfilehash: 15a28cd5937be103aac888a5fc5485e39854a1b4
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319952"
---
# <a name="microsoft-hybrid-identity-solutions"></a>Rozwiązania tożsamości hybrydowej firmy Microsoft

[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hybrydowych rozwiązań tożsamości umożliwia synchronizowanie obiektów katalogu lokalnego z usługą Azure AD, nadal Zarządzanie użytkownikami lokalnymi. Pierwszy decyzję o wydaniu podczas planowania do synchronizacji usługi lokalnego systemu Windows Server Active Directory z usługą Azure AD jest, czy chcesz użyć zsynchronizować tożsamości lub tożsamość federacyjna. Tożsamości synchronizowane i, opcjonalnie skrótów haseł, należy umożliwić użytkownikom Użyj tego samego hasła dostępu do zarówno lokalnych, jak i zasobów organizacji w chmurze. Dla bardziej zaawansowanych wymagań scenariusza, takich jak logowanie jednokrotne (SSO) lub uwierzytelniania MFA w środowisku lokalnym należy wdrożyć usługi Active Directory Federation Services (AD FS) w celu sfederowania tożsamości. 

Dostępnych jest kilka opcji dotyczących konfigurowania tożsamości hybrydowej. Ten artykuł zawiera informacje ułatwiające wybór najlepszego hosta dla Twojej organizacji, w oparciu o łatwość wdrażania i wymaga określonego Zarządzanie tożsamościami i dostępem. Podczas planowania, model tożsamości, który najlepiej odpowiada wymaganiom Twojej organizacji, należy również wziąć pod uwagę czas, istniejącej infrastruktury, złożoności i kosztów. Czynniki te różnią się dla każdej organizacji i mogą ulec zmianie wraz z upływem czasu. Jednak w przypadku zmiany wymagań, masz również elastyczność, aby przełączyć się do modelu inna tożsamość.

> [!TIP]
> Te rozwiązania są dostarczane przez [program Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="synchronized-identity"></a>Tożsamości synchronizowane 

Synchronizacja tożsamości jest najprostszym sposobem synchronizowania lokalnego katalogu obiektów (użytkowników i grup) z usługą Azure AD. 

![Tożsamość hybrydowa zsynchronizowane](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Synchronizacja tożsamości jest metoda Najłatwiejszym i najszybszym, użytkownicy nadal trzeba utrzymywać oddzielnych haseł dla zasobów w chmurze. Aby tego uniknąć, możesz również (opcjonalnie) [synchronizacji skrótów haseł użytkowników](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) do katalogu usługi Azure AD. Synchronizację skrótów haseł pozwala użytkownikom na logowanie się do zasobów organizacji oparte na chmurze z taką samą nazwę użytkownika i hasło, którego używają lokalnie. Program Azure AD Connect okresowo sprawdza zmiany i przechowuje synchronizacji katalogu usługi Azure AD w katalogu lokalnego. Atrybut użytkownika lub hasło jest zmienione w lokalnej usłudze Active Directory, jest automatycznie aktualizowany w usłudze Azure AD. 

W przypadku większości organizacji, którzy tylko zezwolić użytkownikom logować się do usługi Office 365, aplikacji SaaS i innych zasobów platformy Azure na podstawie usługi AD zaleca się domyślnej opcji synchronizacji haseł. Jeśli to nie zadziała za Ciebie, konieczne będzie wybór między uwierzytelnianiem przekazującym i usług AD FS.

> [!TIP]
> Hasła użytkowników są przechowywane w lokalnych Windows Server Active Directory w postaci wartości skrótu, który reprezentuje hasła rzeczywisty użytkownik. Wartość skrótu jest wynikiem funkcji matematycznych jednokierunkowe (algorytmu wyznaczania wartości skrótu). Nie istnieje metoda można przywrócić wynik jednokierunkowa funkcja zwykły tekst wersję hasła. Nie można używać skrótu hasła do logowania do sieci lokalnej. Gdy postanowisz synchronizować haseł usługi Azure AD Connect wyodrębnia wartości skrótów haseł z lokalnej usługi Active Directory i stosuje zapewnienia dodatkowego bezpieczeństwa przetwarzania, aby wartość skrótu hasła przed jest zsynchronizowany z usługą Azure AD. Synchronizację haseł można również wraz z zapisywaniem zwrotnym haseł umożliwiające Samoobsługowe resetowanie haseł w usłudze Azure AD. Ponadto umożliwia logowanie jednokrotne (SSO) dla użytkowników na komputerach przyłączonych do domeny, które są podłączone do sieci firmowej. Za pomocą logowania jednokrotnego włączonych użytkowników wystarczy wprowadzić nazwę użytkownika do bezpiecznego dostępu do zasobów w chmurze. 
>

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

[Uwierzytelnianie przekazywane usługi Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) rozwiązaniem jest proste hasło sprawdzania poprawności dla usług platformy Azure na podstawie usługi AD za pomocą usługi Active Directory w środowisku lokalnym. Jeśli zasady zabezpieczeń i zgodności dla Twojej organizacji nie zezwalaj na wysyłanie haseł użytkowników, nawet w formie skrótu i potrzebujesz do obsługi klasycznych logowania jednokrotnego dla urządzeń przyłączonych do domeny, zaleca się obliczyć przy użyciu uwierzytelniania przekazywanego. Uwierzytelniania przekazywanego nie wymaga żadnych wdrożenia w sieci obwodowej, co upraszcza infrastruktura wdrażania w porównaniu z usługami AD FS. Gdy użytkownicy logują się przy użyciu usługi Azure AD, ta metoda uwierzytelniania sprawdza poprawność haseł użytkowników bezpośrednio w odniesieniu do usługi Active Directory w środowisku lokalnym.

![Uwierzytelnianie przekazywane](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Przy użyciu uwierzytelniania przekazywanego nie ma potrzeby złożone sieci infrastruktury, a nie trzeba przechowywać haseł lokalnych w chmurze. W połączeniu z logowania jednokrotnego, uwierzytelniania przekazywanego zapewnia naprawdę zintegrowanego środowiska pracy, podczas logowania się do usługi Azure AD lub innych usług w chmurze.

Uwierzytelnianie przekazywane jest skonfigurowany z usługi Azure AD Connect, który używa prostego lokalnego agenta, który nasłuchuje żądań sprawdzania poprawności hasła. Agenta można łatwo wdrożyć na wielu komputerach w celu zapewnienia wysokiej dostępności i równoważenia obciążenia. Ponieważ cała komunikacja jest tylko ruchu wychodzącego, nie jest wymagane dla łącznika do zainstalowania w sieci obwodowej. Wymagania dotyczące komputera serwera dla łącznika są następujące:

- Windows Server 2012 R2 lub nowszy
- Przyłączony do domeny w lesie, za pomocą którego użytkownicy są weryfikowane

## <a name="federated-identity-ad-fs"></a>Tożsamość federacyjna (AD FS)

Aby uzyskać większą kontrolę nad jak użytkownicy uzyskują dostęp usługi Office 365 i innych usług w chmurze, można skonfigurować synchronizacji katalogów za pomocą logowania jednokrotnego (SSO) przy użyciu [Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). Federowanie logowania użytkownika z usługami AD FS deleguje uwierzytelniania do serwera lokalnego, która weryfikuje poświadczenia użytkownika. W tym modelu poświadczeń usługi Active Directory w środowisku lokalnym nigdy nie są przekazywane do usługi Azure AD.

![Tożsamość federacyjna](./media/choose-hybrid-identity-solution/federated-identity.png)

Nazywane również Federację tożsamości, ta metoda logowania zapewnia czy uwierzytelnianie wszystkich użytkowników jest kontrolowany w środowisku lokalnym i umożliwia administratorom zaimplementować bardziej rygorystyczne poziomów kontroli dostępu. Federację tożsamości z usługami AD FS jest to opcja najbardziej skomplikowany i wymaga wdrożenia dodatkowych serwerów w sieci w środowisku lokalnym. Federację tożsamości również zatwierdzenia można świadczenie pomocy technicznej 24 x 7 w danej infrastrukturze usługi Active Directory i usług AD FS. Tak wysokiego poziomu pomocy technicznej jest to konieczne, ponieważ jeśli dostępu do sieci Internet w środowisku lokalnym, kontroler domeny lub serwery usług AD FS są niedostępne, użytkownicy nie mogą się logować do usług w chmurze.

> [!TIP]
> Jeśli zdecydujesz się za pomocą usług federacyjnych Active Directory Federation Services (AD FS), możesz opcjonalnie skonfigurować synchronizację haseł do przechowywania kopii zapasowych na wypadek awarii infrastruktury usług AD FS.
>

## <a name="common-scenarios-and-recommendations"></a>Typowe scenariusze i zalecenia

Poniżej przedstawiono niektóre typowe tożsamości i dostępu do zarządzania scenariuszy hybrydowych obejmujących zalecenia dotyczące hybrydowych, które tożsamości opcja (lub opcje) może być odpowiednie dla każdego.

|Należy:|PWS i logowania jednokrotnego<sup>1</sup>| PTA i logowania jednokrotnego<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synchronizuj nowe, skontaktuj się z pomocą, konta użytkowników i grup automatycznie utworzone w mojej lokalnej usługi Active Directory w chmurze.|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Konfigurowanie mojej dzierżawy na potrzeby scenariuszy hybrydowych usługi Office 365|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Moje użytkownicy mogą się zalogować, a dostęp do usług w chmurze przy użyciu swojego hasła w środowisku lokalnym|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementowanie logowania jednokrotnego przy użyciu poświadczeń firmowych|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)| ![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png) |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Upewnij się, że nie skróty haseł są przechowywane w chmurze| |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Włącz uwierzytelnianie wieloskładnikowe rozwiązania lokalne| | |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Obsługa uwierzytelniania za pomocą kart inteligentnych dla użytkowników<sup>4</sup>| | |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|
|Wyświetlanie powiadomień dotyczących wygasania haseł w portalu pakietu Office i na pulpicie systemu Windows 10| | |![Zalecane](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> synchronizacji haseł za pomocą logowania jednokrotnego.
>
> <sup>2</sup> uwierzytelniania przekazywanego i logowania jednokrotnego. 
>
> <sup>3</sup> federacyjne logowanie jednokrotne za pomocą usług AD FS.
>
> <sup>4</sup> usług AD FS można zintegrować z Enterprise PKI, aby umożliwić Zaloguj się przy użyciu certyfikatów. Te certyfikaty mogą być soft certyfikaty wdrożone za pomocą zaufanego inicjowania obsługi administracyjnej kanałów, takich jak certyfikaty zarządzania urządzeniami Przenośnymi lub zasad grupy lub za pomocą kart inteligentnych (w tym PIV/z dostępem Warunkowym karty) lub Hello dla firm (relacji zaufania certyfikatu). Aby uzyskać więcej informacji na temat obsługi uwierzytelniania za pomocą kart inteligentnych, zobacz [ten blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej w środowisku Azure weryfikacji koncepcji](https://aka.ms/aad-poc)
[zainstalować program Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)
[monitorowania synchronizacji tożsamości hybrydowej](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
