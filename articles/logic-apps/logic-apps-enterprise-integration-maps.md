---
title: Przekształć element XML przy użyciu map XSLT — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodawanie map XSLT, które przekształcać pliki XML w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 07/08/2016
ms.openlocfilehash: c5e5e0a0a3f8bd5feedc00d5bbfb76a1453ccc84
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123560"
---
# <a name="add-maps-for-xml-transformation-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dodawanie map na potrzeby transformacje XML w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Integracja dla przedsiębiorstw przy użyciu map do przekształcania danych XML między formatami. Mapa jest dokument XML, który definiuje dane w dokumencie, który powinien zostać przekształcone na inny format. 

## <a name="why-use-maps"></a>Dlaczego warto używać mapy?

Załóżmy, że regularnie zleceń B2B lub faktur od klienta, który używa formatu YYYMMDD dat. Jednak w Twojej organizacji, możesz przechowywać daty w formacie MMDDYYY. Możesz użyć mapy do *Przekształcanie* format daty YYYMMDD do MMDDYYY przed zapisaniem szczegóły zamówienia lub faktury w bazie danych działań klientów.


## <a name="how-do-i-create-a-map"></a>Jak utworzyć mapę?

Możesz tworzyć projekty integracji BizTalk przy użyciu [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw") dla programu Visual Studio 2015. Następnie można utworzyć pliku Mapa integracji programu, który pozwala wizualnie mapować elementy między dwoma plikami schematu XML. Po skompilowaniu projektu, trzeba będzie dokument XSLT.

Jeśli mapa zawiera odwołanie do zestawu zewnętrznego, a następnie obie muszą być przesłane do konta integracji. Powinny one być przekazany w określonej kolejności, najpierw zestawu, a następnie mapę, która odwołuje się do zestawu.


## <a name="how-do-i-add-a-map"></a>Jak dodać mapę?

1. W witrynie Azure portal wybierz **Przeglądaj**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. W polu wyszukiwania filtr wprowadź **integracji**, a następnie wybierz **kont integracji** z listy wyników.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Wybierz konto integracji, w której chcesz dodać mapę.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Wybierz **mapy** kafelka.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Po otwarciu strony mapy wybierz **Dodaj**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Wprowadź **nazwa** mapy. Aby przekazać plik mapy, wybierz ikonę folderu po prawej stronie **mapy** pola tekstowego. Po zakończeniu procesu przekazywania wybierz **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Po platforma Azure dodaje mapy z kontem integracji, zostanie wyświetlony komunikat na ekranie, który pokazuje, czy plik mapy został dodany, czy nie. Po otrzymaniu tej wiadomości, wybierz **mapy** Kafelek, aby można było wyświetlić nowo dodanych mapy.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Jak dodać zestaw?
Otwórz konto integracji, które chcesz przekazać zestawu.

1. Wybierz **zestawy** kafelka.

    ![integrationaccount-assembly-tile](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Po otwarciu strony zestawów wybierz **Dodaj**. Wprowadź **nazwa** dla Twojego zestawu. Aby przekazać plik zestawu, wybierz ikonę folderu po prawej stronie **zestawu** pola tekstowego. Po zakończeniu procesu przekazywania wybierz **OK**.

    ![Dodawanie zestawu](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Jak edytować mapę?

Musisz przekazać nowy plik mapy ze zmianami, które chcesz. Można najpierw pobrać mapy do edycji.

Aby przekazać nowej mapy, która zastępuje istniejącą mapę, wykonaj następujące kroki.

1. Wybierz **mapy** kafelka.

2. Po otwarciu strony mapy Wybierz mapę, która ma być edytowany.

3. Na **mapy** wybierz **aktualizacji**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. W selektorze plików wybierz plik mapy, który chcesz przekazać, a następnie wybierz **Otwórz**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Jak usunąć mapy?

1. Wybierz **mapy** kafelka.

2. Po otwarciu strony mapy Wybierz mapę, którą chcesz usunąć.

3. Wybierz **Usuń**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Upewnij się, że chcesz usunąć mapy.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")  
* [Dowiedz się więcej o umowach dotyczących](../logic-apps/logic-apps-enterprise-integration-agreements.md "Dowiedz się więcej o umowach dotyczących integracji przedsiębiorstw")  
* [Dowiedz się więcej na temat przekształceń](logic-apps-enterprise-integration-transform.md "więcej informacji na temat transformacji integracji przedsiębiorstw")  

