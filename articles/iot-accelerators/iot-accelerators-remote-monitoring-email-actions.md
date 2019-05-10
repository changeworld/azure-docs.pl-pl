---
title: Wiadomość e-mail akcji w obrębie zdalne monitorowanie — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak dodawanie akcji poczty e-mail do nowej lub istniejącej reguły.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: fbb5f92258ff31dd7077bb1ade7fa7e5644c8bac
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466955"
---
# <a name="add-an-email-action"></a>Dodawanie akcji poczty e-mail

Adres e-mail akcje upewnić się, nie przegapisz alertów. Akcji poczty e-mail można dodać do istniejącej reguły lub tworząc nową regułę.

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, należy wdrożone wystąpienie akcelerator rozwiązań zdalnego monitorowania w ramach subskrypcji platformy Azure.

Aby utworzyć lub zmodyfikować reguły, użytkownik musi być [ **administratora**, lub mieć odpowiednie uprawnienia](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Edytowanie istniejącej reguły

Wykonaj następujące kroki, aby dodać akcji w wiadomości e-mail do istniejącej reguły:

1. Przejdź do rozwiązania do zdalnego monitorowania.

1. Z **pulpit nawigacyjny**, przejdź do **reguły** strony:

    ![Strona reguł](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kliknij pole wyboru obok istniejącej reguły do zmodyfikowania, a następnie kliknij przycisk **Edytuj** u góry. Można edytować **reguły** zostanie wyświetlony panel.

1. W **akcji** sekcji, Przełącz **włączoną pocztą E-mail** do **na**.

1. Włącz akcji poczty e-mail w akcelerator rozwiązań po raz pierwszy, należy najpierw [Zaloguj się do programu Outlook](#outlook).

1. Wprowadź adres e-mail do adresatów pole i naciśnij klawisz **Enter** klucza dla każdego z adresów e-mail do dodania:

    ![Wpis adresu](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Wprowadź temat wiadomości e-mail.

1. Wprowadź wszelkie uwagi, dodatkowych adresatów wiadomości e-mail jako zwykły tekst. Możesz użyć HTML formatowanie, jeśli użytkownik [edytować szablon wiadomości e-mail](#htmledit).

1. Upewnij się, że **stan reguły** ustawiono **włączone**.

1. Kliknij przycisk **Zastosuj**.

## <a name="create-a-new-rule"></a>Tworzenie nowej reguły

Wykonaj następujące kroki, aby dodać akcji poczty e-mail, podczas tworzenia nowej reguły:

1. Przejdź do rozwiązania do zdalnego monitorowania.

1. Z **pulpit nawigacyjny**, przejdź do **reguły** strony:

    ![Strona reguł](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Postępuj zgodnie z instrukcjami w [Utwórz sekcję reguły](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Wykonaj kroki opisane w [utworzyć zaawansowaną regułę](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) sekcji aż do momentu, w którym można ustawić **poziom ważności**. Nie klikaj pozycji **Zastosuj** jeszcze.

1. W **akcji** sekcji, Przełącz **włączoną pocztą E-mail** do **na**.

1. Włącz akcji poczty e-mail w akcelerator rozwiązań po raz pierwszy, należy najpierw [Zaloguj się do programu Outlook](#outlook).

1. Wprowadź adres e-mail do adresatów pole i naciśnij klawisz **Enter** klucza dla każdego z adresów e-mail do dodania:

    ![Wpis adresu](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Wprowadź temat wiadomości e-mail.

1. Wprowadź wszelkie uwagi, dodatkowych adresatów wiadomości e-mail jako zwykły tekst. Możesz użyć HTML formatowanie, jeśli użytkownik [edytować szablon wiadomości e-mail](#htmledit).

1. Upewnij się, że **stan reguły** ustawiono **włączone**.

1. Kliknij przycisk **Zastosuj**.

Reguły za pomocą akcji poczty e-mail jest teraz włączony. Każdorazowo, gdy akcja zostanie wywołany, nowy adres e-mail jest wysyłana do adresatów.

## Zaloguj się do programu Outlook <a name="outlook"></a>

Włącz akcji poczty e-mail w akceleratorze rozwiązań po raz pierwszy użytkownik musi zalogować się do programu Outlook. Ta akcja konfiguruje konta e-mail, które wysyła powiadomienia e-mail.

> [!NOTE]
> Należy tworzenia konkretnego konta programu Outlook, po prostu dla powiadomień akcelerator rozwiązań i użyć tego konta, po włączeniu pierwszej akcji w wiadomości e-mail.

### <a name="contributor-role-outlook-setup"></a>Instalator programu Outlook roli współautora

Jeśli ktoś w **Współautor** roli w subskrypcji wdrożyć akcelerator rozwiązań, aplikacja nie ma wystarczających uprawnień do konfigurowania i weryfikowania akcji poczty e-mail przy użyciu interfejsu użytkownika sieci Web.

Przed rozpoczęciem należy utworzyć konto programu Outlook służące do wysyłania powiadomień e-mail z Twój akcelerator rozwiązań.

Poniższe kroki pokazują sposób konfigurowania i weryfikowania ręcznie akcji poczty e-mail:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do grupy zasobów dla Twój akcelerator rozwiązań.

1. Kliknij przycisk **łącznik usługi Office 365**:

    ![Połączenie interfejsu API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Kliknij transparent, aby rozpocząć proces autoryzacji:

    ![Autoryzuj](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Kliknij przycisk **autoryzować**. Zostanie wyświetlony monit Zaloguj się. Konto, którego używasz do logowania powinien być adres e-mail aplikacja używa do wysyłania powiadomień e-mail:

    ![Autoryzuj przycisku](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Kliknij przycisk **Zapisz** u dołu. Twojej autoryzacji, zakończy się pomyślnie, jeśli transparent został usunięty.

1. Aby zmienić adres e-mail, z którego wysyłane są powiadomienia z, kliknij przycisk **połączenia Edytuj interfejsu API**.

    ![Zmień adres e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Instalator programu Outlook roli właściciela

Jeśli ktoś w **właściciela** roli w subskrypcji wdrożyć akcelerator rozwiązań, aplikacji można sprawdzić w akcji poczty e-mail zostały poprawnie skonfigurowane przy użyciu interfejsu użytkownika sieci Web.

Przed rozpoczęciem należy utworzyć konto programu Outlook służące do wysyłania powiadomień e-mail z Twój akcelerator rozwiązań.

Poniższe kroki ułatwiają Zaloguj się i Ustawianie akcji poczty e-mail:

1. Kliknij, aby zalogować się do programu Outlook. Nastąpi przekierowanie do witryny Azure portal:

   ![Zaloguj się do programu Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Kliknij przycisk **autoryzować**. Zostanie wyświetlony monit Zaloguj się. Konto, którego używasz do logowania powinien być adres e-mail aplikacja używa do wysyłania powiadomień e-mail:

1. Kliknij pozycję **Zapisz**. Wróć do Twój akcelerator rozwiązań, a następnie odśwież stronę.

1. Jeśli zostały pomyślnie skonfigurowane powiadomienia e-mail, zostanie wyświetlony ten komunikat:

   ![Pomyślne Outlook logowanie](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Dostosuj wiadomości e-mail HTML <a name="htmledit"></a>

Out-of--box, zdalne monitorowanie akcelerator rozwiązań udostępnia podstawowy szablon HTML dla akcji w wiadomości e-mail. Szablon wiadomości e-mail są używane wartości z ustawień akcji poczty e-mail. Poniżej przedstawiono przykładową wiadomość e-mail:

![przykład wiadomości e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Poniższe kroki pokazują, jak edytować szablon wiadomości e-mail w formacie HTML. Na przykład można dołączyć więcej informacji, lub dodać niestandardowych obrazów:

1. Sklonuj repozytorium platformy Java lub .NET zdalnego monitorowania GitHub:

1. Przejdź do lokalizacji szablonu wiadomości e-mail:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Można dodać lub usunąć wszelkie parametry tego szablonu, aby dostosować komunikat o. Można również dodać, usunąć lub zamienić wywołania, zgodnie z potrzebami:

    Na przykład w kodzie .NET:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Na przykład w kodzie języka Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametry w szablonie formę `${...}`. Aby usunąć parametr, należy usunąć żądanego wiersza. Aby dodać parametr, Dodaj wiersz o wartości do wstawienia.

1. Aby dodać obrazy lub niestandardowego tekstu, należy zaktualizować plik EmailTemplate.HTML bezpośrednio.

## <a name="throttling"></a>Ograniczanie przepływności

Akcelerator rozwiązań zdalnego monitorowania korzysta z programu Outlook do wysyłania powiadomień e-mail. Program Outlook ogranicza liczbę wiadomości e-mail wysłanych na [30 wiadomości e-mail na 1 minutę](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Klientów poczty e-mail, otrzymywania wiadomości e-mail może również ograniczać liczbę wiadomości e-mail odebrane na minutę. Skontaktuj się z klienta określonej wiadomości e-mail na ograniczenia. Podczas konfigurowania powiadomień e-mail dla reguły reguły należy obliczyć średnie wartości w okresie co najmniej jedną minutę, a następnie używa wartości błyskawicznych:

![Obliczania średniej](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Kolejne kroki

W przewodniku pokazano, jak dodawanie akcji poczty e-mail do nowej lub istniejącej reguły w ramach rozwiązania monitorowania zdalnego. Przewodnik po pokazano też, możesz i sposobu edytowania kodu HTML, który definiuje format komunikatu.

Sugerowane następnym krokiem jest, aby dowiedzieć się więcej [sposób używania alertów i rozwiązywanie problemów z urządzeniami](iot-accelerators-remote-monitoring-maintain.md).
