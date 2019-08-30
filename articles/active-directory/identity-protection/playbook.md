---
title: Azure Active Directory Identity Protection element PlayBook | Microsoft Docs
description: Dowiedz się, w jaki sposób Azure AD Identity Protection pozwala na ograniczenie zdolności osoby atakującej do korzystania ze złamanej tożsamości lub urządzenia oraz zabezpieczenia tożsamości lub urządzenia, które było wcześniej podejrzane lub uznane za zagrożone.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fcf24256634ef11b575348d9da7d6bbbab8b67c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127764"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection element PlayBook

Ta element PlayBook ułatwia:

* Wypełnianie danych w środowisku ochrony tożsamości poprzez symulowanie wykrywania ryzyka i luk w zabezpieczeniach
* Konfigurowanie zasad dostępu warunkowego opartego na ryzyku i testowanie wpływu tych zasad

## <a name="simulating-risk-detections"></a>Symulowanie wykrywania ryzyka

Ta sekcja zawiera kroki umożliwiające symulowanie następujących typów wykrywania ryzyka:

* Logowania z anonimowych adresów IP (łatwe)
* Logowania z nieznanych lokalizacji (umiarkowanie)
* Niemożliwa podróż do nietypowych lokalizacji (trudne)

Inne wykrycia ryzyka nie mogą być symulowane w sposób bezpieczny.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Aby uzyskać więcej informacji na temat tego wykrywania ryzyka, zobacz [logowania z anonimowych adresów IP](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Wykonanie poniższej procedury wymaga zastosowania:

- [Przeglądarka sieci Tor](https://www.torproject.org/projects/torbrowser.html.en) do symulowania anonimowych adresów IP. Może być konieczne użycie maszyny wirtualnej, jeśli Twoja organizacja ogranicza użycie przeglądarki sieci Tor.
- Konto testowe, które nie zostało jeszcze zarejestrowane na potrzeby uwierzytelniania wieloskładnikowego.

**Aby zasymulować logowanie z anonimowego adresu IP, wykonaj następujące czynności**:

1. Korzystając z [przeglądarki tor](https://www.torproject.org/projects/torbrowser.html.en), przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Wprowadź poświadczenia konta, które mają być wyświetlane w raporcie **logowania z anonimowych adresów IP** .

Logowanie zostanie wyświetlone na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10-15 minut. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Logowania z nieznanych lokalizacji

Aby uzyskać więcej informacji na temat tego wykrywania ryzyka, zobacz [logowania z nieznanych lokalizacji](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Aby symulować nieznane lokalizacje, musisz zalogować się z lokalizacji i urządzenia, z którego konto testowe nie zalogował się przed.

Poniższa procedura używa nowo utworzonego:

- Połączenie sieci VPN, aby symulować nową lokalizację.
- Maszyna wirtualna, która symuluje nowe urządzenie.

Wykonanie poniższej procedury wymaga skorzystania z konta użytkownika, które ma:

- Co najmniej 30-dniowa historia logowania.
- Włączono uwierzytelnianie wieloskładnikowe.

**Aby zasymulować logowanie z nieznanej lokalizacji, wykonaj następujące czynności**:

1. Gdy logujesz się przy użyciu konta testowego, niepowodzenie żądania MFA przez nieprzekazywanie żądania MFA.
2. Korzystając z nowej sieci VPN, przejdź [https://myapps.microsoft.com](https://myapps.microsoft.com) do i wprowadź poświadczenia konta testowego.

Logowanie zostanie wyświetlone na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10-15 minut.

### <a name="impossible-travel-to-atypical-location"></a>Niemożliwa podróż do nietypowej lokalizacji

Aby uzyskać więcej informacji na temat tego wykrywania ryzyka, zobacz [niemożliwa podróż do nietypowej lokalizacji](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Symulowanie niemożliwego stanu podróży jest trudne, ponieważ algorytm używa uczenia maszynowego do oddzielenia fałszywych wartości false, takich jak niemożliwa podróż ze znanych urządzeń lub logowania z sieci VPN używanych przez innych użytkowników w katalogu. Ponadto algorytm wymaga od 14 dni i 10 logowań użytkownika przed rozpoczęciem generowania wykrycia ryzyka. Ze względu na złożone modele uczenia maszynowego i powyższe reguły istnieje prawdopodobieństwo, że następujące kroki nie spowodują wykrycia ryzyka. Można wykonać replikację tych kroków dla wielu kont usługi Azure AD, aby opublikować to wykrywanie ryzyka.

**W celu symulowania niemożliwej podróży do nietypowej lokalizacji wykonaj następujące czynności**:

1. Korzystając z standardowej przeglądarki, przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Wprowadź poświadczenia konta, dla którego chcesz wygenerować niemożliwe wypróbowanie wyjazdu.
3. Zmień agenta użytkownika. Możesz zmienić agenta użytkownika w programie Internet Explorer z usługi Narzędzia deweloperskie lub zmienić agenta użytkownika w przeglądarce Firefox lub Chrome przy użyciu dodatku User-Agent przełącznik.
4. Zmień adres IP. Adres IP można zmienić przy użyciu sieci VPN, dodatku sieci Tor lub nowego komputera na platformie Azure w innym centrum danych.
5. Zaloguj się, aby [https://myapps.microsoft.com](https://myapps.microsoft.com) korzystać z tych samych poświadczeń co poprzednio i w ciągu kilku minut od momentu wcześniejszego zalogowania.

Logowanie zostanie wyświetlone na pulpicie nawigacyjnym ochrony tożsamości w ciągu 2-4 godzin.

## <a name="simulating-vulnerabilities"></a>Symulowanie luk w zabezpieczeniach
Luki w zabezpieczeniach są słabe w środowisku usługi Azure AD, które mogą być wykorzystywane przez zły aktora. Obecnie 3 typy luk w zabezpieczeniach są naAzure AD Identity Protection, które wykorzystują inne funkcje usługi Azure AD. Te luki w zabezpieczeniach zostaną wyświetlone na pulpicie nawigacyjnym ochrony tożsamości automatycznie po skonfigurowaniu tych funkcji.

* [Multi-Factor Authentication](../authentication/multi-factor-authentication.md) usługi Azure AD
* [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/)usługi Azure AD.
* [Privileged Identity Management](../privileged-identity-management/pim-configure.md)usługi Azure AD. 

## <a name="testing-security-policies"></a>Testowanie zasad zabezpieczeń

Ta sekcja zawiera kroki testowania ryzyka użytkownika i zasad zabezpieczeń dotyczących ryzyka związanego z logowaniem.

### <a name="user-risk-security-policy"></a>Zasady zabezpieczeń dotyczące ryzyka użytkownika

Aby uzyskać więcej informacji, zobacz temat [How To: Configure the user risk policy](howto-user-risk-policy.md) (Jak skonfigurować zasady dotyczące ryzyka związanego z użytkownikiem).

![Ryzyko użytkownika](./media/playbook/02.png "Element PlayBook")

**Aby przetestować zasady zabezpieczeń dotyczące ryzyka użytkownika, wykonaj następujące czynności**:

1. Zaloguj się do [https://portal.azure.com](https://portal.azure.com) programu przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Przejdź do programu **Identity Protection**. 
3. Na stronie **Azure AD Identity Protection** kliknij pozycję **zasady ryzyka użytkownika**.
4. W sekcji **przypisania** wybierz żądanych użytkowników (i grupy) i poziom ryzyka użytkownika.

    ![Ryzyko użytkownika](./media/playbook/03.png "Element PlayBook")

5. W sekcji kontrolki wybierz żądaną kontrolę dostępu (np. Wymagaj zmiany hasła).
5. Jako **Wymuszaj zasady**wybierz pozycję **wyłączone**.
6. Podnieś poziom ryzyka dla użytkownika przy użyciu konta testowego, na przykład symulacja jednego z wykrycia ryzyka kilka razy.
7. Poczekaj kilka minut, a następnie sprawdź, czy poziom użytkownika dla użytkownika to średni. Jeśli nie, Symuluj więcej wykryć ryzyka dla użytkownika.
8. Jako **Wymuszaj zasady**wybierz pozycję **włączone**.
9. Teraz można testować dostęp warunkowy oparty na ryzyku, logując się przy użyciu użytkownika z podwyższonym poziomem ryzyka.

### <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń dotyczące zagrożeń związanych z logowaniem

Aby uzyskać więcej informacji, zobacz temat [How To: Configure the sign-in risk policy](howto-sign-in-risk-policy.md) (Jak skonfigurować zasady dotyczące ryzyka związanego z logowaniem).

![Ryzyko związane z logowaniem](./media/playbook/01.png "Element PlayBook")

**Aby przetestować zasady dotyczące ryzyka związanego z logowaniem, wykonaj następujące czynności:**

1. Zaloguj się do [https://portal.azure.com](https://portal.azure.com) programu przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Przejdź do **Azure AD Identity Protection**.
3. Na stronie głównej **Azure AD Identity Protection** kliknij pozycję **zasady ryzyka**dotyczącego logowania. 
4. W sekcji **przypisania** wybierz żądanych użytkowników (i grupy) i poziom ryzyka związanego z logowaniem.

    ![Ryzyko związane z logowaniem](./media/playbook/04.png "Element PlayBook")

5. W sekcji **Controls (formanty** ) wybierz odpowiednią kontrolę dostępu (na przykład **Wymagaj uwierzytelniania**wieloskładnikowego). 
6. Jako **Wymuszaj zasady**wybierz pozycję **włączone**.
7. Kliknij polecenie **Zapisz**.
8. Teraz można testować dostęp warunkowy oparty na ryzyku, logując się przy użyciu ryzykownej sesji (na przykład przy użyciu przeglądarki tor). 

## <a name="see-also"></a>Zobacz także

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
