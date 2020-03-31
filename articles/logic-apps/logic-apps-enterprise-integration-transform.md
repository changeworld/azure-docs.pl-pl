---
title: Przekształcanie formatu XML między formatami
description: Tworzenie przekształceń lub map konwertowania formatów XML między formatami w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792383"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Tworzenie map, które przekształcają formaty XML między formatami w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack

Łącznik transformacji integracji przedsiębiorstwa konwertuje dane z jednego formatu na inny. Na przykład może mieć wiadomość przychodzącą, która zawiera bieżącą datę w formacie YearMonthDay. Transformacja służy do formatowania daty w formacie MonthDayYear.

## <a name="what-does-a-transform-do"></a>Do czego robi się transformacja?
A Transform, który jest również znany jako mapa, składa się ze źródłowego schematu XML (dane wejściowe) i docelowego schematu XML (dane wyjściowe). Można użyć różnych wbudowanych funkcji, aby ułatwić manipulowanie lub kontrolowanie danych, w tym manipulacje ciągami, przypisania warunkowe, wyrażenia arytmetyczne, formatery daty i godziny, a nawet konstrukcje pętli.

## <a name="how-to-create-a-transform"></a>Jak stworzyć transformację?
Transformację/mapę można utworzyć za pomocą [zestawu SDK integracji](https://aka.ms/vsmapsandschemas)programu Visual Studio Enterprise . Po zakończeniu tworzenia i testowania transformacji, przekazać transformację do konta integracji. 

## <a name="how-to-use-a-transform"></a>Jak korzystać z transformacji
Po przesłaniu transformacji/mapy do konta integracji, można go użyć do utworzenia aplikacji logiki. Aplikacja Logika uruchamia przekształcenia za każdym razem, gdy aplikacja logiki jest wyzwalana (i istnieje zawartość wejściowa, która musi zostać przekształcona).

**Oto kroki, aby użyć transformacji:**

### <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie konta integracji i dodawanie do niego mapy  

Teraz, gdy już zadbasz o wymagania wstępne, nadszedł czas, aby utworzyć aplikację logiki:  

1. Utwórz aplikację logiki i [połącz ją z kontem integracji,](../logic-apps/logic-apps-enterprise-integration-accounts.md "Dowiedz się, jak połączyć konto integracji z aplikacją Logika") które zawiera mapę.
2. Dodawanie wyzwalacza **żądania** do aplikacji Logika  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Dodaj akcję **Przekształć XML,** wybierając najpierw pozycję **Dodaj akcję**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Wprowadź *przekształcenie* wyrazu w polu wyszukiwania, aby filtrować wszystkie akcje do tego, którego chcesz użyć  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Wybierz akcję **Przekształcanie XML**   
6. Dodaj przekształconą **zawartość** XML. Jako **zawartość**można użyć dowolnych danych XML otrzymanych w żądaniu HTTP. W tym przykładzie wybierz treść żądania HTTP, które wyzwoliło aplikację Logika.

   > [!NOTE]
   > Upewnij się, że zawartość pliku **XML transformacji** to XML. Jeśli zawartość nie jest w formacie XML lub jest zakodowana w formacie base4, należy określić wyrażenie, które przetwarza zawartość. Na przykład można [functions](logic-apps-workflow-definition-language.md#functions)użyć funkcji ```@base64ToBinary``` , takich ```@xml``` jak dekodowanie zawartości lub przetwarzania zawartości jako XML.
 

7. Wybierz nazwę **map,** który ma być używany do przeprowadzenia transformacji. Mapa musi być już na koncie integracji. We wcześniejszym kroku już nadałeś aplikacji logiki dostęp do konta integracji, które zawiera mapę.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Zapisz wyniki dotychczasowej pracy  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

W tym momencie zakończono konfigurowanie mapy. W rzeczywistych aplikacjach można przechowywać przekształcone dane w aplikacji LOB, takiej jak SalesForce. Można łatwo jako akcja, aby wysłać dane wyjściowe transformacji do Salesforce. 

Teraz można przetestować transformację, wysyłając żądanie do punktu końcowego HTTP.  


## <a name="features-and-use-cases"></a>Funkcje i przypadki użycia
* Transformacja utworzona na mapie może być prosta, na przykład kopiowanie nazwy i adresu z jednego dokumentu do drugiego. Można też tworzyć bardziej złożone przekształcenia przy użyciu gotowych operacji mapy.  
* Wiele operacji mapy lub funkcje są łatwo dostępne, w tym ciągi, funkcje daty i tak dalej.  
* Można wykonać bezpośrednią kopię danych między schematami. W mapera zawarte w zestawie SDK jest to tak proste, jak rysowanie linii, która łączy elementy w schemacie źródłowym z ich odpowiednikami w schemacie docelowym.  
* Podczas tworzenia mapy można wyświetlić graficzną reprezentację mapy, która pokazuje wszystkie utworzone relacje i łącza.
* Użyj funkcji Test Map, aby dodać przykładowy komunikat XML. Za pomocą jednego kliknięcia można przetestować utworzoną mapę i wyświetlić wygenerowane dane wyjściowe.  
* Przekazywanie istniejących map  
* Obejmuje obsługę formatu XML.

## <a name="advanced-features"></a>Funkcje zaawansowane

### <a name="reference-assembly-or-custom-code-from-maps"></a>Zestaw referencyjny lub niestandardowy kod z map 
Akcja transformacji obsługuje również mapy lub przekształca w odniesieniu do zgromadzeń zewnętrznych. Ta funkcja umożliwia wywołania niestandardowego kodu platformy .NET bezpośrednio z map XSLT. Oto wymagania wstępne do użycia zestawu na mapach.

* Mapa i zestaw, do którego odwołuje się mapa, muszą zostać [przesłane na konto integracji.](./logic-apps-enterprise-integration-maps.md) 

  > [!NOTE]
  > Mapa i zestaw muszą zostać przesłane w określonej kolejności. Należy przekazać zestaw przed przekazaniem mapy, która odwołuje się do zestawu.

* Mapa musi mieć również te atrybuty i sekcję CDATA, która zawiera wywołanie kodu zestawu:

    * **nazwa** jest nazwą zestawu niestandardowego.
    * **obszar nazw** jest obszar nazw w zestawie, który zawiera kod niestandardowy.

  W tym przykładzie pokazano mapę, która odwołuje się do zestawu `circumreference` o nazwie "XslUtilitiesLib" i wywołuje metodę z zestawu.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Znacznik zamówienia bajtów
Domyślnie odpowiedź z transformacji rozpoczyna się od znaku kolejności bajtów (BOM). Dostęp do tej funkcji można uzyskać tylko podczas pracy w edytorze widoku kodu. Aby wyłączyć tę funkcję, należy określić `disableByteOrderMark` `transformOptions` dla właściwości:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Dowiedz się więcej
* [Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiecie integracji w przedsiębiorstwie")  
* [Dowiedz się więcej o mapach](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej o mapach integracji przedsiębiorstwa")  

