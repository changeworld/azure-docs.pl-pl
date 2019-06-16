---
title: HTTPS Endpoint | Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla punktu końcowego protokołu HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pabutler
ms.openlocfilehash: 058ab30358811cb66ae033fb464974639569ae18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935265"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Skonfiguruj zarządzanie potencjalnymi klientami przy użyciu punktu końcowego HTTPS

Punkt końcowy HTTPS można użyć do obsługi portalu Azure Marketplace i AppSource potencjalnych klientów. Potencjalni klienci mogą być zapisywane, mogą być zapisywane system zarządzania relacjami z klientami (CRM) lub wysyłane jako wiadomość e-mail z powiadomieniem. W tym artykule opisano sposób konfigurowania zarządzania potencjalnymi klientami przy użyciu [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) usługi automation.

## <a name="create-a-flow-using-microsoft-flow"></a>Tworzenie przepływu przy użyciu Microsoft Flow

1. Otwórz [przepływu](https://flow.microsoft.com/) strony sieci Web. Wybierz **Zaloguj** lub wybierz **Zarejestruj się bezpłatnie** utworzyć bezpłatne konto usługi Flow.

2. Zaloguj się i wybierz **moje przepływy** na pasku menu.

    ![Moje przepływy](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Wybierz **+ Utwórz z pustego**.

    ![Utwórz z pustego](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Wybierz **Utwórz z pustego**.

    ![Utwórz z pustego](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. W **Wyszukaj łączniki i wyzwalacze** wpisz "żądanie" możesz znaleźć odpowiedniego łącznika na żądanie.
6. W obszarze **wyzwalaczy**, wybierz opcję **zostanie odebrane żądanie po HTTP**. 

    ![Wybierz wyzwalacz Odebrano żądanie HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Użyj jednej z następujących czynności do skonfigurowania **schemat JSON treści żądania**:

   - Kopiuj [schematu JSON](#json-schema) na końcu tego artykułu w **schemat JSON treści żądania** pola tekstowego.
   - Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. W **wpisz lub wklej przykładowy ładunek JSON** Wklej w polu tekstowym [przykład kodu JSON](#json-example). Wybierz **gotowe** do utworzenia schematu.

   >[!Note]
   >W tym momencie przepływu możesz połączyć się z systemem CRM lub skonfiguruj wiadomość e-mail z powiadomieniem.

### <a name="to-connect-to-a-crm-system"></a>Aby połączyć się z systemem CRM

1. Wybierz **+ nowy krok**.
2. Wybierz wybranym za pomocą akcji do tworzenia nowych rekordów w systemie CRM. Poniższy ekran przechwytywania pokazuje **Dynamics 365 — Utwórz nowy rekord** jako przykład.

    ![Utwórz nowy rekord](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Podaj **nazwa organizacji** to dane wejściowe połączenia łącznika. Wybierz **prowadzi** z **nazwa jednostki** listy rozwijanej.

    ![Wybierz potencjalnych klientów](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Przepływ przedstawia formularz zapewniające informacje o potencjalnym kliencie. Wybierając polecenie Dodaj zawartość dynamiczną, można mapować elementy z danych wejściowych żądania. Poniższy ekran przechwytywania pokazuje **OfferTitle** jako przykład.

    ![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mapowanie pól, a następnie wybrać **Zapisz** Aby zapisać przepływ.

6. Adres URL HTTP POST jest tworzony w żądaniu. Skopiuj ten adres URL i używać go jako punktu końcowego protokołu HTTPS.

    ![Adres URL żądania HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Aby skonfigurować powiadomienia e-mail

1. Wybierz **+ nowy krok**.
2. W obszarze **wybierz akcję**, wybierz opcję **akcje**.
3. W obszarze **Akcje** wybierz pozycję **Wyślij wiadomość e-mail**.

    ![Dodawanie akcji poczty e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. W **Wyślij wiadomość e-mail**, skonfiguruj następujące wymagane pola:

   - **Aby** -Wprowadź co najmniej jeden prawidłowy adres e-mail.
   - **Temat** -Flow zapewnia możliwość dodawania zawartości dynamicznej, takie jak **LeadSource** na poniższym zrzucie ekranu.

     ![Dodawanie akcji poczty e-mail przy użyciu zawartości dynamicznej](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Treść** — z listy zawartości dynamicznej, Dodaj potrzebne informacje w treści wiadomości e-mail. Na przykład, LastName, FirstName, poczty E-mail, i firmowych.

   Po zakończeniu konfigurowania powiadomień e-mail, będzie to wyglądać jak przykład pokazany na poniższym zrzucie ekranu.

   ![Dodawanie akcji poczty e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Wybierz **Zapisz** na zakończenie przepływu.
6. Adres URL HTTP POST jest tworzony w żądaniu. Skopiuj ten adres URL i używać go jako punktu końcowego protokołu HTTPS.

    ![Adres URL żądania HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie swojej oferty, aby wysłać potencjalnych klientów do punktu końcowego HTTPS

Po skonfigurowaniu informacji zarządzania potencjalnymi klientami ofertą wybierz **punktu końcowego HTTPS** dla **prowadzić docelowy** i wklej adres URL HTTP POST skopiowanym w poprzednim kroku.  

![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Podczas generowania potencjalnych klientów, firma Microsoft wysyła potencjalnych klientów do usługi Flow, Pobierz kierowane do jego CRM systemu lub adresu e-mail, który został skonfigurowany.

## <a name="json-schema-and-example"></a>Przykład i schematu JSON

Przykład testu JSON korzysta z następującego schematu:

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

Można skopiować i edytować w poniższym przykładzie JSON do użycia jako test w usługi Flow MS.

### <a name="json-example"></a>Przykład kodu JSON

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

## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze tego nie zrobiono, należy skonfigurować klientów [prowadzi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) w portalu Cloud Partner.
