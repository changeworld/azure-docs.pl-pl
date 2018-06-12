---
title: Dostęp do źródła danych na lokalnym dla usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Skonfiguruj bramę danych lokalnych, dostępu do źródeł danych w obiektach z aplikacji logiki
keywords: dostęp do danych lokalnych, transfer danych, szyfrowania, źródła danych
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 0bf51f22e41ec78ef1dca7cba7bd5e26cbe1d969
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300010"
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>Nawiązywać połączenia ze źródłami danych lokalnie z aplikacji logiki za pomocą bramy danych lokalnych

Dostęp do źródeł danych w obiektach z aplikacji logiki, należy skonfigurować bramę danych lokalnych, używanego przez aplikacje logiki z obsługiwanych łączników. Brama działa jako mostka zapewnia transfer danych szybki i szyfrowanie między źródłami danych lokalnie i aplikacje logiki. Brama przekazuje dane z lokalnych źródeł w kanałach zaszyfrowane za pomocą usługi Azure Service Bus. Cały ruch pochodzi jako bezpieczny ruch wychodzący z agentem bramy. Dowiedz się więcej o [działanie bramy danych](logic-apps-gateway-install.md#gateway-cloud-service). 

Brama obsługuje połączenia z tych źródeł danych na lokalnym:

*   BizTalk Server 2016
*   DB2  
*   System plików
*   Informix
*   MQ
*   MySQL
*   Baza danych Oracle
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   Sharepoint
*   Oprogramowanie SQL Server
*   Teradata

Te kroki pokazują, jak skonfigurować bramę danych lokalnych do pracy z aplikacji logiki. Aby uzyskać więcej informacji o obsługiwanych łączników, zobacz [łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md). 

Aby uzyskać informacje o sposobie używania bramy z innymi usługami, zobacz następujące artykuły:

*   [Microsoft Power BI lokalnej bramy danych](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure bramy danych lokalnych usług Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Brama danych lokalnych Flow firmy Microsoft](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Brama danych lokalnych PowerApps firmy Microsoft](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Wymagania

* Użytkownik musi mieć już [zainstalowana brama danych na komputerze lokalnym](logic-apps-gateway-install.md).

* Po zalogowaniu się do portalu Azure, należy użyć tego samego konta firmowego lub szkolnego, który został użyty do [instalowanie bramy danych lokalnych](logic-apps-gateway-install.md#requirements). Konto logowania również musi mieć subskrypcję platformy Azure do użycia podczas tworzenia zasobu bramy w portalu Azure instalacji bramy.

* Instalacji bramy nie może być już żądane przez zasobów Azure bramy. Można skojarzyć instalacji bramy tylko do jednego zasobu Azure bramy. Oświadczenia odbywa się podczas tworzenia zasobu bramy, aby instalacja jest niedostępne dla innych zasobów.

* Brama danych lokalna działa jako usługa systemu Windows i jest skonfigurowany do użycia `NT SERVICE\PBIEgwService` dla systemu Windows usługi poświadczeń logowania. Do tworzenia i obsługi zasobu bramy w portalu Azure [konta usługi systemu Windows](../logic-apps/logic-apps-gateway-install.md) musi mieć co najmniej **współautora** uprawnienia. 

  > [!NOTE]
  > Konto usługi systemu Windows różni się od konto używane do łączenia się z danymi lokalnymi źródeł, a z platformy Azure służbowe konto używane do logowania do usługi w chmurze.

## <a name="install-the-on-premises-data-gateway"></a>Instalowanie bramy danych lokalnych

Jeśli nie jest jeszcze, wykonaj [kroki, aby zainstalować bramę danych lokalnych](logic-apps-gateway-install.md). Przed kontynuowaniem inne czynności upewnij się, zainstalować bramę danych na komputerze lokalnym.

<a name="create-gateway-resource"></a>

## <a name="create-an-azure-resource-for-the-on-premises-data-gateway"></a>Tworzenie zasobu platformy Azure dla bramy danych lokalnych

Po zainstalowaniu bramy na komputerze lokalnym, należy utworzyć bramy danych jako zasób w systemie Azure. Ten krok powoduje również skojarzenie zasobu bramy z subskrypcją platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). Upewnij się, że korzysta się z tym samym pracy Azure służbowego adresu e-mail używane do instalowania bramy.

2. W menu głównym Azure wybierz **Utwórz zasób** > **integracji przedsiębiorstwa** > **bramy danych lokalnych**.

   ![Znajdź "brama lokalna dane"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na **Utwórz bramę połączenia** Podaj te szczegóły, aby utworzyć zasób bramy danych:

    * **Nazwa**: Wprowadź nazwę dla zasobu bramy. 

    * **Subskrypcja**: Wybierz subskrypcję platformy Azure do skojarzenia z zasobu bramy. 
    Ta subskrypcja powinna być tej samej subskrypcji co aplikację logiki.
   
      Domyślna subskrypcja opiera się na konto platformy Azure, używany do logowania.

    * **Grupa zasobów**: Utwórz grupę zasobów lub wybierz istniejącą grupę zasobów podczas wdrażania zasobu bramy. 
    Grupy zasobów ułatwiają zarządzanie powiązane zasoby Azure jako kolekcja.

    * **Lokalizacja**: Azure ogranicza tej lokalizacji do tego samego regionu, które zostały wybrane do usługi bramy w chmurze podczas [instalacja bramy](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Upewnij się, że lokalizacja zasobu bramy odpowiada lokalizacji usługi bramy chmury. W przeciwnym razie instalacji bramy mogą nie być wyświetlane na liście Zainstalowane bramy do wyboru w następnym kroku.
      > 
      > Dla zasobu bramy i aplikację logiki, można używać różnych regionach.

    * **Nazwa instalacji**: Jeśli instalacji bramy nie została jeszcze wybrana, wybierz bramę, która została wcześniej zainstalowana. 

    Aby dodać zasobu bramy do pulpitu nawigacyjnego platformy Azure, wybierz **Przypnij do pulpitu nawigacyjnego**. 
    Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

    Na przykład:

    ![Podaj szczegóły, aby utworzyć bramę danymi lokalnymi](./media/logic-apps-gateway-connection/createblade.png)

    Aby znaleźć lub wyświetlić bramy danych w dowolnym momencie z menu głównego Azure, wybierz **wszystkie usługi**. 
    W polu wyszukiwania wpisz "lokalnej bramy danych", a następnie wybierz **bram danych lokalnych**.

    ![Znajdź "lokalnej bramy danych"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-your-logic-app-to-the-on-premises-data-gateway"></a>Łączenie aplikacji logiki do bramy danych lokalnych

Teraz, utworzeniu zasobu brama danych i skojarzone z subskrypcją platformy Azure z tego zasobu, należy utworzyć połączenie między usługą aplikacji logiki i bramy danych.

> [!NOTE]
> Lokalizacja połączenia bramy musi znajdować się w tym samym regionie co aplikację logiki, ale można użyć bramy danych, który istnieje w innym regionie.

1. W portalu Azure Utwórz lub Otwórz aplikację logiki w Projektancie aplikacji logiki.

2. Dodaj łącznik, który obsługuje połączenia lokalnego, takie jak SQL Server.

3. Następujące kolejności, wybierz **Połącz za pośrednictwem bramy danych lokalnych**, Podaj unikatową nazwę połączenia i wymagane informacje i wybierz zasób bramy danych, którego chcesz używać. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   > [!TIP]
   > Nazwa połączenia unikatowy pomaga łatwo zidentyfikować to połączenie później, szczególnie w przypadku tworzenia wielu połączeń. Jeśli to konieczne, należy również uwzględnić kwalifikowaną dla nazwy użytkownika. 

   ![Utwórz połączenie między bramą logiki aplikacji i danych](./media/logic-apps-gateway-connection/blankconnection.png)

Gratulujemy, połączenie bramy jest teraz gotowy do aplikacji logiki do użycia.

## <a name="edit-your-gateway-connection-settings"></a>Edytuj ustawienia połączenia bramy

Po utworzeniu połączenia bramy aplikacji logiki można później zaktualizować ustawienia dla tego połączenia.

1. Aby znaleźć połączenia bramy:

   * W menu aplikacji logiki w obszarze **narzędzi programistycznych**, wybierz pozycję **połączenia interfejsu API**. 
   
     **Połączenia interfejsu API** w okienku zostaną wyświetlone wszystkie połączenia interfejsu API skojarzone z aplikacji logiki, w tym połączenia bramy.

     ![Przejdź do aplikacji logiki, wybierz "Interfejsu API połączeń"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Lub, w menu głównym Azure, przejdź do **więcej usług** > **sieci Web i mobilność** > **połączenia interfejsu API** dla wszystkich połączeń interfejsu API, łącznie z bramy połączenia, które są skojarzone z subskrypcją platformy Azure. 

   * Lub, w menu głównym Azure, przejdź do **wszystkie zasoby** dla wszystkich połączeń interfejsu API, w tym połączenia bramy, które są skojarzone z subskrypcją platformy Azure.

2. Wybierz połączenie bramy, który chcesz wyświetlić lub edytować, a następnie wybierz pozycję **połączenia Edytuj interfejsu API**.

   > [!TIP]
   > Jeśli aktualizacje nie zostały wprowadzone, spróbuj [zatrzymanie i ponowne uruchomienie usługi systemu Windows bramy](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Przełącz lub usunąć zasób lokalną bramy danych

Do utworzenia zasobu różnych bramy, skojarzyć bramy z innego zasobu lub usunąć zasobów bramy, można usunąć zasobów bramy bez wpływu na instalacji bramy. 

1. W menu głównym Azure, przejdź do **wszystkie zasoby**. 
2. Znajdź i zaznacz pozycję zasobu bramy danych.
3. Wybierz **bramy danych lokalnych**, a na pasku narzędzi zasobów wybierz **usunąć**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dla usługi logic apps](./logic-apps-examples-and-scenarios.md)
