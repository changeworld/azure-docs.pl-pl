---
title: Co to jest co jeśli narzędzie dostępu warunkowego usługi Azure Active Directory?
description: Dowiedz się, jak może zrozumieć wpływ zasad dostępu warunkowego w swoim środowisku.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: d9bdc35e732a84920800424a260610fd6f068c94
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626094"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Co to jest co jeśli narzędzie dostępu warunkowego usługi Azure Active Directory?

[Dostęp warunkowy](../active-directory-conditional-access-azure-portal.md) jest to funkcja usługi Azure Active Directory (Azure AD), która umożliwia kontrolowanie sposobu autoryzowany dostęp użytkowników aplikacji w chmurze. Skąd wiadomo, czego można oczekiwać, formularz zasady dostępu warunkowego w danym środowisku? Aby odpowiedzieć na to pytanie, można użyć **dostępu warunkowego narzędzie analizy warunkowej**.

W tym artykule wyjaśniono, jak można użyć tego narzędzia do testowania zasad dostępu warunkowego.

## <a name="what-it-is"></a>Co to jest

**Warunkowego dostępu, co, jeśli narzędzie zasad** umożliwia zrozumienie wpływu zasad dostępu warunkowego w swoim środowisku. Zamiast testu kierowania zasad, ręcznie wykonując wiele operacji logowania to narzędzie umożliwia ocenę symulowane logowania użytkownika. Symulacja szacuje wpływ tym logowania ma zasady i generuje raport symulacji. Raport nie zawiera tylko warunkowe zastosowane zasady dostępu, ale także [zasady klasyczne](policy-migration.md#classic-policies) jeśli takie istnieją.    

Co jeśli narzędzia również pozwala szybko określić zasady, które są stosowane do określonego użytkownika. Informacje, można użyć na przykład, jeśli trzeba rozwiązać problem.  

## <a name="how-it-works"></a>Jak to działa

W **dostępu warunkowego narzędzie analizy warunkowej**, najpierw musisz skonfigurować ustawienia logowania scenariusza, które chcesz zasymulować. Te ustawienia obejmują:

- Użytkownik, który chcesz przetestować 

- Aplikacje w chmurze, które użytkownik może próbować uzyskać dostęp do

- Warunki, w których dostęp do konfiguruje chmurze jest wykonywane w aplikacji
     
Kolejnym krokiem może zainicjować symulacji Uruchom ocenia ustawienia. Tylko te zasady, które są włączone są częścią uruchomić ocenę.


Po zakończeniu oceny narzędzie generuje raport o usterce zasad.


> [!NOTE]
> Obecnie co jeśli narzędzie nie obsługuje grup zagnieżdżonych. Jeśli użytkownik znajduje się w grupie i że grupa jest członkiem innej grupy, który jest używany w zasadach dostępu warunkowego, a następnie co zrobić, jeśli narzędzie nie wyświetla prawidłowo wpływu tych zasad dla użytkownika. 


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

To ustawienie naśladuje [warunek platform urządzeń](conditions.md#device-platforms) i stanowi odpowiednik **wszystkich platform (w tym nieobsługiwane)**. 
### <a name="client-apps"></a>Aplikacje klienckie

To ustawienie naśladuje [warunek aplikacje klienckie](conditions.md#client-apps).
Domyślnie to ustawienie powoduje, że ocena wszystkich zasad mających **przeglądarki** lub **aplikacje mobilne i klienci stacjonarni** albo indywidualnie lub oba wybrane. Ta funkcja wykrywa także zasady, które wymuszają **programu Exchange ActiveSync (EAS)**. To ustawienie można zawęzić, wybierając:

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

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 

- Jeśli chcesz przeprowadzić migrację zasady klasyczne, zobacz [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md)  
