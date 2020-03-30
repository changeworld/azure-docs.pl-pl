---
title: Co to jest tryb tylko do raportu dostępu warunkowego? - Usługa Azure Active Directory
description: Jak można uzyskać pomoc dotyczącą trybu tylko do raportu podczas wdrażania zasad dostępu warunkowego
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295280"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Co to jest tryb tylko do raportu dostępu warunkowego?

Dostęp warunkowy jest szeroko stosowany przez naszych klientów, aby zachować bezpieczeństwo, stosując odpowiednie mechanizmy kontroli dostępu w odpowiednich okolicznościach. Jednak jednym z wyzwań związanych z wdrażaniem zasad dostępu warunkowego w organizacji jest określenie wpływu na użytkowników końcowych. Może być trudne do przewidzenia liczby i nazw użytkowników, na które mają wpływ typowe inicjatywy wdrażania, takie jak blokowanie uwierzytelniania starszego, wymaganie uwierzytelniania wieloskładnikowego dla populacji użytkowników lub implementowanie zasad ryzyka logowania. 

Tryb tylko do raportu to nowy stan zasad dostępu warunkowego, który umożliwia administratorom ocenę wpływu zasad dostępu warunkowego przed włączeniem ich w ich środowisku.  Wraz z wydaniem trybu tylko do raportu:

- Zasady dostępu warunkowego można włączyć w trybie tylko do raportu.
- Podczas logowania zasady w trybie tylko do raportu są oceniane, ale nie są wymuszane.
- Wyniki są rejestrowane na kartach **Dostęp warunkowy** i **Tylko do raportu (Wersja zapoznawcza)** szczegółów dziennika logowania.
- Klienci korzystający z subskrypcji usługi Azure Monitor mogą monitorować wpływ swoich zasad dostępu warunkowego przy użyciu skoroszytu wglądu w dostęp warunkowy.

> [!WARNING]
> Zasady w trybie tylko do raportów, które wymagają zgodnych urządzeń, mogą monitować użytkowników na komputerach Mac, iOS i Android, aby wybrali certyfikat urządzenia podczas oceny zasad, nawet jeśli zgodność urządzenia nie jest wymuszana. Monity te mogą powtarzać się, dopóki urządzenie nie zostanie zgodnie. Aby uniemożliwić użytkownikom końcowym odbieranie monitów podczas logowania, wyklucz platformy urządzeń Mac, iOS i Android z zasad dotyczących zgodności z urządzeniami.

![Karta Tylko do raportu w dzienniku logowania usługi Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Wyniki polityki

Gdy zasady w trybie tylko do raportu są oceniane dla danego logowania, istnieją cztery nowe możliwe wartości wyników:

| Wynik | Opis |
| --- | --- |
| Tylko raport: Sukces | Wszystkie skonfigurowane warunki zasad, wymagane nieinterakcyjne formanty dotacji i formanty sesji zostały spełnione. Na przykład wymóg uwierzytelniania wieloskładnikowego jest spełniony przez oświadczenie usługi MFA już obecne w tokenie lub zgodne zasady urządzenia jest spełniony przez wykonanie sprawdzania urządzenia na zgodnym urządzeniu. |
| Tylko raport: Niepowodzenie | Wszystkie skonfigurowane warunki zasad zostały spełnione, ale nie wszystkie wymagane nieinterakcyjne formanty dotacji lub formanty sesji zostały spełnione. Na przykład zasady stosuje się do użytkownika, gdzie formant bloku jest skonfigurowany lub urządzenie nie spełnia zgodnych zasad urządzenia. |
| Tylko raport: wymagana akcja użytkownika | Wszystkie skonfigurowane warunki zasad zostały spełnione, ale akcja użytkownika będzie wymagana do spełnienia wymaganych formantów dotacji lub kontroli sesji. W trybie tylko do raportu użytkownik nie jest monitowany o spełnienie wymaganych formantów. Na przykład użytkownicy nie są monitowani o wyzwania uwierzytelniania wieloskładnikowego lub warunki użytkowania.   |
| Tylko raport: Niestosowano | Nie wszystkie skonfigurowane warunki zasad zostały spełnione. Na przykład użytkownik jest wykluczony z zasad lub zasady dotyczą tylko niektórych zaufanych nazwanych lokalizacji. |

## <a name="conditional-access-insights-workbook"></a>Skoroszyt usługi Statystyka dostępu warunkowego

Administratorzy mają możliwość tworzenia wielu zasad w trybie tylko do raportu, dlatego konieczne jest zrozumienie zarówno indywidualnego wpływu każdej zasady, jak i połączonego wpływu wielu zasad ocenianych razem. Nowy skoroszyt usługi Warunkowy wgląd w dane dostępu warunkowego umożliwia administratorom wizualizowanie zapytań dostępu warunkowego i monitorowanie wpływu zasad dla danego zakresu czasu, zestawu aplikacji i użytkowników. 
 
## <a name="next-steps"></a>Następne kroki

[Konfigurowanie trybu tylko do raportu w zasadach dostępu warunkowego](howto-conditional-access-report-only.md)
