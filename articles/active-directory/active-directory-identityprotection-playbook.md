---
title: Azure Active Directory Identity Protection podręcznika dotyczącego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Azure AD Identity Protection umożliwia ograniczenie możliwości osoby atakującej, która wykorzystać, którego bezpieczeństwo zostało naruszone tożsamości lub urządzenie i secure tożsamości lub urządzeń, które wcześniej podejrzenia lub znane naruszenia.
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, usługa cloud app discovery, zarządzanie aplikacjami, zabezpieczeń, ryzyka, poziom ryzyka, luki w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 210d097f0719725a0ecf145ce536875a383b04e6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure podręcznikowym ochronę tożsamości w usłudze Active Directory

Tego podręcznika dotyczącego ułatwia:

* Wypełnij dane w środowisku Identity Protection według symulowanie symulacje zdarzenia o podwyższonym ryzyku i luk w zabezpieczeniach
* Konfigurowanie zasad dostępu warunkowego opartego na zagrożenia i testowanie wpływu tych zasad


## <a name="simulating-risk-events"></a>Symulacja zdarzeń o podwyższonym ryzyku

W tej sekcji przedstawiono kroki symulowanie następujące typy zdarzeń ryzyka:

* Logowania z anonimowych adresów IP (prosty)
* Logowania z nieznanych lokalizacji (umiarkowany)
* Niemożliwa podróż do nietypowych lokalizacji (trudne)

Inne zdarzenia o podwyższonym ryzyku nie symulowane w bezpieczny sposób.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Aby uzyskać więcej informacji na temat danego zdarzenia ryzyka, zobacz [logowania z anonimowych adresów IP](active-directory-reporting-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Wykonanie poniższej procedury wymaga użycia:

- [Przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en) do symulowania anonimowych adresów IP. Należy użyć maszyny wirtualnej, jeśli Twoja organizacja ogranicza za pomocą przeglądarki Tor.
- Konto testu, który nie jest jeszcze zarejestrowany w usłudze Multi-Factor authentication.

**Aby symulować logowanie z anonimowego adresu IP, wykonaj następujące kroki**:

1. Przy użyciu [przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en), przejdź do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Wprowadź poświadczenia konta, która ma być wyświetlana w **logowania z anonimowych adresów IP** raportu.

Przy logowaniu są wyświetlane na pulpicie nawigacyjnym ochronę tożsamości w ciągu 10 – 15 minut. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Logowania z nieznanych lokalizacji

Aby uzyskać więcej informacji na temat danego zdarzenia ryzyka, zobacz [logowania z nieznanych lokalizacji](active-directory-reporting-risk-events.md#sign-in-from-unfamiliar-locations). 

Aby symulować nieznanych lokalizacji, należy zalogować się w lokalizacji i urządzenie, którego konto testu nie zalogował się z przed.

Poniższa procedura wykorzystuje nowo utworzone:

- Połączenie sieci VPN, aby symulować nowej lokalizacji.

- Maszyna wirtualna, aby symulować nowego urządzenia.

Wykonanie poniższej procedury można używać konta użytkownika, które ma wymaga:

- Co najmniej 30-dniowej logowania w historii.
- Uwierzytelnianie wieloskładnikowe jest włączone.


**Aby symulować logowania z nieznanych lokalizacji, wykonaj następujące kroki**:

1. Podczas logowania się przy użyciu konta testu, nie żądanie uwierzytelniania MFA nie przekazując żądanie uwierzytelniania MFA.
2. Przy użyciu nowego sieć VPN, przejdź do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) , a następnie wprowadź poświadczenia konta testowego.
   

Przy logowaniu są wyświetlane na pulpicie nawigacyjnym ochronę tożsamości w ciągu 10 – 15 minut.

### <a name="impossible-travel-to-atypical-location"></a>Niemożliwa podróż do nietypowych lokalizacji

Aby uzyskać więcej informacji na temat danego zdarzenia ryzyka, zobacz [Impossible podróż do nietypowych lokalizacji](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations). 

Symuluje warunku niemożliwa podróż jest trudne, ponieważ algorytm chwastów limit false alarmów, takich jak niemożliwa podróż ze znanych urządzeń lub logowania z sieci VPN, które są używane przez innych użytkowników w katalogu uczenia maszynowego. Ponadto algorytm wymaga historię logowania w ciągu ostatnich 14 dni i 10 logowania użytkownika, przed rozpoczęciem generowania zdarzeń o podwyższonym ryzyku. Z powodu złożonych modeli uczenia maszynowego i powyżej reguły istnieje ryzyko, że nie spowoduje następujące kroki zdarzenia ryzyka. Można replikować te kroki dla wielu kont usługi Azure AD do publikowania zdarzeń tego ryzyka.


**Aby symulować niemożliwa podróż do nietypowych lokalizacji, wykonaj następujące kroki**:

1. Za pomocą przeglądarki standardowe, przejdź do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Wprowadź poświadczenia konta, które mają zostać wygenerowane zdarzenie niemożliwa podróż ryzyka dla.
3. Zmień agenta użytkownika. Można zmienić agenta użytkownika w programie Internet Explorer z Developer Tools, lub zmień agenta użytkownika przeglądarki Firefox lub Chrome przy użyciu dodatku przełącznik agenta użytkownika.
4. Zmienianie adresu IP. Adres IP można zmienić za pomocą sieci VPN, dodatek Tor, lub kręci się nowego komputera na platformie Azure w centrum danych.
5. Zaloguj się do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) przy użyciu tych samych poświadczeń jako przed i za kilka minut po poprzednim logowanie.

Rejestrowanie zostaną wyświetlone na pulpicie nawigacyjnym ochronę tożsamości w ciągu 2-4 godzin.

## <a name="simulating-vulnerabilities"></a>Symuluje luk w zabezpieczeniach
Luki w zabezpieczeniach występują słabych w środowisku usługi Azure AD, które mogą być używane przez aktora nieprawidłowy. Obecnie 3 typy luk w zabezpieczeniach są udostępniane w Azure AD Identity Protection, zwiększają inne funkcje usługi Azure AD. Te luki w zabezpieczeniach będą wyświetlane na pulpicie nawigacyjnym Identity Protection automatycznie po skonfigurowaniu tych funkcji.

* Usługi Azure AD [uwierzytelnianie wieloskładnikowe](authentication/multi-factor-authentication.md)
* Usługi Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Usługi Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 


## <a name="testing-security-policies"></a>Testowanie zasad zabezpieczeń

Ta sekcja zawiera kroki do badania ryzyka użytkownika i zasady zabezpieczeń logowania ryzyka.


### <a name="user-risk-security-policy"></a>Zasady zabezpieczeń użytkownika ryzyka

Aby uzyskać więcej informacji, zobacz [zasady zabezpieczeń użytkownika ryzyka](active-directory-identityprotection.md#user-risk-security-policy).

![Ryzyko użytkownika](./media/active-directory-identityprotection-playbook/02.png "Podręcznikowym")


**Aby przetestować zasad zabezpieczeń dla użytkownika ryzyka, wykonaj następujące kroki**:

1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Przejdź do **ochronę tożsamości**. 
3. Na **Azure AD Identity Protection** kliknij przycisk **zasad ryzyka użytkownika**.
4. W **przypisania** wybierz odpowiednich użytkowników (i grup) i poziom ryzyka użytkownika.

    ![Ryzyko użytkownika](./media/active-directory-identityprotection-playbook/03.png "Podręcznikowym")

5. W sekcji Formanty wybierz żądany kontroli dostępu (np. wymagają zmiany hasła).
5. Jako **wymuszać zasady**, wybierz pozycję **poza**.
6. Podniesienie poziomu ryzyka użytkownika testowe konto, na przykład symulując jednego zdarzenia ryzyka kilka razy.
7. Poczekaj kilka minut, a następnie sprawdź, czy dla użytkownika na poziomie użytkownika jest średnia liczba godzin. W przeciwnym razie symulować więcej zdarzenia ryzyka dla użytkownika.
8. Jako **wymuszać zasady**, wybierz pozycję **na**.
9. Teraz możesz przetestować dostępu warunkowego opartego na ryzyko użytkownika, logując się przy użyciu użytkownik z poziomem ryzyka z podwyższonym poziomem uprawnień.
    
    

### <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń logowania ryzyka

Aby uzyskać więcej informacji, zobacz [zasady zabezpieczeń użytkownika ryzyka](active-directory-identityprotection.md#user-risk-security-policy).

![Logowanie ryzyka](./media/active-directory-identityprotection-playbook/01.png "Podręcznikowym")


**Aby przetestować znak w zasadzie ryzyka, należy wykonać następujące czynności:**

1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.

2. Przejdź do **Azure AD Identity Protection**.

3. W głównym **Azure AD Identity Protection** kliknij przycisk **logowania zasad ryzyka**. 

4. W **przypisania** sekcji, wybierz odpowiednich użytkowników (i grup) i zaloguj się poziom ryzyka.

    ![Logowanie ryzyka](./media/active-directory-identityprotection-playbook/04.png "Podręcznikowym")


5. W **formanty** wybierz żądany kontroli dostępu (na przykład **wymusić uwierzytelnianie wieloskładnikowe**). 

6. Jako **wymuszać zasady**, wybierz pozycję **na**.

7. Kliknij pozycję **Zapisz**.

8. Teraz możesz przetestować logowania dostępu warunkowego opartego na ryzyko logując się przy użyciu ryzykowne sesji (na przykład przy użyciu przeglądarki Tor). 

 




## <a name="see-also"></a>Zobacz także

- [Ochronę tożsamości usługi Azure Active Directory](active-directory-identityprotection.md)

