---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się, w jaki sposób Azure AD Identity Protection pozwala na ograniczenie zdolności osoby atakującej do korzystania ze złamanej tożsamości lub urządzenia oraz zabezpieczenia tożsamości lub urządzenia, które było wcześniej podejrzane lub uznane za zagrożone.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99a542d3208d5871d88c966fffc65cf16e0fbeee
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335394"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co to jest Azure Active Directory Identity Protection?

Azure Active Directory ochrony [tożsamości](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) umożliwia organizacjom Konfigurowanie zautomatyzowanych odpowiedzi w celu wykrycia podejrzanych działań związanych z tożsamościami użytkowników.

## <a name="get-started"></a>Wprowadzenie

Firma Microsoft ma zabezpieczone tożsamości oparte na chmurze przez ponad dekadę. W przypadku Azure Active Directory Identity Protection w środowisku można użyć tych samych systemów ochrony, które są używane przez firmę Microsoft do zabezpieczania tożsamości.

Większość naruszeń zabezpieczeń odbywa się, gdy osoby atakujące uzyskują dostęp do środowiska przez kradzież tożsamości użytkownika. W ciągu lat osoby atakujące stają się coraz bardziej skuteczne w odniesieniu do naruszeń innych firm i korzystaniu z zaawansowanych ataków wyłudzania informacji. Gdy tylko osoba atakująca uzyska dostęp do nawet niskich kont użytkowników z niskim poziomem uprawnień, jest stosunkowo łatwa do uzyskania dostępu do ważnych zasobów firmy poprzez przenoszenie boczne.

W związku z tym należy:

- Chroń wszystkie tożsamości bez względu na ich poziom uprawnień
- Aktywnie Zapobiegaj naruszeniu naruszonych tożsamości

Odnajdywanie zagrożonych tożsamości nie jest łatwe. Azure Active Directory używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania anomalii i podejrzanych zdarzeń, które wskazują potencjalnie zagrożone tożsamości. Przy użyciu tych danych Ochrona tożsamości generuje raporty i alerty, które umożliwiają ocenę wykrytych problemów i podejmowanie odpowiednich działań zaradczych lub zaradczych.

Azure Active Directory Identity Protection jest więcej niż narzędzie do monitorowania i raportowania. Aby chronić tożsamości organizacji, można skonfigurować zasady oparte na ryzyku, które automatycznie reagują na wykryte problemy po osiągnięciu określonego poziomu ryzyka. Te zasady, oprócz innych kontroli dostępu warunkowego zapewniane przez Azure Active Directory i [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), mogą automatycznie blokować lub inicjować adaptacyjne akcje korygowania, w tym resetowanie haseł i Wymuszanie uwierzytelniania wieloskładnikowego.

### <a name="identity-protection-capabilities"></a>Możliwości ochrony tożsamości

**Wykrywanie luk w zabezpieczeniach i ryzykownych kont:**  

- Udostępnianie niestandardowych zaleceń w celu poprawy ogólnej stan zabezpieczeń dzięki wyróżnieniu luk w zabezpieczeniach
- Obliczanie poziomów ryzyka związanego z logowaniem
- Obliczanie poziomów ryzyka użytkownika

**Badanie zdarzeń o podwyższonym ryzyku:**

- Wysyłanie powiadomień dla zdarzeń o podwyższonym ryzyku
- Badanie zdarzeń o podwyższonym ryzyku przy użyciu odpowiednich i kontekstowych informacji
- Zapewnianie podstawowych przepływów pracy do śledzenia dochodzeń
- Zapewnianie łatwego dostępu do akcji korygowania, takich jak resetowanie hasła

**Zasady dostępu warunkowego opartego na ryzyku:**

- Zasady ograniczające ryzykowne logowania przez blokowanie logowań lub wymaganie wyzwań związanych z uwierzytelnianiem wieloskładnikowym
- Zasady blokowania lub zabezpieczania ryzykownych kont użytkowników
- Zasady wymagające, aby użytkownicy rejestrowali się w celu uwierzytelniania wieloskładnikowego

## <a name="identity-protection-roles"></a>Role ochrony tożsamości

Aby równoważyć obciążenie działaniami związanymi z zarządzaniem w ramach implementacji usługi Identity Protection, można przypisać kilka ról. Azure AD Identity Protection obsługuje 3 role katalogu:

| Role | Można wykonać | Nie można wykonać |
| :-- | --- | --- |
| Administrator globalny | Pełny dostęp do ochrony tożsamości, dołączanie do ochrony tożsamości| |
| Administrator zabezpieczeń | Pełny dostęp do programu Identity Protection | Dołączanie usługi Identity Protection, resetowanie haseł dla użytkownika |
| Czytelnik zabezpieczeń | Dostęp tylko do odczytu do programu Identity Protection | Dołączanie do ochrony tożsamości, korygowanie użytkowników, Konfigurowanie zasad, resetowanie haseł |

Aby uzyskać więcej informacji, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Wykrywanie

### <a name="vulnerabilities"></a>Luki w zabezpieczeniach

Azure Active Directory Identity Protection analizuje konfigurację i wykrywa luki w zabezpieczeniach, które mogą mieć wpływ na tożsamości użytkownika. Aby uzyskać więcej informacji, zobacz [luki w zabezpieczeniach wykryte przez Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Zdarzenia o podwyższonym ryzyku

Azure Active Directory używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych działań, które są związane z tożsamościami użytkownika. System tworzy rekord dla każdej wykrytej podejrzanej akcji. Te rekordy są również znane jako zdarzenia ryzyka.  
Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Badanie

Twoja podróż przez usługę Identity Protection zazwyczaj rozpoczyna się od pulpitu nawigacyjnego ochrony tożsamości.

![Korygowanie](./media/overview/1000.png "Korygowanie")

Pulpit nawigacyjny zapewnia dostęp do następujących elementów:

- Raporty, takie jak **Użytkownicy oflagowani w związku z ryzykiem**, **zdarzenia ryzyka** i **luki** w zabezpieczeniach
- Ustawienia, takie jak konfiguracja **zasad zabezpieczeń**, **powiadomienia** i rejestracja w usłudze **uwierzytelniania** wieloskładnikowego

Zwykle jest to punkt początkowy do badania, który jest procesem przeglądania działań, dzienników i innych istotnych informacji związanych ze zdarzeniem ryzyka w celu podjęcia decyzji o tym, czy kroki zaradcze lub zaradcze są niezbędne oraz jak tożsamość była naruszone i zapoznaj się z używaniem złamanej tożsamości.

Możesz powiązać działania dochodzeniowe z powiadomieniami Azure Active Directory [powiadomień](notifications.md) wysyłanych na adres e-mail.

## <a name="policies"></a>Zasady

Aby zaimplementować automatyczne odpowiedzi, Azure Active Directory Identity Protection oferuje trzy zasady:

- [Zasady rejestracji uwierzytelniania wieloskładnikowego](howto-mfa-policy.md)
- [Zasady ryzyka dla użytkowników](howto-user-risk-policy.md)
- [Zasady dotyczące ryzyka związanego z logowaniem](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Kanał 9: Usługa Azure AD i tożsamość show: Wersja zapoznawcza programu Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Włączanie Azure Active Directory Identity Protection](enable.md)
