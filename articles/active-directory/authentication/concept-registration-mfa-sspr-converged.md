---
title: Osiągnięcia zbieżności rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (publiczna wersja zapoznawcza)
description: Uwierzytelnianie wieloskładnikowe systemu Azure AD i samoobsługi resetowaniem hasła rejestracji (publiczna wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: a3058eb3472b2bc9109ebc7b93b83b9f27091edd
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487595"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Osiągnięcia zbieżności rejestracji samoobsługowego resetowania hasła i usługi Azure Multi-Factor Authentication (publiczna wersja zapoznawcza)

Do tej pory użytkowników były wymagane do zarejestrowania metod uwierzytelniania dla usługi Azure Multi-Factor Authentication (MFA) i samoobsługowego resetowania haseł (SSPR) w dwóch różnych portali. Wielu użytkowników zostały mylić, że podobne metody były używane dla usługi Azure MFA i samoobsługowego resetowania HASEŁ i nie może zarejestrować się w obu portalach. Niektórzy użytkownicy nie mogą następnie do użycia usługi Azure MFA lub funkcji samoobsługowego resetowania HASEŁ, podczas potrzebny, prowadzącego do działu pomocy technicznej. 

Teraz użytkownicy mogą zarejestrować jeden raz i Uzyskaj korzyści z usługi Azure MFA i samoobsługowego resetowania HASEŁ. Użytkownicy nie muszą zarejestrować swoje metody uwierzytelniania dla tych funkcji, dwa razy.  

Przed włączeniem nowe środowisko w Twojej organizacji, firma Microsoft zaleca zapoznanie się w tym artykule i [dokumentacji użytkownika](https://aka.ms/securityinfoguide) zrozumienie wpływu, jaki proces będzie mieć na użytkowników. Dokumentacja użytkownika umożliwia uczenie i przygotowanie użytkowników do nowego środowiska pracy i zapewnić pomyślne wdrożenie.

|     |
| --- |
| Osiągnięcia zbieżności rejestracji samoobsługowego resetowania hasła i usługi Azure Multi-Factor Authentication to funkcja publicznej wersji zapoznawczej usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Aby umożliwić użytkownikom rejestrowanie metod uwierzytelniania dla usługi Azure Multi-Factor Authentication i Samoobsługowe resetowanie haseł w jednym środowisku, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal jako administrator globalny lub administrator użytkowników.
2. Przejdź do **usługi Azure Active Directory** > **ustawienia użytkownika** > **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania nimi zabezpieczające**, możesz włączyć dla **wybrane** grupy użytkowników lub dla **wszystkich** użytkowników.

Użytkownicy mogą teraz przejść do [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) zarejestrować swoje metody uwierzytelniania dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Aby dowiedzieć się więcej na temat użytkownicy zobaczą w nowe środowisko, zobacz [dokumentacji użytkownika](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Po włączeniu tego środowiska, użytkownicy rejestrowanie lub Potwierdzanie ich numer telefonu lub aplikacji mobilnej za pomocą nowego środowiska mogą używać ich do uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli te metody są włączone w zasadach uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Jeśli wyłączysz następnie tego środowiska, użytkownicy, którzy przejdź do poprzedniej strony rejestracji samoobsługowego resetowania HASEŁ w https:/aka.ms/ssprsetup będą musieli wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do strony.  

## <a name="how-it-works"></a>Jak to działa

Jeśli użytkownik został zarejestrowany wcześniej metody uwierzytelniania za pomocą osobnych środowisk rejestracji uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ, ich nie trzeba rejestrować informacje ponownie. Ale jeśli ustawienia wymagać od użytkowników rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, może zobaczyć monit Przejrzyj swoje informacje o zabezpieczeniach podczas logowania.

Jeśli użytkownik został zarejestrowany metodę, która może służyć do uwierzytelniania Wieloskładnikowego, są monitowani o wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do nowego środowiska.

Jeśli użytkownik nie został jeszcze zarejestrowany rejestracji ma być wymuszane dla uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, są monitowani rejestrowania się podczas logowania.

Użytkownicy, którzy są monit o zarejestrowanie podczas logowania, zobacz następujące środowiska:

![Osiągnięcia zbieżności rejestracji. Konfigurowanie metod jako nowy użytkownik.](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> To środowisko jest wyświetlana tylko wtedy, gdy użytkownik jest monitowany o rejestrowanie podczas logowania. Użytkownicy, którzy przejść bezpośrednio do doświadczeń https://aka.ms/setupsecurityinfo Zobacz inną wersję środowiska, który jest opisany w dalszej części tego artykułu.

Zmiana metody uwierzytelniania wyświetlany oparta na metodach włączone w zasad uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Użytkownik jest proszony o zarejestrować minimalną liczbę metod uwierzytelniania, konieczne jest zgodne z zasadami uwierzytelniania Wieloskładnikowego i/lub zasad samoobsługowego resetowania HASEŁ. W przypadku elastyczność w metody uwierzytelniania, które użytkownik może zarejestrować, użytkownik może wybrać **wybierz informacje o zabezpieczeniach** wybrać inne metody uwierzytelniania.  

> [!NOTE]
> Jeśli zostanie włączone zarówno powiadomienia aplikacji mobilnej, jak i kodu aplikacji mobilnej, użytkowników, którzy rejestracji aplikacji Microsoft Authenticator za pośrednictwem powiadomienia umożliwia powiadomienie i kod zweryfikować swoją tożsamość.

W przeciwieństwie do poprzednich proces rejestracji usługi MFA użytkownicy nie będą monitowani zarejestrować hasła aplikacji, podczas realizacji nowego środowiska rejestracji. Powinny one zamiast tego wykonaj czynności opisane w samouczku haseł aplikacji, aby zarejestrować haseł aplikacji w nowym środowisku.  

Po zakończeniu rejestracji użytkownik jest ustawiana automatycznie ich domyślną metodą uwierzytelniania Wieloskładnikowego. Jeśli użytkownik nie zarejestrowano aplikację wystawcy uwierzytelnienia, domyślną metodą jest równa aplikacji. Jeśli użytkownik nie zarejestrował aplikację wystawcy uwierzytelnienia i rejestrować tylko numeru telefonu, domyślną metodą jest równa połączeń telefonicznych. Użytkownicy mogą zmieniać domyślne, przechodząc do https://aka.ms/setupsecurityinfo i wybierając polecenie **zmienić ustawienie domyślne**.  

Jeśli rejestracja nie jest wymuszana, użytkownicy mogą zarządzać ich własnych metod uwierzytelniania w https://aka.ms/setupsecurityinfo. Jeśli użytkownik został zarejestrowany wcześniej metodę, która może służyć do uwierzytelniania Wieloskładnikowego, użytkownicy zostaną poproszeni o wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do strony.  

![Osiągnięcia zbieżności rejestracji. Edytuj metody jako zarejestrowanego użytkownika.](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Na tej stronie użytkownicy widzą metody uwierzytelniania wcześniej zarejestrowanego i metod uwierzytelniania, zarejestrowanych dla nich, takie jak telefon biurowy. Użytkownicy mogą również dodawać, edytować lub usunąć metody ich uwierzytelniania (z wyjątkiem telefonu biurowego).  

Dzienniki inspekcji dla tego nowego środowiska istnieje w kategorii metod uwierzytelniania w dzienniku inspekcji.  

## <a name="known-issues"></a>Znane problemy

**Po użytkownik rejestruje telefonie za pomocą wiadomości SMS, domyślną metodą uwierzytelniania Wieloskładnikowego jest ustawiony na połączenie telefoniczne**

   * Niektórzy użytkownicy zauważyć, że ich domyślną metodą uwierzytelniania Wieloskładnikowego jest ustawiona na połączenie telefoniczne, po ich zarejestrowania numeru telefonu za pomocą wiadomości SMS. Użytkownicy mogą rozwiązać ten problem, zmieniając ich domyślną metodę zgodnie z instrukcjami w artykule [Zarządzanie zabezpieczających (wersja zapoznawcza)](../user-help/security-info-manage-settings.md#change-your-info).

**Użytkownik nie może korzystać nowe środowisko rejestracji, po administrator wyłączy ich domyślną metodę**

   * Niektórzy użytkownicy może nie móc uzyskać dostęp do nowego środowiska rejestracji, jeśli administrator wyłączył wcześniej zarejestrowaną wartością domyślną metodę uwierzytelniania Wieloskładnikowego. Poniżej przedstawiono przykładowy scenariusz:
      1. Użytkownik wcześniej zarejestrowany numeru telefonu i ustaw ich domyślną metodę połączenie telefoniczne.
      2. Administrator wyłącza połączenie telefoniczne jako metodę uwierzytelniania Wieloskładnikowego dla dzierżawy.
      3. Użytkownik jest monitowany o rejestrowanie podczas logowania, ponieważ muszą zarejestrować dodatkową metodę umożliwiającą spełniają dzierżawy zasad samoobsługowego resetowania HASEŁ.
      4. Użytkownik próbuje zarejestrować, ale nie może uzyskać dostęp do strony i zablokowane w pętli, ponieważ nie ustawiono domyślną metodę.

## <a name="next-steps"></a>Kolejne kroki

[Informacje o sposobie wdrażania usługi Azure AD samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Dowiedz się, jak wymagać uwierzytelniania wieloskładnikowego dla logowania](howto-mfa-getstarted.md)

[Dowiedz się, jak skonfigurować metody uwierzytelniania użytkownika](https://aka.ms/securityinfoguide)
