---
title: Punkt końcowy HTTPS | Dokumentacja firmy Microsoft
description: Skonfiguruj zarządzanie potencjalnymi klientami dla protokołu Https.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810242"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Skonfiguruj zarządzanie potencjalnymi klientami przy użyciu punktu końcowego HTTPS

Punkt końcowy HTTPS służy do obsługi portalu Azure Marketplace i AppSource potencjalnych klientów, które mogą być zapisywane do systemu CRM. W tym artykule opisano sposób konfigurowania zarządzania potencjalnymi klientami przy użyciu automatyzacji usługi Microsoft Flow.


## <a name="create-a-flow-using-microsoft-flow"></a>Tworzenie przepływu przy użyciu Microsoft Flow

1.  Otwórz [przepływu](https://flow.microsoft.com/) strony sieci Web. Wybierz **Zaloguj** lub wybierz **Zarejestruj się bezpłatnie** utworzyć bezpłatne konto usługi Flow.

2.  Zaloguj się i wybierz **moje przepływy** na pasku menu.

    ![Moje przepływy](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Wybierz **Utwórz z pustego**.

    ![Utwórz z pustego](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Wybierz **żądań/odpowiedzi** łącznika, a następnie wyszukaj wyzwalacza żądania. 

    ![Utwórz z pustego](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Wybierz **żądania** wyzwalacza.
    ![Wyzwalacza żądania](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Kopiuj **przykład kodu JSON** na końcu tego artykułu w **schemat JSON treści żądania**.

7.  Dodaj nowy krok, a następnie wybierz wybranym za pomocą akcji do tworzenia nowych rekordów w systemie CRM. Następny ekran przechwytywania pokazuje **Dynamics 365 — Utwórz nowy rekord** jako przykład.

    ![Utwórz nowy rekord](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Podaj dane wejściowe połączenia dla Twojego łącznika, a następnie wybierz pozycję **prowadzi** jednostki.

    ![Wybierz potencjalnych klientów](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Przepływy przedstawia formularz zapewniające informacje o potencjalnym kliencie. Wybierając polecenie Dodaj zawartość dynamiczną, można mapować elementy z danych wejściowych żądania.

    ![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Mapowanie pól, a następnie wybrać **Zapisz** Aby zapisać przepływ.

11. Adres URL HTTP POST jest tworzony w żądaniu. Skopiuj ten adres URL i używać go jako punktu końcowego protokołu HTTPS.

    ![Adres URL żądania HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie swojej oferty, aby wysłać potencjalnych klientów do punktu końcowego HTTPS

Po skonfigurowaniu informacji zarządzania potencjalnymi klientami ofertą wybierz **punktu końcowego HTTPS** prowadzić docelowego i wklej adres URL HTTP POST został skopiowany w poprzednim kroku.  

![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

Podczas generowania potencjalnych klientów, firma Microsoft wyśle do usługi Flow, Pobierz kierowane do systemu CRM, które zostały skonfigurowane potencjalnych klientów.


## <a name="json-example"></a>Przykład kodu JSON

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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
