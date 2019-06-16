---
title: Wersje usługi Azure MFA i planów zużycie — usługi Azure Active Directory
description: Informacje dotyczące klienta uwierzytelnianie wieloskładnikowe i różnych metod i wersje dostępne.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ee55dd3aebca869da47bbc994f546aa4fe528
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496771"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Jak uzyskać usługę Azure Multi-Factor Authentication

Jeśli chodzi o ochronę kont, weryfikację dwuetapową powinna być standardowa całej organizacji. Ta funkcja jest szczególnie ważne w przypadku konta, które mają uprzywilejowany dostęp do zasobów. Z tego powodu firma Microsoft oferuje funkcje weryfikacji dwuetapowej podstawowe usługi Office 365 i administratorów usługi Azure Active Directory (Azure AD) bez dodatkowych kosztów. Jeśli chcesz uaktualnić funkcje dla administratorów lub rozszerzyć pewne weryfikacji dwuetapowej w pozostałej części użytkowników, możesz kupić usługę Azure Multi-Factor Authentication na kilka sposobów.

> [!IMPORTANT]
> W tym artykule jest przeznaczona do przewodnika, aby lepiej zrozumieć różne sposoby, aby kupić usługę Azure Multi-Factor Authentication. Aby uzyskać szczegółowe informacje na temat cen i rozliczeń, należy zawsze zapoznać się [uwierzytelnianie wieloskładnikowe, na stronie z cennikiem](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostępne wersje usługi Azure Multi-Factor Authentication

W poniższej tabeli opisano różnice między trzy wersje usługi Multi-Factor authentication:

| Version | Opis |
| --- | --- |
| Usługa Multi-Factor Authentication dla usługi Office 365 <br> Microsoft 365 Business | Ta wersja jest zarządzana z portalu usługi Office 365 lub Microsoft 365. Administratorzy mogą [zabezpieczanie zasobów usługi Office 365 za pomocą weryfikacji dwuetapowej](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Ta wersja jest częścią subskrypcji usługi Office 365 lub Microsoft 365 Business. |
| Uwierzytelnianie wieloskładnikowe dla administratorów usługi Azure AD | Użytkownicy z przypisaną rolą administratora globalnego usługi Azure AD w dzierżawach usługi Azure AD można włączyć weryfikację dwuetapową, bez ponoszenia dodatkowych kosztów. |
| Azure Multi-Factor Authentication | Często określane jako "pełnej" wersji, uwierzytelnianie wieloskładnikowe systemu Azure oferuje wizje możliwości. Zapewnia dodatkowe opcje konfiguracji za pomocą [witryny Azure portal](https://portal.azure.com), zaawansowane raportowanie i obsługuje szeroką gamę lokalnych i aplikacji w chmurze. Usługa Azure Multi-Factor Authentication to funkcja [usługi Azure Active Directory — wersja Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features). |

> [!NOTE]
> Nowi klienci nie mogą kupować usługi Azure Multi-Factor Authentication jako autonomiczny oferty od 1 września 2018 r. Uwierzytelnianie wieloskładnikowe, będą w dalszym ciągu być dostępna jako funkcja w licencji usługi Azure AD Premium.

## <a name="feature-comparison-of-versions"></a>Porównanie funkcji wersji

Poniższa tabela zawiera listę funkcji, które są dostępne w różnych wersjach usługi Azure Multi-Factor Authentication.

> [!NOTE]
> Ta tabela porównawcza w tym artykule omówiono funkcje, które są dostępne w ramach każdej wersji usługi Multi-Factor Authentication. Jeśli masz pełną usługę Azure Multi-Factor Authentication, niektóre funkcje mogą być dostępne w zależności od tego, czy używasz [MFA w chmurze lub uwierzytelnianie wieloskładnikowe w środowisku lokalnym](concept-mfa-whichversion.md).
>

| Cecha | Usługa Multi-Factor Authentication dla usługi Office 365 | Uwierzytelnianie wieloskładnikowe dla administratorów usługi Azure AD | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Ochrona konta administratora usługi Azure AD za pomocą usługi MFA |● |● (tylko konta Administrator globalny usługi Azure AD) |● |
| Aplikacji mobilnej jako drugi składnik |● |● |● |
| Połączenie telefoniczne jako drugi składnik |● |● |● |
| Wiadomość SMS jako drugi składnik |● |● |● |
| Hasła aplikacji dla klientów, którzy nie obsługują usługi MFA |● |● |● |
| Kontrola administracyjna nad metod weryfikacji |● |● |● |
| Ochrona konta bez uprawnień administratora za pomocą usługi MFA |● | |● |
| Tryb numeru PIN | | |● |
| Alert dotyczący wykrycia oszustwa | | |● |
| Raporty usługi MFA | | |● |
| Jednorazowe obejście | | |● |
| Niestandardowe powitania dla połączeń telefonicznych | | |● |
| Niestandardowe identyfikacji numeru dla połączeń telefonicznych | | |● |
| Zaufane adresy IP | | |● |
| Pamiętanie uwierzytelniania MFA w przypadku zaufanych urządzeń |● |● |● |
| Usługa MFA dla aplikacji lokalnych | | |● |

> [!IMPORTANT]
> Począwszy od marca 2019 opcji połączenia telefonicznego nie będą dostępne dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ użytkowników w dzierżawach bezpłatnej/wersji próbnej usługi Azure AD. Ta zmiana nie wpływa na wiadomości SMS. Połączenie telefoniczne będą nadal dostępne dla użytkowników w płatną dzierżaw usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżaw bezpłatnej/wersji próbnej usługi Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Jak włączyć usługę Azure Multi-Factor Authentication dla administratorów usługi Azure AD

Użytkownicy z przypisaną rolą administratora globalnego w dzierżawach usługi Azure AD można włączyć weryfikację dwuetapową dla kont administratora globalnego usługi Azure AD bez ponoszenia dodatkowych kosztów. Jeśli korzystasz z Account firmy Microsoft, możesz zarejestrować uwierzytelnianie wieloskładnikowe zgodnie ze wskazówkami zawartymi w artykule pomocy technicznej konta Microsoft, [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Jeśli nie używasz Account firmy Microsoft, włączyć uwierzytelnianie wieloskładnikowe dla administratorów globalnych, zgodnie ze wskazówkami zawartymi w artykule [jak, które wymuszają weryfikację dwuetapową dla użytkownika lub grupy](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Jak kupić usługę Azure Multi-Factor Authentication

Zakup licencji, które obejmują usługi Azure Multi-Factor Authentication, takich jak Azure Active Directory — wersja Premium lub pakietu licencji, która obejmuje usługi Azure AD Premium lub dostępu warunkowego, a następnie przypisać je do użytkowników w usłudze Azure Active Directory.

### <a name="consumption-based-licensing"></a>Licencjonowanie na podstawie użycia

Licencjonowanie na podstawie użycia nie jest już dostępna dla nowych klientów od 1 września 2018 r.

Od 1 września 2018 r. nowe dostawców uwierzytelniania nie jest już może zostać utworzony. Istniejące dostawców uwierzytelniania mogą nadal może być używany i zaktualizowane. Uwierzytelnianie wieloskładnikowe będzie dostępna funkcja licencje usługi Azure AD Premium w.

Korzystając z dostawcę usługi Azure Multi-Factor Authentication, istnieją dwa modele użycia, dostępności, które są rozliczane w ramach subskrypcji platformy Azure:

1. **Za każdego włączonego użytkownika** — dla przedsiębiorstw, które chcesz włączyć weryfikację dwuetapową dla stałej liczby pracowników, którzy będą regularnie uwierzytelniania. Rozliczanie za użytkownika opiera się na liczbie użytkowników korzystających usługi MFA w dzierżawie usługi Azure AD i usługi Serwer Azure MFA. Jeśli użytkownicy są włączone dla usługi MFA w usługi Azure AD i serwer Azure MFA, domen, synchronizacja (Azure AD Connect) jest włączona, a następnie zliczamy większy zbiór użytkowników. Jeśli synchronizacja domeny nie jest włączona, a następnie zliczamy sumę wszystkich użytkowników włączone dla usługi MFA w usłudze Azure AD i serwer Azure MFA. Opłata jest naliczana proporcjonalnie do liczby i zgłoszone w systemie handlu codziennie.

   > [!NOTE]
   > Przykład rozliczeń 1: Masz 5000 użytkowników usługi MFA już dziś. MFA system dzieli tę liczbę przez 31 i raporty 161.29 użytkowników w danym dniu. Jutro włączysz 15 większej liczby użytkowników, dzięki czemu MFA system zgłasza 161.77 użytkowników w danym dniu. Do końca cyklu rozliczeniowego całkowita liczba użytkowników rozliczane przy użyciu subskrypcji platformy Azure do dodaje wokół 5000.
   >
   > Rozliczenia przykład 2: Masz kombinację użytkowników z licencjami użytkowników nie pozwala uzyskać dostawcę usługi MFA Azure na użytkownika, aby stracone. Istnieją 4500 pakietu Enterprise Mobility + Security licencji w dzierżawie usługi, ale 5000 użytkowników włączone dla usługi MFA. Twoja subskrypcja platformy Azure jest opłata 500 użytkowników, proporcjonalnie do liczby i codziennie zgłaszane 16.13 użytkowników.
   >

1. **Na uwierzytelnienie** — dla przedsiębiorstw, które chcesz włączyć weryfikację dwuetapową dla dużej grupy użytkowników, którzy rzadko potrzebują uwierzytelniania. Rozliczenia opiera się na liczbie żądania weryfikacji dwuetapowej, niezależnie od tego, czy te weryfikacji powiedzie się lub odmówiono. Ten sposób rozliczania pojawia się na swoim wyciągu użycia platformy Azure w pakietach 10 uwierzytelnień i jest zgłaszana codziennie.

   > [!NOTE]
   > Przykład rozliczeń 3: Obecnie usługa Azure MFA otrzymała 3,105 żądania weryfikacji dwuetapowej. Twoja subskrypcja platformy Azure jest naliczana za 310.5 pakiety uwierzytelniania.
   >

Należy pamiętać może mieć licencje, ale nadal jest naliczana na podstawie użycia konfiguracji. Po skonfigurowaniu dostawcy usługi MFA Azure na uwierzytelnienie są rozliczenie dla każdego żądania weryfikacji dwuetapowej, nawet te, które wykonywane przez użytkowników, którzy mają licencje. Konfigurowanie dostawcy usługi MFA Azure na użytkownika w domenie, która nie jest połączony z dzierżawą usługi Azure AD możesz są naliczane opłaty za włączonego użytkownika, nawet jeśli użytkownicy będą mieli licencje na usługę Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej szczegółów cennika, zobacz [cennik usługi Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Wybierz, czy mają zostać wdrożone usługi Azure MFA [w chmurze lub środowisku lokalnym](concept-mfa-whichversion.md)
