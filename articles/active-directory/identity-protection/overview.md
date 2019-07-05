---
title: Usługa Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure AD Identity Protection umożliwia ograniczenie możliwości osobie atakującej wykorzystanie tożsamości ze złamanymi zabezpieczeniami lub urządzenia oraz ochronę tożsamości lub urządzeń, które wcześniej podejrzewa lub znane naruszenia.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 660793cdba61b5438283ea54a9f7349bdfbc98e1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440507"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co to jest Azure Active Directory Identity Protection?

Usługa Azure Active Directory [tożsamości](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection umożliwia organizacjom skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników.

## <a name="get-started"></a>Rozpoczęcie pracy

Tożsamości w chmurze dla ponad dekadę ma bezpieczne Microsoft. Za pomocą usługi Azure Active Directory Identity Protection w danym środowisku, można użyć tych samych systemach ochrony, używaną przez firmę Microsoft do zabezpieczania tożsamości.

Większość naruszenia zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradzież tożsamości użytkownika. W ciągu lat osoby atakujące stały się coraz bardziej efektywne w wykorzystaniu naruszeń innych firm i za pomocą zaawansowanych wyłudzanie informacji. Tak szybko, jak osoba atakująca uzyska dostęp do nawet użytkownika niski uprzywilejowanych kont, jest stosunkowo łatwe uzyskanie dostępu do zasobów firmy ważne za pośrednictwem ruchu poprzecznego.

W wyniku tego następujące czynności:

- Ochrona wszystkich tożsamości, niezależnie od ich poziomu uprawnień

- Proaktywnie uniemożliwić ze złamanymi zabezpieczeniami tożsamości są użyte

Wykrywanie tożsamości ze złamanymi zabezpieczeniami jest nie łatwym zadaniem. Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmów heurystycznych w celu wykrycia anomalii i podejrzanych zdarzeń, które wskazują na potencjalne naruszenia tożsamości. Przy użyciu tych danych, Identity Protection generuje raporty i alerty, które umożliwiają użytkownikowi oceniać wykryte problemy i podjąć odpowiednie środki zaradcze lub działania korygujące.

Usługa Azure Active Directory Identity Protection jest większa niż monitorowania i raportowania narzędzia. W celu ochrony tożsamości w organizacji, można skonfigurować opartych na ryzykach zasady, które automatycznie odpowiadać na wykryte problemy, jeśli osiągnięty poziom ryzyka określony. Te zasady, oprócz innych kontrolek dostępu warunkowego, dostarczone przez usługę Azure Active Directory i [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), można automatycznie Blokuj lub zainicjować działania korygujące adaptacyjne, w tym Resetowanie haseł i wymuszanie uwierzytelniania wieloskładnikowego.

### <a name="identity-protection-capabilities"></a>Funkcje ochrony tożsamości

**Wykrywanie luk w zabezpieczeniach i ryzykowne konta:**  

- Zapewnianie niestandardowych zalecenia, aby poprawić ogólny stan zabezpieczeń przez wyróżnianie luk w zabezpieczeniach
- Obliczanie poziomy ryzyka logowania
- Obliczanie poziomów ryzyka użytkownika

**Badanie zdarzeń o podwyższonym ryzyku:**

- Wysyłanie powiadomień dla zdarzeń o podwyższonym ryzyku
- Badanie zdarzeń o podwyższonym ryzyku, korzystając z informacji istotnych i kontekstowych
- Zapewnienie podstawowych przepływów pracy do śledzenia dochodzenia
- Zapewnianiu łatwego dostępu do akcji korygowania, takie jak resetowanie haseł

**Zasady dostępu warunkowego na podstawie ryzyka:**

- Zasady wyeliminowanie ryzykowne logowania za pomocą blokowania logowania bądź wymagają takiej wezwań do uwierzytelnienia Multi-Factor Authentication
- Zasady na wartość Blokuj lub kont bezpiecznego ryzykownych użytkowników
- Zasady, aby wymagać od użytkowników rejestracji do uwierzytelniania wieloskładnikowego

## <a name="identity-protection-roles"></a>Role ochrony tożsamości

Aby zrównoważyć obciążenie działań z zakresu zarządzania całym implementacji Identity Protection, można przypisać kilku ról. Usługa Azure AD Identity Protection obsługuje 3 role katalogu:

| Role | Można zrobić | Nie można wykonać |
| :-- | --- | --- |
| Administrator globalny | Pełny dostęp do usługi Identity Protection dołączanie Identity Protection| |
| Administrator zabezpieczeń | Pełny dostęp do usługi Identity Protection | Dołączanie Identity Protection, resetować hasła dla użytkownika |
| Czytelnik zabezpieczeń | Dostęp tylko do odczytu do usługi Identity Protection | Korygowanie użytkowników dołączanie Identity Protection, konfigurowanie zasad, resetowania haseł |

Aby uzyskać więcej informacji, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Wykrywanie

### <a name="vulnerabilities"></a>Luki w zabezpieczeniach

Usługa Azure Active Directory Identity Protection analizuje konfigurację i wykrywa luki w zabezpieczeniach, które mogą mieć wpływ na tożsamości użytkownika. Aby uzyskać więcej informacji, zobacz [luk w zabezpieczeniach wykrywanych przez usługi Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Zdarzenia o podwyższonym ryzyku

Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmy heurystyczne wykryć podejrzane akcje, które są związane z tożsamościami użytkowników. System tworzy rekord dla każdego wykryte podejrzane działania. Te rekordy są również nazywane zdarzeń o podwyższonym ryzyku.  
Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Badanie

Ci się podróż przez ochronę tożsamości zwykle zaczyna się od pulpitu nawigacyjnego Identity Protection.

![Korygowanie](./media/overview/1000.png "korygowania")

Pulpit nawigacyjny zapewnia dostęp do następujących elementów:

- Raporty takie jak **użytkownicy oflagowani w związku z ryzykiem**, **zdarzeń o podwyższonym ryzyku** i **luk w zabezpieczeniach**
- Ustawienia, takie jak konfiguracja usługi **zasad zabezpieczeń**, **powiadomienia** i **rejestracji uwierzytelniania wieloskładnikowego**

Zwykle to punkt początkowy dla badania, czyli procesu recenzowania działania, dzienniki i inne istotne informacje związane z zdarzenie o podwyższonym ryzyku, można zdecydować, czy kroki korygowania i ograniczania ryzyka są niezbędne, i jak oceniasz tożsamości naruszone i zrozumieć używania tożsamości ze złamanymi zabezpieczeniami.

Możesz powiązać badanie działań mających na celu [powiadomienia](notifications.md) ochrony usługi Azure Active Directory, wysyła wiadomości e-mail.

## <a name="policies"></a>Zasady

Aby zaimplementować automatycznych odpowiedzi, Azure Active Directory Identity Protection oferuje trzy zasady:

- [Zasady rejestracji uwierzytelniania wieloskładnikowego](howto-mfa-policy.md)
- [Zasady dotyczące ryzyka związanego z użytkownika](howto-user-risk-policy.md)
- [Zasady ryzyka logowania](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Witryna Channel 9: Usługa Azure AD i wyświetlanie tożsamości: Identity Protection w wersji zapoznawczej](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Włączanie usługi Azure Active Directory Identity Protection](enable.md)
