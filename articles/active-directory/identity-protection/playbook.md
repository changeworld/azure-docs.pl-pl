---
title: Podręcznik usługi Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure AD Identity Protection umożliwia ograniczenie możliwości osobie atakującej wykorzystanie tożsamości ze złamanymi zabezpieczeniami lub urządzenia oraz ochronę tożsamości lub urządzeń, które wcześniej podejrzewa lub znane naruszenia.
services: active-directory
keywords: Usługa Azure active directory identity protection rozwiązania cloud discovery, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 697bb8a60861acb120e92d8fd1dda3892a957b57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60294382"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Podręcznik usługi Azure Active Directory Identity Protection

Ten podręcznik ułatwia:

* Wprowadź dane w środowisku Identity Protection przez symulowanie zdarzeń o podwyższonym ryzyku i luk w zabezpieczeniach
* Konfigurowanie zasad dostępu warunkowego na podstawie ryzyka i przetestuj wpływ tych zasad


## <a name="simulating-risk-events"></a>Symulowanie ryzykownych zdarzeń

Ta sekcja zawiera kroki symulowania następujące typy zdarzeń o podwyższonym ryzyku:

* Logowania z anonimowych adresów IP (proste)
* Logowania z nieznanych lokalizacji (średni)
* Niemożliwa podróż do nietypowych lokalizacji (trudne)

Inne zdarzenia o podwyższonym ryzyku nie może być symulowane w bezpieczny sposób.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Aby uzyskać więcej informacji na temat danego zdarzenia o podwyższonym ryzyku, zobacz [logowania z anonimowych adresów IP](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Wykonanie poniższej procedury wymaga użycia:

- [Przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en) do symulacji anonimowych adresów IP. Może być konieczne używanie maszyny wirtualnej, jeśli Twoja organizacja — ograniczenie za pomocą przeglądarki sieci Tor.
- Konta testowego, który nie został jeszcze zarejestrowany do uwierzytelniania wieloskładnikowego.

**Aby zasymulować logowania z anonimowych adresów IP, wykonaj następujące czynności**:

1. Za pomocą [przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en), przejdź do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Wprowadź poświadczenia konta mają być wyświetlane w **logowania z anonimowych adresów IP** raportu.

Identyfikator logowania pojawia się na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10 – 15 minut. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Logowania z nieznanych lokalizacji

Aby uzyskać więcej informacji na temat danego zdarzenia o podwyższonym ryzyku, zobacz [logowania z nieznanych lokalizacji](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Aby zasymulować nieznanych lokalizacji, należy zalogować się w lokalizacji i urządzenie, którego konto testu nie zalogował z przed.

Poniższa procedura wykorzystuje nowo utworzony:

- Połączenie sieci VPN, aby zasymulować nowej lokalizacji.

- Maszyna wirtualna, aby zasymulować nowego urządzenia.

Wykonując poniższą procedurę, musisz użyć konta użytkownika, który ma:

- Co najmniej 30-dniową historię logowania.
- Uwierzytelnianie wieloskładnikowe jest włączone.


**Aby zasymulować logowanie z nieznanej lokalizacji, wykonaj następujące czynności**:

1. Podczas logowania się za pomocą konta testowego, należy zakończyć się niepowodzeniem żądania uwierzytelniania MFA, nie przekazując żądanie uwierzytelniania MFA.
2. Przy użyciu sieci VPN nowe, przejdź do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) i wprowadź poświadczenia konta testu.
   

Identyfikator logowania pojawia się na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10 – 15 minut.

### <a name="impossible-travel-to-atypical-location"></a>Niemożliwa podróż do nietypowych lokalizacji

Aby uzyskać więcej informacji na temat danego zdarzenia o podwyższonym ryzyku, zobacz [niemożliwą zmianę podróż do nietypowych lokalizacji](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Symulowanie warunków niemożliwa zmiana lokalizacji jest trudne, ponieważ będzie używać Algorytm uczenia maszynowego w celu chwastów się fałszywych alarmów, takich jak więc podróż jest wykluczona z dobrze znanych urządzeń lub operacji logowania z wirtualnych sieci prywatnych, które są używane przez innych użytkowników w katalogu. Ponadto algorytm wymaga historię logowania w ciągu 14 dni i 10 logowania użytkownika, zanim rozpocznie generowanie zdarzeń o podwyższonym ryzyku. Z powodu złożonych modeli uczenia maszynowego i powyżej reguł istnieje szansa, że poniższe kroki nie doprowadzi do zdarzenia o podwyższonym ryzyku. Można replikować te kroki dla wielu kont usługi Azure AD opublikować to zdarzenie o podwyższonym ryzyku.


**Aby zasymulować niemożliwe przemieszczenie do nietypowej lokalizacji, wykonaj następujące czynności**:

1. Za pomocą przeglądarki użytkownika standardowego, przejdź do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Wprowadź poświadczenia konta, którego chcesz wygenerować zdarzenie o podwyższonym ryzyku więc podróż jest wykluczona.
3. Zmień agenta użytkownika. Można zmienić agenta użytkownika w programie Internet Explorer z narzędzi deweloperskich lub zmień agenta użytkownika w Firefox lub Chrome przy użyciu dodatku przełącznik agenta użytkownika.
4. Zmienianie adresu IP. Adres IP można zmienić za pomocą sieci VPN, dodatek Tor lub wdrażanie nowej maszyny na platformie Azure w centrum danych.
5. Zaloguj się do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) przy użyciu tych samych poświadczeń, jak poprzednio, jak i w ramach po kilku minutach od poprzedniego logowania.

Identyfikator logowania pojawia się na pulpicie nawigacyjnym ochrony tożsamości w ciągu 2 – 4 godzin.

## <a name="simulating-vulnerabilities"></a>Symulowanie luk w zabezpieczeniach
Luki w zabezpieczeniach występują luki w środowisku usługi Azure AD, które może zostać wykorzystana przez nieuprawnione. Obecnie 3 typy luk w zabezpieczeniach są udostępniane w usłudze Azure AD Identity Protection, korzystających z innych funkcji usługi Azure AD. Te luki w zabezpieczeniach pojawi się na pulpicie nawigacyjnym ochrony tożsamości automatycznie po skonfigurowaniu tych funkcji.

* Usługa Azure AD [uwierzytelnianie wieloskładnikowe](../authentication/multi-factor-authentication.md)
* Usługa Azure AD [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Usługa Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 


## <a name="testing-security-policies"></a>Testowanie zasad zabezpieczeń

Ta sekcja zawiera kroki do badania ryzyka związanego z użytkownikiem i ryzyko logowania zasad zabezpieczeń.


### <a name="user-risk-security-policy"></a>Zasady zabezpieczeń ryzyka użytkownika

Aby uzyskać więcej informacji, zobacz temat [How To: Configure the user risk policy](howto-user-risk-policy.md) (Jak skonfigurować zasady dotyczące ryzyka związanego z użytkownikiem).

![Ryzyko związane z użytkownikiem](./media/playbook/02.png "elementu Playbook")


**Aby przetestować zasady zabezpieczeń ryzyka użytkownika, wykonaj następujące czynności**:

1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Przejdź do **Identity Protection**. 
3. Na **usługi Azure AD Identity Protection** kliknij **zasad ryzyka dla użytkownika**.
4. W **przypisania** wybierz żądaną użytkowników (i grup) i poziomu ryzyka użytkownika.

    ![Ryzyko związane z użytkownikiem](./media/playbook/03.png "elementu Playbook")

5. W sekcji Formanty wybierz żądaną kontroli dostępu (np. wymagane zmiany hasła).
5. Jako **wymuszanie zasad**, wybierz opcję **poza**.
6. Podniesienie poziomu ryzyka użytkownika konta testowego, na przykład, symulując jednego ze zdarzeń o podwyższonym ryzyku kilka razy.
7. Poczekaj kilka minut, a następnie sprawdź, czy poziom użytkownika dla użytkownika to średni. W przeciwnym razie symulować więcej zdarzeń o podwyższonym ryzyku dla użytkownika.
8. Jako **wymuszanie zasad**, wybierz opcję **na**.
9. Teraz możesz przetestować dostęp warunkowy na podstawie ryzyka użytkownika, logując się przy użyciu poziom ryzyka związanego z podwyższonym poziomem uprawnień użytkownika.
    
    

### <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń ryzyka logowania

Aby uzyskać więcej informacji, zobacz temat [How To: Configure the sign-in risk policy](howto-sign-in-risk-policy.md) (Jak skonfigurować zasady dotyczące ryzyka związanego z logowaniem).

![Ryzyko logowania](./media/playbook/01.png "elementu Playbook")


**Aby przetestować znakiem w zasadzie ryzyka, wykonaj następujące czynności:**

1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.

2. Przejdź do **Azure AD Identity Protection**.

3. W głównym **usługi Azure AD Identity Protection** kliknij **zasad ryzyka logowania**. 

4. W **przypisania** sekcji, wybierz żądane użytkowników (i grup) i zaloguj się poziom ryzyka.

    ![Ryzyko logowania](./media/playbook/04.png "elementu Playbook")


5. W **kontrolki** wybierz żądaną kontroli dostępu (na przykład **Wymagaj uwierzytelniania wieloskładnikowego**). 

6. Jako **wymuszanie zasad**, wybierz opcję **na**.

7. Kliknij pozycję **Zapisz**.

8. Teraz możesz przetestować dostęp warunkowy na podstawie ryzyka logowania, logując się przy użyciu ryzykowne sesji (na przykład przy użyciu przeglądarki sieci Tor). 

 




## <a name="see-also"></a>Zobacz także

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)

