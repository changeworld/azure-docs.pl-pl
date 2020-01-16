---
title: Wersje i plany użycia usługi Azure MFA — Azure Active Directory
description: Informacje o kliencie usługi MFA i różnych dostępnych metodach i wersjach.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1528bffc613d2e8ab2c0150095d90791b649198a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979498"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Jak uzyskać Multi-Factor Authentication platformy Azure

W przypadku ochrony Twoich kont weryfikacja dwuetapowa powinna być standardowa w całej organizacji. Ta funkcja jest szczególnie ważna w przypadku kont, które mają uprzywilejowany dostęp do zasobów. Z tego powodu firma Microsoft oferuje podstawowe funkcje weryfikacji dwuetapowej dla administratorów pakietu Office 365 i usługi Azure Active Directory (Azure AD) bez dodatkowych kosztów. Jeśli chcesz uaktualnić funkcje dla administratorów lub rozszerzyć weryfikację dwuetapową do pozostałych użytkowników, możesz kupić Multi-Factor Authentication platformy Azure na kilka sposobów.

> [!IMPORTANT]
> Ten artykuł ma na celu ułatwienie zrozumienia różnych sposobów kupowania Multi-Factor Authentication platformy Azure. Aby uzyskać szczegółowe informacje na temat cen i rozliczeń, zawsze należy zapoznać się ze [stroną cennika Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostępne wersje usługi Azure Multi-Factor Authentication

W poniższej tabeli opisano różnice między wersjami usługi uwierzytelniania wieloskładnikowego:

| Wersja | Opis |
| --- | --- |
| Opcja bezpłatna | Klienci korzystający z bezpłatnych korzyści płynących z usługi Azure AD mogą używać [ustawień domyślnych zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) w celu włączenia uwierzytelniania wieloskładnikowego w swoich środowiskach. |
| Uwierzytelnianie Multi-Factor Authentication dla usługi Office 365 | Ta wersja jest zarządzana z poziomu portalu Office 365 lub Microsoft 365. Administratorzy mogą [zabezpieczać zasoby pakietu Office 365 przy użyciu weryfikacji dwuetapowej](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Ta wersja jest częścią subskrypcji pakietu Office 365. |
| Multi-Factor Authentication dla administratorów usługi Azure AD | Użytkownicy przypisani do roli administratora globalnego usługi Azure AD w dzierżawach usługi Azure AD mogą włączyć weryfikację dwuetapową bez dodatkowych kosztów. |
| Usługa Azure Multi-Factor Authentication | Usługa Azure Multi-Factor Authentication jest często określana jako wersja pełna. Zapewnia dodatkowe opcje konfiguracji w ramach [Azure Portal](https://portal.azure.com), zaawansowanego raportowania i obsługi wielu aplikacji lokalnych i w chmurze. Multi-Factor Authentication platformy Azure to funkcja [Azure Active Directory — wersja Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) i [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> Nowi klienci nie mogą już kupować Multi-Factor Authentication platformy Azure jako autonomicznej oferty obowiązującej 1 września, 2018. Uwierzytelnianie wieloskładnikowe będzie nadal dostępne jako funkcja w Azure AD — wersja Premium lub Microsoft 365 Business licencji.

## <a name="feature-comparison-of-versions"></a>Porównanie funkcji w wersjach

Poniższa tabela zawiera listę funkcji, które są dostępne w różnych wersjach systemu Azure Multi-Factor Authentication.

> [!NOTE]
> W tej tabeli porównawczej omówiono funkcje, które są częścią każdej wersji Multi-Factor Authentication. Jeśli masz pełną usługę Azure Multi-Factor Authentication, niektóre funkcje mogą być niedostępne w zależności od tego, czy korzystasz z [uwierzytelniania wieloskładnikowego w chmurze czy](concept-mfa-whichversion.md)w środowisku lokalnym.
>

| Funkcja | Uwierzytelnianie Multi-Factor Authentication dla usługi Office 365 | Multi-Factor Authentication dla administratorów usługi Azure AD | Usługa Azure Multi-Factor Authentication | Domyślne ustawienia zabezpieczeń |
| --- |:---:|:---:|:---:|:---:|
| Ochrona kont administratorów usługi Azure AD za pomocą uwierzytelniania wieloskładnikowego |● |● (Tylko konta administratora globalnego usługi Azure AD) |● |● |
| Aplikacja mobilna jako drugi składnik |● |● |● |● |
| Połączenie telefoniczne jako drugi czynnik |● |● |● |   |
| Wiadomość SMS jako drugi składnik |● |● |● |   |
| Hasła aplikacji dla klientów, którzy nie obsługują usługi MFA |● |● |● |   |
| Kontrola administracyjna nad metodami weryfikacji |● |● |● |   |
| Ochrona kont innych niż administracyjne za pomocą usługi MFA |● | |● |● |
| Tryb numeru PIN | | |● |   |
| Alert dotyczący oszustwa | | |● |   |
| Raporty usługi MFA | | |● |   |
| Jednokrotne obejście | | |● |   |
| Niestandardowe powitania dla połączeń telefonicznych | | |● |   |
| Niestandardowy identyfikator obiektu wywołującego dla połączeń telefonicznych | | |● |   |
| Zaufane adresy IP | | |● |   |
| Pamiętanie uwierzytelniania MFA w przypadku zaufanych urządzeń |● |● |● |   |
| Uwierzytelnianie wieloskładnikowe dla aplikacji lokalnych | | |● |   |

> [!IMPORTANT]
> Począwszy od marca 2019 opcje połączenia telefonicznego nie będą dostępne dla usługi MFA i SSPR użytkowników w bezpłatnych/bezpłatnych dzierżawach Azure AD. Ta zmiana nie ma wpływu na wiadomości SMS. Połączenie telefoniczne będzie nadal dostępne dla użytkowników w płatnych dzierżawach usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżawy usługi Azure AD bezpłatne/próbne.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Jak włączyć usługę Azure Multi-Factor Authentication dla administratorów usługi Azure AD

Użytkownicy, którym przypisano rolę administratora globalnego w dzierżawach usługi Azure AD, mogą włączyć weryfikację dwuetapową dla swoich kont administratora globalnego usługi Azure AD bez dodatkowych kosztów. Jeśli używasz konta Microsoft, możesz zarejestrować się w celu korzystania z uwierzytelniania wieloskładnikowego, korzystając ze wskazówek zawartych w artykule dotyczącym pomocy technicznej konto Microsoft [— Informacje o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Jeśli nie korzystasz z konta Microsoft, Włącz uwierzytelnianie wieloskładnikowe dla administratorów globalnych, korzystając ze wskazówek zawartych w artykule [jak wymagać weryfikacji dwuetapowej dla użytkownika lub grupy](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Jak kupić Multi-Factor Authentication platformy Azure

Kup licencje, które obejmują Multi-Factor Authentication platformy Azure, takie jak Azure Active Directory — wersja Premium, lub pakiet licencji, który obejmuje Azure AD — wersja Premium lub dostęp warunkowy, i przypisz je do użytkowników w Azure Active Directory.

### <a name="consumption-based-licensing"></a>Licencjonowanie na podstawie użycia

Licencjonowanie oparte na zużyciu nie jest już dostępne dla nowych klientów obowiązujące od 1 września 2018.

Od 1 września 2018 nowych dostawców uwierzytelniania nie można już tworzyć. Istniejący dostawcy uwierzytelniania mogą nadal być używane i aktualizowane. Uwierzytelnianie wieloskładnikowe będzie nadal mieć dostępną funkcję w Azure AD — wersja Premium licencji.

W przypadku korzystania z dostawcy usługi Azure Multi-Factor Authentication dostępne są dwa modele użycia, które są rozliczane za pośrednictwem subskrypcji platformy Azure:

1. Dla **poszczególnych włączonych użytkowników** — dla przedsiębiorstw, które chcą włączyć weryfikację dwuetapową dla stałej liczby pracowników, którzy regularnie potrzebują uwierzytelniania. Opłaty za użytkownika są naliczane na podstawie liczby użytkowników włączonych w dzierżawie usługi Azure AD i serwera usługi Azure MFA. Jeśli użytkownicy są włączeni do uwierzytelniania wieloskładnikowego w usłudze Azure AD i na serwerze usługi Azure MFA, a synchronizacja domen (Azure AD Connect) jest włączona, Policzmy większy zbiór użytkowników. Jeśli synchronizacja domeny nie jest włączona, liczba wszystkich użytkowników włączonych dla usługi MFA w usłudze Azure AD i serwerze usługi Azure MFA. Opłaty są naliczane proporcjonalnie do liczby dni w systemie Commerce system.

   > [!NOTE]
   > Przykład rozliczeń 1: dla usługi MFA już dziś włączono 5 000 użytkowników. System MFA dzieli ten numer o 31 i zgłasza 161,29 użytkowników na ten dzień. Jutro jest umożliwienie 15 większej liczby użytkowników, dlatego system MFA zgłasza 161,77 użytkowników na ten dzień. Na koniec cyklu rozliczeniowego całkowita liczba użytkowników rozliczanych w ramach subskrypcji platformy Azure jest dodawana do około 5 000.
   >
   > Przykład rozliczeń 2: masz kombinację użytkowników z licencjami i użytkownikami bez, więc masz dostawcę usługi Azure MFA dla poszczególnych użytkowników, aby wprowadzić różnicę. W dzierżawie są 4 500 Enterprise Mobility + Security licencje, ale dla usługi MFA są włączone użytkownicy 5 000. Twoja subskrypcja platformy Azure jest rozliczana dla 500 użytkowników, proporcjonalnie i raportowanych jako użytkownicy 16,13.
   >

1. **Za uwierzytelnianie** — dla przedsiębiorstw, które chcą włączyć weryfikację dwuetapową dla dużej grupy użytkowników, którzy rzadko potrzebują uwierzytelniania. Opłaty są naliczane na podstawie liczby żądań weryfikacji dwuetapowej, bez względu na to, czy te weryfikacje powiodło się lub zostały odrzucone. Ta rozliczenia pojawiają się w zestawieniu dotyczącym użycia platformy Azure w pakietach 10 uwierzytelnień i jest raportowane codziennie.

   > [!NOTE]
   > Przykład rozliczenia 3: dzisiaj usługa Azure MFA otrzymała żądania weryfikacji 3 105 2-etapowe. Za subskrypcję platformy Azure są naliczane opłaty za pakiety uwierzytelniania 310,5.
   >

Należy pamiętać, że można korzystać z licencji, ale nadal są naliczane opłaty za konfigurację opartą na zużyciu. Jeśli skonfigurowano dostawcę usługi Azure MFA dla uwierzytelniania, opłaty są naliczane za każde żądanie weryfikacji dwuetapowej, nawet te żądania wykonywane przez użytkowników z licencjami. W przypadku skonfigurowania dostawcy usługi Azure MFA dla poszczególnych użytkowników w domenie, która nie jest połączona z dzierżawą usługi Azure AD, opłaty są naliczane za włączonych użytkowników, nawet jeśli użytkownicy mają licencje w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
