---
title: Zarządzanie ustawieniami weryfikacji dwuetapowej | Dokumentacja firmy Microsoft
description: Zarządzanie, jak używasz usługi Azure Multi-Factor Authentication, włącznie ze zmianami swoje informacje kontaktowe i konfigurowanie urządzenia.
services: multi-factor-authentication
keywords: Klient uwierzytelniania wieloskładnikowego, problem z uwierzytelnianiem, identyfikator korelacji
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 8d84574283aa0c94ce303b0a7e3bde335c0eb2b8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26626661"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Zarządzanie ustawieniami na potrzeby weryfikacji dwuetapowej
W tym artykule odpowiedzi na pytania o tym, jak można zaktualizować ustawień uwierzytelnianie dwuetapowe weryfikacji lub Multi-Factor. Jeśli występują problemy dotyczące logowania do konta, dotyczą [wystąpił problem w trakcie weryfikacji dwuetapowej](multi-factor-authentication-end-user-troubleshoot.md) dla pomocy w rozwiązywaniu problemów.

## <a name="where-to-find-the-settings-page"></a>Gdzie można znaleźć na stronie ustawień
W zależności od tego, jak firma skonfigurowane uwierzytelnianie wieloskładnikowe Azure istnieje kilka miejsc, w którym można zmienić ustawienia, takie jak numer telefonu.

Jeśli firma obsługuje wysyłanie określony adres URL lub kroki, aby zarządzać weryfikację dwuetapową, wykonaj te instrukcje. W przeciwnym razie poniższe instrukcje powinny działać na każdy inny. Jeśli wykonaj następujące kroki, ale nie widzisz tych samych opcji, oznacza to, że konto służbowe dostosowanego własnych portalu. Poproś administratora o jego łącza do portalu usługi Azure Multi-Factor Authentication.

1. Zaloguj się do [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Wybierz nazwę konta w prawym górnym, a następnie wybierz **profilu**.  
3. Wybierz **dodatkowej weryfikacji zabezpieczeń**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Dodatkowe zabezpieczenia weryfikacji ładowania strony z ustawieniami.

    ![Biurowego](./media/multi-factor-authentication-end-user-manage/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Chcesz zmienić mój numer telefonu lub dodać dodatkowej numer
Należy skonfigurować numer telefonu uwierzytelniania dodatkowego.  Ponieważ sieci głównego numeru telefonu i aplikacji mobilnej prawdopodobnie znajdują się w tej samej telefonu, numeru telefonu dodatkowej jest jedynym sposobem można wrócić do konta Jeśli telefon zostanie utracony lub skradziony.

> [!NOTE]
> Jeśli nie mają dostęp do sieci głównego numeru telefonu i potrzebujesz pomocy, uzyskiwanie w do swojego konta, zobacz nasze tematy pomocy w [wystąpił problem w trakcie weryfikacji dwuetapowej](multi-factor-authentication-end-user-troubleshoot.md).  

**Aby zmienić numer telefonu głównej:**  

1. Na stronie weryfikacji dodatkowe zabezpieczenia zaznacz pole tekstowe z bieżący numer telefonu i edytować za pomocą nowy numer telefonu.  
2. Wybierz pozycję **Zapisz**.  
3. Jeśli jest to liczba, dla której korzystasz z preferowaną opcję weryfikacji, należy sprawdzić nowy numer, zanim będzie można go zapisać.  

**Aby dodać numer telefonu dodatkowej:**  

1. Na stronie weryfikacji dodatkowe zabezpieczenia, zaznacz pole wyboru obok pozycji **numer telefonu uwierzytelniania alternatywny.**  
2. Wprowadź numer telefonu pomocniczy w polu tekstowym.  
3. Wybierz **zapisać** i zakończeniu zmiany.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Żądaj weryfikacji dwuetapowej ponownie na urządzeniu, które zostały oznaczone jako zaufany

W zależności od ustawienia organizacji może być stwierdzający, pole wyboru "nie pytaj ponownie o **X** dni" po wykonaniu weryfikacji dwuetapowej w przeglądarce. Jeśli to pole wyboru i utraty urządzenia lub podejrzenie, że Twoje konto jest zagrożone należy przywrócić weryfikacji dwuetapowej dla wszystkich urządzeń.

1. Na stronie weryfikacji dodatkowe zabezpieczenia wybierz **przywracania usługi Multi-Factor authentication na wcześniej zaufanych urządzeniach**.
2. Przy następnym zalogowaniu się na dowolnym urządzeniu zostanie wyświetlony monit do przeprowadzenia weryfikacji dwuetapowej.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Jak wyczyścić Authenticator firmy Microsoft z urządzeniem stary i Przenieś do nowego?
Odinstaluj aplikację z urządzenia lub zresetowania urządzenia, nie powoduje usunięcia aktywacji wewnętrznych. Aby uzyskać więcej informacji, zobacz [Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

## <a name="next-steps"></a>Następne kroki
* Uzyskać porady dotyczące rozwiązywania problemów i pomoc na temat [wystąpił problem w trakcie weryfikacji dwuetapowej](multi-factor-authentication-end-user-troubleshoot.md)
* Konfigurowanie [hasła aplikacji](multi-factor-authentication-end-user-app-passwords.md) dla wszystkich aplikacji, które nie obsługują weryfikacji dwuetapowej.
