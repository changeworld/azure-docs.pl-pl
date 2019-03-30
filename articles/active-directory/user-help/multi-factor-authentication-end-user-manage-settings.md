---
title: Zarządzanie ustawieniami weryfikacji dwuetapowej — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarządzanie, jak używać usługi Azure Multi-Factor Authentication, włącznie ze zmianami Twoich informacji kontaktowych i konfigurowanie urządzenia.
services: active-directory
keywords: Klient uwierzytelniania wieloskładnikowego, problem z uwierzytelnianiem, identyfikator korelacji
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433c2d712ca4867a5ec59f86c333511070b6d507
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665053"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Zarządzanie ustawieniami weryfikacji dwuetapowej
Ten artykuł zawiera odpowiedzi na pytania o tym, jak można zaktualizować ustawień uwierzytelniania dwuetapowego weryfikacji lub usługi Multi-Factor Authentication. Jeśli występują problemy z logowaniem do swojego konta, zobacz [występują problemy z weryfikacji dwuetapowej](multi-factor-authentication-end-user-troubleshoot.md) pomocy w rozwiązywaniu problemów.

## <a name="where-to-find-the-settings-page"></a>Gdzie można znaleźć na stronie ustawień
W zależności od tego, jak firma skonfigurować uwierzytelnianie wieloskładnikowe systemu Azure istnieje kilka miejsc, w którym można zmienić ustawienia, takie jak numer telefonu.

Jeśli dział pomocy technicznej Twojej firmy wysyłanie określonych adresów URL lub kroki, aby zarządzać weryfikacji dwuetapowej, wykonaj te instrukcje. W przeciwnym razie zgodnie z poniższymi instrukcjami powinny działać dla wszystkich else. Jeśli wykonaj następujące kroki, ale nie widzisz tych samych opcji, oznacza to, że pracy lub nauki dostosować własne portalu. Poproś administratora o link do portalu usługi Azure Multi-Factor Authentication.

**Aby przejść do strony weryfikacji zabezpieczeń**

- Przejdź do pozycji https://aka.ms/MFASetup (Plik > Nowy > Inny).

    ![Biurowego](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Jeśli kliknięcie tego linku nie działa, można także uzyskać, aby **dodatkowej weryfikacji zabezpieczeń** strony, wykonując następujące czynności:

1. Zaloguj się do [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2. Wybierz nazwę swojego konta w prawym górnym rogu, a następnie wybierz **profilu**.

3. Wybierz **dodatkowej weryfikacji zabezpieczeń**.  

    ![Usługa Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. Strona weryfikacji zabezpieczeń ładuje się z ustawieniami.

    ![Biurowego](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Chcę zmienić mój numer telefonu lub Dodaj pomocniczy numer
Należy skonfigurować numeru telefonu uwierzytelniania pomocniczego.  Ponieważ Twoje podstawowego numeru telefonu i aplikacji mobilnej prawdopodobnie znajdują się na ten sam numer telefonu, dodatkowy numer telefonu jest najszybszym sposobem można wrócić do swojego konta w przypadku utraty lub kradzieży telefonu.

> [!NOTE]
> Jeśli nie masz dostępu z podstawowym numerem telefonu i potrzebujesz pomocy przy uzyskiwanie w do swojego konta, zobacz [występują problemy z weryfikacji dwuetapowej](multi-factor-authentication-end-user-troubleshoot.md) artykuł, aby uzyskać dalszą pomoc.  

**Aby zmienić numer telefonu głównej:**  

1. Na **dodatkowej weryfikacji zabezpieczeń** stronie, wybierz pole tekstowe z bieżącego numeru telefonu i edytuje za pomocą nowego numeru telefonu.  
2. Wybierz pozycję **Zapisz**.  
3. Ten numer telefonu jest liczbą, używanego dla Twojego preferowaną opcję weryfikacji, należy sprawdzić nowy numer, zanim będzie można go zapisać.  

**Aby dodać dodatkowy numer telefonu:**  

1. Na stronie weryfikacji dodatkowe zabezpieczenia, zaznacz pole wyboru obok pozycji **alternatywny numer telefonu uwierzytelniania.**  
2. Wprowadź numer telefonu pomocniczy w polu tekstowym.  
3. Wybierz **Zapisz** i zmiany zostaną zakończone.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Wymagaj weryfikacji dwuetapowej ponownie na urządzeniu, które zostały oznaczone jako zaufany

W zależności od ustawień organizacji, mogą mieć pola wyboru, który jest wyświetlany komunikat "nie pytaj ponownie przez **X** dni" podczas wykonywania weryfikacji dwuetapowej w przeglądarce. Jeśli to pole wyboru i utraty urządzenia lub podejrzenie, że Twoje konto zostanie naruszony należy przywrócić weryfikację dwuetapową dla wszystkich urządzeń.

1. Na stronie weryfikacji zabezpieczeń wybierz **przywracania usługi Multi-Factor authentication na wcześniej zaufanych urządzeniach**.
2. Przy następnym zalogowaniu się na dowolnym urządzeniu, zostanie wyświetlony monit weryfikacji dwuetapowej.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Jak wyczyścić Microsoft Authenticator z stare urządzenia i przenieść do nowego?
Odinstaluj aplikację z urządzenia lub zresetowania urządzenia, nie powoduje usunięcia aktywacji na zapleczu. Aby uzyskać więcej informacji, zobacz [Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="next-steps"></a>Kolejne kroki
* Uzyskaj porady dotyczące rozwiązywania problemów i pomoc na temat [występują problemy z weryfikacji dwuetapowej](multi-factor-authentication-end-user-troubleshoot.md)
* Konfigurowanie [haseł aplikacji](multi-factor-authentication-end-user-app-passwords.md) dla wszystkich aplikacji, które nie obsługują weryfikacji dwuetapowej.
