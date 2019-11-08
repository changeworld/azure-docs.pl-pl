---
title: Punkt końcowy HTTPS | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla punktu końcowego HTTPS.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 83efb9cfd1ee7464a334ebc4064dbfaa20ab30de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812287"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurowanie zarządzania liderem za pomocą punktu końcowego HTTPS

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w centrum partnerskim na potrzeby otrzymywania potencjalnych klientów w witrynie Azure Marketplace i AppSource, można użyć punktu końcowego HTTPS w usłudze MS Flow, aby obsłużyć te potencjalni klienci. Za pomocą punktu końcowego HTTPS te potencjalni klienci mogą być wysyłane jako powiadomienia e-mail lub mogą być zapisywane w systemie zarządzania relacjami z klientami (CRM) obsługiwanym przez program MS Flow. Instrukcje zawarte w tym artykule przeprowadzą Cię przez proces podstawowy w celu utworzenia nowego przepływu przy użyciu Microsoft Flow, który spowoduje wygenerowanie adresu URL POST protokołu HTTP, który zostanie wprowadzony do portalu wydawców w polu **adres URL punktu końcowego** usługi zarządzania potencjalnymi > https. Uwzględniono również instrukcje dotyczące sposobu testowania przepływu za pomocą narzędzia o nazwie [Poster](https://www.getpostman.com/downloads/) , które można znaleźć w trybie online.

## <a name="create-a-flow-using-microsoft-flow"></a>Tworzenie przepływu przy użyciu Microsoft Flow

1. Otwórz stronę sieci Web [przepływu](https://flow.microsoft.com/) . Wybierz pozycję **Zaloguj**lub jeśli nie masz jeszcze konta, wybierz pozycję **zarejestruj się bezpłatnie** , aby utworzyć bezpłatne konto przepływu.

2. Zaloguj się i wybierz pozycję **Moje przepływy** na pasku menu.

3. Wybierz pozycję **+ zautomatyzowany — od pustej**.

    ![Moje przepływy + automatyczne — z pustego](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. W oknie *kompilacja przepływu automatycznego* wybierz pozycję **Pomiń**. 

    ![Kompiluj zautomatyzowany przepływ — Pomiń](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. W polu **Wyszukaj łączniki i wyzwalacze** wpisz "Request" (żądanie), aby znaleźć łącznik żądania.
6. W obszarze *wyzwalacze*wybierz opcję **po odebraniu żądania HTTP**. 

    ![Łącznik żądań — wyzwalacze](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. W oknie dialogowym *po odebraniu żądania HTTP* skopiuj i wklej poniższy schemat JSON do pola tekstowego **schemat JSON treści żądania** . Ten schemat jest używany przez firmę Microsoft do przechowywania danych potencjalnych klientów.

    ![Łącznik żądań — wyzwalacze](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>W tym momencie konfiguracji można wybrać opcję nawiązywania połączenia z systemem CRM lub skonfigurować powiadomienie e-mail. Postępuj zgodnie z pozostałymi instrukcjami.

### <a name="to-connect-to-a-crm-system"></a>Aby nawiązać połączenie z systemem CRM

1. Wybierz pozycję **+ nowy krok**.
2. Wybierz dowolnie wybrany system CRM, wyszukując w nim *łączniki i akcje wyszukiwania*, a następnie wybierz je w sekcji *działania* z akcją, aby utworzyć nowy rekord. Na poniższym zrzucie ekranu przedstawiono **Dynamics 365 — Utwórz** nowy rekord jako przykład.

    ![Utwórz nowy rekord](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Podaj **nazwę organizacji** skojarzoną z systemem CRM. Wybierz pozycję **potencjalni klienci** z listy rozwijanej **Nazwa jednostki** .

    ![Wybierz potencjalnych klientów](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow pokazuje formularz do udostępniania informacji o potencjalnych klientach. Możesz mapować elementy z żądania wejściowego, wybierając pozycję Dodaj zawartość dynamiczną. Poniższy przykład przechwytywania ekranu pokazuje **OfferTitle** .

    ![Dodaj zawartość dynamiczną](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Zamapuj odpowiednie pola, a następnie wybierz pozycję **Zapisz** , aby zapisać przepływ. Zostanie utworzony adres URL POST protokołu HTTP, który jest dostępny w oknie *po odebraniu żądania HTTP* . Skopiuj ten adres URL przy użyciu kontrolki Kopiuj znajdującej się po prawej stronie adresu URL POST protokołu HTTP — jest to ważne, aby nie przerzucać żadnej części całego adresu URL. Zapisz ten adres URL, ponieważ będzie on potrzebny podczas konfigurowania zarządzania liderem w portalu wydawców.

    ![Po odebraniu żądania HTTP.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Aby skonfigurować powiadomienie e-mail

1. Po ukończeniu schematu JSON wybierz pozycję **+ nowy krok**.
2. W obszarze **Wybierz akcję**wybierz pozycję **Akcje**.
3. W obszarze **Akcje**wybierz pozycję **Wyślij wiadomość e-mail (Office 365 Outlook)** .

    >[!Note]
    >Jeśli chcesz użyć innego wyszukiwania dostawcy poczty e-mail, a następnie wybierz opcję *Wyślij wiadomość e-mail z powiadomieniem (pocztą)* jako akcję.

    ![Dodaj akcję poczty e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. W oknie **wysyłanie wiadomości e-mail** skonfiguruj następujące wymagane pola:

   - **Aby** wprowadzić co najmniej jeden prawidłowy adres e-mail, z którego będą wysyłane potencjalni klienci.
   - Przepływ **tematu** umożliwia dodanie zawartości dynamicznej, na przykład **LeadSource** , na poniższym zrzucie ekranu. Zacznij od wpisania nazwy pola, a następnie klikając listę dynamiczne pobranie zawartości z okna podręcznego. 

        >[!Note] 
        > Podczas dodawania nazw pól można wykonać każdą z nich przy użyciu ":", a następnie wprowadzić polecenie, aby utworzyć nowy wiersz. Po dodaniu nazw pól można dodać każdy skojarzony parametr z listy pobrań dynamicznych.

        ![Dodawanie akcji poczty e-mail przy użyciu zawartości dynamicznej](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Treść** — z listy dynamiczne pobranie zawartości Dodaj odpowiednie informacje w treści wiadomości e-mail. Na przykład LastName, FirstName, email i Company. <br> <br> Po zakończeniu konfigurowania powiadomienia e-mail będzie on wyglądał jak przykład na poniższym zrzucie ekranu.


       ![Dodaj akcję poczty e-mail](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Wybierz pozycję **Zapisz** , aby zakończyć przepływ. Adres URL POST protokołu HTTP jest tworzony i jest dostępny w oknie *po odebraniu żądania HTTP* . Skopiuj ten adres URL przy użyciu kontrolki Kopiuj znajdującej się po prawej stronie adresu URL POST protokołu HTTP — jest to ważne, aby nie przerzucać żadnej części całego adresu URL. Zapisz ten adres URL, ponieważ będzie on potrzebny podczas konfigurowania zarządzania liderem w portalu wydawców.

   ![ADRES URL POST PROTOKOŁU HTTP ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testowanie

Możesz sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, wykonując następujące kroki przy użyciu narzędzia o nazwie [Poster](https://app.getpostman.com/app/download/win64), które można pobrać w trybie online. Jest on dostępny dla systemu Windows. 

1. Uruchom aplikację Poster i wybierz pozycję **nowe** **żądanie** > , aby skonfigurować narzędzie testowe. 

   ![Żądanie instalacji narzędzia testowego](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Wypełnij formularz *Zapisz żądanie* , a następnie **Zapisz** go w utworzonym folderze.

   ![Zapisz żądanie](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Z listy rozwijanej wybierz pozycję **post** . 

   ![Testowanie mojego przepływu](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Wklej adres URL POST protokołu HTTP z przepływu utworzonego w usłudze MS Flow, w którym znajduje się *adres URL żądania*.

   ![Wklej adres URL POST protokołu HTTP](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Wróć do [przepływu](https://flow.microsoft.com/) i Znajdź utworzony przepływ w celu wysłania potencjalnych klientów, przechodząc do **obszaru Moje przepływy** z paska menu przepływ.  Wybierz trzy kropki obok nazwy przepływu i wybierz pozycję **Edytuj**.

   ![Moje przepływy — Edytuj](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Wybierz pozycję **Testuj** w prawym górnym rogu, wybierz pozycję "Chcę wykonać akcję wyzwalacza", a następnie wybierz pozycję **Testuj**. W górnej części ekranu pojawi się wskaźnik informujący o tym, że test został uruchomiony

   ![Test Flow-Trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Wróć do aplikacji w programie Poster i wybierz pozycję **Wyślij** obok miejsca, w którym został wklejony adres URL https.

   ![Testowanie mojego przepływu — wysyłanie](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Wróć do przepływu i sprawdź wynik. Jeśli wszystko działa zgodnie z oczekiwaniami, zobaczysz komunikat informujący o pomyślnym zakończeniu.

   ![Przepływ — Sprawdź wyniki](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Użytkownik powinien również otrzymać wiadomość e-mail. Sprawdź skrzynkę odbiorczą poczty e-mail. 

    >[!Note] 
    >Jeśli nie widzisz wiadomości e-mail z testu, Sprawdź swoje spam i foldery śmieci. Poniżej zobaczysz tylko etykiety pól dodane podczas konfigurowania powiadomienia e-mail. Jeśli jako rzeczywisty potencjalny klient został wygenerowany z oferty, zobaczysz również rzeczywiste informacje z kontaktu potencjalnego klienta w treści i w wierszu tematu.

   ![Odebrano wiadomość e-mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące czynności:

1. Przejdź do strony **Konfiguracja oferty** dla swojej oferty.
2. Wybierz pozycję **Połącz** w sekcji Zarządzanie potencjalnymi klientami.
3. W oknie podręcznym szczegóły połączenia wybierz pozycję **punkt końcowy https** dla **miejsca docelowego potencjalnego klienta** i wklej w adresie URL post protokołu HTTP z przepływu utworzonego w ramach wcześniejszych kroków w polu **adres URL punktu końcowego HTTPS** .
4. Wybierz pozycję **Zapisz**. 

>[!Note] 
>Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.

Gdy są generowane potencjalni klienci, firma Microsoft wysyła potencjalni klienci do przepływu, który jest kierowany do skonfigurowanego systemu CRM lub adresu e-mail.

![Zarządzanie potencjalnymi klientami — łączenie](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

