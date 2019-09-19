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
ms.date: 09/01/2019
ms.openlocfilehash: dcb0fe4da968408a261e387c636cc548fa757a09
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036652"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Nawiązywanie połączenia z lokalnymi źródłami danych z Azure Logic Apps

Aby uzyskać dostęp do źródeł danych w środowisku lokalnym z poziomu aplikacji logiki, utwórz zasób lokalnej bramy danych w Azure Portal. Aplikacje logiki mogą następnie używać [łączników lokalnych](../connectors/apis-list.md#on-premises-connectors). W tym artykule przedstawiono sposób tworzenia zasobu bramy platformy Azure *po* [pobraniu i zainstalowaniu bramy na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). Aby uzyskać więcej informacji o bramie, zobacz [jak działa Brama](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> W celu nawiązania połączenia z sieciami wirtualnymi platformy Azure Rozważ utworzenie [*środowiska usługi integracji*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Aby uzyskać informacje o sposobach korzystania z bramy z innymi usługami, zobacz następujące artykuły:

* [Lokalna Brama danych Power BI firmy Microsoft](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow lokalnej bramy danych](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps lokalnej bramy danych](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

W przypadku Azure Logic Apps lokalna Brama danych obsługuje [Łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) dla następujących źródeł danych:

* BizTalk Server 2016
* System plików
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* Oprogramowanie SharePoint Server
* SQL Server
* Teradata

Mimo że sama Brama nie wiąże się z dodatkowymi kosztami, [model cenowy Logic Apps](../logic-apps/logic-apps-pricing.md) ma zastosowanie do tych łączników i innych operacji w Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* [Lokalna Brama danych została już zainstalowana na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md).

* Masz to [samo konto platformy Azure i subskrypcję platformy Azure](../logic-apps/logic-apps-gateway-install.md#requirements) , które zostały użyte podczas instalacji lokalnej bramy danych.

* Nie połączono wcześniej instalacji bramy z innym zasobem bramy na platformie Azure.

  Podczas tworzenia zasobu bramy należy wybrać instalację bramy do skojarzenia z zasobem bramy. Instalacja już połączonej bramy nie jest dostępna do wyboru podczas tworzenia zasobów bramy.

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
   | **Nazwa zasobu** | Nazwa zasobu bramy, która może zawierać tylko litery`-`, cyfry, łączniki (), podkreślenia (`_`), nawiasy (`(`, `)`) i kropki (`.`). |
   | **Subskrypcja** | Twoja subskrypcja platformy Azure, która musi być taka sama jak instalacja bramy i aplikacja logiki. Domyślna subskrypcja jest oparta na koncie platformy Azure użytym do zalogowania się. |
   | **Grupa zasobów** | [Grupa zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) , której chcesz użyć |
   | **Location** | Ten sam region, który został wybrany dla usługi bramy w chmurze podczas [instalacji bramy](../logic-apps/logic-apps-gateway-install.md). W przeciwnym razie instalacja bramy nie zostanie wyświetlona na liście **Nazwa instalacji** do wybrania. Lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy. |
   | **Nazwa instalacji** | Jeśli instalacja bramy nie została jeszcze wybrana, wybierz wcześniej zainstalowaną bramę. Wcześniej połączone instalacje bramy nie będą wyświetlane na tej liście, aby można było wybrać. |
   |||

   Oto przykład:

   ![Podaj szczegóły, aby utworzyć lokalną bramę danych](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Nawiązywanie połączenia z danymi lokalnymi

Po utworzeniu zasobu bramy i skojarzeniu subskrypcji platformy Azure z tym zasobem możesz teraz utworzyć połączenie między aplikacją logiki i lokalnym źródłem danych przy użyciu bramy.

1. W Azure Portal Utwórz lub Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Dodaj łącznik obsługujący połączenia lokalne, na przykład **SQL Server**.

1. Wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**. 

1. W przypadku **bram**wybierz utworzony zasób bramy.

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

1. Aby znaleźć wszystkie połączenia interfejsu API tylko dla aplikacji logiki, w menu aplikacji logiki w obszarze **Narzędzia programistyczne**wybierz pozycję **połączenia interfejsu API**.
   
   ![W menu aplikacji logiki wybierz pozycję "połączenia interfejsu API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Wybierz połączenie bramy, którego chcesz użyć, a następnie wybierz pozycję **Edytuj połączenie z interfejsem API**.

   > [!TIP]
   > Jeśli aktualizacje nie zaczną obowiązywać, spróbuj [zatrzymać i ponownie uruchomić konto usługi bramy systemu Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) na potrzeby instalacji bramy.

Aby znaleźć wszystkie połączenia interfejsu API skojarzone z subskrypcją platformy Azure: 

* W głównym menu platformy Azure przejdź do pozycji **wszystkie usługi** >  > **połączenia internetowego interfejsu API**.
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

**P**: Dlaczego nie widzę mojej instalacji bramy podczas tworzenia zasobu bramy na platformie Azure? <br/>
**Odp.:** Ten problem może wystąpić z następujących powodów:

* Instalacja bramy została już zarejestrowana i przejęta przez inny zasób bramy na platformie Azure. Instalacje bramy nie pojawiają się na liście wystąpień po utworzeniu zasobów bramy. Aby sprawdzić rejestracje bramy w Azure Portal, przejrzyj wszystkie zasoby platformy Azure z typem **lokalnych bram danych** dla *wszystkich* subskrypcji platformy Azure.

* Tożsamość usługi Azure AD dla osoby, która zainstalowała bramę, różni się od osoby, która zarejestrowano w Azure Portal. Sprawdź, czy zalogowano się za pomocą tej samej tożsamości, na której zainstalowano bramę.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dotyczące usługi Logic Apps](./logic-apps-examples-and-scenarios.md)
