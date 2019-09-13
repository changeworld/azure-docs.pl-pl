---
title: Konwertowanie danych JSON przy użyciu transformacji płynnej — Azure Logic Apps | Microsoft Docs
description: Tworzenie transformacji lub map dla zaawansowanych przekształceń JSON przy użyciu szablonu Logic Apps i płynu
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 203c57a2755a3287566a774e2878a87b847337b9
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900656"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Wykonaj zaawansowane przekształcenia JSON przy użyciu szablonów płynnych w Azure Logic Apps

W aplikacjach logiki można wykonywać podstawowe przekształcenia JSON z akcjami operacji danych natywnych, takimi jak **Redaguj** lub **Analizuj dane JSON**. Aby przeprowadzić zaawansowane przekształcenia JSON, możesz tworzyć szablony lub mapy z [płynnością](https://shopify.github.io/liquid/), czyli językiem szablonu Open-Source dla elastycznych aplikacji sieci Web. Szablon płynu definiuje sposób przekształcania danych wyjściowych JSON i obsługuje bardziej złożone przekształcenia JSON, takie jak iteracje, przepływy sterowania, zmienne i tak dalej. 

Aby można było wykonać transformację płynną w aplikacji logiki, należy najpierw zdefiniować mapowanie JSON na format JSON z szablonem płynnym i sklepem, który jest mapowany na koncie integracji. W tym artykule przedstawiono sposób tworzenia i używania tego szablonu lub mapy ciekłej. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Możesz też [zarejestrować się, aby uzyskać subskrypcję z opcją płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/purchase-options/)użyciem.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Podstawowe [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Podstawowa wiedza na temat [języka z szablonem płynnym.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Utwórz szablon płynny lub mapę dla konta integracji

1. Na potrzeby tego przykładu Utwórz przykładowy szablon płynny opisany w tym kroku. W szablonie płynnym można używać [filtrów ciekłych](https://shopify.github.io/liquid/basics/introduction/#filters), które używają [DotLiquid](https://dotliquidmarkup.org/) i C# konwencji nazewnictwa. 

   > [!NOTE]
   > Upewnij się, że nazwy filtrów używają *wielkości liter* w szablonie. W przeciwnym razie filtry nie będą działały.

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

2. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure wybierz pozycję **wszystkie zasoby**. W polu wyszukiwania Znajdź i wybierz swoje konto integracji.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  W obszarze **składniki**wybierz pozycję **Maps**.

    ![Wybieranie map](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Wybierz pozycję **Dodaj** i podaj następujące szczegóły dotyczące mapy:

   | Właściwość | Value | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | JsonToJsonTemplate | Nazwa mapy, która jest w tym przykładzie "JsonToJsonTemplate" | 
   | **Typ mapy** | **obrotow** | Typ mapy. W przypadku transformacji JSON do formatu JSON należy wybrać pozycję **Liquid**. | 
   | **Zmapować** | "SimpleJsonToJsonTemplate.liquid" | Istniejący szablon płynu lub plik mapy do użycia na potrzeby transformacji, czyli "SimpleJsonToJsonTemplate. Liquid" w tym przykładzie. Aby znaleźć ten plik, można użyć selektora plików. |
   ||| 

   ![Dodaj szablon płynny](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Dodawanie akcji ciekłej dla transformacji JSON

1. W Azure Portal wykonaj następujące kroki, aby [utworzyć pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. W Projektancie aplikacji logiki Dodaj [wyzwalacz żądania](../connectors/connectors-native-reqres.md#add-request) do aplikacji logiki.

3. W obszarze wyzwalacza wybierz pozycję **nowy krok**. 
   W polu wyszukiwania wprowadź "Liquid" jako filtr, a następnie wybierz tę akcję: **Przekształć JSON do formatu JSON-Liquid**

   ![Znajdź i wybierz akcję ciekłej](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Kliknij wewnątrz pola **zawartość** , aby wyświetlić listę zawartości dynamicznej, a następnie wybierz pozycję token **treści** .
  
   ![Wybierz treść](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Z listy **Mapa** wybierz swój szablon płynny, czyli "JsonToJsonTemplate" w tym przykładzie.

   ![Wybieranie mapy](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Jeśli lista Maps jest pusta, najprawdopodobniej Twoja aplikacja logiki nie jest połączona z kontem integracji. 
   Aby połączyć aplikację logiki z kontem integracji, które ma szablon lub mapę płynną, wykonaj następujące kroki:

   1. W menu aplikacji logiki wybierz pozycję **Ustawienia przepływu pracy**.

   2. Na liście **Wybierz konto integracji** wybierz swoje konto integracji i wybierz pozycję **Zapisz**.

      ![Połącz aplikację logiki z kontem integracji](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Wyślij dane wejściowe JSON do aplikacji logiki z [](https://www.getpostman.com/postman) programu Poster lub podobnego narzędzia. Przekształcone dane wyjściowe JSON z aplikacji logiki wyglądają podobnie jak w tym przykładzie:
  
![Przykładowe dane wyjściowe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Więcej przykładowych akcji w postaci ciekłej
Płyn nie jest ograniczony tylko do transformacji JSON. Oto inne dostępne akcje transformacji, które używają cieczy.

* Przekształć JSON do tekstu
  
  Oto szablon płynu używany do tego przykładu:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:
  
   ![Przykład danych wyjściowych JSON do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Przekształć element XML do formatu JSON
  
  Oto szablon płynu używany do tego przykładu:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykład danych wyjściowych XML do formatu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Przekształć XML do tekstu
  
  Oto szablon płynu używany do tego przykładu:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykład danych wyjściowych XML do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw] (../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw")  
* [Dowiedz się więcej o mapach] (../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstwa")  

