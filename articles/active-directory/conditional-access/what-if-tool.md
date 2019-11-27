---
title: Narzędzie What If dostępu warunkowego — Azure Active Directory
description: Dowiedz się, jak zrozumieć wpływ zasad dostępu warunkowego w danym środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5846934a8ad8455ca375b4bc54fc46d45aba1cd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379976"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Rozwiązywanie problemów przy użyciu narzędzia What If w dostępie warunkowym

[Dostęp warunkowy](../active-directory-conditional-access-azure-portal.md) jest możliwością Azure Active Directory (Azure AD), która umożliwia kontrolowanie sposobu, w jaki autoryzowani użytkownicy uzyskują dostęp do aplikacji w chmurze. Jak wiesz, czego można oczekiwać od zasad dostępu warunkowego w danym środowisku? Aby odpowiedzieć na to pytanie, można użyć **narzędzia What If dostępu warunkowego**.

W tym artykule wyjaśniono, jak można użyć tego narzędzia do testowania zasad dostępu warunkowego.

## <a name="what-it-is"></a>Co to jest

**Narzędzie zasady What If dostępu warunkowego** pozwala zrozumieć wpływ zasad dostępu warunkowego w danym środowisku. Zamiast testować zasady, przeprowadzając wiele logowań w sposób ręczny, to narzędzie umożliwia ocenianie symulowanego logowania użytkownika. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji. Raport nie tylko zawiera zasady dostępu warunkowego, ale również [zasady klasyczne](policy-migration.md#classic-policies) , jeśli istnieją.    

Narzędzie **What If** umożliwia szybkie ustalenie zasad, które mają zastosowanie do określonego użytkownika. Możesz użyć tych informacji, na przykład jeśli trzeba rozwiązać problem.    

## <a name="how-it-works"></a>Jak to działa

W **narzędziu dostępu warunkowego What If**należy najpierw skonfigurować ustawienia scenariusza logowania, który ma zostać symulowany. Te ustawienia obejmują:

- Użytkownik, który ma zostać przetestowany 
- Aplikacje w chmurze, do których użytkownik podejmie próbę uzyskania dostępu
- Warunki, w których odbywa się dostęp do konfigurowania aplikacji w chmurze
     
Następnym krokiem jest zainicjowanie przebiegu symulacji, który służy do obliczania ustawień. Tylko zasady, które są włączone, są częścią przebiegu ewaluacyjnego.

Po zakończeniu oceny narzędzie generuje raport dotyczący odpowiednich zasad.

## <a name="running-the-tool"></a>Uruchamianie narzędzia

Narzędzie **What If** można znaleźć na stronie **[zasady dostępu warunkowego](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** w Azure Portal.

Aby uruchomić narzędzie, na pasku narzędzi na górze listy zasad kliknij pozycję **What If**.

![What If](./media/what-if-tool/01.png)

Aby można było uruchomić ocenę, należy skonfigurować ustawienia.

## <a name="settings"></a>Ustawienia

Ta sekcja zawiera informacje o ustawieniach przebiegu symulacji.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Użytkownik

Można wybrać tylko jednego użytkownika. Jest to jedyne wymagane pole.

### <a name="cloud-apps"></a>Aplikacje w chmurze

Wartość domyślna tego ustawienia to **wszystkie aplikacje w chmurze**. Ustawienie domyślne służy do oceny wszystkich dostępnych zasad w danym środowisku. Można zawęzić zakres do zasad mających wpływ na określone aplikacje w chmurze.

### <a name="ip-address"></a>Adres IP

Adres IP jest pojedynczym adresem IPv4, aby naśladować [warunek lokalizacji](location-condition.md). Adres reprezentuje adres połączony z Internetem urządzenia używany przez użytkownika do logowania. Adres IP urządzenia można zweryfikować, na przykład przechodząc do obszaru [mój adres IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Platformy urządzeń

To ustawienie naśladuje [warunek platformy urządzeń](conditions.md#device-platforms) i reprezentuje odpowiednik **wszystkich platform (w tym nieobsługiwane)** . 

### <a name="client-apps"></a>Aplikacje klienckie

To ustawienie naśladuje [warunek aplikacji klienckich](conditions.md#client-apps).
Domyślnie to ustawienie powoduje obliczenie wszystkich zasad mających zarówno **przeglądarkę** , **jak i aplikacje mobilne oraz klientów klasycznych** pojedynczo lub oba wybrane. Wykrywa także zasady, które wymuszają program **Exchange ActiveSync (EAS)** . To ustawienie można zawęzić, wybierając pozycję:

- **Przeglądarka** oceni wszystkie zasady, które mają co najmniej wybraną **przeglądarkę** . 
- **Aplikacje mobilne i klienci stacjonarni** w celu ocenienia wszystkich zasad, dla których wybrano co najmniej **aplikacje mobilne i klienci stacjonarni** . 

### <a name="sign-in-risk"></a>Ryzyko związane z logowaniem

To ustawienie naśladuje [warunek ryzyka związanego z logowaniem](conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Sprawozdanie 

Aby rozpocząć ocenę, kliknij **What If**. Wynik oceny zawiera raport, który składa się z: 

![What If](./media/what-if-tool/03.png)

- Wskaźnik określający, czy w środowisku istnieją zasady klasyczne
- Zasady dotyczące użytkownika
- Zasady, które nie mają zastosowania do użytkownika

Jeśli dla wybranych aplikacji w chmurze istnieją [zasady klasyczne](policy-migration.md#classic-policies) , zostanie wyświetlony wskaźnik. Klikając wskaźnik, nastąpi przekierowanie do strony zasady klasyczne. Na stronie zasady klasyczne można migrować zasady klasyczne lub po prostu je wyłączyć. Możesz powrócić do wyniku oceny, zamykając Tę stronę.

Na liście zasad, które są stosowane dla wybranego użytkownika, można również znaleźć listę [kontrolek](controls.md#grant-controls) i kontrolek [sesji](controls.md#session-controls) , które użytkownik musi spełnić.

Na liście zasad, które nie mają zastosowania do użytkownika, możesz również znaleźć powody, dla których te zasady nie są stosowane. Dla każdej z wymienionych zasad powód reprezentuje pierwszy warunek, który nie był spełniony. Możliwą przyczyną niestosowania zasad jest zasada wyłączona, ponieważ nie są one jeszcze oceniane.   

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md). 
- Jeśli chcesz przeprowadzić migrację klasycznych zasad, zobacz [Migrowanie zasad klasycznych w Azure Portal](policy-migration.md)  
