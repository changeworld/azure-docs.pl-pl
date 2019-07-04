---
title: 'Azure Active Directory Domain Services: Ustawienia powiadomień | Dokumentacja firmy Microsoft'
description: Ustawienia powiadomień dla usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: a89b13d40f4eea08ecdb0f1eb8d68d1a146aca2b
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472794"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Ustawienia powiadomień w usługach domenowych Azure AD

Powiadomienia dotyczące usług domenowych Azure AD umożliwia zaktualizowania zaraz po wykryciu alert o kondycji w domenie zarządzanej.  

Ta funkcja jest dostępna tylko dla zarządzanych domen, które nie znajdują się w klasycznych sieciach wirtualnych.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Jak sprawdzić ustawienia powiadomień poczty e-mail i usług domenowych Azure AD

1. Przejdź do [strony usług domenowych Azure AD](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w witrynie Azure portal
2. Wybierz domenę zarządzaną z tabeli
3. W obszarze nawigacji po lewej stronie, wybierz **ustawień powiadomień**

Na stronie wyświetla listę wszystkich adresatów wiadomości e-mail dla powiadomień e-mail dla usługi Azure AD Domain Services.

## <a name="what-does-an-email-notification-look-like"></a>Jak wygląda powiadomienie e-mail?

Poniższej ilustracji przedstawiono przykład wiadomość e-mail z powiadomieniem:

![Przykład powiadomienia e-mail](./media/active-directory-domain-services-alerts/email-alert.png)

Adres e-mail Określa domeny zarządzanej, który ten alert jest obecny na, zapewniając czas wykrycia oraz link do strony kondycji usług domenowych Azure AD w witrynie Azure portal.

> [!WARNING]
> Zawsze upewnij się, że wiadomość e-mail pochodzi z zweryfikowanych nadawcy Microsoft przed kliknięciem przycisku łącza w wiadomości e-mail. Wiadomości e-mail zawsze pochodzą z wiadomości e-mail azure-noreply@microsoft.com


## <a name="why-would-i-receive-email-notifications"></a>Dlaczego będzie otrzymywać powiadomień pocztą e-mail?

Azure AD Domain Services wysyła powiadomienia e-mail o ważnych aktualizacji informacje o domenie.  Te powiadomienia są tylko w przypadku pilnych sprawach, będzie miało wpływ na usługi, które powinny być kierowany od razu. Każde powiadomienie e-mail jest wyzwalany przez alert w domenie zarządzanej. Te alerty są również wyświetlane w witrynie Azure portal i mogą być wyświetlane na [strony kondycji usług domenowych Azure AD](check-health.md).

Azure AD Domain Services nie wysyła wiadomości e-mail do tej listy dla anonsu, aktualizacji lub celów sprzedaży.

## <a name="when-will-i-receive-email-notifications"></a>Kiedy będzie otrzymywał powiadomienia e-mail

Natychmiast otrzymasz powiadomienie po [nowy alert](troubleshoot-alerts.md) znajduje się w domenie zarządzanej. Jeśli alert nie zostanie rozwiązany, powiadomienia e-mail będą wysyłane Przypominamy, co cztery dni.

## <a name="who-should-receive-the-email-notifications"></a>Kto powinien otrzymywać powiadomienia e-mail?


 Zalecane jest lista adresatów poczty e-mail na składa się z osób, które mogą administrować i wprowadzić zmiany do domeny zarządzanej usług domenowych Azure AD. Ta lista wiadomości e-mail powinny być uważane za swoje "pierwszy obiektów odpowiadających" na wykryte problemy. Jeśli masz więcej niż pięć dodatkowych wiadomości e-mail, które chcesz dodać, zaleca się utworzenie listy dystrybucyjnej, zamiast tego dodać do listy powiadomień.

Jesteś w stanie dodać maksymalnie pięć dodatkowych wiadomości e-mail dla powiadomień dotyczących usług domenowych Azure AD. Ponadto możesz również mają wszyscy administratorzy globalni w katalogu, a każdy członek grupy "Administratorzy usługi AAD DC" otrzymywać powiadomień pocztą e-mail w usługach domenowych Azure AD. Azure AD Domain Services wysyła powiadomienia do maksymalnie 100 adresów e-mail, łącznie z listą Administratorzy globalni i Administratorzy usługi AAD DC.


## <a name="how-to-add-an-additional-email-recipient"></a>Jak dodać odbiorcy wiadomości e-mail dodatkowe

> [!WARNING]
> Po zmianie ustawień powiadomień, zmieniasz ustawienia powiadomień dla całej domeny zarządzanej, nie tylko siebie.

1. Przejdź do [strony usług domenowych Azure AD](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w witrynie Azure portal.
2. Kliknij domenę zarządzaną.
3. W okienku nawigacji po lewej stronie, kliknij przycisk **ustawień powiadomień**.
4. Aby dodać wiadomość e-mail, wpisz adres e-mail w tabeli dodatkowych adresatów.
5. W okienku nawigacji górnej strony, kliknij przycisk "Zapisz".

## <a name="frequently-asked-questions"></a>Często zadawane pytania

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Otrzymuję wiadomość e-mail z powiadomieniem o alercie, ale gdy jest zalogowany do witryny Azure portal nie wystąpił żaden alert. Co się stało?

Jeśli alert został rozwiązany, alert nie będzie wyświetlany w witrynie Azure portal. Najbardziej prawdopodobnym powodem jest czy ktoś inny, która odbiera powiadomienia e-mail rozwiązać alert w domenie zarządzanej lub był automatycznie rozwiązany przez usług domenowych Azure AD.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Dlaczego nie można edytować ustawienia powiadomień?

Jeśli nie możesz uzyskać dostęp do strony ustawienia powiadomień w witrynie Azure portal, nie masz uprawnień do edytowania usług domenowych Azure AD. Należy skontaktować się z administratorem globalnym, aby uzyskać uprawnienia do edytowania zasobów usługi Azure AD Domain Services lub usunięte z listy adresatów.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Nie mogę otrzymywać powiadomienia e-mail, mimo że można podać Mój adres e-mail. Dlaczego?

Sprawdź folder spamu lub śmieci w wiadomości e-mail powiadomienia i upewnij się, że lista dozwolonych nadawcy (azure-noreply@microsoft.com).

## <a name="next-steps"></a>Kolejne kroki
- [Rozwiąż alerty w domenie zarządzanej](troubleshoot-alerts.md)
- [Więcej informacji na temat usługi Azure AD Domain Services](overview.md)
- [Skontaktuj się z zespołem produktu](contact-us.md)

## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](contact-us.md).
