---
title: Co to jest co jeśli narzędzie jest dostępne w usłudze Azure Active Directory dostępu warunkowego?
description: Dowiedz się, jak może zrozumieć wpływ zasad dostępu warunkowego w swoim środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97d2ec4099045e17b8482fcde313d31720083583
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506739"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Co to jest co jeśli narzędzie jest dostępne w usłudze Azure Active Directory dostępu warunkowego?

[Dostęp warunkowy](../active-directory-conditional-access-azure-portal.md) jest to funkcja usługi Azure Active Directory (Azure AD), która umożliwia kontrolowanie sposobu autoryzowany dostęp użytkowników aplikacji w chmurze. Skąd wiadomo, czego oczekiwać od zasad dostępu warunkowego w środowisku? Aby odpowiedzieć na to pytanie, można użyć **narzędzie warunkowego dostępu What If**.

W tym artykule wyjaśniono, jak można użyć tego narzędzia do testowania zasad dostępu warunkowego.

## <a name="what-it-is"></a>Co to jest

**Narzędzia Zasady warunkowego dostępu What If** umożliwia zrozumienie wpływu zasad dostępu warunkowego w swoim środowisku. Zamiast testować zasady, przeprowadzając wiele logowań w sposób ręczny, to narzędzie umożliwia ocenianie symulowanego logowania użytkownika. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji. Raport nie zawiera tylko zastosowane zasady dostępu warunkowego, ale także [zasady klasyczne](policy-migration.md#classic-policies) jeśli takie istnieją.    

**Co zrobić, jeśli** narzędzie zapewnia sposób, aby szybko określić zasady, które są stosowane do określonego użytkownika. Informacje, można użyć na przykład, jeśli trzeba rozwiązać problem.    

## <a name="how-it-works"></a>Jak to działa

W **narzędzie warunkowego dostępu What If**, najpierw musisz skonfigurować ustawienia logowania scenariusza, które chcesz zasymulować. Te ustawienia obejmują:

- Użytkownik, który chcesz przetestować 
- Aplikacje w chmurze, które użytkownik może próbować uzyskać dostęp do
- Warunki, w których dostęp do konfiguruje chmurze jest wykonywane w aplikacji
     
Kolejnym krokiem może zainicjować symulacji Uruchom ocenia ustawienia. Tylko te zasady, które są włączone są częścią uruchomić ocenę.

Po zakończeniu oceny narzędzie generuje raport o usterce zasad.

## <a name="running-the-tool"></a>Uruchamianie narzędzia

Możesz znaleźć **co zrobić, jeśli** narzędzie **[dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** strony w witrynie Azure portal.

Aby uruchomić to narzędzie, na pasku narzędzi na górze listy zasad, kliknij przycisk **co zrobić, jeśli**.

![A co jeżeli](./media/what-if-tool/01.png)

Zanim będzie można uruchomić oceny, należy skonfigurować ustawienia.

## <a name="settings"></a>Ustawienia

Ta sekcja zawiera przy użyciu informacji o ustawieniach uruchamiania symulacji.

![A co jeżeli](./media/what-if-tool/02.png)

### <a name="user"></a>Użytkownik

Można wybrać tylko jeden użytkownik. Jest to jedyne wymagane pole.

### <a name="cloud-apps"></a>Aplikacje w chmurze

Wartość domyślna tego ustawienia to **wszystkie aplikacje w chmurze**. Ustawieniem domyślnym przeprowadza ocenę wszystkich dostępnych zasad w danym środowisku. Można zawęzić zakres, do wywierania wpływu na aplikacje w chmurze określonych zasad.

### <a name="ip-address"></a>Adres IP

Adres IP jest jeden adres IPv4, aby mógł naśladować [warunek lokalizacji](location-condition.md). Adres reprezentuje adres urządzenia używane przez użytkownika na potrzeby logowania z Internetem. Możesz sprawdzić adres IP urządzenia, na przykład, przechodząc do [jaki jest mój adres IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Platformy urządzeń

To ustawienie naśladuje [warunek platform urządzeń](conditions.md#device-platforms) i stanowi odpowiednik **wszystkich platform (w tym nieobsługiwane)** . 

### <a name="client-apps"></a>Aplikacje klienckie

To ustawienie naśladuje [warunek aplikacje klienckie](conditions.md#client-apps).
Domyślnie to ustawienie powoduje, że ocena wszystkich zasad mających **przeglądarki** lub **aplikacje mobilne i klienci stacjonarni** albo indywidualnie lub oba wybrane. Ta funkcja wykrywa także zasady, które wymuszają **programu Exchange ActiveSync (EAS)** . To ustawienie można zawęzić, wybierając:

- **Przeglądarka** do oceny, wszystkie zasady, o co najmniej **przeglądarki** wybrane. 
- **Aplikacje mobilne i klienci stacjonarni** do oceny, wszystkie zasady, o co najmniej **aplikacje mobilne i klienci stacjonarni** wybrane. 

### <a name="sign-in-risk"></a>Ryzyko logowania

To ustawienie naśladuje [warunkiem ryzyka logowania](conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Ocena 

Rozpocznij ocenę, klikając **co zrobić, jeśli**. Wynik oceny zawiera raport, który składa się z: 

![A co jeżeli](./media/what-if-tool/03.png)

- Wskazuje czy istnieją zasady klasyczne w danym środowisku
- Zasady, które są stosowane do użytkownika
- Zasady, które nie mają zastosowania do użytkownika

Jeśli [zasady klasyczne](policy-migration.md#classic-policies) istnieje dla aplikacji w wybranej chmurze, wskaźnik jest dołączona. Klikając pozycję wskaźnika, nastąpi przekierowanie do strony zasady klasyczne. Na stronie zasad klasycznych można migracji zasad klasycznych lub po prostu ją wyłączyć. Możesz wrócić do swojej wynik oceny, zamykając tej strony.

Na liście zasad, które są stosowane do wybranych użytkowników, możesz również znaleźć listę [Udziel kontroli](controls.md#grant-controls) i [sesji](controls.md#session-controls) kontrolki użytkownika musi spełniać.

Na liście zasad, które nie mają zastosowania do użytkownika można, a także znajdować powodów dlaczego te zasady nie mają zastosowania. Dla każdej z wymienionych zasad przyczynę reprezentuje pierwszy warunek, który nie był spełniony. Możliwa przyczyna zasady, która nie została zastosowana jest wyłączone zasady, ponieważ nie są analizowane dalsze.   

## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 
- Jeśli chcesz przeprowadzić migrację zasady klasyczne, zobacz [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md)  
