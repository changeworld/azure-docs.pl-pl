---
title: Symulowanie wykrywania ryzyka w usłudze Azure AD Identity Protection
description: Dowiedz się, jak symulować wykrywanie ryzyka w ucho.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886937"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Symulowanie wykrywania ryzyka w ochronie tożsamości

Administratorzy mogą chcieć symulować ryzyko w swoim środowisku w celu wykonania następujących elementów:

- Wypełnianie danych w środowisku ochrony tożsamości przez symulowanie wykrywania ryzyka i luk w zabezpieczeniach.
- Skonfiguruj oparte na ryzyku zasady dostępu warunkowego i przetestuj wpływ tych zasad.

W tym artykule przedstawiono kroki dotyczące symulowania następujących typów wykrywania ryzyka:

- Anonimowy adres IP (łatwy)
- Nieznane właściwości logowania (umiarkowane)
- Nietypowe podróże (trudne)

Inne wykrywanie ryzyka nie może być symulowane w bezpieczny sposób.

Więcej informacji na temat każdego wykrywania ryzyka można znaleźć w artykule, [Co to jest ryzyko](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Anonimowy adres IP

Wykonanie następującej procedury wymaga użycia:

- [Przeglądarka Tora](https://www.torproject.org/projects/torbrowser.html.en) do symulacji anonimowych adresów IP. Może być konieczne użycie maszyny wirtualnej, jeśli twoja organizacja ogranicza korzystanie z przeglądarki Tora.
- Konto testowe, które nie jest jeszcze zarejestrowane dla uwierzytelniania wieloskładnikowego platformy Azure.

**Aby symulować logowanie z anonimowego adresu IP, wykonaj następujące czynności:**

1. Korzystając z [przeglądarki Tora,](https://www.torproject.org/projects/torbrowser.html.en)przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Wprowadź poświadczenia konta, które ma być wyświetlane w raporcie **Logowania z anonimowych adresów IP.**

Logowanie pojawi się na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10 - 15 minut. 

## <a name="unfamiliar-sign-in-properties"></a>Nieznane właściwości logowania

Aby symulować nieznane lokalizacje, musisz zalogować się z lokalizacji i urządzenia, z którego konto testowe nie zalogowało się wcześniej.

Poniższa procedura wykorzystuje nowo utworzony:

- połączenie VPN, aby symulować nową lokalizację.
- Maszyna wirtualna, aby symulować nowe urządzenie.

Wykonanie poniższej procedury wymaga użycia konta użytkownika, które ma:

- Co najmniej 30-dniowa historia logowania.
- Azure Uwierzytelnianie wieloskładnikowe włączone.

**Aby symulować logowanie z nieznanej lokalizacji, wykonaj następujące czynności:**

1. Podczas logowania się przy użyciu konta testowego nie powiedzie się wyzwanie uwierzytelniania wieloskładnikowego (MFA), nie przechodząc wyzwania usługi MFA.
2. Korzystając z nowej sieci [https://myapps.microsoft.com](https://myapps.microsoft.com) VPN, przejdź do konta testowego i wprowadź go.

Logowanie pojawi się na pulpicie nawigacyjnym ochrony tożsamości w ciągu 10 - 15 minut.

## <a name="atypical-travel"></a>Nietypowe podróże

Symulowanie nietypowego stanu podróży jest trudne, ponieważ algorytm używa uczenia maszynowego do wykrywania fałszywych alarmów, takich jak nietypowe podróże ze znanych urządzeń lub logowania z sieci VPN, które są używane przez innych użytkowników w katalogu. Ponadto algorytm wymaga historii logowania 14 dni i 10 logowania użytkownika, zanim zacznie generować wykrywanie ryzyka. Ze względu na złożone modele uczenia maszynowego i powyższe reguły istnieje prawdopodobieństwo, że następujące kroki nie doprowadzą do wykrycia ryzyka. Można replikować te kroki dla wielu kont usługi Azure AD, aby symulować to wykrywanie.

**Aby symulować nietypowe wykrywanie ryzyka podróży, wykonaj następujące czynności:**

1. Korzystając ze standardowej [https://myapps.microsoft.com](https://myapps.microsoft.com)przeglądarki, przejdź do .  
2. Wprowadź poświadczenia konta, dla którego chcesz wygenerować nietypowe wykrywanie ryzyka podróży.
3. Zmień agenta użytkownika. Agent użytkownika w programie Microsoft Edge można zmienić z Narzędzia programistyczne (F12).
4. Zmień swój adres IP. Możesz zmienić swój adres IP za pomocą sieci VPN, dodatku Tora lub utworzyć nową maszynę wirtualną na platformie Azure w innym centrum danych.
5. Zaloguj się [https://myapps.microsoft.com](https://myapps.microsoft.com) do używania tych samych poświadczeń, co przed i w ciągu kilku minut po poprzednim logowanie.

Logowanie pojawi się na pulpicie nawigacyjnym ochrony tożsamości w ciągu 2-4 godzin.

## <a name="testing-risk-policies"></a>Testowanie zasad ryzyka

W tej sekcji przedstawiono kroki testowania użytkownika i zasady ryzyka logowania utworzone w [artykule Jak: Konfigurowanie i włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Zasady dotyczące ryzyka użytkownika

Aby przetestować zasady zabezpieczeń ryzyka użytkownika, wykonaj następujące czynności:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do**przeglądu****zabezpieczeń** >  **usługi Azure Active Directory** > .
1. Wybierz **pozycję Konfiguruj zasady ryzyka użytkownika**.
   1. W obszarze **Przydziały**
      1. **Użytkownicy** — wybierz **wszystkich użytkowników** lub Wybierz osoby **i grupy,** jeśli ograniczysz wdrożenie.
         1. Opcjonalnie można wykluczyć użytkowników z zasad.
      1. **Warunki** - **Użytkownik ryzyko** Microsoft zalecenie jest ustawienie tej opcji na **Wysoki**.
   1. W obszarze **Kontrole**
      1. **Dostęp** — zalecenie firmy Microsoft polega **na zezwoleniu na dostęp** i **wymaganie zmiany hasła**.
   1. **Wymuszanie wyłączonych zasad** - **Off**
   1. **Zapisz** — ta akcja spowoduje powrót do strony **Przegląd.**
1. Podnieś ryzyko użytkownika konta testowego, na przykład symulując kilka razy jedno z wykrywania ryzyka.
1. Zaczekaj kilka minut, a następnie sprawdź, czy ryzyko dla użytkownika zostało podwyższone. Jeśli nie, symuluj więcej wykrywania ryzyka dla użytkownika.
1. Wróć do zasad dotyczących ryzyka i ustaw **wymuszaj zasady** **na Włączone** i **Zapisz** zmianę zasad.
1. Teraz można przetestować dostęp warunkowy oparty na ryzyku użytkownika, logując się przy użyciu użytkownika z podwyższonym poziomem ryzyka.

### <a name="sign-in-risk-security-policy"></a>Zasady zabezpieczeń ryzyka logowania

Aby przetestować zasady dotyczące ryzyka logowania, wykonaj następujące czynności:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do**przeglądu****zabezpieczeń** >  **usługi Azure Active Directory** > .
1. Wybierz **pozycję Konfiguruj zasady ryzyka logowania**.
   1. W obszarze **Przydziały**
      1. **Użytkownicy** — wybierz **wszystkich użytkowników** lub Wybierz osoby **i grupy,** jeśli ograniczysz wdrożenie.
         1. Opcjonalnie można wykluczyć użytkowników z zasad.
      1. **Warunki** - **Ryzyko logowania Microsoft** zaleca, aby ustawić tę opcję na Średni i **wyższy**.
   1. W obszarze **Kontrole**
      1. **Dostęp** — zalecenie firmy Microsoft polega **na zezwoleniu na dostęp** i **wymaganie uwierzytelniania wieloskładnikowego**.
   1. **Wymuszaj zasady** - **włączone**
   1. **Zapisz** — ta akcja spowoduje powrót do strony **Przegląd.**
1. Teraz możesz przetestować dostęp warunkowy oparty na ryzyku logowania, logując się przy użyciu ryzykownej sesji (na przykład za pomocą przeglądarki Tora). 

## <a name="next-steps"></a>Następne kroki

- [Co to jest ryzyko?](concept-identity-protection-risks.md)

- [Jak: Konfigurowanie i włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md)

- [Ochrona tożsamości w usłudze Azure Active Directory](overview-identity-protection.md)
