---
title: Przekształć kod XML między formatami — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz przekształcenia lub mapy, które konwertują XML między formatami w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.date: 07/08/2016
ms.openlocfilehash: 4ebd96613378bbd907beb5109343a2427b1300b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60427292"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Tworzenie map, które przetwarzają XML między formatami w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Łącznik transformacji integracji przedsiębiorstw konwertuje dane z jednego formatu na inny format. Na przykład masz wiadomości przychodzącej, która zawiera bieżącą datę w formacie YearMonthDay. Przekształcenia można użyć, aby ponownie formatować daty w formacie MonthDayYear.

## <a name="what-does-a-transform-do"></a>Do czego służy przekształcenia?
Przekształcenie, który jest znany także jako mapę, składa się z schematu XML źródła (wejście) i schemat XML docelowego (dane wyjściowe). Różne funkcje wbudowane można użyć w celu manipulowania lub kontrolować dane, tym działań na ciągach, warunkowe przypisania, wyrażenia arytmetyczne, elementy formatujące czasu daty, a nawet pętle.

## <a name="how-to-create-a-transform"></a>Jak utworzyć przekształcenie?
Za pomocą programu Visual Studio można utworzyć przekształcenie/mapę [SDK integracji przedsiębiorstw](https://aka.ms/vsmapsandschemas). Po zakończeniu tworzenia i testowania transformacji, możesz przekazać przekształcenia na koncie integracji. 

## <a name="how-to-use-a-transform"></a>Jak używać transformacji
Po przekazaniu przekształcenie/map na koncie integracji, służy do tworzenia aplikacji logiki. Aplikacja logiki jest uruchamiany przekształceń zawsze wtedy, gdy aplikacja logiki jest wyzwalana (i ma zawartość danych wejściowych, które muszą zostać przekształcone).

**Poniżej przedstawiono czynności w celu przekształcenia**:

### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto integracji i dodać mapę do niego  

Skoro już wykonywane z wymagań wstępnych, nadszedł czas na tworzenie aplikacji logiki:  

1. Tworzenie aplikacji logiki i [połączyć go z kontem integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md "Dowiedz się, jak połączyć konto integracji aplikacji logiki") zawierający mapy.
2. Dodaj **żądania** wyzwalacz aplikacji logiki  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Dodaj **Przekształcanie XML** akcji, wybierając najpierw **Dodaj akcję**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Wprowadź słowo *Przekształcanie* w polu wyszukiwania, aby wszystkie akcje do tego, którego chcesz używać filtrowania  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Wybierz **Przekształcanie XML** akcji   
6. Dodanie pliku XML, **zawartości** , należy przekształcić. Możesz użyć dowolnego dane XML, pojawi się w żądaniu HTTP jako **zawartości**. W tym przykładzie wybierz treści żądania HTTP, która wyzwoliła aplikacji logiki.

   > [!NOTE]
   > Upewnij się, że zawartość **Przekształcanie XML** jest plikiem XML. Jeśli zawartość nie jest w formacie XML lub zakodowane w formacie base64, należy określić wyrażenie, które przetwarza zawartość. Na przykład, można użyć [funkcje](logic-apps-workflow-definition-language.md#functions), takiej jak ```@base64ToBinary``` dekodowania zawartości lub ```@xml``` przetwarzania zawartości w formacie XML.
 

7. Wybierz nazwę **mapy** chcesz użyć do wykonania przekształcenia. Mapa musi być na koncie integracji. W poprzednim kroku już udostępniła usługi dostępu do aplikacji logiki z kontem integracji, która zawiera mapę.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Zapisz swoją pracę  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

W tym momencie po zakończeniu konfigurowania mapy. W przypadku aplikacji rzeczywistych można zapisać przekształcone dane w aplikacji biznesowych, takich jak usługa SalesForce. Możesz z łatwością jako akcję, aby wysłać dane wyjściowe transformacji do usługi Salesforce. 

Teraz możesz przetestować swoje przekształcenia przez zgłaszającą żądanie do punktu końcowego HTTP.  


## <a name="features-and-use-cases"></a>Funkcje i przypadki użycia
* Przekształcenie utworzone w mapie mogą być proste, taką jak kopiowanie nazwy i adresu z jednego dokumentu do innego. Alternatywnie można tworzyć bardziej złożone przekształcenia przy użyciu operacji mapy poza pole.  
* Wiele operacji mapy lub funkcje są łatwo dostępne, w tym ciągi, funkcje czasu daty i tak dalej.  
* Możesz tworzyć kopię danych bezpośrednie między schematów. W mapowania dołączony do zestawu SDK jest to proste i polega na rysowanie linii łączącej elementy w schemacie źródła z ich odpowiedników w schemacie docelowego.  
* Podczas tworzenia mapy, możesz wyświetlić graficzną reprezentację mapa, która pokazuje wszystkie relacje i tworzysz łącza.
* Funkcja mapy testów umożliwia dodawanie przykładowy komunikat XML. Za pomocą prostego kliknięcia możesz przetestować mapy, który został utworzony i zobacz wygenerowanych danych wyjściowych.  
* Przekazywanie istniejącej mapy  
* Obsługuje XML format.

## <a name="advanced-features"></a>Funkcje zaawansowane

### <a name="reference-assembly-or-custom-code-from-maps"></a>Odwołanie do zestawu lub niestandardowego kodu z mapami 
Działania przekształcania również obsługuje mapy lub przekształcenia w odniesieniu do zestawu zewnętrznego. Ta funkcja umożliwia wywołania niestandardowego kodu platformy .NET bezpośrednio z map XSLT. Poniżej przedstawiono wymagania wstępne do użycia zestawu w społeczności maps.

* Mapy i zestaw stanowiący odwołanie z potrzeb mapy jako [przekazane do konta integracji](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapy i zestawu są wymagane do załadowania w określonej kolejności. Przed przekazaniem mapę, która odwołuje się do zestawu, należy przekazać zestawu.

* Mapa musi także mieć tych atrybutów i sekcja CDATA, która zawiera wywołanie do kodu zestawu:

    * **Nazwa** jest nazwą zestawu niestandardowego.
    * **przestrzeń nazw** jest przestrzeń nazw w swoim zestawie, który zawiera kod niestandardowy.

  Ten przykład przedstawia mapę, która odwołuje się do zestawu o nazwie "XslUtilitiesLib" i wywołuje `circumreference` metodę z zestawu.

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


### <a name="byte-order-mark"></a>Znacznika kolejności bajtów
Domyślnie odpowiedzi z transformacja rozpoczyna się za pomocą znacznika kolejności bajtów (BOM). Funkcji tej można skorzystać tylko podczas pracy w edytorze widoku kodu. Aby wyłączyć tę funkcję, należy określić `disableByteOrderMark` dla `transformOptions` właściwości:

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
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")  
* [Dowiedz się więcej na temat map](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstw")  

