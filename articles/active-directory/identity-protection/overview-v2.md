---
title: Co to jest Azure Active Directory Identity Protection (odświeżone)? | Microsoft Docs
description: Co to jest Azure Active Directory Identity Protection (odświeżone)?
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2987f8fb116bfcbb1698335c3aca6f1fd8eb633e
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717285"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Co to jest Azure Active Directory Identity Protection (odświeżone)?

Środowisko ochrony tożsamości zostało odświeżone, aby lepiej chronić tożsamości organizacji. Ten odświeżony interfejs zawiera następujące informacje:

- Przeprojektowana obsługa administracyjna, która koncentruje się na podwyższonym ryzyku związanym z ryzykiem użytkownika i ryzykiem związanym z logowaniem
- Zaawansowane środowisko dochodzeniowe z obsługą filtrowania, sortowania i plików do pobrania
- Ulepszone Obliczanie ryzyka dla użytkowników, które ułatwiają określenie priorytetów działań dla użytkowników, których najprawdopodobniej zostanie naruszone bezpieczeństwo
- Obsługa nowego interfejsu API w celu umożliwienia programistycznego dostępu do danych o podwyższonym ryzyku
- Uproszczony proces przesyłania opinii administratora, który umożliwia natychmiastowe włączenie ochrony użytkowników
- Nowe nadzorowane Uczenie maszynowe w celu poprawy dokładności ocen ryzyka

Bezpieczeństwo to najpopularniejsze zagadnienia dla organizacji. Większość naruszeń zabezpieczeń odbywa się, gdy osoby atakujące uzyskują dostęp do środowiska przez kradzież tożsamości użytkownika. W ciągu lat osoby atakujące stają się coraz bardziej skuteczne w odniesieniu do naruszeń innych firm i korzystaniu z zaawansowanych ataków wyłudzania informacji. Gdy tylko osoby atakujące uzyskują dostęp do nawet niskich kont użytkowników z niskim poziomem uprawnień, to stosunkowo łatwe do uzyskania dostępu do ważnych zasobów firmy za pośrednictwem ruchu poprzecznego. 

Aby odpowiedzieć na te zagrożenia, Azure AD Identity Protection umożliwia: 

- Aktywnie Zapobiegaj naruszeniu naruszonych tożsamości 
- Automatycznie ograniczaj ryzyko po wykryciu podejrzanych działań 
- Zbadaj ryzykownych użytkowników i zaloguj się, aby rozwiązać potencjalne luki w zabezpieczeniach  
- Otrzymywać alerty, gdy ryzyko użytkownika osiągnie określony próg 

Azure AD Identity Protection to funkcja Azure Active Directory — wersja Premium P2, która umożliwia skonfigurowanie zasad w taki sposób, aby automatycznie reagować w przypadku naruszenia tożsamości użytkownika lub gdy ktoś inny niż właściciel konta próbuje zalogować się przy użyciu ich Identity. Te zasady, oprócz innych kontroli dostępu warunkowego udostępniane przez usługę Azure AD, mogą automatycznie blokować dostęp lub inicjować akcje zaradcze, takie jak resetowanie hasła lub Wymuszanie uwierzytelniania wieloskładnikowego. Ponadto funkcja ochrony tożsamości oferuje funkcje monitorowania i raportowania w celu uzyskania dokładniejszego wglądu w ryzyko i potencjalne kompromisy w organizacji. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>Zdarzenia o podwyższonym ryzyku

Azure AD Identity Protection wykrywa następujące zdarzenia dotyczące ryzyka: 

| Typ zdarzenia o podwyższonym ryzyku | Opis | Typ wykrywania |
| --- | --- | --- |
| Nietypowa podróż | Zaloguj się z nietypowej lokalizacji na podstawie ostatnich logowań użytkownika. | W trybie offline |
| Anonimowy adres IP | Zaloguj się przy użyciu anonimowego adresu IP (na przykład: Przeglądarka tor, Anonymizer sieci VPN). | Czas rzeczywisty |
| Nieznane właściwości logowania | Zaloguj się przy użyciu właściwości, które nie były ostatnio widziane dla danego użytkownika. | Czas rzeczywisty |
| Adres IP połączony ze złośliwym oprogramowaniem | Zaloguj się przy użyciu połączonego adresu IP złośliwego oprogramowania | W trybie offline |
| Nieujawnione poświadczenia | To zdarzenie ryzyka wskazuje, że ujawniono prawidłowe poświadczenia użytkownika | W trybie offline |

## <a name="types-of-risk"></a>Rodzaje ryzyka 

Ochrona tożsamości jest oparta na dwóch typach ryzyka:

- Ryzyko logowania
- Ryzyko związane z użytkownikiem

### <a name="sign-in-risk"></a>Ryzyko logowania

Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie jest autoryzowane przez właściciela tożsamości.

Istnieją dwie oceny ryzyka związanego z logowaniem: 

- **Ryzyko związane z logowaniem (w czasie rzeczywistym)** — ryzyko związane z logowaniem (w czasie rzeczywistym) odbywa się na podstawie wszystkich wykryć w czasie rzeczywistym, które są wyzwalane podczas przetwarzania logowania.  
- **Ryzyko związane z logowaniem (agregowanie)** — ryzyko związane z logowaniem (zagregowanym) to łączne ryzyko związane z logowaniem. Jest on obliczany przez model uczenia maszynowego, który uważa:
   - Wykrywanie w czasie rzeczywistym (opisane powyżej)
   - Wykrywanie w trybie offline (wyzwalane po zalogowaniu) 
   - Wszystkie inne funkcje logowania

### <a name="user-risk"></a>Ryzyko związane z użytkownikiem

Ryzyko użytkownika reprezentuje prawdopodobieństwo naruszenia bezpieczeństwa tożsamości. 

Ryzyko użytkownika jest obliczane przez uwzględnienie wszystkich zagrożeń związanych z użytkownikiem:

- Wszystkie ryzykowne logowania
- Wszystkie zdarzenia ryzyka, które nie są powiązane z logowaniem
- Ryzyko bieżącego użytkownika
- Wszelkie działania zaradcze lub odrzucające ryzyko wykonywane na użytkownikach do daty

## <a name="how-identity-protection-detects-risk"></a>Jak Ochrona tożsamości wykrywa ryzyko  

Usługa Azure AD korzysta z uczenia maszynowego do wykrywania anomalii i podejrzanych działań przy użyciu obu sygnałów wykrytych w czasie rzeczywistym podczas logowania, a także sygnałów nieprawdziwych związanych z użytkownikami i ich działaniami związanymi z logowaniem. Przy użyciu tych danych Ochrona tożsamości oblicza ryzyko związane z logowaniem w czasie rzeczywistym za każdym razem, gdy użytkownik się uwierzytelnia, a także określa ogólny poziom ryzyka użytkownika dla każdego użytkownika. Program Identity Protection umożliwia automatyczne podejmowanie działań związanych z wykrywaniem ryzyka przez skonfigurowanie ryzyka użytkownika ochrony tożsamości i zasad dotyczących ryzyka związanego z logowaniem.  

Aby zrozumieć, jak Ochrona tożsamości wykrywa ryzyko, istnieją dwie ważne pojęcia: ryzyko związane z ryzykiem i ryzyko związane z logowaniem. Ryzyko związane z logowaniem odzwierciedla prawdopodobieństwo, że dane żądanie uwierzytelnienia nie jest autoryzowane przez właściciela tożsamości. Istnieją dwa typy zagrożeń związanych z logowaniem: w czasie rzeczywistym i łącznie. Ryzyko związane z logowaniem w czasie rzeczywistym jest wykrywane podczas próby logowania (na przykład logowania z anonimowych adresów IP). Całkowite ryzyko związane z logowaniem to zagregowana liczba wykrytych zagrożeń związanych z logowaniem w czasie rzeczywistym, a także wszelkie kolejne zdarzenia niezwiązane z ryzykiem w czasie rzeczywistym skojarzone z logowaniem użytkownika (na przykład niemożliwym podróżem). Ryzyko użytkownika odzwierciedla ogólne prawdopodobieństwo naruszenia przez zły aktora danej tożsamości. Ryzyko użytkownika zawiera wszystkie działania związane z ryzykiem dla danego użytkownika, w tym:

- Ryzyko związane z logowaniem w czasie rzeczywistym
- Kolejne ryzyko związane z logowaniem
- Ryzykowne wykrywanie użytkowników.   

 ![Ruch](./media/overview-v2/01.png)

W powyższej grafice zostanie podsumowany przepływ linii bazowej na potrzeby wykrywania ryzyka ochrony tożsamości i odpowiedzi dla danego logowania.  

## <a name="common-scenarios"></a>Typowe scenariusze 

Przyjrzyjmy się przykładowi pracownika firmy Contoso. 

1. Pracownik próbuje zalogować się do usługi Exchange Online z przeglądarki sieci Tor. W momencie logowania usługa Azure AD wykrywa zdarzenia ryzyka w czasie rzeczywistym. 
2. Usługa Azure AD wykryje, że pracownik loguje się z anonimowego adresu IP, wyzwalając średni poziom ryzyka związanego z logowaniem. 
3. Pracownik jest monitowany przez wiersz polecenia usługi MFA, ponieważ administrator IT firmy Contoso skonfigurował zasady dostępu warunkowego dotyczące ryzyka logowania do usługi Identity Protection. Zasady wymagają uwierzytelniania wieloskładnikowego dla ryzyka związanego z logowaniem średnim lub wyższym. 
4. Pracownik przeszedł monit usługi MFA i uzyskuje dostęp do programu Exchange Online, a poziom ryzyka użytkownika nie jest zmieniany. 

Co się stało w tle? Próba logowania z przeglądarki tor wyzwoliła ryzyko związane z logowaniem w czasie rzeczywistym w usłudze Azure AD dla anonimowego adresu IP. Ponieważ usługa Azure AD przetworzyła żądanie, stosuje zasady dotyczące ryzyka logowania skonfigurowane w ramach usługi Identity Protection, ponieważ poziom ryzyka logowania pracownika spełnił próg (średni). Ze względu na to, że pracownik był wcześniej zarejestrowany dla usługi MFA, był w stanie odpowiedzieć na wyzwanie usługi MFA i zdać się na nie. Możliwość pomyślnego przekazania wyzwania MFA do usługi Azure AD, że była najkorzystniej właścicielem tożsamości, a poziom ryzyka użytkownika nie rośnie. 

Ale co zrobić, jeśli pracownik nie był przy próbie zalogowania? 

1. Złośliwy aktor z poświadczeniami pracownika próbuje zalogować się do swojego konta usługi Exchange Online z przeglądarki sieci Tor, ponieważ próbuje on ukryć swój adres IP. 
2. Usługa Azure AD wykryje, że próba logowania pochodzi z anonimowego adresu IP, wyzwalając ryzyko związane z logowaniem w czasie rzeczywistym. 
3. Złośliwy aktor jest zakwestionowana przez wiersz polecenia usługi MFA, ponieważ administrator IT firmy Contoso skonfigurował zasady dostępu warunkowego dotyczącego logowania do usługi Identity Protection, aby wymagać uwierzytelniania wieloskładnikowego w przypadku, gdy ryzyko związane z logowaniem ma wartość średnią lub wyższą. 
4. Złośliwy aktor kończy się niepowodzeniem żądania MFA i nie może uzyskać dostępu do konta usługi Exchange Online pracownika. 
5. Niepowodzenie monitu usługi MFA wyzwoliło zdarzenie ryzyka, które ma zostać zarejestrowane, przez zwiększenie ryzyka dla użytkowników w przypadku przyszłych logowań. 

Teraz, gdy złośliwy aktor próbował uzyskać dostęp do konta Sarah, zobaczymy, co się stanie przy następnym zalogowaniu pracownika. 

1. Pracownik próbuje zalogować się do usługi Exchange Online z programu Outlook. W momencie logowania usługa Azure AD wykrywa zdarzenia ryzyka w czasie rzeczywistym, a także każde wcześniejsze ryzyko związane z użytkownikiem. 
2. Usługa Azure AD nie wykrywa żadnego ryzyka związanego z logowaniem w czasie rzeczywistym, ale wykrywa duże ryzyko związane z ryzykiem w poprzednich scenariuszach.  
3. Pracownik jest wyzwaniem monitu o zresetowanie hasła, ponieważ administrator IT firmy Contoso skonfigurował zasady ryzyka użytkownika ochrony tożsamości, aby wymagać zmiany hasła podczas logowania użytkownika. 
4. Od momentu zarejestrowania pracownika na potrzeby usługi SSPR i MFA pomyślnie zresetowano hasło. 
5. Przez zresetowanie hasła pracownika nie są już naruszone i ich tożsamość powraca do stanu bezpiecznego. 
6. Są rozwiązane zdarzenia poprzedniego pracownika, a poziom ryzyka użytkownika jest automatycznie resetowany jako odpowiedź w celu ograniczenia naruszenia bezpieczeństwa poświadczeń. 

## <a name="how-do-i-configure-identity-protection"></a>Jak mogę skonfigurować ochronę tożsamości? 

Aby rozpocząć pracę z usługą Identity Protection, najpierw Skonfiguruj zasady ryzyka dla użytkowników i zasady dotyczące ryzyka związanego z logowaniem. Po skonfigurowaniu i zastosowaniu tych zasad do grupy testów można symulować zdarzenia ryzyka, aby zrozumieć, w jaki sposób Ochrona tożsamości będzie odpowiadać w danym środowisku. Poniższe przewodniki szybki start zawierają wskazówki dotyczące konfigurowania wyżej wymienionych zasad i testów w środowisku. 

Usługa Identity Protection obsługuje trzy role w usłudze Azure AD w celu zrównoważenia działań związanych z zarządzaniem w ramach wdrożenia: 

| Role | Można wykonać | Nie można wykonać |
| --- | --- | --- |
| Administrator globalny | Pełny dostęp do ochrony tożsamości, dołączanie do ochrony tożsamości | |
| Administrator zabezpieczeń | Pełny dostęp do programu Identity Protection | Dołączanie usługi Identity Protection, resetowanie haseł dla użytkownika |
| Czytelnik zabezpieczeń | Dostęp tylko do odczytu do programu Identity Protection | Dołączanie do ochrony tożsamości, korygowanie użytkowników, Konfigurowanie zasad, resetowanie haseł| 

Aby uzyskać więcej informacji, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
 
## <a name="licensing"></a>Licencjonowanie

>[!NOTE]
> W trakcie publicznej wersji zapoznawczej ochrony tożsamości (odświeżone) tylko Azure AD — wersja Premium klienci P2 będą mieli dostęp do raportu ryzykownych użytkowników i raport dotyczący ryzykownych logowań.

| Możliwość | Usługa Azure AD — warstwa Premium P2 | Usługa Azure AD — warstwa Premium P1 | Azure AD — wersja Podstawowa/bezpłatnie |
| --- | --- | --- | --- |
| Zasady dotyczące ryzyka związanego z użytkownikiem | Yes | Nie | Nie |
| Zasady dotyczące ryzyka związanego z logowaniem | Tak | Nie | Nie |
| Raport ryzykownych użytkowników | Pełny dostęp | Ograniczone informacje | Ograniczone informacje |
| Raport dotyczący ryzykownych logowań | Pełny dostęp | Ograniczone informacje | Ograniczone informacje |
| Zasady rejestracji w usłudze MFA | Tak | Nie | Nie |

## <a name="next-steps"></a>Następne kroki 

Aby rozpocząć pracę z usługą Identity Protection, zobacz [Konfigurowanie zasad dotyczących ryzyka związanego](quickstart-sign-in-risk-policy.md)z logowaniem. 
