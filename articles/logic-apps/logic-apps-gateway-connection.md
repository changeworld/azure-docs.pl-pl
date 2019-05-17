---
title: Dostęp do źródeł danych w środowisku lokalnym z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Łączenie danych lokalnych źródeł z aplikacji logiki przez utworzenie lokalnej bramy danych
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 0580fe09c2cb6569724a9b4365233a3142645a47
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546270"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Łączenie ze źródłami danych lokalnych z usługi Azure Logic Apps

Dostęp do źródeł danych w środowisku lokalnym pochodzących z tworzonych aplikacji logiki, utwórz zasób bramy danych lokalnych w witrynie Azure portal. Następnie można użyć aplikacji usługi logic apps [łączniki lokalne](../logic-apps/logic-apps-gateway-install.md#supported-connections). W tym artykule przedstawiono sposób tworzenia zasobu bramy usługi Azure *po* możesz [pobrać i zainstalować bramę na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Aby połączyć się z sieciami wirtualnymi platformy Azure, należy rozważyć utworzenie [ *środowisko usługi integracji* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zamiast tego. 

Aby uzyskać informacje o sposobie używania bramy z innymi usługami, zobacz następujące artykuły:

* [Brama danych lokalnych usługi Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Brama danych lokalnych Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Brama danych lokalnych Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Usługa Azure Analysis Services na lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Masz już [pobrać i zainstalować bramę data gateway na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md).

* Instalację bramy nie jest już skojarzona z zasobem bramy na platformie Azure. Instalację bramy można połączyć tylko do jednego zasobu bramy, które odbywa się podczas tworzenia zasobu bramy i wybierz opcję instalacji bramy. To połączenie powoduje, że instalacja bramy dla innych zasobów.

* Gdy Zaloguj się do witryny Azure portal i utworzenia zasobu bramy, należy upewnić się, logowania konta, które zostało wcześniej używanych do [zainstalować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md#requirements) oraz takie same [subskrypcji platformy Azure ](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) użytą do zainstalowania bramy. Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

* Do tworzenia i obsługi zasobów bramy w witrynie Azure portal usługi [konta usługi Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) wymaga co najmniej **Współautor** uprawnienia. Lokalna brama danych działa jako usługa Windows i jest skonfigurowany do użycia `NT SERVICE\PBIEgwService` Windows poświadczeń logowania usługi. 

  > [!NOTE]
  > Windows, które konta usług różni się od konta używanego do łączenia z danymi w środowisku lokalnym źródeł i z platformy Azure konto służbowe używane do logowania do usług w chmurze.

## <a name="download-and-install-gateway"></a>Pobieranie i instalowanie bramy

Przed kontynuowaniem kroków opisanych w tym artykule upewnij się, że brama jest już zainstalowana na komputerze lokalnym.
Jeśli jeszcze nie, wykonaj kroki, aby [pobieranie i instalowanie lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Tworzenie zasobu platformy Azure dla bramy

Po zainstalowaniu bramy na komputerze lokalnym, będzie można utworzyć zasobu platformy Azure dla bramy. Ten krok powoduje również skojarzenie zasobu bramy z subskrypcją platformy Azure.

1. Zaloguj się w witrynie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Upewnij się, korzystając z tego samego roboczych platformy Azure lub służbowy adres e-mail używany do instalacji bramy.

2. W głównym menu platformy Azure, wybierz **Utwórz zasób** > 
**integracji** > **lokalnej bramy danych**.

   ![Znajdź "lokalna brama danych"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na **Utwórz bramę połączenia** Podaj te informacje dotyczące zasobu bramy:

   | Właściwość | Opis | 
   |----------|-------------|
   | **Nazwa** | Nazwa zasobu bramy | 
   | **Subskrypcja** | Nazwa subskrypcji platformy Azure, która powinna być tej samej subskrypcji co aplikacja logiki. Domyślna subskrypcja opiera się na konto platformy Azure, używane do logowania. | 
   | **Grupa zasobów** | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) służący do organizowania powiązanych zasobów | 
   | **Lokalizacja** | Azure ogranicza możliwość użycia tej lokalizacji na tym samym regionie, który został wybrany dla Usługa bramy w chmurze podczas [instalacji bramy](../logic-apps/logic-apps-gateway-install.md). <p>**Uwaga**: Upewnij się, że ta lokalizacja zasobu bramy jest zgodna lokalizacji usługi w chmurze bramy. W przeciwnym razie instalacji bramy może nie być widoczna na liście zainstalowanych bram można wybrać w następnym kroku. Można użyć różnych regionach, bramy zasobu bazy danych i aplikacji logiki. | 
   | **Nazwa instalacji** | Jeśli Twoja instalacja bramy nie została jeszcze wybrana, wybierz bramę, która została wcześniej zainstalowana. | 
   | | | 

   Oto przykład:

   ![Podaj szczegóły, aby utworzyć lokalnej bramy danych](./media/logic-apps-gateway-connection/createblade.png)

4. Aby dodać zasobu bramy do pulpitu nawigacyjnego platformy Azure, wybierz **Przypnij do pulpitu nawigacyjnego**. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   Aby znaleźć lub wyświetlić bramy w dowolnym momencie, w menu głównym platformy Azure, wybierz pozycję **wszystkich usług**. 
   W polu wyszukiwania wprowadź "lokalnej bramy danych", a następnie wybierz pozycję **na poziomie lokalnych bram danych**.

   ![Znajdź "na poziomie lokalnych bram danych"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Nawiązywanie połączenia z danymi lokalnymi

Po utworzeniu zasobu bramy i skojarzyć swoją subskrypcję platformy Azure z tego zasobu, możesz teraz utworzyć połączenie między aplikacją logiki i źródła danych w środowisku lokalnym przy użyciu bramy.

1. W witrynie Azure portal należy utworzyć lub otworzyć aplikację logiki w Projektancie aplikacji logiki.

2. Dodaj łącznik, który obsługuje połączenia w środowisku lokalnym, na przykład **programu SQL Server**.

3. Teraz należy skonfigurować połączenie:

   1. Wybierz **Połącz za pośrednictwem lokalnej bramy danych**. 

   2. Aby uzyskać **bram**, wybierz wcześniej utworzonego zasobu bramy. 

      Mimo że lokalizacji połączenia bramy muszą istnieć w tym samym regionie, co aplikacja logiki, możesz wybrać bramy w innym regionie.

   3. Podaj nazwę połączenia unikatowy i inne wymagane informacje. 

      Nazwa połączenia unikatowych pomaga łatwo identyfikować to połączenie później, szczególnie w przypadku utworzenia wielu połączeń. Jeśli ma to zastosowanie, również obejmować kwalifikowaną dla nazwy użytkownika.
   
      Oto przykład:

      ![Utwórz połączenie między bramy danych i aplikacji logiki](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 

Połączenie bramy sieci jest gotowy do użycia aplikacji logiki.

## <a name="edit-connection"></a>Edytowanie połączenia

Po utworzeniu połączenia z bramą aplikacji logiki można później zaktualizować ustawienia dla tego połączenia.

1. Znajdź połączenie bramy sieci:

   * Aby znaleźć wszystkie połączenia interfejsu API dla samą aplikację logiki, w menu aplikacji logiki w obszarze **narzędzia programistyczne**, wybierz opcję **połączenia interfejsu API**. 
   
     ![Przejdź do aplikacji logiki, wybierz pozycję "Połączenia API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Aby znaleźć wszystkie połączenia interfejsu API skojarzone z subskrypcją platformy Azure: 

     * W menu głównym platformy Azure, przejdź do **wszystkich usług** > **Web** > **połączenia interfejsu API**. 
     * Lub, w menu głównym platformy Azure, przejdź do **wszystkie zasoby**.

2. Wybierz połączenie bramy, a następnie wybierz **połączenia Edytuj interfejsu API**.

   > [!TIP]
   > Jeśli aktualizacje nie zostały wprowadzone, spróbuj [zatrzymanie i ponowne uruchomienie bramy usługi Windows](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Usuwanie zasobu bramy

Do tworzenia zasobu bramy innej, skojarzenia bramy z innego zasobu lub usuwanie zasobu bramy, bez wywierania wpływu na instalacji bramy można usunąć zasobów bramy. 

1. W menu głównym platformy Azure, przejdź do **wszystkie zasoby**. 

2. Znajdź i wybierz zasób usługi bramy.

3. Jeśli nie jest jeszcze wybrana, w menu zasobów bramy, wybierz **lokalnej bramy danych**. 

4. Na pasku narzędzi zasobów wybierz **Usuń**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dla usługi logic apps](./logic-apps-examples-and-scenarios.md)
