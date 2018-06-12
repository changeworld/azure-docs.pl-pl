---
title: Przekształcanie XML przy użyciu map XSLT - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodaj XSLT mapy do transformacji danych XML przy użyciu usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: e0a8e5dfd42d447b4e049574b346c41fe0067c9f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299141"
---
# <a name="add-maps-for-xml-data-transform"></a>Dodaj mapy dla transformacji danych XML

Integracji przedsiębiorstwa do transformacji danych XML między formatami wykorzystuje mapy. Mapa jest dokument XML, który definiuje dane w dokumencie, który powinien zostać przekształcone w innym formacie. 

## <a name="why-use-maps"></a>Dlaczego warto używać mapy?

Załóżmy, że regularnie B2B zamówień lub faktur od klienta, który używa formatu YYYMMDD dla daty. Jednak w organizacji, przechowywać w formacie MMDDYYY daty. Możesz użyć mapy do *przekształcenie* format daty YYYMMDD do MMDDYYY przed przekazaniem szczegółów zamówienia lub faktury działania bazy danych klienta.


## <a name="how-do-i-create-a-map"></a>Jak utworzyć mapę?

Można tworzyć projektów BizTalk integracji z [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw") dla programu Visual Studio 2015. Następnie można utworzyć pliku Mapa integracji programu, który pozwala wizualnie mapy elementów między dwoma plikami schematu XML. Po utworzeniu tego projektu należy dokument XSLT.

Jeśli mapa zawiera odwołanie do zestawu zewnętrznych, następnie zarówno należy przekazać do konta integracji. Należy je najpierw przekazać w określonej kolejności zestawu, a następnie mapy, który odwołuje się do zestawu.


## <a name="how-do-i-add-a-map"></a>Jak dodać mapy?

1. W portalu Azure wybierz **Przeglądaj**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. W polu filtru wyszukiwania wprowadź **integracji**, a następnie wybierz pozycję **konta integracji** z listy wyników.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Wybierz konto integracji, w której chcesz dodać mapy.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Wybierz **mapy** kafelka.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Po otwarciu strony mapy wybierz **Dodaj**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Wprowadź **nazwa** mapy. Aby przekazać plik mapy, wybierz ikonę folderu w prawej części **mapy** pola tekstowego. Po zakończeniu procesu przekazywania, wybierz **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Po Azure dodaje mapy do swojego konta integracji, zostanie wyświetlony komunikat na ekranie, który pokazuje, czy plik mapy dodano lub nie. Po ten komunikat zostanie wyświetlony, wybierz **mapy** sąsiadująco w celu wyświetlania nowo dodanych mapy.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Jak dodać zestaw?
Otwórz konto integracji, której chcesz przekazać zestawu.

1. Wybierz **zestawy** kafelka.

    ![integrationaccount-assembly-tile](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Po otwarciu strony zestawy wybierz **Dodaj**. Wprowadź **nazwa** Twojego zestawu. Aby przekazać plik zestawu, wybierz ikonę folderu w prawej części **zestawu** pola tekstowego. Po zakończeniu procesu przekazywania, wybierz **OK**.

    ![Dodawanie zestawu](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Jak edytować mapy?

Musisz przekazać nowy plik mapy z żądanych zmian. Można najpierw pobrać mapy do edycji.

Aby przekazać nowy mapy, który zastępuje istniejące mapy, wykonaj następujące kroki.

1. Wybierz **mapy** kafelka.

2. Po otwarciu strony mapy, wybierz mapy, który chcesz edytować.

3. Na **mapy** wybierz pozycję **aktualizacji**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. Selektor plików wybierz plik mapy, który chcesz przekazać, a następnie wybierz **Otwórz**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Jak usunąć mapy?

1. Wybierz **mapy** kafelka.

2. Po otwarciu strony mapy, wybierz mapę, którą chcesz usunąć.

3. Wybierz **usunąć**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Upewnij się, że chcesz usunąć mapy.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw")  
* [Dowiedz się więcej na temat umów](../logic-apps/logic-apps-enterprise-integration-agreements.md "więcej informacji na temat umowy integracji dla przedsiębiorstw")  
* [Dowiedz się więcej o transformacji](logic-apps-enterprise-integration-transform.md "Dowiedz się więcej o przedsiębiorstwie transformacje integracji")  

