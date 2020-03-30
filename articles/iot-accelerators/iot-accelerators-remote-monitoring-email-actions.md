---
title: Akcja poczty e-mail w ramach zdalnego monitorowania — platforma Azure | Dokumenty firmy Microsoft
description: W tym przewodniku pokazano, jak dodać akcję e-mail do nowej lub istniejącej reguły.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168365"
---
# <a name="add-an-email-action"></a>Dodawanie akcji e-mail

Akcje e-mailowe pomagają upewnić się, że nigdy nie przegapisz alertów. Akcję wiadomości e-mail można dodać do istniejącej reguły lub podczas tworzenia nowej reguły.

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz wdrożonego wystąpienia akceleratora rozwiązań do zdalnego monitorowania w ramach subskrypcji platformy Azure.

Aby utworzyć lub zmodyfikować regułę, użytkownik musi być [ **administratorem**lub mieć odpowiednie uprawnienia.](iot-accelerators-remote-monitoring-rbac.md)

## <a name="edit-an-existing-rule"></a>Edytowanie istniejącej reguły

Wykonaj następujące kroki, aby dodać akcję wiadomości e-mail do istniejącej reguły:

1. Przejdź do rozwiązania do zdalnego monitorowania.

1. Na **pulpicie nawigacyjnym**przejdź do strony **Reguły:**

    ![Strona Reguły](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kliknij pole wyboru obok istniejącej reguły, aby zmodyfikować, a następnie kliknij pozycję **Edytuj** u góry. Pojawi się edytowalny panel **Reguła.**

1. W sekcji **Akcja** przełącz **włączono funkcję Poczta e-mail** **na Włączone**.

1. Przy pierwszym włączeniu akcji poczty e-mail w akceleratorze rozwiązania należy [zalogować się do programu Outlook](#outlook).

1. Wprowadź adres e-mail w polu adresata i naciśnij klawisz **Enter** dla każdego adresu e-mail, aby dodać:

    ![Wpis adresu](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Wprowadź temat wiadomości e-mail.

1. Wprowadź dodatkowe uwagi dla adresatów wiadomości e-mail jako zwykły tekst. Formatowania HTML można używać podczas [edytowania szablonu wiadomości e-mail](#htmledit).

1. Upewnij się, że **stan reguły** jest ustawiony na **Włączone**.

1. Kliknij przycisk **Zastosuj**.

## <a name="create-a-new-rule"></a>Tworzenie nowej reguły

Wykonaj następujące kroki, aby dodać akcję wiadomości e-mail podczas tworzenia nowej reguły:

1. Przejdź do rozwiązania do zdalnego monitorowania.

1. Na **pulpicie nawigacyjnym**przejdź do strony **Reguły:**

    ![Strona Reguły](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Wykonaj kroki opisane w [sekcji Tworzenie reguły](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Wykonaj kroki opisane w sekcji [utwórz regułę zaawansowaną](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) do punktu, w którym ustawiono **poziom ważności**. Nie klikaj jeszcze **przycisku Zastosuj.**

1. W sekcji **Akcja** przełącz **włączono funkcję Poczta e-mail** **na Włączone**.

1. Przy pierwszym włączeniu akcji poczty e-mail w akceleratorze rozwiązania należy [zalogować się do programu Outlook](#outlook).

1. Wprowadź adres e-mail w polu adresata i naciśnij klawisz **Enter** dla każdego adresu e-mail, aby dodać:

    ![Wpis adresu](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Wprowadź temat wiadomości e-mail.

1. Wprowadź dodatkowe uwagi dla adresatów wiadomości e-mail jako zwykły tekst. Formatowania HTML można używać podczas [edytowania szablonu wiadomości e-mail](#htmledit).

1. Upewnij się, że **stan reguły** jest ustawiony na **Włączone**.

1. Kliknij przycisk **Zastosuj**.

Reguła z akcją e-mail jest teraz włączona. Za każdym razem, gdy akcja jest wyzwalana, do adresatów jest wysyłana nowa wiadomość e-mail.

## <a name="sign-in-to-outlook"></a>Logowanie się do programu Outlook<a name="outlook"></a>

Przy pierwszym włączeniu akcji poczty e-mail w akceleratorze rozwiązań należy zalogować się do programu Outlook. Ta akcja konfiguruje konto e-mail, które wysyła powiadomienia e-mail.

> [!NOTE]
> Należy utworzyć określone konto programu Outlook tylko dla powiadomień akceleratora rozwiązań i używać tego konta po włączeniu pierwszej akcji poczty e-mail.

### <a name="contributor-role-outlook-setup"></a>Konfiguracja roli współautora programu Outlook

Jeśli ktoś w roli **współautora** w subskrypcji wdrożył akcelerator rozwiązań, aplikacja nie ma wystarczających uprawnień do konfigurowania i weryfikowania akcji poczty e-mail za pośrednictwem interfejsu użytkownika sieci Web.

Przed rozpoczęciem należy utworzyć konto programu Outlook, które będzie używane do wysyłania powiadomień e-mail z akceleratora rozwiązań.

Poniższe kroki pokazują, jak ręcznie skonfigurować i zweryfikować akcje poczty e-mail:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

1. Przejdź do grupy zasobów dla akceleratora rozwiązań.

1. Kliknij **łącznik office365:**

    ![Połączenie interfejsu API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Kliknij baner, aby rozpocząć proces autoryzacji:

    ![autoryzacja](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Kliknij pozycję **Autoryzuj**. Zostanie wyświetlony monit o zalogowanie się. Konto używane do logowania powinno być adresem e-mail używanym przez aplikację do wysyłania powiadomień e-mail:

    ![Przycisk Autoryzuj](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Kliknij **pozycję Zapisz** u dołu. Autoryzacja zostanie udzielona, jeśli baner zniknie.

1. Aby zmienić adres e-mail, z którego są wysyłane powiadomienia, kliknij przycisk **Edytuj połączenie interfejsu API**.

    ![zmienianie adresu e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Konfiguracja roli właściciela programu Outlook

Jeśli ktoś w roli **właściciela** w subskrypcji wdrożył akcelerator rozwiązań, aplikacja może sprawdzić akcje poczty e-mail zostały poprawnie skonfigurowane za pośrednictwem interfejsu użytkownika sieci Web.

Przed rozpoczęciem należy utworzyć konto programu Outlook, które będzie używane do wysyłania powiadomień e-mail z akceleratora rozwiązań.

Poniższe kroki pomagają zalogować się i skonfigurować akcje poczty e-mail:

1. Kliknij, aby zalogować się do programu Outlook. Użytkownik został przesuń do witryny Azure portal:

   ![Logowanie się do programu Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Kliknij pozycję **Autoryzuj**. Zostanie wyświetlony monit o zalogowanie się. Konto używane do logowania powinno być adresem e-mail używanym przez aplikację do wysyłania powiadomień e-mail:

1. Kliknij przycisk **Zapisz**. Wróć do akceleratora rozwiązań i odśwież stronę.

1. Jeśli powiadomienie e-mail zostało pomyślnie skonfigurowane, zostanie wyświetlony ten komunikat:

   ![Pomyślne zalogowanie się programu Outlook](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Dostosowywanie kodu HTML poczty e-mail<a name="htmledit"></a>

Gotowy akcelerator rozwiązań do zdalnego monitorowania udostępnia podstawowy szablon HTML dla wiadomości e-mail akcji. Szablon wiadomości e-mail używa wartości z ustawień akcji wiadomości e-mail. Oto przykładowy adres e-mail:

![przykład wiadomości e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

W poniższych krokach pokazano, jak edytować szablon wiadomości e-mail w formacie HTML. Można na przykład dołączyć więcej informacji lub dodać obrazy niestandardowe:

1. Klonuj repozytorium Monitorowania Zdalnego Lub .NET GitHub:

1. Przejdź do lokalizacji szablonu wiadomości e-mail:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Można dodać lub usunąć wszystkie parametry w tym szablonie, aby dostosować wiadomość. W razie potrzeby można również dodawać, usuwać lub zamieniać połączenia:

    Na przykład w kodzie .NET:`emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Na przykład w kodzie Java:`this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametry w szablonie przybierają formę `${...}`. Aby usunąć parametr, usuń wymagany wiersz. Aby dodać parametr, dodaj wiersz z wartością do wstawienia.

1. Aby dodać obrazy lub tekst niestandardowy, należy zaktualizować plik EmailTemplate.HTML bezpośrednio.

## <a name="throttling"></a>Ograniczanie przepływności

Akcelerator rozwiązania zdalnego monitorowania używa programu Outlook do wysyłania powiadomień e-mail. Program Outlook ogranicza liczbę e-maili wysyłanych do [30 wiadomości e-mail na 1 minutę](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Klienci poczty e-mail otrzymujący wiadomości e-mail mogą również ograniczyć liczbę e-maili otrzymanych na minutę. Skontaktuj się z konkretnym klientem poczty e-mail, aby uzyskać ograniczenia. Podczas konfigurowania powiadomienia e-mail dla reguły reguła powinna obliczać średnie wartości w okresie co najmniej jednej minuty i nie używać wartości błyskawicznych:

![Obliczenia średniej](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku pokazano, jak dodać akcję poczty e-mail do nowej lub istniejącej reguły w ramach rozwiązania do zdalnego monitorowania. Przewodnik pokazał również i jak edytować kod HTML definiujący format wiadomości.

Sugerowany następny krok to dowiedzieć [się, jak korzystać z alertów i rozwiązać problemy z urządzeniem.](iot-accelerators-remote-monitoring-maintain.md)
