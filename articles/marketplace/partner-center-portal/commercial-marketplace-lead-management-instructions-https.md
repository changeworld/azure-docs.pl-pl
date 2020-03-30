---
title: Punkt końcowy HTTPS | Azure Marketplace
description: Konfigurowanie zarządzania potencjalnymi klientami dla punktu końcowego HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: dsindona
ms.openlocfilehash: 6a0131cf94759fc529a52ea33d5392a60c5ef30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281599"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurowanie zarządzania potencjalnymi klientami przy użyciu punktu końcowego HTTPS

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w Centrum partnerów do odbierania potencjalnych klientów w portalu Azure Marketplace i AppSource, można użyć punktu końcowego HTTPS w usłudze MS Flow do obsługi tych potencjalnych klientów. W punkcie końcowym HTTPS przewody te mogą być wysyłane jako powiadomienie e-mail lub mogą być zapisywane w systemie zarządzania relacjami z klientami (CRM) obsługiwanym przez MS Flow. Instrukcje w tym artykule poprowadzi Cię przez podstawowy proces tworzenia nowego przepływu przy użyciu usługi Microsoft Flow, który wygeneruje adres URL HTTP POST, który zostanie wprowadzony w portalu publikowania dla pola Adres URL zarządzania potencjalnymi klientami > **https.** Ponadto, zawarte są instrukcje, w jaki sposób można przetestować przepływ za pomocą narzędzia o nazwie [Listonosz,](https://www.getpostman.com/downloads/) które można znaleźć w Internecie.

## <a name="create-a-flow-using-microsoft-flow"></a>Tworzenie przepływu przy użyciu usługi Microsoft Flow

1. Otwórz stronę internetową [Flow.](https://flow.microsoft.com/) Wybierz **pozycję Zaloguj się**lub jeśli nie masz jeszcze konta, wybierz pozycję Zarejestruj się **bezpłatnie,** aby utworzyć bezpłatne konto Flow.

2. Zaloguj się i wybierz **pozycję Moje przepływy** na pasku menu.

3. Wybierz **+Automated - z pustego**.

    ![Moje przepływy + Zautomatyzowane - z pustego](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. W oknie *Tworzenie przepływu automatycznego* wybierz pozycję **Pomiń**. 

    ![Tworzenie przepływu automatycznego - Pomiń](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. W polu **Wyszukaj łączniki i wyzwalacze** wpisz "request", aby znaleźć łącznik żądania.
6. W obszarze *Wyzwalacze*wybierz pozycję **Po odebraniu żądania HTTP**. 

    ![łącznik żądania — wyzwalacze](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. W *oknie Kiedy żądanie HTTP jest odbierane* kopiuj i wklej schemat JSON poniżej w polu tekstowym **Schemat JSON treści żądania.** Ten schemat jest używany przez firmę Microsoft do przechowywania danych potencjalnego klienta.

    ![łącznik żądania — wyzwalacze](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **Schemat JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>W tym momencie w konfiguracji można wybrać połączenie z systemem CRM lub skonfigurowanie powiadomienia e-mail. Postępuj zgodnie z pozostałymi instrukcjami na podstawie wyboru.

### <a name="to-connect-to-a-crm-system"></a>Aby połączyć się z systemem CRM

1. Wybierz pozycję **+ Nowy krok**.
2. Wybierz wybrany system CRM, wyszukując go tam, gdzie jest *komunikat Szukaj łączników i akcji,* i wybierz go w sekcji *Akcje* z akcją, aby utworzyć nowy rekord. Następujące zrzut ekranu pokazuje **Dynamics 365 — utwórz** nowy rekord jako przykład.

    ![Tworzenie nowego rekordu](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Podaj **nazwę organizacji** skojarzoną z systemem CRM. Wybierz **pozycję Potencjalni klienci** z listy rozwijanej Nazwa **encji.**

    ![Wybieranie potencjalnych klientów](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Przepływ pokazuje formularz do dostarczania informacji o potencjalnych klientach. Elementy można mapować z żądania wprowadzania, wybierając opcję dodawania zawartości dynamicznej. Poniższe zrzut ekranu pokazuje **OfferTitle** jako przykład.

    ![Dodaj zawartość dynamiczną](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Zamapuj żądane pola, a następnie wybierz pozycję **Zapisz,** aby zapisać przepływ. Zostanie utworzony adres URL http post i jest dostępny w oknie *Po odebraniu żądania HTTP.* Skopiuj ten adres URL za pomocą formantu kopiowania, który znajduje się po prawej stronie adresu URL HTTP POST - jest to ważne, aby nie przegapić omyłkowo żadnej części całego adresu URL. Zapisz ten adres URL, ponieważ będzie potrzebny podczas konfigurowania zarządzania potencjalnymi klientami w portalu publikowania.

    ![Po odebraniu żądania HTTP.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Aby skonfigurować powiadomienia e-mail

1. Po zakończeniu schematu JSON wybierz **+ Nowy krok**.
2. W obszarze **Wybierz akcję**wybierz pozycję **Akcje**.
3. W obszarze **Akcje**wybierz pozycję **Wyślij wiadomość e-mail (Office 365 Outlook)**.

    >[!Note]
    >Jeśli chcesz użyć innego dostawcy poczty e-mail, wyszukaj i wybierz *opcję Wyślij powiadomienie e-mail (Mail)* jako akcję.

    ![Dodawanie akcji e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. W oknie **Wyślij wiadomość e-mail** skonfiguruj następujące wymagane pola:

   - **Do** - Wprowadź co najmniej jeden prawidłowy adres e-mail, na który zostaną wysłane potencjalni klienci.
   - **Temat** — przepływ umożliwia dodanie zawartości dynamicznej, takiej jak **LeadSource** w poniższym przechwytywaniu ekranu. Zacznij od wpisania nazwy pola, a następnie kliknięcia listy wyboru zawartości dynamicznej w oknie podręcznym. 

        >[!Note] 
        > Podczas dodawania nazw pól można śledzić każdy z ":" a następnie wprowadź, aby utworzyć nowy wiersz. Po dodaniu nazw pól można dodać każdy skojarzony parametr z dynamicznej listy pobrania.

        ![Dodawanie akcji wiadomości e-mail przy użyciu zawartości dynamicznej](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Treść** — na liście wyboru zawartości dynamicznej dodaj żądane informacje w treści wiadomości e-mail. Na przykład LastName, FirstName, E-mail i Firma. <br> <br> Po zakończeniu konfigurowania powiadomienia e-mail będzie wyglądać jak przykład w poniższym przechwytywaniu ekranu.


       ![Dodawanie akcji e-mail](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Wybierz **pozycję Zapisz,** aby zakończyć przepływ. Adres URL HTTP POST jest tworzony i jest dostępny w oknie *Po odebraniu żądania HTTP.* Skopiuj ten adres URL za pomocą formantu kopiowania, który znajduje się po prawej stronie adresu URL HTTP POST - jest to ważne, aby nie przegapić omyłkowo żadnej części całego adresu URL. Zapisz ten adres URL, ponieważ będzie potrzebny podczas konfigurowania zarządzania potencjalnymi klientami w portalu publikowania.

   ![Adres URL WPISU HTTP ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testowanie

Możesz sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, wykonując następujące kroki za pomocą narzędzia o nazwie [Listonosz](https://app.getpostman.com/app/download/win64), które można pobrać online. Ta usługa jest dostępna dla systemu Windows. 

1. Uruchom listonosz i wybierz **nowe** > **żądanie,** aby skonfigurować narzędzie testowe. 

   ![Żądanie skonfigurowania narzędzia testowego](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Wypełnij formularz *Zapisz żądanie,* a następnie **zapisz** w utworzonym folderze.

   ![Zapisywanie żądania](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Z listy rozwijanej **wybierz pozycję OPUBLIKUJ.** 

   ![Przetestuj mój przepływ](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Wklej adres URL HTTP POST z przepływu utworzonego w MS Flow, gdzie jest on komunikat *Enter adres URL żądania*.

   ![Wklej adres URL WPISu HTTP](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Wróć do [przepływu](https://flow.microsoft.com/) i znajdź przepływ utworzony w celu wysyłania potencjalnych klientów, przechodząc do **opcji Moje przepływy** z paska menu Przepływ.  Wybierz 3 kropki obok nazwy przepływu i wybierz pozycję **Edytuj**.

   ![Moje przepływy - Edytuj](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Wybierz **opcję Testuj** w prawym górnym rogu, wybierz opcję "Wykonam akcję wyzwalacza", a następnie wybierz **opcję Testuj**. W górnej części ekranu pojawi się informacja wskazująca, że test został rozpoczęty

   ![Przepływ testowy - wyzwalacz](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Wróć do aplikacji Postman i wybierz **pozycję Wyślij** obok miejsca, w którym wklejono adres URL HTTPS.

   ![Przetestuj mój przepływ - Wyślij](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Wróć do swojego przepływu i sprawdź wynik. Jeśli wszystko działa zgodnie z oczekiwaniami, zostanie wyświetlony komunikat informujący, że zakończyło się pomyślnie.

   ![Przepływ - Sprawdź wyniki](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Powinieneś również otrzymać wiadomość e-mail. Sprawdź swoją skrzynkę e-mail. 

    >[!Note] 
    >Jeśli nie widzisz wiadomości e-mail z testu, sprawdź foldery spamu i wiadomości-śmieci. Poniżej można zauważyć tylko etykiety pól dodane podczas konfigurowania powiadomienia e-mail. Gdyby był to rzeczywisty potencjalny klient wygenerowany z oferty, można również wyświetlić rzeczywiste informacje z kontaktu potencjalnego klienta w treści i w wierszu Temat.

   ![Otrzymana wiadomość e-mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS

Gdy będziesz gotowy skonfigurować informacje o zarządzaniu potencjalnymi klientami dla oferty w portalu publikowania, wykonaj poniższe czynności:

1. Przejdź do strony **Ustawienia oferty** dla swojej oferty.
2. Wybierz **pozycję Połącz** w sekcji Zarządzanie potencjalnymi klientami.
3. W oknie podręcznym Szczegóły połączenia wybierz **pozycję Punkt końcowy HTTPS** dla **miejsca docelowego potencjalnego klienta** i wklej adres URL HTTP POST z utworzonego przepływu, wykonując wcześniejsze kroki w polu URL punktu **końcowego HTTPS.**
4. Wybierz **pozycję Zapisz**. 

>[!Note] 
>Musisz zakończyć konfigurowanie pozostałej części oferty i opublikować ją, zanim będzie można odbierać potencjalnych klientów dla oferty.

Gdy potencjalni klienci są generowani, firma Microsoft wysyła potencjalnych klientów do przepływu, które są kierowane do skonfigurowanych skonfigurowanych adresów e-mail systemu CRM.

![Zarządzanie potencjalnymi klientami - połączenie](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

