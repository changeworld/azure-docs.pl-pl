---
title: 'Azure Active Directory Domain Services: Włączanie synchronizacji skrótów haseł | Dokumentacja firmy Microsoft'
description: Wprowadzenie do usługi Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 35add0737d90d7f550fa6d907718c3c193dd60c4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474227"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Włączanie synchronizacji skrótów haseł w usługach Azure Active Directory Domain Services
W poprzednich zadaniach włączono usługi Azure Active Directory Domain Services dla dzierżawy usługi Azure Active Directory (Azure AD). Kolejnym krokiem jest włączenie synchronizacji skrótów haseł wymaganych do uwierzytelniania NT LAN Manager (NTLM) i Kerberos w usługach Azure AD Domain Services. Po skonfigurowaniu synchronizacji skrótów haseł użytkownik może zalogować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

Wykonywane czynności są różne dla kont użytkowników tylko w chmurze i kont użytkowników synchronizowanych z katalogu lokalnego przy użyciu programu Azure AD Connect. 

<br>

| **Typ konta użytkownika** | **Czynności do wykonania** |
| --- |---|
| **Konta użytkowników chmury utworzone w usłudze Azure AD** |**&#x2713;** [Postępuj zgodnie z instrukcjami w tym artykule](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Konta użytkownika synchronizowane z poziomu katalogu lokalnego** |**&#x2713;** [Zsynchronizuj skróty haseł dla kont użytkowników synchronizowanych z lokalnej usługi AD do domeny zarządzanej](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Może być konieczne wykonanie obu zestawów czynności.**
> Jeśli dzierżawa usługi Azure AD ma kombinację użytkowników tylko w chmurze i użytkowników z lokalnej usługi AD, należy ukończyć oba zestawy czynności.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Zadanie 5. Włączanie synchronizacji skrótów haseł do domeny zarządzanej dla kont użytkowników tylko w chmurze
Usługi Azure Active Directory Domain Services wymagają skrótów haseł w formacie odpowiednim do uwierzytelniania NTLM i Kerberos, aby uwierzytelnić użytkowników w domenie zarządzanej. Usługa Azure AD nie będzie generować ani przechowywać skrótów haseł w formacie wymaganym podczas uwierzytelniania NTLM i Kerberos, jeśli nie zostaną włączone usługi Azure Active Directory Domain Services dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD nie przechowuje również żadnych poświadczeń haseł w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie ma możliwości automatycznego generowania skrótów haseł protokołu NTLM ani Kerberos w oparciu o istniejące poświadczenia użytkownika.

> [!NOTE]
> **Jeśli organizacja ma konta użytkowników tylko w chmurze, wszyscy użytkownicy, którzy muszą korzystać z usług Azure Active Directory Domain Services, muszą zmienić swoje hasła.** Konto użytkownika tylko w chmurze to konto, które zostało utworzone w katalogu usługi Azure AD przy użyciu witryny Azure Portal lub poleceń cmdlet programu Azure AD PowerShell. Takie konta użytkownika nie są synchronizowane z poziomu katalogu lokalnego.
>
>

Ten proces zmiany haseł powoduje generowanie w usłudze Azure AD skrótów haseł wymaganych przez usługi Azure Active Directory Domain Services na potrzeby uwierzytelniania Kerberos i NTLM. Możesz unieważnić hasła wszystkich użytkowników w dzierżawie, którzy muszą korzystać z usług Azure Active Directory Domain Services, lub poinformować ich o konieczności zmiany haseł.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Włączanie generowania skrótów haseł NTLM i Kerberos dla kont użytkowników tylko w chmurze
Poniżej znajdują się instrukcje zmieniania haseł dla użytkowników:

1. Przejdź do strony [panelu dostępu do usługi Azure AD](https://myapps.microsoft.com) dla organizacji.

    ![Uruchamianie panelu dostępu usługi Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. W prawym górnym rogu kliknij swoją nazwę i wybierz pozycję **Profil** z menu.

    ![Wybieranie profilu](./media/active-directory-domain-services-getting-started/select-profile.png)

3. Na stronie **Profil** kliknij pozycję **Zmień hasło**.

    ![Kliknięcie pozycji „Zmień hasło”](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Jeśli opcja **Zmień hasło** nie jest wyświetlana w oknie panelu dostępu, upewnij się, że Twoja organizacja ma skonfigurowane [zarządzanie hasłami w usłudze Azure AD](../active-directory/authentication/quickstart-sspr.md).
   >
   >
4. Na stronie **Zmiana hasła** wpisz istniejące hasło (stare), a następnie wpisz nowe hasło i je zatwierdź.

    ![Użytkownik zmienia hasło](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Kliknij przycisk **prześlij**.

Nowego hasła w usługach Azure Active Directory Domain Services można używać już kilka minut po jego zmianie. Po około 20 minutach użytkownicy będą mogli logować się na komputerach dołączonych do domeny zarządzanej przy użyciu nowo zmienionego hasła.

## <a name="related-content"></a>Powiązana zawartość
* [Jak zaktualizować własne hasło](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
* [Wprowadzenie do zarządzania hasłami w usłudze Azure AD](../active-directory/authentication/quickstart-sspr.md)
* [Włączanie synchronizacji skrótów haseł w usługach Azure Active Directory Domain Services dla zsynchronizowanej dzierżawy usługi Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Zarządzaj domeną usługi Azure AD Domain Services](manage-domain.md)
* [Dołączanie maszyny wirtualnej z systemem Windows do domeny zarządzanej usług Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Dołączanie maszyny wirtualnej z systemem Red Hat Enterprise Linux do domeny zarządzanej usług Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
