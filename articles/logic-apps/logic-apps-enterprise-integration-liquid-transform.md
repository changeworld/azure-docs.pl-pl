---
title: Konwertuj dane JSON przy użyciu płynne transformacji - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz transformacje lub mapy dla zaawansowanych przekształcenia JSON przy użyciu Logic Apps i płynnych szablonu.
services: logic-apps
documentationcenter: ''
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 42a102c9b2663380a93d56cc5f8fb82abaa83b74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299515"
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Wykonywać zaawansowane przekształcenia JSON przy użyciu szablonu płynne

Aplikacje logiki platformy Azure obsługuje podstawowe przekształcenia JSON za pośrednictwem danych natywnych operacji akcje, takie jak **Redaguj** lub **przeanalizować JSON**. Do przekształceń zaawansowane JSON można płynne szablonów aplikacji logiki. 
[Płynne](https://shopify.github.io/liquid/) jest językiem open source szablonu aplikacji sieci web elastyczne.
 
W tym artykule Dowiedz się, jak używać mapy płynne lub szablonu, który obsługuje bardziej złożone przekształcenia JSON, takie jak liczba iteracji, przepływami sterowania, zmienne i tak dalej. Zanim będzie można wykonać przekształcenia płynne w aplikacji logiki, należy zdefiniować mapowania z formatu JSON do formatu JSON z mapy płynne i magazynu, które mapują na koncie integracji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). W przeciwnym razie możesz [skorzystać z subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/purchase-options/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Podstawowy [konta integracji](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Tworzenie szablonu płynne lub mapy dla Twojego konta integracji

1. Tworzenie szablonu płynne próbki w ramach tego przykładu. Płynne szablon definiuje sposób przekształcania danych wejściowych JSON, zgodnie z opisem w tym miejscu:

   ``` json
   {%- assign deviceList = content.devices | Split: ', ' -%}
      {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
      }
   ```
   > [!NOTE]
   > Jeśli szablon płynne wykorzystuje dowolne [filtry](https://shopify.github.io/liquid/basics/introduction/#filters), te filtry musi zaczynać się wielkimi literami. 

2. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

3. W menu głównym Azure wybierz **wszystkie zasoby**. 

4. W polu wyszukiwania Znajdź i wybierz konto integracji.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  Na kafelku konta integracji wybierz **mapy**.

   ![Wybierz mapy](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Wybierz **Dodaj** i Podaj te szczegóły mapy:

   * **Nazwa**: Nazwa mapy, czyli "JsontoJsonTemplate" w tym przykładzie
   * **Mapowanie typu**: typ mapy. Ciąg JSON do przekształcenia JSON, należy zaznaczyć **płynne**.
   * **Mapa**: istniejące płynne szablon lub plik mapowania na potrzeby przekształcania, czyli "SimpleJsonToJsonTemplate.liquid" w tym przykładzie. Aby znaleźć ten plik, można użyć selektora plików.

   ![Dodaj szablon płynne](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Dodaj akcję płynne dla transformacji JSON

1. [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Dodaj [wyzwalacza żądania](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) do aplikacji logiki.

3. Wybierz **+ nowy krok > Dodaj akcję**. W polu wyszukiwania wprowadź *płynne*i wybierz **płynnych - przekształcenie JSON na format JSON**.

   ![Znajdź i wybierz akcję płynne](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. W **zawartości** wybierz opcję **treści** z listy zawartości dynamicznej lub listy parametrów, pojawi się zależności.
  
   ![Wybierz treści](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Z **mapy** wybierz szablon płynny jest "JsonToJsonTemplate" w tym przykładzie.

   ![Wybierz mapy](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Jeśli lista jest pusta, aplikację logiki prawdopodobnie nie jest połączony z kontem integracji. 
   Aby połączyć aplikację logiki konta integracji, które ma płynne szablon lub mapy, wykonaj następujące kroki:

   1. W menu aplikacji logiki, wybierz **ustawienia przepływu pracy**.
   2. Z **wybierz konto integracji** listy, wybierz konto integracji i wybierz **zapisać**.

   ![Łącze aplikacji logiki do konta integracji](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Publikowania danych wejściowych JSON do aplikacji logiki z [Postman](https://www.getpostman.com/postman) lub podobnego narzędzia. Przekształcone dane wyjściowe JSON aplikacji logiki wygląda następująco:
  
![Przykładowe dane wyjściowe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Więcej przykładów płynne akcji
Płynnych nie jest ograniczone do tylko przekształcenia JSON. Poniżej przedstawiono inne akcje dostępne przekształcania, korzystających z płynnych.

* Przekształć JSON do tekstu
  
  Oto płynne szablon używany w tym przykładzie:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:
  
   ![Przykładowe dane wyjściowe JSON do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Przekształć element XML do formatu JSON
  
  Oto płynne szablon używany w tym przykładzie:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykładowe dane wyjściowe XML do ciągu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Przekształcanie XML do tekstu
  
  Oto płynne szablon używany w tym przykładzie:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykładowe dane wyjściowe XML do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw")  
* [Dowiedz się więcej o mapy](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstwa")  

