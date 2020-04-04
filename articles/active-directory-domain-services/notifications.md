---
title: Powiadomienia e-mail dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować powiadomienia e-mail w celu ostrzegania o problemach w domenie zarządzanej usług domenowych Usługi domenowe Active Directory platformy Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8c6d59889e572893877f2178cade57e07aa91413
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654786"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Konfigurowanie powiadomień e-mail w przypadku problemów z usługami domenowymi Active Directory platformy Azure

Kondycja domeny zarządzanej usług domenowych usługi Active Directory platformy Azure (Usługi Usługi Azure AD DS) jest monitorowana przez platformę Platformy Azure. Strona stanu kondycji w witrynie Azure Portal zawiera wszystkie alerty dla domeny zarządzanej. Aby upewnić się, że problemy są odpowiedzi na w odpowiednim czasie, powiadomienia e-mail można skonfigurować do raportowania alertów kondycji, jak tylko zostaną wykryte w domenie zarządzanej usług Azure AD DS.

W tym artykule pokazano, jak skonfigurować adresatów powiadomień e-mail dla domeny zarządzanej usług Azure AD DS.

## <a name="email-notification-overview"></a>Omówienie powiadomień e-mail

Aby ostrzegać o problemach z domeną zarządzaną usługą Azure AD DS, możesz skonfigurować powiadomienia e-mail. Te powiadomienia e-mail określają domenę zarządzaną usług Azure AD DS, w której alert jest obecny, a także podaje czas wykrywania i łącze do strony kondycji w witrynie Azure portal. Następnie można wykonać podane porady dotyczące rozwiązywania problemów, aby rozwiązać problemy.

Poniższe przykładowe powiadomienie e-mail wskazuje, że ostrzeżenie krytyczne lub alert został wygenerowany w domenie zarządzanej usługi Azure AD DS:

![Przykładowe powiadomienie e-mail](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Przed kliknięciem łącza w wiadomości upewnij się, że wiadomość e-mail pochodzi od zweryfikowanego nadawcy firmy Microsoft. Powiadomienia e-mail zawsze `azure-noreply@microsoft.com` pochodzą z adresu.

### <a name="why-would-i-receive-email-notifications"></a>Dlaczego miałbym otrzymywać powiadomienia e-mail?

Usługi Azure AD DS wysyła powiadomienia e-mail dla ważnych aktualizacji dotyczących domeny zarządzanej. Powiadomienia te dotyczą tylko pilnych problemów, które mają wpływ na usługę i powinny zostać natychmiast rozwiązane. Każde powiadomienie e-mail jest wyzwalane przez alert w domenie zarządzanej usług Azure AD DS. Alerty są również wyświetlane w witrynie Azure portal i można je wyświetlać na [stronie kondycji usług Azure AD DS][check-health].

Usługi Azure AD DS nie wysyłają wiadomości e-mail w celach reklamowych, aktualizacji ani sprzedaży.

### <a name="when-will-i-receive-email-notifications"></a>Kiedy otrzymam powiadomienia e-mail?

Powiadomienie jest wysyłane natychmiast po znalezieniu [nowego alertu][troubleshoot-alerts] w domenie zarządzanej usług Azure AD DS. Jeśli alert nie zostanie rozwiązany, dodatkowe powiadomienia e-mail są wysyłane jako przypomnienie co cztery dni.

### <a name="who-should-receive-the-email-notifications"></a>Kto powinien otrzymywać powiadomienia e-mail?

Lista adresatów wiadomości e-mail dla usług Azure AD DS powinna składać się z osób, które mogą administrować domeną zarządzaowaną i wprowadzać zmiany. Ta lista e-mail powinna być uważana za "pierwszą odpowiedź" na wszelkie alerty i problemy.

Możesz dodać maksymalnie pięciu dodatkowych adresatów wiadomości e-mail do powiadomień e-mail. Jeśli chcesz więcej niż pięciu adresatów powiadomień e-mail, utwórz listę dystrybucyjną i dodaj ją do listy powiadomień.

Można również wybrać, aby wszyscy *administratorzy globalni* katalogu usługi Azure AD i każdy członek grupy *Administratorzy kontrolera domeny usługi AAD* otrzymywali powiadomienia e-mail. Usługi Azure AD DS wysyła tylko powiadomienia do maksymalnie 100 adresów e-mail, w tym listy administratorów globalnych i administratorów kontrolera domeny usługi AAD.

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby przejrzeć istniejących adresatów powiadomień e-mail lub dodać dodatkowych adresatów, wykonaj następujące czynności:

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Usługi domenowe usługi Azure AD**.
1. Wybierz domenę zarządzana usługą Azure AD DS, taką jak *aaddscontoso.com*.
1. Po lewej stronie okna zasobu usług Azure AD DS wybierz pozycję **Ustawienia powiadomień**. Wyświetlane są istniejące adresaci powiadomień e-mail.
1. Aby dodać adresata wiadomości e-mail, wprowadź go w tabeli dodatkowych adresatów.
1. Po zakończeniu wybierz **pozycję Zapisz** w nawigacji górnej.

> [!WARNING]
> Po zmianie ustawień powiadomień ustawienia powiadomień dla całej domeny zarządzanej usług Azure AD DS są aktualizowane, a nie tylko siebie.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Otrzymałem powiadomienie e-mail o alertie, ale gdy zalogowałem się do witryny Azure portal, nie było żadnego alertu. Co się stało?

Jeśli alert zostanie rozwiązany, alert zostanie wyczyszczony z witryny Azure portal. Najbardziej prawdopodobną przyczyną jest to, że ktoś inny, kto odbiera powiadomienia e-mail rozwiązał alert w domenie zarządzanej usług Azure AD DS lub został automatycznie rozwiązany przez platformę Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Dlaczego nie mogę edytować ustawień powiadomień?

Jeśli nie możesz uzyskać dostępu do strony ustawień powiadomień w witrynie Azure Portal, nie masz uprawnień do edytowania domeny zarządzanej usług Azure AD DS. Musisz skontaktować się z administratorem globalnym, aby uzyskać uprawnienia do edytowania zasobu usług Azure AD DS lub zostać usunięty z listy adresatów.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Wydaje się, że nie otrzymuję powiadomień e-mail, mimo że podałem swój adres e-mail. Dlaczego?

Sprawdź folder spamu lub wiadomości-śmieci w wiadomości e-mail, `azure-noreply@microsoft.com`aby uzyskać powiadomienie i upewnij się, że zezwolił nadawcy .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania niektórych problemów, które mogą być zgłaszane, zobacz [Rozwiązywanie alertów w domenie zarządzanej usług Azure AD DS][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
