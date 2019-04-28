---
title: Konwertujące dane JSON przy użyciu transformacji Liquid — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz przekształcenia lub mapy dla zaawansowanych transformacji JSON za pomocą aplikacji logiki i płynnych szablonu
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 5472a8ce2670a34174d6d39f0d90faca8a7002ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467527"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Wykonywać zaawansowane przekształceń JSON i łącznika Liquid szablonów w usłudze Azure Logic Apps

Można wykonać podstawowe przekształceń JSON w aplikacjach logiki, za pomocą danych natywnych operacji akcji takich jak **Compose** lub **Przeanalizuj dane JSON**. Wykonywanie zaawansowanych przekształceń JSON, można utworzyć szablony lub mapy za pomocą [Liquid](https://shopify.github.io/liquid/), który jest językiem szablonu typu open source dla aplikacji sieci web elastyczne. Płynne szablon definiuje sposób przekształcania danych wyjściowych JSON i obsługuje bardziej złożonych przekształceń JSON, takich jak iteracji przepływów sterowania, zmienne i tak dalej. 

Zanim będzie można wykonywać przekształcenia Liquid w aplikacji logiki, należy najpierw zdefiniować za pomocą pliku JSON do formatu JSON mapowania przy użyciu szablonu płynne i magazynu, które mapują na koncie integracji. W tym artykule przedstawiono sposób tworzenia i używania tego szablonu Liquid lub mapy. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Ewentualnie [logowania do subskrypcji płatności](https://azure.microsoft.com/pricing/purchase-options/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Podstawowy [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Podstawową wiedzę na temat o [język Liquid szablonu.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Tworzenie płynnych szablon lub mapy dla konta integracji

1. W tym przykładzie należy utworzyć przykładowy szablon Liquid opisane w tym kroku. W szablonie Liquid służy [filtry cieczy](https://shopify.github.io/liquid/basics/introduction/#filters), wykorzystującymi [DotLiquid](https://dotliquidmarkup.org/) i C# konwencji nazewnictwa. 

   > [!NOTE]
   > Upewnij się, użyj nazwy filtru *zasad stosowania wielkich liter* w szablonie. W przeciwnym razie filtry nie będzie działać.

   ```json
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

2. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure, wybierz **wszystkie zasoby**. W polu wyszukiwania Znajdź i wybierz swoje konto integracji.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  W obszarze **składniki**, wybierz opcję **mapy**.

    ![Wybierz mapy](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Wybierz **Dodaj** i Podaj te szczegóły dla mapy:

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | JsonToJsonTemplate | Nazwa mapy, czyli "JsonToJsonTemplate" w tym przykładzie | 
   | **Typ mapowania** | **liquid** | Typ mapy. Dla formatu JSON do formatu JSON transformacji, musisz wybrać **liquid**. | 
   | **Mapy** | "SimpleJsonToJsonTemplate.liquid" | Istniejącego Liquid szablon lub mapy pliku na potrzeby przekształcania, czyli "SimpleJsonToJsonTemplate.liquid" w tym przykładzie. Aby znaleźć ten plik, można użyć selektora plików. |
   ||| 

   ![Dodaj szablon liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Dodaj akcję Liquid dla transformacji JSON

1. W witrynie Azure portal, wykonaj następujące kroki, aby [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. W Projektancie aplikacji logiki Dodaj [wyzwalacza żądania](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) do aplikacji logiki.

3. W obszarze wyzwalacza wybierz **nowy krok**. 
   W polu wyszukiwania wprowadź "cieczy" jako filtr i wybierz następującą akcję: **Przekształć JSON do formatu JSON — cieczy**

   ![Znajdowanie i wybieranie akcji Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Kliknij wewnątrz **zawartości** tak, aby wyświetlić listę zawartości dynamicznej, a wybierz **treści** tokenu.
  
   ![Wybieranie treści](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Z **mapy** , wybierz szablon Liquid "JsonToJsonTemplate" znajduje się w tym przykładzie na liście.

   ![Wybierz mapę](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Jeśli lista mapy jest pusta, najprawdopodobniej aplikacja logiki nie jest połączony z kontem integracji. 
   Aby połączyć aplikację logiki z kontem integracji, które ma Liquid szablon lub mapy, wykonaj następujące kroki:

   1. W menu aplikacji logiki, wybierz **ustawienia przepływu pracy**.

   2. Z **wybierz konto integracji** wybierz swoje konto integracji i wybierz polecenie **Zapisz**.

      ![Połącz aplikację logiki z kontem integracji](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

Wpis w danych wejściowych JSON do aplikacji logiki z [Postman](https://www.getpostman.com/postman) lub podobnego narzędzia. Przekształcone dane wyjściowe JSON z aplikacji logiki będzie wyglądać następująco:
  
![Przykładowe dane wyjściowe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Więcej przykładów akcji Liquid
Cieczy nie jest ograniczona do tylko przekształceń JSON i łącznika. Poniżej przedstawiono inne akcje dostępnych transformacji, korzystających z płynnych.

* Przekształć JSON do tekstu
  
  Oto Liquid szablon używany w tym przykładzie:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:
  
   ![Przykładowe dane wyjściowe JSON do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Przekształć element XML do formatu JSON
  
  Oto Liquid szablon używany w tym przykładzie:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykładowe dane wyjściowe XML do formatu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Przekształć element XML do tekstu
  
  Oto Liquid szablon używany w tym przykładzie:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykładowe dane wyjściowe XML do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")  
* [Dowiedz się więcej na temat map](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstw")  

