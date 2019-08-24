---
title: Dostęp do źródeł danych w środowisku lokalnym z Azure Logic Apps | Microsoft Docs
description: Nawiązywanie połączenia z lokalnymi źródłami danych z usługi Logic Apps przez tworzenie lokalnej bramy danych
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 029dc8daaf456c155d46eefa699772882bdabee5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982868"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Nawiązywanie połączenia z lokalnymi źródłami danych z Azure Logic Apps

Aby uzyskać dostęp do źródeł danych w środowisku lokalnym z poziomu aplikacji logiki, utwórz zasób lokalnej bramy danych w Azure Portal. Aplikacje logiki mogą następnie używać łączników [lokalnych](../logic-apps/logic-apps-gateway-install.md#supported-connections). W tym artykule przedstawiono sposób tworzenia zasobu bramy platformy Azure *po* [pobraniu i zainstalowaniu bramy na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> W celu nawiązania połączenia z sieciami wirtualnymi platformy Azure Rozważ utworzenie [*środowiska usługi integracji*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Aby uzyskać informacje o sposobach korzystania z bramy z innymi usługami, zobacz następujące artykuły:

* [Lokalna Brama danych Power BI firmy Microsoft](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow lokalnej bramy danych](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps lokalnej bramy danych](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Brama danych została już pobrana [i zainstalowana na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md).

* Instalacja bramy nie jest już skojarzona z zasobem bramy na platformie Azure. Instalację bramy można połączyć tylko z jednym zasobem bramy, co ma miejsce podczas tworzenia zasobu bramy i wybrania instalacji bramy. To łączenie sprawia, że instalacja bramy jest niedostępna dla innych zasobów.

* Po zalogowaniu się do Azure Portal i utworzeniu zasobu bramy upewnij się, że używasz tego samego konta logowania, które było wcześniej używane do [zainstalowania lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md#requirements) wraz z tą samą [subskrypcją platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) , która została użyta do zainstalowania usługi punkt. Jeśli nie masz jeszcze subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure</a>.

* Aby można było utworzyć i zachować zasób bramy w Azure Portal, [konto usługi systemu Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) musi mieć co najmniej uprawnienia współautora. Lokalna Brama danych działa jako usługa systemu Windows i jest skonfigurowana do korzystania `NT SERVICE\PBIEgwService` z poświadczeń logowania usługi systemu Windows. 

  > [!NOTE]
  > Konto usługi systemu Windows różni się od konta używanego do łączenia się z lokalnymi źródłami danych oraz z konta służbowego platformy Azure używanego do logowania się w usługach w chmurze.

## <a name="download-and-install-gateway"></a>Pobierz i zainstaluj bramę

Przed wykonaniem kroków opisanych w tym artykule upewnij się, że brama jest już zainstalowana na komputerze lokalnym.
Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z instrukcjami, aby [pobrać i zainstalować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Utwórz zasób platformy Azure dla bramy

Po zainstalowaniu bramy na komputerze lokalnym można utworzyć zasób platformy Azure dla bramy. Ten krok powoduje także skojarzenie zasobu bramy z subskrypcją platformy Azure.

1. Zaloguj się w witrynie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Upewnij się, że używasz tego samego adresu e-mail platformy Azure, który jest używany do instalacji bramy.

2. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób** > 
**integracja** > **lokalnej bramy danych**.

   ![Znajdź "lokalna Brama danych"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na stronie **Tworzenie bramy połączeń** podaj te informacje dotyczące zasobu bramy:

   | Właściwość | Opis | 
   |----------|-------------|
   | **Nazwa zasobu** | Nazwa zasobu bramy, która może zawierać tylko litery`-`, cyfry, łączniki (), podkreślenia (`_`), nawiasy (`(`, `)`) i kropki (`.`). | 
   | **Subskrypcja** | Nazwa subskrypcji platformy Azure, która powinna być tą samą subskrypcją co aplikacja logiki. Domyślna subskrypcja jest oparta na koncie platformy Azure użytym do zalogowania się. | 
   | **Grupa zasobów** | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) do organizowania powiązanych zasobów | 
   | **Location** | Platforma Azure ogranicza tę lokalizację do tego samego regionu, który został wybrany dla usługi bramy w chmurze podczas [instalacji bramy](../logic-apps/logic-apps-gateway-install.md). <p>**Uwaga**: Upewnij się, że lokalizacja zasobów bramy jest zgodna z lokalizacją usługi w chmurze bramy. W przeciwnym razie instalacja bramy może nie pojawić się na liście zainstalowane bramy, aby można było wybrać ją w następnym kroku. Możesz użyć różnych regionów dla zasobu bramy i aplikacji logiki. | 
   | **Nazwa instalacji** | Jeśli instalacja bramy nie została jeszcze wybrana, wybierz wcześniej zainstalowaną bramę. | 
   | | | 

   Oto przykład:

   ![Podaj szczegóły, aby utworzyć lokalną bramę danych](./media/logic-apps-gateway-connection/createblade.png)

4. Aby dodać zasób bramy do pulpitu nawigacyjnego platformy Azure, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   Aby znaleźć lub wyświetlić bramę w dowolnym momencie, w głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. 
   W polu wyszukiwania wprowadź ciąg "lokalna Brama danych", a następnie wybierz pozycję **lokalne bramy danych**.

   ![Znajdowanie "lokalnych bram danych"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Nawiązywanie połączenia z danymi lokalnymi

Po utworzeniu zasobu bramy i skojarzeniu subskrypcji platformy Azure z tym zasobem możesz teraz utworzyć połączenie między aplikacją logiki i lokalnym źródłem danych przy użyciu bramy.

1. W Azure Portal Utwórz lub Otwórz aplikację logiki w Projektancie aplikacji logiki.

2. Dodaj łącznik obsługujący połączenia lokalne, na przykład **SQL Server**.

3. Teraz skonfiguruj połączenie:

   1. Wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**. 

   2. W przypadku **bram**wybierz wcześniej utworzony zasób bramy. 

      Mimo że lokalizacja połączenia bramy musi znajdować się w tym samym regionie, w którym znajduje się aplikacja logiki, można wybrać bramę w innym regionie.

   3. Podaj unikatową nazwę połączenia i inne wymagane informacje. 

      Unikatowa nazwa połączenia ułatwia łatwe zidentyfikowanie tego połączenia, szczególnie w przypadku tworzenia wielu połączeń. Jeśli ma to zastosowanie, Uwzględnij także kwalifikowaną domenę dla nazwy użytkownika.
   
      Oto przykład:

      ![Utwórz połączenie między aplikacją logiki a bramą danych](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 

Połączenie bramy jest teraz gotowe do użycia przez aplikację logiki.

## <a name="edit-connection"></a>Edytuj połączenie

Po utworzeniu połączenia bramy dla aplikacji logiki można później zaktualizować ustawienia dla tego konkretnego połączenia.

1. Znajdź połączenie bramy:

   * Aby znaleźć wszystkie połączenia interfejsu API tylko dla aplikacji logiki, w menu aplikacji logiki w obszarze **Narzędzia programistyczne**wybierz pozycję **połączenia interfejsu API**. 
   
     ![Przejdź do aplikacji logiki, wybierz pozycję "połączenia interfejsu API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Aby znaleźć wszystkie połączenia interfejsu API skojarzone z subskrypcją platformy Azure: 

     * W głównym menu platformy Azure przejdź do pozycji **wszystkie usługi** >  > **połączenia internetowego interfejsu API**. 
     * Lub w głównym menu platformy Azure przejdź do pozycji **wszystkie zasoby**.

2. Wybierz połączenie bramy, którego chcesz użyć, a następnie wybierz pozycję **Edytuj połączenie z interfejsem API**.

   > [!TIP]
   > Jeśli aktualizacje nie zaczną obowiązywać, spróbuj [zatrzymać i ponownie uruchomić usługę bramy systemu Windows](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Usuń zasób bramy

Aby utworzyć inny zasób bramy, skojarz bramę z innym zasobem lub Usuń zasób bramy, możesz usunąć zasób bramy bez wpływu na instalację bramy. 

1. W głównym menu platformy Azure przejdź do pozycji **wszystkie zasoby**. 

2. Znajdź i wybierz zasób bramy.

3. Jeśli jeszcze tego nie zrobiono, w menu zasobów bramy wybierz pozycję **lokalna Brama danych**. 

4. Na pasku narzędzi zasobów wybierz pozycję **Usuń**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dotyczące usługi Logic Apps](./logic-apps-examples-and-scenarios.md)
