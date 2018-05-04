---
title: Wersje usługi Azure MFA i planów zużycie | Dokumentacja firmy Microsoft
description: Informacje o kliencie usługi Multi-Factor Authentication i różnych metod i dostępne wersje. Szczegółowe informacje dotyczące każdego planu zużycie
keywords: ''
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: c5ed3674c724b30962fbd52b9955ec89c35745d1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Jak uzyskać uwierzytelnianie wieloskładnikowe Azure

Jeśli chodzi o ochronę konta weryfikacji dwuetapowej powinna być standardowe całej organizacji. Ta funkcja jest szczególnie ważne w przypadku konta z uprawnieniami administracyjnymi, które mają uprzywilejowany dostęp do zasobów. Z tego powodu firma Microsoft oferuje funkcje weryfikacji dwuetapowej podstawowe usługi Office 365 i Azure administratorzy nie żadnymi dodatkowymi kosztami. Jeśli chcesz uaktualnić funkcje dla administratorów lub rozszerzyć weryfikację dwuetapową do pozostałej części użytkowników, możesz kupić Azure Multi-Factor Authentication. 

W tym artykule opisano różnice między wersjami oferowane dla administratorów i pełną wersję usługi Azure MFA. Jeśli wszystko jest gotowe do wdrożenia pełną usługi Azure MFA oferty, dalszej części artykułu opisano opcje wdrażania i jak Microsoft oblicza zużycia.


>[!IMPORTANT]
>W tym artykule ma być przewodnik ułatwią zrozumienie różnych sposobów kupić usługę Azure Multi-Factor Authentication. Aby uzyskać szczegółowe informacje na temat cennik i rozliczenia, należy zawsze zapoznać się [uwierzytelnianie wieloskładnikowe, na stronie dotyczącej cen](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostępne wersje usługi Azure Multi-Factor Authentication

W poniższej tabeli opisano różnice między trzy wersje usługi Multi-Factor Authentication:

| Wersja | Opis |
| --- | --- |
| Usługa Multi-Factor Authentication dla usługi Office 365 |Ta wersja działa wyłącznie z aplikacjami usługi Office 365 i jest zarządzany z portalu usługi Office 365. Administratorzy mogą [zabezpieczenia zasobów usługi Office 365 w trakcie weryfikacji dwuetapowej](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Ta wersja jest częścią subskrypcji usługi Office 365. |
| Uwierzytelnianie wieloskładnikowe dla administratorów usługi Azure AD | Użytkownicy z przypisaną rolę administratora globalnego w dzierżaw usługi Azure AD można włączyć weryfikację dwuetapową dla konta administratora globalnego usługi Azure AD bez ponoszenia dodatkowych kosztów.|
| Azure Multi-Factor Authentication | Często określany jako "pełnej" wersji, uwierzytelnianie wieloskładnikowe Azure oferuje najszerszym zestaw funkcji. Zapewnia dodatkowe opcje konfiguracji za pomocą [portalu Azure](https://portal.azure.com), zaawansowane raportowanie i obsługuje szeroką gamę lokalnych i aplikacji w chmurze. Uwierzytelnianie wieloskładnikowe platformy Azure jest uwzględniona w [planów usługi Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) i [Enterprise Mobility + Security planów](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing)i może zostać wdrożony w chmurze lub lokalnie. |

## <a name="feature-comparison-of-versions"></a>Porównanie funkcji wersji
Poniższa tabela zawiera listę funkcji, które są dostępne w różnych wersjach Azure Multi-Factor Authentication.

> [!NOTE]
> Ta tabela porównawcza opisano funkcje, które są częścią każdej wersji usługi Multi-Factor Authentication. Jeśli masz pełne usługi Azure Multi-Factor Authentication, niektóre funkcje mogą być dostępne w zależności od tego, czy używasz [MFA w chmurze lub uwierzytelnianie wieloskładnikowe lokalnymi](concept-mfa-whichversion.md).


| Cecha | Usługa Multi-Factor Authentication dla usługi Office 365 | Uwierzytelnianie wieloskładnikowe dla administratorów usługi Azure AD | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Ochrona kont administratorów usługi Azure AD za pomocą usługi MFA |● |● (tylko dla konta administratora globalnego usługi Azure AD) |● |
| Aplikacji mobilnej jako czynnika |● |● |● |
| Połączenie telefoniczne jako czynnika |● |● |● |
| SMS jako czynnika |● |● |● |
| Hasła aplikacji dla klientów, które nie obsługują uwierzytelniania Wieloskładnikowego |● |● |● |
| Administrator kontrolę nad metody weryfikacji |● |● |● |
| Ochrona konta bez uprawnień administratora za pomocą usługi MFA |● (tylko dla aplikacji usługi Office 365) | |● |
| Tryb numeru PIN | | |● |
| Alert dotyczący wykrycia oszustwa | | |● |
| Raporty usługi MFA | | |● |
| Jednorazowe obejście | | |● |
| Niestandardowe powitania dla połączeń telefonicznych | | |● |
| Identyfikator wywołującego niestandardowych dla połączeń telefonicznych | | |● |
| Zaufane adresy IP | | |● |
| Pamiętanie uwierzytelniania MFA w przypadku zaufanych urządzeń |● |● |● |
| Zestaw MFA SDK | | |● (przestarzałe) | 
| Usługi MFA dla aplikacji lokalnych | | |● |

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Jak włączyć uwierzytelnianie wieloskładnikowe Azure dla administratorów usługi Azure AD
Użytkownicy z przypisaną rolę administratora globalnego w dzierżaw usługi Azure AD można włączyć weryfikację dwuetapową dla konta administratora globalnego usługi Azure AD bez ponoszenia dodatkowych kosztów. Jeśli używasz Account Microsoft można zarejestrować w usłudze Multi-Factor authentication [tutaj](https://support.microsoft.com/en-us/help/12408/microsoft-account-about-two-step-verification). Jeśli nie używasz Account firmy Microsoft, należy włączyć uwierzytelnianie wieloskładnikowe dla administratorów globalnych [tutaj](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).

## <a name="how-to-get-azure-multi-factor-authentication"></a>Jak uzyskać uwierzytelnianie wieloskładnikowe Azure
Jeśli chcesz wszystkie funkcje oferowane przez usługi Azure Multi-Factor Authentication, dostępnych jest kilka opcji:

### <a name="option-1---mfa-licenses"></a>Opcja 1 — licencji usługi MFA

Zakupu licencji Azure Multi-Factor Authentication i przypisać je do użytkowników w usłudze Azure Active Directory. 

Jeśli używasz tej opcji, Utwórz dostawcy usługi Azure Multi-Factor Authentication tylko, jeśli należy zapewnić weryfikacji dwuetapowej dla niektórych użytkowników, które nie mają licencji. W przeciwnym razie należy może zostać użyta, dwa razy.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>Opcja 2 — powiązane licencji, które obejmują usługi MFA

Zakupu licencji, które obejmują usługi Azure Multi-Factor Authentication, takich jak Azure Active Directory Premium lub pakietu Enterprise Mobility + Security i przypisać je do użytkowników w usłudze Azure Active Directory. 

Jeśli używasz tej opcji, należy utworzyć dostawcę usługi Azure Multi-Factor Authentication tylko wtedy, gdy musisz także podać weryfikacji dwuetapowej dla niektórych użytkowników, które nie mają licencji. W przeciwnym razie należy może zostać użyta, dwa razy. 

### <a name="option-3---mfa-consumption-based-model"></a>Opcja 3 - MFA na podstawie zużycia modelu

Utwórz dostawcę usługi Azure Multi-Factor Authentication w ramach subskrypcji platformy Azure. Azure MFA dostawcy są zasobów platformy Azure, które są rozliczane przed Twoje umowy Enterprise Agreement, Azure zobowiązań pieniężnych lub karty kredytowej, takie jak innych zasobów platformy Azure. Tych dostawców można tworzyć tylko w pełnej Azure subskrypcji, innymi subskrypcji platformy Azure, które mają 0 $ limit wydatków. Ograniczone subskrypcje są tworzone podczas aktywacji licencji, podobnie jak w opcjach 1 i 2. 

Podczas korzystania z dostawcy usługi Azure Multi-Factor Authentication, istnieją dwa modele użycia dostępne, które są rozliczane za pośrednictwem subskrypcji platformy Azure:  

1. **Każdego włączonego użytkownika** — w przypadku przedsiębiorstw, które chcesz włączyć weryfikację dwuetapową dla stałej liczby pracowników, którzy regularnie muszą uwierzytelniania. Karta użytkownika opiera się na liczbę użytkowników włączone dla usługi MFA w dzierżawie usługi Azure AD i z serwera usługi Azure MFA. Jeśli użytkownicy są włączone dla usługi MFA w obu usługi Azure AD i serwera usługi Azure MFA i synchronizacji domeny (Azure AD Connect) jest włączona, a następnie możemy liczba większy zestaw użytkowników. Jeśli synchronizacja domeny nie jest włączona, a następnie możemy liczba sumę wszystkich użytkowników włączone dla usługi MFA w usłudze Azure AD i serwera usługi Azure MFA. Obliczone proporcjonalnie i zgłoszone w systemie Commerce codziennie rozliczeniami. 

  > [!NOTE]
  > Przykład rozliczeń 1: 5000 użytkowników włączone dla usługi MFA dzisiaj. MFA system dzieli ten numer 31, a użytkownicy 161.29 raportów dla danego dnia. Jutro 15 większą liczbę użytkowników, zostanie włączone, MFA system zgłasza 161.77 użytkowników do danego dnia. Do końca cyklu rozliczeniowego całkowita liczba użytkowników rozliczane względem subskrypcji platformy Azure do dodaje wokół 5000. 
  >
  > Przykład rozliczeń 2: masz kombinację użytkowników z licencjami i użytkowników bez, dlatego należy dostawcę dla poszczególnych użytkowników usługi Azure MFA powstała różnica. Brak 4500 pakietu Enterprise Mobility + licencji zabezpieczeń w swojej dzierżawy, ale 5000 użytkownikami włączone dla usługi MFA. Subskrypcji platformy Azure jest rozliczany 500 użytkownikom, obliczone proporcjonalnie i codziennie zgłoszone jako 16.13 użytkowników. 

2. **Każde uwierzytelnianie** — w przypadku przedsiębiorstw, które chcesz włączyć weryfikację dwuetapową dla dużej grupy użytkowników, którzy rzadko potrzebują uwierzytelniania. Karta opiera się na liczbę żądań weryfikacji dwuetapowej, niezależnie od tego, czy te weryfikacji powiedzie się lub nie są dozwolone. Ta karta pojawia się w instrukcji użycia platformy Azure w pakietach 10 uwierzytelnienia i codziennie zgłaszania. 

  > [!NOTE]
  > Przykład rozliczeń 3: dzisiaj w usłudze Azure MFA Odebrano 3,105 żądania weryfikacji dwuetapowej. Twoja subskrypcja platformy Azure jest on rozliczany 310.5 pakietów uwierzytelniania. 

Należy pamiętać może mieć licencji usługi Azure MFA, ale nadal uzyskać rozliczane na podstawie zużycia konfiguracji. Jeśli konfigurujesz dostawcę dla uwierzytelniania usługi Azure MFA rozliczenie jest przeprowadzane przy każdym żądaniu weryfikacji dwuetapowej, nawet te, które wykonywane przez użytkowników, którzy mają licencje. Jeśli konfigurujesz dostawcę dla poszczególnych użytkowników usługi Azure MFA w domenie, która nie jest połączona z dzierżawy usługi Azure AD, nawet jeśli użytkownicy mają licencje na usługę Azure AD są rozliczane każdego włączonego użytkownika. 

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacje o cenach, zobacz [cennik usługi Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Określ, czy do wdrożenia usługi Azure MFA [w chmurze lub lokalnie](concept-mfa-whichversion.md)
