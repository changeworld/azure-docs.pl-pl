---
title: Narzędzie Co zrobić w przypadku dostępu warunkowego — usługa Azure Active Directory
description: Dowiedz się, jak zrozumieć wpływ zasad dostępu warunkowego na środowisko.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc87b434664ba12cefeb233972e749f631d8a2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620680"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Rozwiązywanie problemów przy użyciu narzędzia Co jeśli w programie "Dostęp warunkowy"

[Dostęp warunkowy](../active-directory-conditional-access-azure-portal.md) to funkcja usługi Azure Active Directory (Azure AD), która umożliwia kontrolowanie sposobu uzyskiwania dostępu autoryzowanym użytkownikom do aplikacji w chmurze. Skąd wiesz, czego można oczekiwać od zasad dostępu warunkowego w twoim środowisku? Aby odpowiedzieć na to pytanie, można użyć **narzędzia Dostęp warunkowy Co jeśli**.

W tym artykule wyjaśniono, jak można użyć tego narzędzia do testowania zasad dostępu warunkowego.

## <a name="what-it-is"></a>Co to jest

**Narzędzie zasad Warunkowy dostęp Co jeśli** umożliwia zrozumienie wpływu zasad dostępu warunkowego na środowisko. Zamiast testować zasady, wykonując wiele logów ręcznie, to narzędzie umożliwia ocenę symulowanego logowania użytkownika. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji. Raport zawiera listę nie tylko stosowanych zasad dostępu warunkowego, ale także [klasycznych zasad,](policy-migration.md#classic-policies) jeśli istnieją.    

Narzędzie **Co jeśli** umożliwia szybkie określenie zasad, które mają zastosowanie do określonego użytkownika. Można użyć tych informacji, na przykład, jeśli chcesz rozwiązać problem.    

## <a name="how-it-works"></a>Jak to działa

W **narzędziu Warunkowy dostęp Co jeśli**należy najpierw skonfigurować ustawienia scenariusza logowania, który ma symulować. Należą do nich następujące ustawienia:

- Użytkownik, którego chcesz przetestować 
- Aplikacje w chmurze, do które użytkownik będzie próbował uzyskać dostęp
- Warunki, w jakich wykonywany jest dostęp do skonfigurowanych aplikacji w chmurze
     
W następnym kroku można zainicjować przebieg symulacji, który ocenia ustawienia. Tylko zasady, które są włączone są częścią przebiegu oceny.

Po zakończeniu oceny narzędzie generuje raport zasad, których dotyczy problem.

## <a name="running-the-tool"></a>Uruchamianie narzędzia

Narzędzie Co **jeśli** można znaleźć na stronie **[Dostęp warunkowy — Zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** w witrynie Azure portal.

Aby uruchomić narzędzie, na pasku narzędzi u góry listy zasad kliknij przycisk **Co jeśli**.

![What If](./media/what-if-tool/01.png)

Przed uruchomieniem oceny należy skonfigurować ustawienia.

## <a name="settings"></a>Ustawienia

W tej sekcji znajdują się informacje o ustawieniach przebiegu symulacji.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Użytkownik

Można wybrać tylko jednego użytkownika. Jest to jedyne wymagane pole.

### <a name="cloud-apps"></a>Aplikacje w chmurze

Domyślnym ustawieniem tego ustawienia jest **Wszystkie aplikacje w chmurze**. Ustawienie domyślne wykonuje ocenę wszystkich dostępnych zasad w twoim środowisku. Zakres można zawęzić do zasad dotyczących określonych aplikacji w chmurze.

### <a name="ip-address"></a>Adres IP

Adres IP jest pojedynczym adresem IPv4, który naśladuje [stan lokalizacji.](location-condition.md) Adres reprezentuje adres internetowy urządzenia używanego przez użytkownika do logowania. Adres IP urządzenia można zweryfikować, na przykład przechodząc do [adresu IP.](https://whatismyipaddress.com)    

### <a name="device-platforms"></a>Platformy urządzeń

To ustawienie naśladuje [stan platform urządzeń](concept-conditional-access-conditions.md#device-platforms) i reprezentuje odpowiednik wszystkich platform **(w tym nieobsługiwał)**. 

### <a name="client-apps"></a>Aplikacje klienckie

To ustawienie naśladuje [stan aplikacji klienckich](concept-conditional-access-conditions.md#client-apps-preview).
Domyślnie to ustawienie powoduje ocenę wszystkich zasad, które są wybierane indywidualnie lub w aplikacjach **mobilnych** i **klientach klasycznych.** Wykrywa również zasady wymuszania **programu Exchange ActiveSync (EAS).** Ustawienie to można zawęzić, wybierając:

- **Przeglądarki** do oceny wszystkich zasad z co najmniej **przeglądarka** wybrana. 
- **Aplikacje mobilne i klienci komputerowi** do oceny wszystkich zasad z wybranymi co najmniej **aplikacjami mobilnymi i klientami komputerowymi.** 

### <a name="sign-in-risk"></a>Ryzyko logowania

To ustawienie naśladuje [stan ryzyka logowania](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Ocena 

Rozpoczęcie oceny można rozpocząć, klikając przycisk **Co jeśli**. Wynik oceny zawiera raport, który składa się z: 

![What If](./media/what-if-tool/03.png)

- Wskaźnik, czy w twoim środowisku istnieją klasyczne zasady
- Zasady mające zastosowanie do użytkownika
- Zasady, które nie mają zastosowania do użytkownika

Jeśli istnieją [klasyczne zasady](policy-migration.md#classic-policies) dla wybranych aplikacji w chmurze, zostanie wyświetlony wskaźnik. Klikając wskaźnik, zostaniesz przekierowany do klasycznej strony zasad. Na stronie zasad klasycznych można przeprowadzić migrację klasycznej zasady lub ją po prostu wyłączyć. Możesz powrócić do wyniku oceny, zamykając tę stronę.

Na liście zasad, które mają zastosowanie do wybranego użytkownika, można również znaleźć listę [formantów dotacji](concept-conditional-access-grant.md) i [kontroli sesji,](concept-conditional-access-session.md) które użytkownik musi spełnić.

Na liście zasad, które nie mają zastosowania do użytkownika, można, a także znaleźć powody, dla których te zasady nie mają zastosowania. Dla każdej wymienionej zasady przyczyna reprezentuje pierwszy warunek, który nie został spełniony. Możliwą przyczyną zasad, które nie są stosowane jest wyłączone zasady, ponieważ nie są one dalej oceniane.   

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymaganie usługi MFA dla określonych aplikacji z dostępem warunkowym usługi Azure Active Directory](app-based-mfa.md).
- Jeśli chcesz skonfigurować zasady dostępu warunkowego dla swojego środowiska, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 
- jeśli chcesz przeprowadzić migrację klasycznych zasad, zobacz [Migrowanie klasycznych zasad w witrynie Azure portal](policy-migration.md)  
