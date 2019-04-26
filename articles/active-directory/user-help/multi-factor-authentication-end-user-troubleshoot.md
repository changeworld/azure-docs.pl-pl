---
title: Rozwiązywanie problemów z weryfikacji dwuetapowej — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dla użytkowników o tym, co należy zrobić, jeśli działają wystąpił problem z usługą Azure Multi-Factor Authentication i weryfikacji dwuetapowej.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: b74049833b055caa112c346b74798893f2c0febf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60476676"
---
# <a name="get-help-with-two-step-verification"></a>Uzyskaj pomoc dotyczącą weryfikacji dwuetapowej

Weryfikacja dwuetapowa była to funkcja zabezpieczeń, które Twoja organizacja używa w celu ochrony Twojego konta. Weryfikacja dwuetapowa jest bezpieczniejsza niż użycie tylko hasła, ponieważ opiera się na dwóch metodach uwierzytelniania: czymś, co znasz, i czymś, co masz. Coś, co wiesz jest hasło, jest coś, co masz Tobie telefonu lub urządzenia. Przy użyciu weryfikacji dwuetapowej może pomóc zatrzymać hakerzy Logowanie jako użytkownik, nawet jeśli uzyskują swoje hasło.

Firma Microsoft oferuje weryfikacji dwuetapowej, ale jest Twojej organizacji, która decyduje, czy używana jest funkcja. Nie można wycofać się, jeśli Twoja organizacja potrzebuje, tak samo, jak nie możesz zrezygnować z przy użyciu hasła, aby chronić swoje konto.

>[!Note]
>Jeśli potrzebujesz więcej informacji na temat używania weryfikacji dwuetapowej za pomocą osobistego konta Microsoft, zobacz [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) artykułu.

## <a name="why-do-i-need-to-use-another-verification-method"></a>Dlaczego trzeba użyć innej metody weryfikacji?

Istnieje kilka powodów dlaczego konieczne może być metodą weryfikacji alternatywny z Twoim kontem. Na przykład:

- **Pamiętasz, telefonie lub urządzenia.** Kilku dni, które pozostaną Telefon w domu, ale nadal musisz się zarejestrować w miejscu pracy. Najpierw należy spróbować logowanie się przy użyciu innej metody, która nie wymaga Twój telefon.

- **Utraty telefonu lub masz nowy numer telefonu.** Jeśli utraty telefonu lub uzyskane nowy numer, możesz zalogować się przy użyciu innej metody lub skontaktuj się z administratorem, aby wyczyścić ustawienia. Zdecydowanie zaleca się umożliwienie Twojego administratora o Jeśli Twój telefon został utraty lub kradzieży odpowiednie aktualizacje można więc ustawić do Twojego konta. Po ustawienia zostaną wyczyszczone, zostanie wyświetlony monit [zarejestrować na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) przy następnym logowaniu.

- **Pobieranie nie tekst uwierzytelniania lub połączenia telefonicznego.** Istnieje kilka powodów dlaczego możesz nie otrzymać tekst lub połączenia telefonicznego. Jeśli w przeszłości zostało pomyślnie udostępnione teksty lub połączeń telefonicznych, to prawdopodobnie wystąpił problem z dostawcy telefonu, a nie konta. Jeśli masz często opóźnienia z powodu nieprawidłowych sygnał, zalecamy użycie [aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) na swoim urządzeniu przenośnym. Ta aplikacja może generować kody losowe zabezpieczeń podczas logowania, bez żadnych sygnału komórki lub połączenie z Internetem.<br><br>Jeśli chcesz odbierać wiadomość tekstową, ask przyjaznego tekstu jako test, aby upewnić się, Uzyskaj ją, a jeśli trafiła do Ciebie wiele wiadomości, upewnij się, że używasz kodu z najbardziej aktualny plan.

- **Hasła aplikacji nie działają.** Hasła aplikacji Zastąp normalne hasło starsze aplikacje klasyczne, które nie obsługują weryfikacji dwuetapowej. Najpierw upewnij się, że hasło została wpisana poprawnie. Jeśli to nie rozwiąże go, spróbuj zalogować się, aby [Utwórz nowe hasło aplikacji](multi-factor-authentication-end-user-app-passwords.md) lub skontaktować się z administratorem, aby [usunąć istniejące hasła aplikacji](../authentication/howto-mfa-userdevicesettings.md) aby można było utworzyć nowy.

## <a name="sign-in-using-another-verification-method"></a>Zaloguj się przy użyciu innej metody weryfikacji

1. zwykle Zaloguj się do swojego konta i wybierz **Zaloguj się w inny sposób** link **weryfikacji dwuetapowej** strony.

    ![Zmiany logowania metodę weryfikacji](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Jeśli nie widzisz **Zaloguj się w inny sposób** łącza, oznacza to, że nie skonfigurowano żadnych innych metod weryfikacji. Musisz skontaktować się z administratorem, aby uzyskać pomoc, logując się do swojego konta. Po zalogowaniu, upewnij się, że możesz dodać metody dodatkowej weryfikacji. Aby uzyskać więcej informacji na temat dodawania metod weryfikacji, zobacz [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md) artykułu.<br><br>Jeśli jest widoczny link, ale nadal nie widać żadnych innych metod weryfikacji, będziesz mieć skontaktuj się z administratorem pomocy przy logowaniu do Twojego konta.

2. Wybierz metodę weryfikacji alternatywnych i kontynuować proces weryfikacji dwuetapowej.

3. Wstecz w trakcie swojego konta, możesz zaktualizować swoje metody weryfikacji (jeśli jest to konieczne). Aby uzyskać więcej informacji o Dodawanie lub zmiana metody, zobacz [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md) artykułu.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nie mogę znaleźć odpowiedzi na problem

Jeśli sprawdzone następujące kroki, ale nadal są uruchomione w problemy, skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="related-topics"></a>Powiązane tematy

* [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)

* [Często zadawane pytania na temat aplikacji Microsoft Authenticator](user-help-auth-app-faq.md)
