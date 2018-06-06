---
title: Azure Active Directory dostępu warunkowego co zrobić, jeśli narzędzie - preview | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przetestować konfiguracji zasad dostępu warunkowego usługi Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 2c4ca3685d15ddd2446590e1ef297362f9aa52ee
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723834"
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Azure Active Directory dostępu warunkowego co zrobić, jeśli narzędzie - preview

[Dostęp warunkowy](active-directory-conditional-access-azure-portal.md) jest możliwości usługi Azure Active Directory (Azure AD), który umożliwia kontrolowanie sposobu autoryzacji dostępu użytkowników do aplikacji w chmurze. Skąd wiadomo, czego można oczekiwać formularza zasady dostępu warunkowego w środowisku? Aby odpowiedzieć na to pytanie, można użyć **dostępu warunkowego co zrobić, jeśli narzędzie**.

W tym artykule opisano, jak za pomocą tego narzędzia do testowania zasad dostępu warunkowego.

## <a name="what-it-is"></a>Co to jest

**Warunkowego dostępu co, jeśli narzędzie zasad** umożliwia zrozumienie wpływu zasad dostępu warunkowego w środowisku. Zamiast kierowania zasad, ręcznie wykonując wielu logowania testu to narzędzie umożliwia ocenę symulowane logowanie użytkownika. Symulacja szacuje wpływu tego logowania ma na zasad i generuje raport symulacji. Raport nie tylko listy warunkowego zastosowane zasady dostępu, ale także [klasycznego zasady](active-directory-conditional-access-migration.md#classic-policies) jeżeli istnieją.    

Co jeśli narzędzia również pozwala szybko określić zasady, które są stosowane do określonego użytkownika. Możesz na przykład skorzystać z informacji, aby rozwiązać problem.  

## <a name="how-it-works"></a>Jak to działa

W **dostępu warunkowego co zrobić, jeśli narzędzie**, należy najpierw skonfigurować ustawienia logowania scenariusza, aby symulować. Te ustawienia obejmują:

- Użytkownik, który ma zostać przetestowana. 

- Aplikacje w chmurze, użytkownik będzie próbował uzyskać dostęp

- Warunki, w których dostęp do chmury konfiguruje odbywa się aplikacji
     
Następnym krokiem można zainicjować symulacji Uruchom ocenia ustawień. Tylko włączone zasady są częścią oceny Uruchom.


Po zakończeniu oceny narzędzie generuje raport odpowiednich zasad.


## <a name="running-the-tool"></a>Uruchamianie narzędzia

Można znaleźć **co zrobić, jeśli** narzędzia w **[dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** strony w portalu Azure.

Aby uruchomić narzędzie, na pasku narzędzi u góry listy zasad, kliknij przycisk **co zrobić, jeśli**.

![A co jeżeli](./media/active-directory-conditional-access-whatif/01.png)

Przed uruchomieniem ocenę, należy skonfigurować ustawienia.

## <a name="settings"></a>Ustawienia

Ta sekcja zawiera informacje o ustawieniach symulacji Uruchom.

![A co jeżeli](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Użytkownik

Można wybrać tylko jeden użytkownik. To jest tylko wymaganego pola.

### <a name="cloud-apps"></a>Aplikacje w chmurze

Wartość domyślna to ustawienie to **wszystkich aplikacji w chmurze**. Ustawieniem domyślnym wykonuje ocenę wszystkich dostępnych zasad w środowisku. Można zawęzić zakres wpływu na aplikacje w chmurze określonych zasad.


### <a name="ip-address"></a>Adres IP

Adres IP jest jeden adres IPv4, aby naśladował [warunku lokalizacji](active-directory-conditional-access-locations.md). Adres reprezentuje internetowy adres urządzenia używane przez użytkownika do logowania. Możesz sprawdzić adres IP urządzenia, na przykład, przechodząc do [co to jest adresu IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Platformy urządzeń

To ustawienie, którego [warunku platformy urządzenia](active-directory-conditional-access-conditions.md#device-platforms) i stanowi odpowiednik **wszystkich platform (nieobsługiwane w tym)**. 
### <a name="client-apps"></a>Aplikacje klienckie

To ustawienie, którego [warunku aplikacje klienckie](active-directory-conditional-access-conditions.md#client-apps).
Domyślnie to ustawienie powoduje, że oceny wszystkich zasad o **przeglądarki** lub **aplikacji mobilnych i klasycznych klientów** albo indywidualnie lub oba wybrane. Ta funkcja wykrywa także zasady, które wymuszają **programu Exchange ActiveSync (EAS)**. To ustawienie można zawęzić, wybierając:

- **Przeglądarka** do oceny wszystkich zasad o co najmniej **przeglądarki** wybrane. 

- **Aplikacje mobilne i klienci usług pulpitu** do oceny wszystkich zasad o co najmniej **aplikacji mobilnych i klasycznych klientów** wybrane. 


### <a name="sign-in-risk"></a>Ryzyko logowania

To ustawienie, którego [warunku logowania ryzyka](active-directory-conditional-access-conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Ocena 

Rozpocznij ocenę, klikając **co zrobić, jeśli**. Wynik oceny zawiera raport, który obejmuje: 

![A co jeżeli](./media/active-directory-conditional-access-whatif/03.png)

- Wskaźnik czy klasycznego zasady istnieją w środowisku
- Zasady, które są stosowane do użytkownika.
- Zasady, które nie mają zastosowanie do użytkownika.


Jeśli [klasycznego zasady](active-directory-conditional-access-migration.md#classic-policies) istnieje dla wybranej chmury aplikacji, są prezentowane wskaźnika. Klikając wskaźnika, nastąpi przekierowanie do strony klasycznego zasad. Na stronie zasad klasycznego możesz migrować klasyczny zasad lub po prostu go wyłączyć. Możesz powrócić do wyników oceny zamknięciem tej strony.

Na liście zasad, które są stosowane do wybranego użytkownika, możesz również znaleźć listę [przyznać formanty](active-directory-conditional-access-controls.md#grant-controls) i [sesji](active-directory-conditional-access-controls.md#session-controls) kontrolki użytkownika spełnia.

Na liście zasad, które nie dotyczą użytkowników można, a także znaleźć uzasadnienie, dlaczego nie można stosować te zasady. Dla każdej z wymienionych zasad powód reprezentuje pierwszy warunek, który nie był spełniony. Możliwych przyczyn zasady, która nie została zastosowana jest wyłączone zasady, ponieważ nie są analizowane dalsze.   



## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wprowadzenie dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 

- Jeśli użytkownik chce migrować klasyczny zasad, zobacz [migracji klasycznego zasad w portalu Azure](active-directory-conditional-access-migration.md)  
