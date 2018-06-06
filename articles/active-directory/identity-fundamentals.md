---
title: Podstawowe informacje na temat zarządzania tożsamość platformy Azure
description: Tożsamości w chmurze są teraz najlepszy sposób, aby zachować kontrolę nad i widoczność, jak i kiedy użytkownicy uzyskują dostęp do danych i aplikacji firmy.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: ''
ms.service: active-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 552c777b9ae8b33bc624436aeecc8ef9e097cf3c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713793"
---
# <a name="fundamentals-of-azure-identity-management"></a>Podstawowe informacje na temat zarządzania tożsamość platformy Azure

Jak coraz więcej zasobów cyfrowych firmy na żywo poza siecią firmową w chmurze i na urządzeniach, doskonałe oparte na chmurze tożsamościami i dostępem rozwiązania do zarządzania staje się to konieczne. Tożsamości w chmurze są teraz najlepszy sposób, aby zachować kontrolę nad i widoczność, jak i kiedy użytkownicy uzyskują dostęp do danych i aplikacji firmy.

Microsoft ma zostały zabezpieczanie tożsamości opartej na chmurze dla za pośrednictwem dekadę, a teraz z [Azure Active Directory (AD)](active-directory-whatis.md), tych samych systemów ochrony są dostępne dla Ciebie. Z usługą Azure AD Administratorzy przedsiębiorstwa można łatwo zapewnienia accountability użytkownika i administratora z lepszych zabezpieczeń i zarządzania niż kiedykolwiek wcześniej.

Azure AD Premium jest oparta na chmurze tożsamościami i dostępem rozwiązaniem do zarządzania z funkcji ochrony zaawansowane umożliwiającą jedną tożsamość bezpieczne dla wszystkich aplikacji, ochrona tożsamości (rozszerzone przez [Microsoft analizy zabezpieczeń wykres](https://www.microsoft.com/en-us/security/intelligence)), a Privileged Identity Management. Nie po prostu inną monitorowania lub narzędzia do raportowania usługi Azure AD Premium chronić tożsamości użytkownika w czasie rzeczywistym i umożliwiają tworzenie zasad dostępu ryzyka, adaptacyjną do ochrony danych organizacji.

Obejrzyj ten krótki film, aby szybko zapoznać zarządzania tożsamościami w usłudze Azure AD i ochrony:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft nie tylko udostępnia tożsamości, która przyjmuje wszędzie, ale także zestaw narzędzi umożliwiających zautomatyzowanie, zabezpieczania oraz zarządzania nimi IT w organizacji. Nawet po pojawienie przetwarzania w chmurze jest nadal żądanie zarządzanie i sterowanie IT zadań, takich jak pomoc techniczna wywołania do resetowania hasła użytkownika, Zarządzanie grupami użytkowników i żądań aplikacji. Komplikując dalsze czynności, pracowników są teraz przynoszą do pracy ich urządzeń osobistych i za pomocą łatwo dostępne SaaS aplikacji, co sprawia, że Obsługa kontroli w aplikacjach firmowych centrów danych i chmurze publicznej platformy istotne wyzwanie.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Połączenie lokalnej usługi Active Directory z usługą Azure AD i Office 365
Organizacji, które zostały wprowadzone dużych inwestycji w lokalnej usłudze Active Directory można rozszerzyć tych inwestycji do chmury dzięki zintegrowaniu ich katalogów lokalnych z usługą Azure AD do [hybrydowe Zarządzanie tożsamościami](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Dzięki temu użytkownicy bardziej wydajnej pracy, zapewniając wspólną tożsamość do uzyskiwania dostępu do zasobów, niezależnie od lokalizacji. Użytkownicy i organizacje mogą korzystać rejestracji jednokrotnej (SSO) dostęp do obu zasobów lokalnych i usług, takich jak Office 365 w chmurze.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) jest jedynym narzędziem koniecznych do uzyskania przeprowadzić integrację. Azure AD Connect zapewnia możliwości dla różnych potrzeb synchronizacji tożsamości i zastępuje starsze wersje narzędzi do integracji tożsamości, takie jak DirSync i Azure AD Sync. Z programem Azure AD Connect za pośrednictwem włączono zarządzanie tożsamościami i synchronizacji między lokalnymi i usługą Azure AD:

- Synchronizacja — ten składnik odpowiada za tworzenie użytkowników, grup i innych obiektów. Odpowiada także za zapewnienie zgodności informacji o tożsamości lokalnych użytkowników i grup z informacjami w chmurze. Zapisywanie zwrotne haseł można włączyć w taki sposób, aby zachować synchronizację katalogów lokalnych podczas aktualizacji hasła w usłudze Azure AD.
- Usługi AD FS - Federacja znajduje się funkcja opcjonalna udostępniane przez usługi Azure AD Connect, które można skonfigurować środowisko hybrydowe przy użyciu lokalnej infrastruktury usług AD FS. Organizacje mogą obsługiwać złożone wdrożenia, takie jak karty inteligentnej lub uwierzytelnianie MFA innych firm mogą posłużyć federacji.
- Monitorowanie kondycji — [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) można zapewnia zaawansowane monitorowanie i centralnej lokalizacji w portalu Azure, aby wyświetlić tego działania.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Zwiększyć wydajność i zmniejszyć koszty pomocy technicznej z samoobsługi i jednego znaku w ramach

Pracownicy są bardziej wydajni, jeśli mają jednego użytkownika i hasło do zapamiętania i spójne środowisko z każdego urządzenia. One również zaoszczędzić czas, kiedy można wykonać samoobsługi zadania, takie jak [zresetować zapomniane hasło](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) lub żąda dostępu do aplikacji bez oczekiwania na pomocy technicznej o pomoc.

Usługi Azure AD [rozszerza w lokalnej usłudze Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) w chmurze, umożliwieniem użytkownikom korzystania swoje konta organizacyjne głównej zarówno urządzeń przyłączonych do domeny, zasobów firmy i wszystkie sieci web i aplikacji SaaS potrzebnych do Służy do wykonywania pracy. Oprócz nie trzeba pamiętać wiele zestawów nazwy użytkowników i hasła, dostęp do aplikacji użytkowników można również można automatycznie udostępniane (lub usuwane) na podstawie ich członkostwa w grupach organizacji i ich stan jako pracownika. Można kontrolować tego dostępu dla galerii aplikacji lub własnych aplikacji lokalnych, został opracowany i opublikowanych za pośrednictwem [serwera Proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Zarządzanie i kontrolowanie dostępu do zasobów firmy
Pomocy firmy Microsoft tożsamościami i dostępem zarządzania rozwiązań IT ochrony dostępu do aplikacji i zasobów w firmowym centrum danych i w chmurze, takich jak włączenie dodatkowe poziomy weryfikacji [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)i [zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Monitorowania podejrzanych działań przez zaawansowane zabezpieczenia raportowania, inspekcji i alerty, pomaga ograniczyć potencjalne problemy.

Zasady dostępu warunkowego w usłudze Azure AD Premium zapewniają możesz administratora przedsiębiorstwa, możliwość tworzenia reguł opartych na zasadach dostępu dla dowolnej platformy Azure AD połączonych aplikacji (aplikacji SaaS, niestandardowe aplikacje działające w aplikacji sieci web w chmurze lub lokalnie). Usługi Azure AD ocenia te zasady w czasie rzeczywistym i wymusza je, gdy użytkownik próbuje uzyskać dostęp do aplikacji. Zasady ochrony tożsamość platformy Azure umożliwiają automatyczne wykonywanie akcji, jeśli został odnaleziony podejrzanych działań. Czynności te mogą obejmować blokuje dostęp do użytkowników o wysokim ryzyku, wymuszając uwierzytelnianie wieloskładnikowe i zresetowanie hasła użytkownika, jeśli wygląda poświadczenia zostały naruszone.


## <a name="azure-active-directory-privileged-identity-management"></a>Zarządzanie uprzywilejowanymi tożsamościami usługi Azure Active Directory

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), dołączone do oferty usługi Azure Active Directory Premium P2 pozwala na wykrycie, ograniczyć i monitorować konta z uprawnieniami administracyjnymi i dostępu do zasobów w usłudze Azure Active Directory i innych firmy Microsoft usługi online. Pomaga również administrowania dokładny okres czasu potrzebnego na żądanie dostępu administracyjnego.

Zarządzanie tożsamościami uprzywilejowanymi można wymusić uprawnień administratora na żądanie tak, aby administratorzy mogą żądać wieloskładnikowego uwierzytelniony, tymczasowy podniesienie swoje uprawnienia dla wstępnie skonfigurowane okresów przed ich kont powrót do normalnego użytkownika Stan.

## <a name="benefits-of-azure-identity"></a>Zalety tożsamość platformy Azure

Tożsamość platformy Azure management można:

-   Tworzenie i zarządzanie nimi jednej tożsamości dla każdego użytkownika w całym przedsiębiorstwie, synchronizacja użytkowników, grup i urządzeń z [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Udostępniany pojedynczego logowania jednokrotnego do tysięcy wstępnie zintegrowanych aplikacji SaaS w tym aplikacji lub zapewnić bezpieczny zdalny dostęp do lokalnych aplikacji SaaS przy użyciu [serwera Proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Włącz zabezpieczenie dostępu do aplikacji przez wymuszenie opartych na regułach [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) dla lokalnych i w chmurze aplikacji.

-   Zwiększenie produktywności użytkowników z [samoobsługowego resetowania hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)i grupy, jak i aplikacji przy użyciu żądań dostępu [MyApps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Korzystać z [wysokiej dostępności i niezawodności](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) na całym świecie, klasy przedsiębiorstwa, oparte na chmurze tożsamościami i dostępem rozwiązania do zarządzania.

## <a name="next-steps"></a>Kolejne kroki
[Więcej informacji o rozwiązaniach tożsamość platformy Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)