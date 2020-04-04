---
title: Dostęp do źródeł danych w środowisku lokalnym
description: Łączenie się z lokalnymi źródłami danych z usługi Azure Logic Apps przez tworzenie lokalnego zasobu bramy danych platformy Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657135"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Connect to on-premises data sources from Azure Logic Apps (Łączenie z lokalnymi źródłami danych z usługi Azure Logic Apps)

Aby uzyskać dostęp do źródeł danych lokalnie z aplikacji logiki, należy utworzyć zasób platformy Azure po [zainstalowaniu *lokalnej bramy danych na* komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). Aplikacje logiki następnie użyć tego zasobu bramy platformy Azure w wyzwalacze i akcje dostarczane przez [łączniki lokalne,](../connectors/apis-list.md#on-premises-connectors) które są dostępne dla usługi Azure Logic Apps.

W tym artykule pokazano, jak utworzyć zasób bramy platformy Azure dla wcześniej [zainstalowanej bramy na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). Aby uzyskać więcej informacji na temat bramy, zobacz [Jak działa brama](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Aby połączyć się z sieciami wirtualnymi platformy Azure, należy rozważyć utworzenie [*środowiska usługi integracji*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zamiast tego. 

Aby uzyskać informacje dotyczące korzystania z bramy z innymi usługami, zobacz następujące artykuły:

* [Lokalna brama danych usługi Microsoft Power Automatyzuj](/power-automate/gateway-reference)
* [Lokalna brama danych usługi Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Lokalna brama danych usługi Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Lokalna brama danych usług Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

W usłudze Azure Logic Apps lokalna brama danych obsługuje [łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) dla tych źródeł danych:

* Serwer BizTalk 2016
* System plików
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Baza danych Oracle
* PostgreSQL
* SAP
* Oprogramowanie SharePoint Server
* SQL Server
* Teradata

Usługa Azure Logic Apps obsługuje operacje odczytu i zapisu za pośrednictwem bramy danych. Jednak te operacje mają [ograniczenia dotyczące ich wielkości ładunku](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Chociaż sama brama nie ponosi dodatkowych kosztów, [model cenowy aplikacji logiki](../logic-apps/logic-apps-pricing.md) ma zastosowanie do tych łączników i innych operacji w usłudze Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* [Lokalna brama danych została już zainstalowana na komputerze lokalnym.](../logic-apps/logic-apps-gateway-install.md)

* Używasz tego samego [konta platformy Azure i subskrypcji,](../logic-apps/logic-apps-gateway-install.md#requirements) który został użyty podczas instalowania tej bramy danych. To konto platformy Azure musi należeć do jednej [dzierżawy lub katalogu usługi Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology)

* Instalacja bramy nie jest jeszcze zarejestrowana i zgłoszone przez inny zasób bramy platformy Azure.

  Podczas tworzenia zasobu bramy w witrynie Azure portal, należy wybrać instalację bramy, która łączy się z zasobem bramy i tylko tego zasobu bramy. W usłudze Azure Logic Apps lokalne wyzwalacze i akcje używają zasobu bramy do łączenia się z lokalnymi źródłami danych. W tych wyzwalaczy i akcji wybierz subskrypcję platformy Azure i skojarzony zasób bramy, który chcesz użyć. Każdy zasób bramy łączy się tylko z jedną instalacją bramy, która łączy tylko jedno konto platformy Azure.

  > [!NOTE]
  > Tylko administrator bramy może utworzyć zasób bramy w witrynie Azure portal. Obecnie jednostki usługi nie są obsługiwane. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Tworzenie zasobu bramy platformy Azure

Po zainstalowaniu bramy na komputerze lokalnym utwórz zasób platformy Azure dla bramy.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu tego samego konta platformy Azure, który został użyty do zainstalowania bramy.

1. W polu wyszukiwania portalu Azure wprowadź "lokalną bramę danych" i wybierz **pozycję Lokalne bramy danych**.

   ![Znajdź "Lokalna brama danych"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. W obszarze **Lokalne bramy danych**wybierz pozycję **Dodaj**.

   ![Dodawanie nowego zasobu platformy Azure dla bramy danych](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. W obszarze **Tworzenie bramy połączenia**podaj te informacje dla zasobu bramy. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   | Właściwość | Opis |
   |----------|-------------|
   | **Nazwa zasobu** | Podaj nazwę zasobu bramy zawierającą tylko litery,`-`cyfry,`_`łączniki (`(`), `)`podkreślenia`.`( ), nawiasy ( , ), lub kropki ( ). |
   | **Subskrypcja** | Wybierz subskrypcję platformy Azure dla konta platformy Azure, który był używany do instalacji bramy. Domyślna subskrypcja jest oparta na koncie platformy Azure, które zostało użyte do zalogowania się. |
   | **Grupa zasobów** | [Grupa zasobów platformy Azure,](../azure-resource-manager/management/overview.md) której chcesz użyć |
   | **Lokalizacja** | Ten sam region lub lokalizacja, która została wybrana dla usługi w chmurze bramy podczas [instalacji bramy](../logic-apps/logic-apps-gateway-install.md). W przeciwnym razie instalacja bramy nie pojawi się na liście **Nazwa instalacji.** Lokalizacja aplikacji logiki może się różnić od lokalizacji zasobów bramy. |
   | **Nazwa instalacji** | Wybierz instalację bramy, która pojawia się na liście tylko wtedy, gdy spełnione są następujące warunki: <p><p>- Instalacja bramy używa tego samego regionu co zasób bramy, który chcesz utworzyć. <br>- Instalacja bramy nie jest połączona z innym zasobem bramy platformy Azure. <br>- Instalacja bramy jest połączona z tym samym kontem platformy Azure, którego używasz do tworzenia zasobu bramy. <br>— Twoje konto platformy Azure należy do jednej [dzierżawy lub katalogu usługi Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) i jest tym samym kontem, które zostało użyte do instalacji bramy. <p><p>Aby uzyskać więcej informacji, zobacz [sekcję Często zadawane pytania.](#faq) |
   |||

   Oto przykład, który pokazuje instalację bramy, która znajduje się w tym samym regionie co zasób bramy i jest połączona z tym samym kontem platformy Azure:

   ![Podaj szczegóły, aby utworzyć zasób bramy danych](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Nawiązywanie połączenia z danymi lokalnymi

Po utworzeniu zasobu bramy i skojarzenia subskrypcji platformy Azure z tym zasobem można teraz utworzyć połączenie między aplikacją logiki a lokalnym źródłem danych przy użyciu bramy.

1. W witrynie Azure portal utwórz lub otwórz aplikację logiki w projektancie aplikacji logiki.

1. Dodaj łącznik, który obsługuje połączenia lokalne, na przykład **SQL Server**.

1. Wybierz **pozycję Połącz za pośrednictwem lokalnej bramy danych**.

1. W obszarze **Bramy**— z listy **Subskrypcje** wybierz subskrypcję platformy Azure, która ma odpowiedni zasób bramy.

1. Na liście **Brama połączenia,** która pokazuje dostępne zasoby bramy w wybranej subskrypcji, wybierz odpowiedni zasób bramy. Każdy zasób bramy jest połączony z instalacją pojedynczej bramy.

   > [!NOTE]
   > Lista bram zawiera zasoby bramy w innych regionach, ponieważ lokalizacja aplikacji logiki może się różnić od lokalizacji zasobu bramy. 

1. Podaj unikatową nazwę połączenia i inne wymagane informacje, które zależą od połączenia, które chcesz utworzyć.

   Unikatowa nazwa połączenia ułatwia późniejsze znalezienie tego połączenia, szczególnie w przypadku utworzenia wielu połączeń. Jeśli ma to zastosowanie, dołącz również kwalifikowaną domenę dla swojej nazwy użytkownika.

   Oto przykład:

   ![Tworzenie połączenia między aplikacją logiki a bramą danych](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Połączenie bramy jest teraz gotowe do użycia aplikacji logiki.

## <a name="edit-connection"></a>Edytowanie połączenia

Aby zaktualizować ustawienia połączenia bramy, można edytować połączenie.

1. Aby znaleźć wszystkie połączenia interfejsu API tylko dla aplikacji logiki, w menu aplikacji logiki w obszarze **Narzędzia programistyczne**wybierz pozycję **Połączenia interfejsu API**.

   ![W menu aplikacji logiki wybierz "Połączenia interfejsu API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Wybierz żądane połączenie bramy, a następnie wybierz pozycję **Edytuj połączenie interfejsu API**.

   > [!TIP]
   > Jeśli aktualizacje nie zostaną zastosowane, spróbuj [zatrzymać i ponownie uruchomić konto usługi systemu Windows bramy](../logic-apps/logic-apps-gateway-install.md#restart-gateway) dla instalacji bramy.

Aby znaleźć wszystkie połączenia interfejsu API skojarzone z subskrypcją platformy Azure:

* Z menu Portal platformy Azure wybierz polecenie **Wszystkie usługi** > Połączenia interfejsu**INTERNETOWEGO** > **API Connections**.
* Z menu Portal platformy Azure wybierz polecenie **Wszystkie zasoby**. Ustaw filtr **Typ** na **Połączenie interfejsu API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Usuwanie zasobu bramy

Aby utworzyć inny zasób bramy, połączyć instalację bramy z innym zasobem bramy lub usunąć zasób bramy, można usunąć zasób bramy bez wpływu na instalację bramy.

1. Z menu portal platformy Azure wybierz pozycję **Wszystkie zasoby**lub wyszukaj i wybierz pozycję **Wszystkie zasoby** z dowolnej strony. Znajdź i wybierz zasób bramy.

1. Jeśli nie jest jeszcze zaznaczona, w menu zasobów bramy wybierz pozycję **Lokalna brama danych**. Na pasku narzędzi zasobu bramy wybierz pozycję **Usuń**.

   Przykład:

   ![Usuwanie zasobu bramy na platformie Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P:** Dlaczego instalacja bramy nie jest wyświetlana podczas tworzenia zasobu bramy na platformie Azure? <br/>
**Odp**.: Ten problem może się zdarzyć z następujących powodów:

* Twoje konto platformy Azure musi być tym samym kontem, które jest połączone z instalacją bramy na komputerze lokalnym. Sprawdź, czy zalogowano się do witryny Azure portal z tą samą tożsamością, która jest połączona z instalacją bramy. Upewnij się również, że twoje konto platformy Azure należy do pojedynczej [dzierżawy lub katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-whatis.md#terminology) i jest ustawione na tę samą dzierżawę lub katalog usługi Azure AD, który był używany podczas instalacji bramy.

* Instalacja zasobu bramy i bramy musi używać tego samego regionu. Jednak lokalizacja aplikacji logiki może się różnić od lokalizacji zasobów bramy.

* Instalacja bramy jest już zarejestrowana i zgłoszone przez inny zasób bramy. Te instalacje nie pojawią się na liście **Nazwa instalacji.** Aby przejrzeć rejestracje bramy w witrynie Azure portal, znajdź wszystkie zasoby platformy Azure, które mają lokalny typ **bramy danych** *we wszystkich* subskrypcjach platformy Azure. Aby odłączyć instalację bramy od innego zasobu bramy, zobacz [Usuwanie zasobu bramy](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dla aplikacji logiki](./logic-apps-examples-and-scenarios.md)
