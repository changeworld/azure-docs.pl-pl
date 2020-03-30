---
title: Punkt końcowy HTTPS | Azure Marketplace
description: Konfigurowanie zarządzania potencjalnymi klientami dla punktu końcowego HTTPS.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: dsindona
ms.openlocfilehash: cb6ef173e97a7c2bbd7d7cad5e5074b1f2d0f066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288601"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurowanie zarządzania potencjalnymi klientami przy użyciu punktu końcowego HTTPS

Punktu końcowego protokołu HTTPS można używać do obsługi potencjalnych klientów w portalu Azure Marketplace i AppSource. Przewody te mogą być zapisywane do tego mogą być zapisywane do systemu zarządzania relacjami z klientami (CRM) lub wysyłane jako powiadomienie e-mail. W tym artykule opisano sposób konfigurowania zarządzania potencjalnymi klientami przy użyciu usługi automatyzacji [usługi Microsoft Flow.](https://powerapps.microsoft.com/automate-processes/)

## <a name="create-a-flow-using-microsoft-flow"></a>Tworzenie przepływu przy użyciu usługi Microsoft Flow

1. Otwórz stronę internetową [Flow.](https://flow.microsoft.com/) Wybierz **pozycję Zaloguj się** lub wybierz zarejestruj się **bezpłatnie,** aby utworzyć bezpłatne konto Flow.

2. Zaloguj się i wybierz **pozycję Moje przepływy** na pasku menu.

    ![Moje przepływy](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Wybierz **+ Utwórz z pustego**.

    ![Tworzenie z pustego](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Wybierz opcję **Utwórz z pustego**.

    ![Tworzenie z pustego](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. W polu **Wyszukaj łączniki i wyzwalacze** wpisz "request", aby znaleźć łącznik żądania.
6. W obszarze **Wyzwalacze**wybierz pozycję **Po odebraniu żądania HTTP**. 

    ![Wybieranie wyzwalacza odebranego żądania HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Aby skonfigurować **schemat JSON treści żądania,** należy wykonać jedną z następujących czynności:

   - Skopiuj [schemat JSON](#json-schema) na końcu tego artykułu do pola tekstowego **Schemat JSON treści żądania.**
   - Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. W polu **tekstowym Wprowadzanie lub wklejanie przykładowego ładunku JSON** wklej w [przykładzie JSON](#json-example). Wybierz **pozycję Gotowe,** aby utworzyć schemat.

   >[!Note]
   >W tym momencie w przepływie można połączyć się z systemem CRM lub skonfigurować powiadomienie e-mail.

### <a name="to-connect-to-a-crm-system"></a>Aby połączyć się z systemem CRM

1. Wybierz pozycję **+ Nowy krok**.
2. Wybierz wybrany system CRM z akcją, aby utworzyć nowy rekord. Następujące zrzut ekranu pokazuje **Dynamics 365 — utwórz nowy rekord** jako przykład.

    ![Tworzenie nowego rekordu](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Podaj **nazwę organizacji,** która jest wejściami połączenia dla łącznika. Wybierz **pozycję Potencjalni klienci** z listy rozwijanej Nazwa **encji.**

    ![Wybieranie potencjalnych klientów](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Przepływ pokazuje formularz do dostarczania informacji o potencjalnych klientach. Elementy można mapować z żądania wprowadzania, wybierając opcję dodawania zawartości dynamicznej. Poniższe zrzut ekranu pokazuje **OfferTitle** jako przykład.

    ![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Zamapuj żądane pola, a następnie wybierz pozycję **Zapisz,** aby zapisać przepływ.

6. W żądaniu zostanie utworzony adres URL wpisu HTTP. Skopiuj ten adres URL i użyj go jako punktu końcowego HTTPS.

    ![Adres URL wpisu HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Aby skonfigurować powiadomienia e-mail

1. Wybierz pozycję **+ Nowy krok**.
2. W obszarze **Wybierz akcję**wybierz pozycję **Akcje**.
3. W obszarze **Akcje** wybierz pozycję **Wyślij wiadomość e-mail**.

    ![Dodawanie akcji e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. W **polu Wyślij wiadomość e-mail**skonfiguruj następujące wymagane pola:

   - **Do** - Wprowadź co najmniej jeden prawidłowy adres e-mail.
   - **Temat** — przepływ umożliwia dodanie zawartości dynamicznej, takiej jak **LeadSource** w poniższym przechwytywaniu ekranu.

     ![Dodawanie akcji wiadomości e-mail przy użyciu zawartości dynamicznej](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Treść** — z listy Zawartość dynamiczna dodaj żądane informacje w treści wiadomości e-mail. Na przykład LastName, FirstName, E-mail i Firma.

   Po zakończeniu konfigurowania powiadomienia e-mail będzie wyglądać jak przykład w poniższym przechwytywaniu ekranu.

   ![Dodawanie akcji e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Wybierz **pozycję Zapisz,** aby zakończyć przepływ.
6. W żądaniu zostanie utworzony adres URL wpisu HTTP. Skopiuj ten adres URL i użyj go jako punktu końcowego HTTPS.

    ![Adres URL wpisu HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS

Podczas konfigurowania informacji o zarządzaniu potencjalnymi klientami dla oferty wybierz pozycję **Punkt końcowy HTTPS** dla **miejsca docelowego potencjalnego klienta** i wklej adres URL HTTP POST skopiowany w poprzednim kroku.  

![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Gdy potencjalni klienci są generowani, firma Microsoft wysyła potencjalnych klientów do przepływu, które są kierowane do skonfigurowanych skonfigurowanych adresów e-mail systemu CRM.

## <a name="json-schema-and-example"></a>Schemat JSON i przykład

W przykładzie testu JSON użyto następującego schematu:

### <a name="json-schema"></a>Schemat JSON

``` json
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

Można skopiować i edytować następujący przykład JSON do użycia jako test w ms przepływu.

### <a name="json-example"></a>Przykład JSON

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, skonfiguruj [potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) w portalu cloud partner.
