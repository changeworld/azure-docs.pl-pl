---
title: Jak używać haseł aplikacji w usłudze Azure MFA? | Microsoft Docs
description: Ta strona pomoże użytkownikom w zrozumieniu, co to są hasła aplikacji i co to są używane dla z uwzględnieniem usługi Azure MFA.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: 290458e95aaed0cc85d83539d9d870c334df45df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059431"
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Co to są hasła aplikacji w usłudze Azure Multi-Factor Authentication?
Niektórych aplikacji nie korzystających z przeglądarki, takich jak klienta natywnego poczty e-mail firmy Apple, który używa programu Exchange Active Sync, obecnie nie obsługują uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe jest włączone dla poszczególnych użytkowników. Oznacza to, że jeśli użytkownik został włączony dla usługi Multi-Factor authentication, a następnie próbuje użyć aplikacji niekorzystających z przeglądarki, nie będą w tym celu. Hasła aplikacji umożliwia to możliwe. Jeśli wymuszanie uwierzytelniania wieloskładnikowego za pomocą zasad dostępu warunkowego, a nie za pomocą usługi MFA na użytkownika, nie można utworzyć hasła aplikacji. Aplikacje używające zasad dostępu warunkowego do kontrolowania dostępu do hasła aplikacji nie jest konieczne.

Po utworzeniu haseł aplikacji, możesz użyć tego zamiast oryginalnemu hasłu z tych aplikacji nie korzystających z przeglądarki. Jest to spowodowane podczas rejestrowania weryfikacji dwuetapowej jest informacją, firma Microsoft nie pozwolimy każdej osobie, zaloguj się przy użyciu hasła, jeśli nie mogą również wykonywać drugą weryfikację. Apple natywnego klienta poczty e-mail na Twój telefon nie można zalogować się jako użytkownik, ponieważ nie można żądać weryfikacji dwuetapowej. Rozwiązanie tego problemu jest do utworzenia bardziej bezpiecznym hasłem aplikacji, które nie są używane w codziennych, ale tylko dla tych aplikacji, które nie obsługują weryfikacji dwuetapowej. Użyj hasła aplikacji, dzięki czemu aplikacje mogą obejście usługi Multi-Factor authentication i kontynuować pracę.

> [!NOTE]
> Klienci pakietu Office 2013 (w tym programu Outlook) obsługuje nowe protokoły uwierzytelniania i może być używany z weryfikacji dwuetapowej.  Oznacza to, że po włączeniu hasła aplikacji nie są wymagane do użycia z klientów Office 2013.  Aby uzyskać więcej informacji, zobacz [pakietu Office 2013 publicznej wersji nowoczesnego uwierzytelniania ogłosiła](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Jak używać haseł aplikacji
Poniżej przedstawiono niektóre kwestie, pamiętaj o tym, jak do stosowania haseł aplikacji.

* Nie twórz haseł aplikacji. Zamiast tego są one generowane automatycznie. Ponieważ trzeba wprowadzić hasło aplikacji, raz w aplikacji, bezpieczniej jest używać bardziej złożonych, automatycznie wygenerowane hasło, a nie jako taki, który można zapamiętać.
* Obecnie istnieje limit 40 haseł na użytkownika. Użytkownik spróbuje utworzyć po użytkownik osiągnął limit, zostanie wyświetlony monit usunąć jeden z istniejących haseł aplikacji, przed utworzeniem nowej.
* Należy użyć jednego hasła aplikacji na każdym urządzeniu, a nie na aplikację. Na przykład można utworzyć hasło aplikacji dla komputera przenośnego i użyć tego hasła aplikacji dla wszystkich aplikacji na tym komputerze. Następnie utwórz drugi hasło aplikacji dla wszystkich aplikacji na pulpicie.
* Są podane hasło aplikacji po raz pierwszy należy zarejestrować weryfikacji dwuetapowej.  Jeśli potrzebujesz także dodatkowe, można je utworzyć.



## <a name="creating-and-deleting-app-passwords"></a>Tworzenie i usuwanie hasła aplikacji
Podczas początkowego logowania są podane hasło aplikacji, którego można używać.  Ponadto można również tworzyć i później usunąć hasła aplikacji.  Jak to zrobić, zależy od tego, jak używać uwierzytelniania wieloskładnikowego. Należy odpowiedzieć na następujące pytania, aby określić, gdzie należy przejść do zarządzania hasłami aplikacji:

1. Czy używasz weryfikację dwuetapową dla Twojego osobistego konta Microsoft? Jeśli tak, należy zapoznać się [haseł aplikacji i weryfikacji dwuetapowej](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) artykuł, aby uzyskać pomoc. Jeśli nie, nadal dwa pytania.

2. OK, aby za pomocą weryfikacji dwuetapowej dla swojego konta firmowego lub szkolnego. Należy go używać do logowania do aplikacji usługi Office 365? Jeśli tak, należy zapoznać się [utworzyć hasło aplikacji dla usługi Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) Aby uzyskać pomoc. Jeśli nie, nadal pytanie 3.

3. Weryfikacja dwuetapowa są używane z platformą Microsoft Azure? Jeśli tak, w dalszym ciągu [Zarządzanie hasłami aplikacji w witrynie Azure portal](#manage-app-passwords-in-the-Azure-portal) dalszej części tego artykułu. Jeśli nie, nadal cztery pytania.

4. Nie masz pewności, w przypadku, gdy używasz weryfikację dwuetapową? W dalszym ciągu [Zarządzanie hasłami aplikacji za pomocą portalu MyApps](#manage-app-passwords-with-the-myapps-portal) dalszej części tego artykułu.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Zarządzanie hasłami aplikacji w witrynie Azure portal
Jeśli używasz weryfikacji dwuetapowej za pomocą platformy Azure, którą chcesz utworzyć hasła aplikacji w witrynie Azure portal.



## <a name="manage-app-passwords-with-the-myapps-portal"></a>Zarządzanie hasłami aplikacji za pomocą portalu MyApps.
Jeśli nie masz pewności, jak używać uwierzytelniania wieloskładnikowego, następnie zawsze możesz utworzyć i usunąć hasła aplikacji za pośrednictwem portalu myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Aby utworzyć hasła aplikacji przy użyciu portalu MyApps
1. Zaloguj się do [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Kliknij swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz **profilu**.
3. Wybierz **dodatkowej weryfikacji zabezpieczeń**.
   ![Wybierz opcję dodatkowa weryfikacja zabezpieczeń — zrzut ekranu](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Wybierz **haseł aplikacji**.
   ![Wybierz aplikację haseł — zrzut ekranu](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Kliknij przycisk **Utwórz**.
6. Wprowadź nazwę hasła aplikacji, a następnie kliknij przycisk **dalej**.
7. Kopiuj hasło aplikacji do Schowka i wklej go w swojej aplikacji.
   ![Utwórz hasło aplikacji](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Aby usunąć hasła aplikacji przy użyciu portalu MyApps
1. Zaloguj się do [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. U góry strony wybierz profil.
3. Wybierz **dodatkowej weryfikacji zabezpieczeń**.

   ![Wybierz opcję dodatkowa weryfikacja zabezpieczeń — zrzut ekranu](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Wybierz **haseł aplikacji**.

   ![Wybierz aplikację haseł — zrzut ekranu](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Obok hasło aplikacji do usunięcia, kliknij **Usuń**.

   ![Usuwanie hasła aplikacji](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Upewnij się, że chcesz usunąć to hasło, klikając **tak**.
7. Po usunięciu hasła aplikacji, możesz kliknąć **Zamknij**.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)

- Wypróbuj [aplikacji Microsoft Authenticator](microsoft-authenticator-app-how-to.md) Aby zweryfikować swoje logowania za pomocą aplikacji powiadomień, nie odbiera teksty lub wywołania.
