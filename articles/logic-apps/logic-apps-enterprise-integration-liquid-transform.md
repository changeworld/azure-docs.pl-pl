---
title: Konwertowanie danych JSON za pomocą transformacji cieczy
description: Tworzenie przekształceń lub map dla zaawansowanych przekształceń JSON przy użyciu aplikacji logiki i szablonu Liquid
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879177"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Wykonywanie zaawansowanych przekształceń JSON za pomocą szablonów liquid w usłudze Azure Logic Apps

Podstawowe przekształcenia JSON można wykonywać w aplikacjach logiki za pomocą natywnych akcji operacji danych, takich jak **Compose** lub **Parse JSON**. Aby przeprowadzić zaawansowane transformacje JSON, można tworzyć szablony lub mapy za pomocą [liquidu](https://shopify.github.io/liquid/), który jest językiem szablonów typu open source dla elastycznych aplikacji internetowych. Szablon Liquid definiuje sposób przekształcania danych wyjściowych JSON i obsługuje bardziej złożone przekształcenia JSON, takie jak iteracje, przepływy sterowania, zmienne i tak dalej.

Przed wykonaniem płynnej transformacji w aplikacji logiki, należy najpierw zdefiniować mapowanie JSON do JSON z szablonem Liquid i przechowywać tę mapę na koncie integracji. W tym artykule pokazano, jak utworzyć i używać tego szablonu lub mapy Liquid.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Podstawowe [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Podstawowa wiedza na temat [języka szablonów cieczy](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Tworzenie szablonu lub mapy Liquid dla konta integracyjnego

1. W tym przykładzie utwórz przykładowy szablon cieczy opisany w tym kroku. W szablonie Liquid można użyć [filtrów cieczy,](https://shopify.github.io/liquid/basics/introduction/#filters)które używają konwencji nazewnictwa [DotLiquid](https://github.com/dotliquid/dotliquid) i C#.

   > [!NOTE]
   > Upewnij się, że nazwy filtrów używają *wielkości liter* w szablonie. W przeciwnym razie filtry nie będą działać. Ponadto mapy mają [limity rozmiaru pliku](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

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

1. W [witrynie Azure portal](https://portal.azure.com)— w `integration accounts`polu wyszukiwania platformy Azure wprowadź i wybierz pozycję **Konta integracji**.

   ![Znajdź "Konta integracji"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Znajdź i wybierz swoje konto integracyjne.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. W okienku **Przegląd** w obszarze **Komponenty**wybierz pozycję **Mapy**.

    ![Wybierz kafelek "Mapy"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. W okienku **Mapy** wybierz pozycję **Dodaj** i podaj te szczegóły dla mapy:

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | `JsonToJsonTemplate` | Nazwa mapy, która jest "JsonToJsonTemplate" w tym przykładzie | 
   | **Typ mapy** | **Cieczy** | Typ mapy. W przypadku transformacji JSON do JSON należy wybrać **płynny**plik . | 
   | **Mapę** | `SimpleJsonToJsonTemplate.liquid` | Istniejący szablon cieczy lub plik mapy do użycia do transformacji, który jest "SimpleJsonToJsonTemplate.liquid" w tym przykładzie. Aby znaleźć ten plik, można użyć selektora plików. Aby zapoznać się z limitami rozmiaru mapy, zobacz [Limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Dodaj szablon Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Dodawanie akcji Płynna dla transformacji JSON

1. W witrynie Azure portal wykonaj następujące kroki, aby [utworzyć pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W projektancie aplikacji logiki dodaj [wyzwalacz żądania](../connectors/connectors-native-reqres.md#add-request) do aplikacji logiki.

1. W obszarze wyzwalacza wybierz pozycję **Nowy krok**. W polu wyszukiwania `liquid` wprowadź jako filtr i wybierz tę akcję: **Przekształć JSON do JSON - Liquid**

   ![Znajdowanie i wybieranie akcji Płynna](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Otwórz listę **Mapa** i wybierz szablon Liquid, który jest "JsonToJsonTemplate" w tym przykładzie.

   ![Wybierz mapę](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Jeśli lista map jest pusta, najprawdopodobniej aplikacja logiki nie jest połączona z kontem integracji. 
   Aby połączyć aplikację logiki z kontem integracji z szablonem lub mapą Liquid, wykonaj następujące kroki:

   1. W menu aplikacji logiki wybierz pozycję **Ustawienia przepływu pracy**.

   1. Z listy **Wybierz konto integracji** wybierz konto integracyjne i wybierz pozycję **Zapisz**.

      ![Łączenie aplikacji logiki z kontem integracji](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Teraz dodaj **content** właściwości do tej akcji. Otwórz listę **Dodaj nowy parametr** i wybierz pozycję **Zawartość**.

   ![Dodaj właściwość "Zawartość" do akcji](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Aby ustawić wartość właściwości **Zawartość,** kliknij wewnątrz pola **Zawartość,** aby wyświetlić dynamiczną listę zawartości. Wybierz **Body** token, który reprezentuje dane wyjściowe zawartości treści z wyzwalacza.

   ![Wybierz token "Treść" dla wartości właściwości "Zawartość"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Gdy wszystko będzie gotowe, akcja będzie wyglądać jak następujący przykład:

   ![Zakończono akcję "Transform JSON to JSON"](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Opublikuj dane wejściowe JSON do aplikacji logiki z [listonosza](https://www.getpostman.com/postman) lub podobnego narzędzia. Przekształcone dane wyjściowe JSON z aplikacji logiki wygląda następująco:
  
![Przykładowe dane wyjściowe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Więcej przykładów działań płynnych
Ciecz nie ogranicza się tylko do przekształceń JSON. Oto inne dostępne akcje transformacji, które używają Liquid.

* Przekształcanie JSON w tekst
  
  Oto szablon Liquid używany w tym przykładzie:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Oto przykładowe dane wejściowe i wyjściowe:
  
   ![Przykładowy wynik JSON do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Przekształcanie xml do JSON
  
  Oto szablon Liquid używany w tym przykładzie:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Oto przykładowe dane wejściowe i wyjściowe:

   ![Przykładowy wyjściowy kod XML do JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Przekształcanie formatu XML w tekst
  
  Oto szablon Liquid używany w tym przykładzie:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Oto przykładowe dane wejściowe i wyjściowe:

   ![Przykładowy wyjściowy kod XML do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiecie integracji w przedsiębiorstwie")  
* [Dowiedz się więcej o mapach](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej o mapach integracji przedsiębiorstwa")  

