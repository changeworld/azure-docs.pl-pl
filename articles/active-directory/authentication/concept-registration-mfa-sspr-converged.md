---
title: Osiągnięcia zbieżności rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (publiczna wersja zapoznawcza)
description: Uwierzytelniania wieloskładnikowego w usłudze Azure AD i samoobsługi resetowaniem hasła rejestracji (publiczna wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: d6915ce659d96021d4185be3818919fcfb9d4371
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492896"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Osiągnięcia zbieżności rejestracji samoobsługowego resetowania hasła i usługi Azure Multi-Factor Authentication (publiczna wersja zapoznawcza)

Do tej pory użytkowników były wymagane do zarejestrowania metod uwierzytelniania dla usługi Azure Multi-Factor Authentication (MFA) i samoobsługowego resetowania haseł (SSPR) w dwóch różnych portali. Wielu użytkowników zostały mylić faktem, że podobne metody były używane dla usługi Azure MFA i samoobsługowego resetowania HASEŁ i nie może zarejestrować się w obu portalach. Ta różnorodność doprowadziło do niektórzy użytkownicy nie będą mogli używać usługi Azure MFA lub funkcji samoobsługowego resetowania HASEŁ w razie potrzeby, co prowadzi do wywołania pomocy technicznej i potencjalnie zły użytkownika. Teraz użytkownicy mogą zarejestrować jeden raz i Uzyskaj korzyści z usługi Azure MFA i samoobsługowego resetowania HASEŁ, eliminując konieczność dwukrotnie Zarejestruj swoje metody uwierzytelniania dla tych funkcji.  

Przed włączeniem nowe środowisko w Twojej organizacji, firma Microsoft zaleca zapoznanie się w tym artykule, a także naszych [dokumentację użytkownika końcowego](https://aka.ms/securityinfoguide) Aby zrozumieć wpływ środowisko będzie mieć na użytkowników. Możesz użyć [dokumentację użytkownika końcowego](https://aka.ms/securityinfoguide) w celu nauczenia i przygotowanie użytkowników do nowego środowiska i upewnij się, pomyślne wdrożenie.

|     |
| --- |
| Osiągnięcia zbieżności rejestracji samoobsługowego resetowania hasła i usługi Azure Multi-Factor Authentication to funkcja publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Aby umożliwić użytkownikom rejestrowanie metod uwierzytelniania dla usługi Azure Multi-Factor Authentication i Samoobsługowe resetowanie haseł w jednym środowisku, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal jako administrator globalny lub administrator użytkowników.
2. Przejdź do **usługi Azure Active Directory**, **ustawienia użytkownika**, **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania nimi zabezpieczające**, możesz włączyć dla **wybrane** grupy użytkowników lub dla **wszystkich** użytkowników.

Użytkownicy mogą teraz przejść do [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) zarejestrować swoje metody uwierzytelniania dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Aby dowiedzieć się więcej na temat użytkownicy zobaczą w nowe środowisko, zobacz nasze [dokumentację użytkownika końcowego](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Po włączeniu to środowisko użytkowników, którzy rejestrowanie lub Potwierdzanie numeru telefonu lub aplikacji mobilnej za pomocą nowego środowiska będą mieć możliwość używania ich do uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli te metody są włączone w zasadach uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. Jeśli wyłączysz następnie tego środowiska, użytkownicy, którzy przejdź do poprzedniej strony rejestracji samoobsługowego resetowania HASEŁ w aka.ms/ssprsetup będą musieli wykonać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do strony.  

## <a name="how-it-works"></a>Jak to działa

Jeśli użytkownik został zarejestrowany wcześniej metody uwierzytelniania za pomocą osobnych środowisk rejestracji uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ, nie muszą zarejestrować tych informacji ponownie. Jednak jeśli ustawienia wymagać od użytkowników rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, może zostać wyświetlony monit, Przejrzyj swoje informacje zabezpieczające, podczas logowania.

Jeśli użytkownik został zarejestrowany metodę, która może służyć do uwierzytelniania Wieloskładnikowego, zostanie monit o przeprowadzenie uwierzytelniania Wieloskładnikowego przed uzyskaniem dostępu do nowego środowiska.

Jeśli rejestracji ma być wymuszane dla uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, a użytkownik nie został jeszcze zarejestrowany, zostanie monit o rejestrowania się podczas logowania.

Użytkownicy, którzy są monit o zarejestrowanie podczas logowania zostanie wyświetlony następujący proces:

![Osiągnięcia zbieżności rejestracji. Konfigurowanie metod jako nowy użytkownik](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Doświadczenia te będą wyświetlane tylko po użytkownik jest monitowany o rejestrowanie podczas logowania. Użytkownicy, którzy przejść bezpośrednio do nowego środowiska w aka.ms/setupsecurityinfo zobaczą inną wersję środowiska, który jest opisany w dalszej części tego artykułu.

Metody uwierzytelniania, pokazano zmieni się zależnie od metody włączone w zasadach uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ. Użytkownik będzie monitowany zarejestrować minimalną liczbę metod uwierzytelniania, konieczne jest zgodne z zasadami uwierzytelniania Wieloskładnikowego i/lub zasad samoobsługowego resetowania HASEŁ. W przypadku elastyczność w metody uwierzytelniania, które użytkownik może zarejestrować, użytkownik może wybrać **wybierz informacje o zabezpieczeniach** wybrać inne metody uwierzytelniania.  

W przeciwieństwie do poprzednich proces rejestracji usługi MFA użytkownicy nie będą monitowani zarejestrować hasła aplikacji, podczas realizacji nowego środowiska rejestracji. Powinny one zamiast tego wykonaj czynności opisane w naszym samouczku haseł aplikacji do rejestrowania haseł aplikacji w nowym środowisku.  

Po zakończeniu rejestracji użytkownik automatycznie można ustawić ich domyślną metodą uwierzytelniania Wieloskładnikowego. Jeśli użytkownik nie zarejestrowano aplikację wystawcy uwierzytelnienia, domyślną metodą zostanie ustawiona do aplikacji. Jeśli użytkownik nie zarejestrował aplikację wystawcy uwierzytelnienia i rejestrować tylko numeru telefonu, domyślną metodą ustawi połączenie telefoniczne. Użytkownicy mogą zmieniać domyślne, przechodząc do [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo) i wybierając polecenie **zmienić ustawienie domyślne**.  

Jeśli rejestracja nie jest wymuszana, użytkownicy mogą zarządzać ich własnych metod uwierzytelniania w [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo). Jeśli użytkownik został zarejestrowany wcześniej metodę, która może służyć do wykonywania uwierzytelniania Wieloskładnikowego, poprosimy przeprowadzenie uwierzytelniania Wieloskładnikowego przed uzyskaniem dostępu do strony.  

![Osiągnięcia zbieżności rejestracji. Edytuj metody co zarejestrowany użytkownik](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Na tej stronie użytkownicy będą widzieć metody uwierzytelniania wcześniej zarejestrowanego i metod uwierzytelniania, zarejestrowanych dla nich, takie jak telefon biurowy. Użytkownicy mogą również dodawać, edytować lub usunąć metody ich uwierzytelniania (z wyjątkiem telefonu biurowego).  

Dzienniki inspekcji dla tego nowego środowiska istnieje w kategorii metod uwierzytelniania w dzienniku inspekcji.  

## <a name="known-issues"></a>Znane problemy

**Po użytkownik rejestruje telefon, za pomocą wiadomości SMS, domyślną metodą uwierzytelniania Wieloskładnikowego jest ustawiony na połączenie telefoniczne**

   * Niektóre użytkownicy mogą zauważyć, że ich domyślną metodą uwierzytelniania Wieloskładnikowego jest ustawiona na połączenie telefoniczne, po zarejestrowaniu się ich numer telefonu, za pomocą wiadomości SMS. Użytkownicy mogą rozwiązać ten problem, zmieniając ich domyślną metodę, postępując zgodnie z instrukcjami przedstawionymi w artykule [Zarządzanie zabezpieczających (wersja zapoznawcza)](../user-help/security-info-manage-settings.md#change-your-info).

**Użytkownik nie mógł uzyskać dostępu do nowego środowiska rejestracji, po administratora wyłącza ich domyślną metodę**

   * Niektórzy użytkownicy mogą nie móc uzyskać dostęp do nowego środowiska rejestracji, gdy ich wcześniej zarejestrowaną wartością domyślną metodę uwierzytelnianie wieloskładnikowe zostało wyłączone przez administratora. Poniżej przedstawiono przykładowy scenariusz:
      1. Użytkownik wcześniej zarejestrowany numeru telefonu i ustaw ich domyślną metodę połączenie telefoniczne.
      2. Administrator wyłącza połączenie telefoniczne jako metodę uwierzytelniania Wieloskładnikowego dla dzierżawy.
      3. Użytkownik jest monitowany o rejestrowanie podczas logowania, ponieważ muszą zarejestrować dodatkową metodę umożliwiającą spełniają dzierżawy zasad samoobsługowego resetowania HASEŁ.
      4. Użytkownik próbuje zarejestrować, ale nie może uzyskać dostęp do strony, z powodu nie posiadają domyślnej metody zestawu i jest zablokowane w pętli.

## <a name="next-steps"></a>Kolejne kroki

[Informacje o sposobie wdrażania usługi Azure AD samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Dowiedz się, jak wymagać uwierzytelniania wieloskładnikowego podczas logowania](howto-mfa-getstarted.md)

[Dokumentacja konfiguracji metody uwierzytelniania użytkownika](https://aka.ms/securityinfoguide)