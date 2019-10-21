---
title: Dostęp do źródeł danych w środowisku lokalnym z Azure Logic Apps
description: Nawiązywanie połączenia z lokalnymi źródłami danych z usługi Logic Apps przez tworzenie lokalnej bramy danych
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/18/2019
ms.openlocfilehash: 4b333df740fbd4c2243ea3f166593ca0a6f4bbad
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675674"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Nawiązywanie połączenia z lokalnymi źródłami danych z Azure Logic Apps

Przed uzyskaniem dostępu do źródeł danych w środowisku lokalnym z poziomu aplikacji logiki należy utworzyć zasób platformy Azure po [zainstalowaniu lokalnej *bramy danych* na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). Aplikacje logiki używają tego zasobu bramy platformy Azure w wyzwalaczach i akcjach udostępnianych przez [Łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) dostępne dla Azure Logic Apps.

W tym artykule przedstawiono sposób tworzenia zasobu bramy platformy Azure dla wcześniej [zainstalowanej bramy na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). Aby uzyskać więcej informacji o bramie, zobacz [jak działa Brama](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> W celu nawiązania połączenia z sieciami wirtualnymi platformy Azure Rozważ utworzenie [*środowiska usługi integracji*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Aby uzyskać informacje o sposobach korzystania z bramy z innymi usługami, zobacz następujące artykuły:

* [Lokalna Brama danych Power BI firmy Microsoft](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow lokalnej bramy danych](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps lokalnej bramy danych](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

W Azure Logic Apps lokalna Brama danych obsługuje [Łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) dla tych źródeł danych:

* BizTalk Server 2016
* System plików
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Baza danych Oracle
* PostgreSQL
* SAP
* SharePoint Server
* Oprogramowanie SQL Server
* Teradata

Azure Logic Apps obsługuje operacje odczytu i zapisu za pomocą bramy danych. Jednak te operacje mają [limity rozmiaru ładunku](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Mimo że sama Brama nie wiąże się z dodatkowymi kosztami, [model cenowy Logic Apps](../logic-apps/logic-apps-pricing.md) ma zastosowanie do tych łączników i innych operacji w Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* [Lokalna Brama danych została już zainstalowana na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md).

* Masz to [samo konto i subskrypcję platformy Azure](../logic-apps/logic-apps-gateway-install.md#requirements) , które zostało użyte podczas instalowania tej bramy danych.

* Instalacja bramy nie została już zarejestrowana i nie została przejęta przez inny zasób bramy platformy Azure.

  Podczas tworzenia zasobu bramy w Azure Portal należy wybrać instalację bramy, która łączy się z zasobem bramy i tylko z tym zasobem bramy. W Azure Logic Apps lokalne wyzwalacze i akcje używają zasobu bramy do łączenia się z lokalnymi źródłami danych. W tych wyzwalaczach i akcjach wybierasz subskrypcję platformy Azure i skojarzony zasób bramy, który ma być używany. Każdy zasób bramy łączy się tylko z jedną instalacją bramy, która łączy tylko z jednym kontem platformy Azure.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Utwórz zasób bramy platformy Azure

Po zainstalowaniu bramy na komputerze lokalnym Utwórz zasób platformy Azure dla bramy.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tego samego konta platformy Azure, które zostało użyte do zainstalowania bramy.

1. W polu wyszukiwania Azure Portal wprowadź wartość "lokalna Brama danych", a następnie wybierz pozycję **lokalne bramy danych**.

   ![Znajdź "lokalna Brama danych"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. W obszarze **lokalne bramy danych**wybierz pozycję **Dodaj**.

   ![Dodawanie bramy danych](./media/logic-apps-gateway-connection/add-gateway.png)

1. W obszarze **Utwórz bramę połączenia**podaj te informacje dla zasobu bramy. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   | Właściwość | Opis |
   |----------|-------------|
   | **Nazwa zasobu** | Podaj nazwę zasobu bramy, która zawiera tylko litery, cyfry, łączniki (`-`), podkreślenia (`_`), nawiasy (`(`, `)`) lub kropki (`.`). |
   | **Subskrypcja** | Wybierz subskrypcję platformy Azure dla konta platformy Azure, która była używana na potrzeby instalacji bramy. Domyślna subskrypcja jest oparta na koncie platformy Azure użytym do zalogowania się. |
   | **Grupa zasobów** | [Grupa zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) , której chcesz użyć |
   | **Lokalizacja** | Ten sam region lub lokalizacja, która została wybrana dla usługi bramy w chmurze podczas [instalacji bramy](../logic-apps/logic-apps-gateway-install.md). W przeciwnym razie instalacja bramy nie zostanie wyświetlona na liście **Nazwa instalacji** . Lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy. |
   | **Nazwa instalacji** | Wybierz instalację bramy, która zostanie wyświetlona na liście tylko wtedy, gdy spełnione są następujące warunki: <p><p>-Znajduje się w tym samym regionie co zasób bramy, który chcesz utworzyć <br>-Odłączenie do innego zasobu bramy platformy Azure <br>— Połączony z tym samym kontem platformy Azure, którego używasz do tworzenia zasobu bramy <p><p>Aby uzyskać więcej informacji, zobacz sekcję [często zadawane pytania](#faq) . |
   |||

   Oto przykład, w którym jest wyświetlana instalacja bramy znajdująca się w tym samym regionie, co zasób bramy i jest połączona z tym samym kontem platformy Azure:

   ![Podaj szczegóły w celu utworzenia zasobu bramy danych](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Nawiązywanie połączenia z danymi lokalnymi

Po utworzeniu zasobu bramy i skojarzeniu subskrypcji platformy Azure z tym zasobem możesz teraz utworzyć połączenie między aplikacją logiki i lokalnym źródłem danych przy użyciu bramy.

1. W Azure Portal Utwórz lub Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Dodaj łącznik obsługujący połączenia lokalne, na przykład **SQL Server**.

1. Wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**.

1. W obszarze **bramy**z listy **subskrypcje** wybierz subskrypcję platformy Azure, dla której chcesz utworzyć żądany zasób bramy.

1. Z listy **brama połączeń** , która wyświetla dostępne zasoby bramy w wybranej subskrypcji, wybierz żądany zasób bramy. Każdy zasób bramy jest połączony z instalacją pojedynczej bramy.

   > [!NOTE]
   > Lista bram zawiera zasoby bramy w innych regionach, ponieważ lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy. 

1. Podaj unikatową nazwę połączenia i inne wymagane informacje, które są zależne od połączenia, które chcesz utworzyć.

   Unikatowa nazwa połączenia ułatwia znalezienie tego połączenia później, szczególnie w przypadku tworzenia wielu połączeń. Jeśli ma to zastosowanie, Uwzględnij także kwalifikowaną domenę dla nazwy użytkownika.

   Oto przykład:

   ![Utwórz połączenie między aplikacją logiki a bramą danych](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Połączenie bramy jest teraz gotowe do użycia przez aplikację logiki.

## <a name="edit-connection"></a>Edytuj połączenie

Aby zaktualizować ustawienia dla połączenia bramy, można edytować połączenie.

1. Aby znaleźć wszystkie połączenia interfejsu API tylko dla aplikacji logiki, w menu aplikacji logiki, w obszarze **Narzędzia programistyczne**wybierz pozycję **połączenia interfejsu API**.

   ![W menu aplikacji logiki wybierz pozycję "połączenia interfejsu API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Wybierz połączenie bramy, którego chcesz użyć, a następnie wybierz pozycję **Edytuj połączenie z interfejsem API**.

   > [!TIP]
   > Jeśli aktualizacje nie zaczną obowiązywać, spróbuj [zatrzymać i ponownie uruchomić konto usługi bramy systemu Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) na potrzeby instalacji bramy.

Aby znaleźć wszystkie połączenia interfejsu API skojarzone z subskrypcją platformy Azure:

* W głównym menu platformy Azure przejdź do pozycji **wszystkie usługi**  > **połączenia interfejsu API** > **sieci Web** .
* Lub w głównym menu platformy Azure przejdź do pozycji **wszystkie zasoby**. Ustaw filtr **typu** dla **połączenia interfejsu API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Usuń zasób bramy

Aby utworzyć inny zasób bramy, Połącz instalację bramy z innym zasobem bramy lub Usuń zasób bramy, a następnie usuń zasób bramy bez wpływu na instalację bramy.

1. Z głównego menu platformy Azure wybierz pozycję **wszystkie zasoby**. Znajdź i wybierz zasób bramy.

1. Jeśli jeszcze tego nie zrobiono, w menu zasobów bramy wybierz pozycję **lokalna Brama danych**. Na pasku narzędzi zasobów bramy wybierz pozycję **Usuń**.

   Na przykład:

   ![Usuń bramę](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P**: Dlaczego nie jest wyświetlana żadna instalacja bramy podczas tworzenia zasobu bramy na platformie Azure? <br/>
Odp.: ten problem może wystąpić **z**następujących powodów:

* Twoje konto platformy Azure musi być tym samym kontem, które jest połączone z instalacją bramy na komputerze lokalnym. Sprawdź, czy zalogowano się do Azure Portal z taką samą tożsamością, która jest połączona z instalacją bramy.

* Instalacja zasobu bramy i bramy musi być używana w tym samym regionie. Jednak lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy.

* Instalacja bramy została już zarejestrowana i przejęta przez inny zasób bramy. Te instalacje nie będą wyświetlane na liście **Nazwa instalacji** . Aby przejrzeć rejestracje bramy w Azure Portal, Znajdź wszystkie zasoby platformy Azure, które mają typ **lokalnych bram danych** w ramach *wszystkich* subskrypcji platformy Azure. Aby odłączyć instalację bramy od innego zasobu bramy, zobacz [usuwanie zasobu bramy](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dotyczące usługi Logic Apps](./logic-apps-examples-and-scenarios.md)
