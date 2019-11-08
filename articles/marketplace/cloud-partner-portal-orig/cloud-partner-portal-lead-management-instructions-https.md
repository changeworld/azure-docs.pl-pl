---
title: Punkt końcowy HTTPS | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla punktu końcowego HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pabutler
ms.openlocfilehash: 817e431f5386b10345d414190e8bda0954ef2aca
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825229"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurowanie zarządzania liderem za pomocą punktu końcowego HTTPS

Możesz użyć punktu końcowego HTTPS do obsługi platformy Azure Marketplace i AppSource potencjalnych klientów. Te potencjalni klienci mogą być zapisywana w systemie, które mogą zostać zapisaniu do systemu zarządzania relacjami z klientami (CRM) lub wysyłane jako powiadomienia e-mail. W tym artykule opisano sposób konfigurowania zarządzania liderem za pomocą usługi [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) Automation.

## <a name="create-a-flow-using-microsoft-flow"></a>Tworzenie przepływu przy użyciu Microsoft Flow

1. Otwórz stronę sieci Web [przepływu](https://flow.microsoft.com/) . Wybierz pozycję **Zaloguj** lub wybierz pozycję **zarejestruj się bezpłatnie** , aby utworzyć bezpłatne konto przepływu.

2. Zaloguj się i wybierz pozycję **Moje przepływy** na pasku menu.

    ![Moje przepływy](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Wybierz pozycję **+ Utwórz z pustego**.

    ![Utwórz z pustego](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Wybierz pozycję **Utwórz z pustego**.

    ![Utwórz z pustego](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. W polu **Wyszukaj łączniki i wyzwalacze** wpisz "Request" (żądanie), aby znaleźć łącznik żądania.
6. W obszarze **wyzwalacze**wybierz opcję **po odebraniu żądania HTTP**. 

    ![Wybierz wyzwalacz odebrane żądanie HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Wykonaj jedną z następujących czynności, aby skonfigurować **schemat JSON treści żądania**:

   - Skopiuj [schemat JSON](#json-schema) na końcu tego artykułu do pola tekstowego **schemat JSON treści żądania** .
   - Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. W polu tekstowym **wprowadź lub wklej przykładowy ładunek JSON** Wklej w [przykładzie JSON](#json-example). Wybierz pozycję **gotowe** , aby utworzyć schemat.

   >[!Note]
   >W tym momencie w przepływie możesz połączyć się z systemem CRM lub skonfigurować powiadomienie e-mail.

### <a name="to-connect-to-a-crm-system"></a>Aby nawiązać połączenie z systemem CRM

1. Wybierz pozycję **+ nowy krok**.
2. Wybierz wybrany system CRM z akcją tworzenia nowego rekordu. Na poniższym zrzucie ekranu przedstawiono **Dynamics 365 — Utwórz nowy rekord** jako przykład.

    ![Utwórz nowy rekord](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Podaj **nazwę organizacji** , która jest danymi wejściowymi połączenia dla łącznika. Wybierz pozycję **potencjalni klienci** z listy rozwijanej **Nazwa jednostki** .

    ![Wybierz potencjalnych klientów](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow pokazuje formularz do udostępniania informacji o potencjalnych klientach. Możesz mapować elementy z żądania wejściowego, wybierając pozycję Dodaj zawartość dynamiczną. Poniższy przykład przechwytywania ekranu pokazuje **OfferTitle** .

    ![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Zamapuj odpowiednie pola, a następnie wybierz pozycję **Zapisz** , aby zapisać przepływ.

6. W żądaniu zostanie utworzony adres URL POST protokołu HTTP. Skopiuj ten adres URL i użyj go jako punktu końcowego HTTPS.

    ![Adres URL post protokołu HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Aby skonfigurować powiadomienie e-mail

1. Wybierz pozycję **+ nowy krok**.
2. W obszarze **Wybierz akcję**wybierz pozycję **Akcje**.
3. W obszarze **Akcje** wybierz pozycję **Wyślij wiadomość e-mail**.

    ![Dodaj akcję poczty e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. W polu **Wyślij wiadomość e-mail**skonfiguruj następujące wymagane pola:

   - **Aby** wprowadzić co najmniej jeden prawidłowy adres e-mail.
   - Przepływ **tematu** umożliwia dodanie zawartości dynamicznej, na przykład **LeadSource** , na poniższym zrzucie ekranu.

     ![Dodawanie akcji poczty e-mail przy użyciu zawartości dynamicznej](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Treść** — z listy zawartość dynamiczna Dodaj odpowiednie informacje w treści wiadomości e-mail. Na przykład LastName, FirstName, email i Company.

   Po zakończeniu konfigurowania powiadomienia e-mail będzie on wyglądał jak przykład na poniższym zrzucie ekranu.

   ![Dodaj akcję poczty e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Wybierz pozycję **Zapisz** , aby zakończyć przepływ.
6. W żądaniu zostanie utworzony adres URL POST protokołu HTTP. Skopiuj ten adres URL i użyj go jako punktu końcowego HTTPS.

    ![Adres URL post protokołu HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS

Podczas konfigurowania informacji o zarządzaniu potencjalnym liderem oferty wybierz pozycję **punkt końcowy https** dla **miejsca docelowego potencjalnego klienta** i wklej w adresie URL post protokołu HTTP skopiowanym w poprzednim kroku.  

![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Gdy są generowane potencjalni klienci, firma Microsoft wysyła potencjalni klienci do przepływu, który jest kierowany do skonfigurowanego systemu CRM lub adresu e-mail.

## <a name="json-schema-and-example"></a>Schemat JSON i przykład

W przykładzie testu JSON jest stosowany następujący schemat:

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

Poniższy przykład kodu JSON można skopiować i edytować, aby użyć go jako testu w usłudze MS Flow.

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

Jeśli jeszcze tego nie zrobiono, należy skonfigurować [potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) w Portal Cloud partner.
