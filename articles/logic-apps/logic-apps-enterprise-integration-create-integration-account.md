---
title: Tworzenie i zarządzanie nimi kont integracji dla rozwiązań B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie, łączenie, przenoszenie i usuwanie kont integracji dla integracji dla przedsiębiorstw i rozwiązań B2B w usłudze Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999615"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Tworzenie i zarządzanie kontami integracji B2B rozwiązania dzięki usłudze logic apps

Przed skompilowaniem [integracji dla przedsiębiorstw i rozwiązań B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) z [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), musisz mieć konto integracji, czyli, gdzie możesz utworzyć, przechowywać i zarządzania, takimi jak artefaktów B2B handlowymi partnerów, umów, mapy, schematów, certyfikaty i tak dalej. Aplikacja logiki może Praca z artefaktami w ramach konta integracji i używanie łączników B2B aplikacji logiki, takich jak sprawdzanie poprawności kodu XML, należy [połączenia z kontem integracji](#link-account) do aplikacji logiki. Aby połączyć je, zarówno integracji konta i logika aplikacji musi mieć *tego samego* lokalizacji platformy Azure lub regionu.

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* Utwórz konto integracji.
* Połącz swoje konto integracji z aplikacją logiki.
* Przenieść konta integracji na platformę Azure w innej grupie zasobów lub subskrypcji.
* Usuń konto integracji.

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

## <a name="create-integration-account"></a>Tworzenie konta integracji

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wpisz "konta integracji" jako filtr, a następnie wybierz pozycję **kont integracji**.

   ![Znajdź konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. W obszarze **kont integracji**, wybierz **Dodaj**.

   ![Wybierz pozycję "Dodaj", aby utworzyć konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Zawierają informacje o koncie integracji: 

   ![Podaj szczegóły konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Właściwość | Wymagane | Przykładowa wartość | Opis | 
   |----------|----------|---------------|-------------|
   | Name | Yes | test-integration-account | Nazwa konta integracji. W tym przykładzie należy użyć określonej nazwy. | 
   | Subskrypcja | Yes | <*Azure-subscription-name*> | Nazwa subskrypcji platformy Azure do użycia | 
   | Grupa zasobów | Yes | test-integration-account-rg | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) używane do organizowania powiązanych zasobów. W tym przykładzie należy utworzyć nową grupę zasobów o określonej nazwie. | 
   | Warstwa cenowa | Yes | Bezpłatna | Warstwa cenowa, którego chcesz używać. W tym przykładzie wybierz **bezpłatna**, ale uzyskać więcej informacji, zobacz [Logic Apps, limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md) i [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Lokalizacja | Yes | Zachodnie stany USA | Region którym będą przechowywane informacje o koncie integracji. Wybierz tej samej lokalizacji co aplikacja logiki lub utworzyć aplikację logiki w tej samej lokalizacji co konto usługi integracji. | 
   | Obszar roboczy usługi Log Analytics | Nie | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. | 
   ||||| 

4. Gdy wszystko będzie gotowe, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

   Gdy platforma Azure wdroży konta integracji w wybranej lokalizacji, która zazwyczaj kończy się w ciągu jednej minuty, platforma Azure otworzy koncie integracji.

   ![Platforma Azure otworzy konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Teraz zanim aplikacja logiki można użyć konta integracji, należy połączyć konto integracji aplikacji logiki.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Link do aplikacji logiki

Aby zapewnić dostęp do usługi logic apps do konta integracji, który zawiera Twoje artefaktów B2B, takich jak handlowymi partnerów, umów, mapy i schematów, Twoje konto integracji należy połączyć swoją aplikację logiki. 

> [!NOTE]
> Integracja aplikacji logiki i konta muszą istnieć w tym samym regionie.

1. W witrynie Azure portal należy znaleźć i otworzyć aplikację logiki.

2. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **ustawienia przepływu pracy**. W **wybierz konto integracji** listy, wybierz konto integracji, aby połączyć aplikację logiki.

   ![Wybierz swoje konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Aby zakończyć połączenie, wybierz **Zapisz**.

   ![Wybierz swoje konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Pomyślnie połączonego konta integracji platformy Azure pokazuje komunikat z potwierdzeniem. 

   ![Azure potwierdza pomyślne łącza](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Teraz Twoja aplikacja logiki można użyć i wszystkie artefakty w ramach konta integracji oraz łączniki B2B, takich jak sprawdzanie poprawności kodu XML i pliku prostego kodowania lub dekodowania.  

## <a name="unlink-from-logic-app"></a>Odłącz od aplikacji logiki

Aby połączyć swoją aplikację logiki na inne konto integracji lub nie są już używane konto integracji z aplikacją logiki, możesz usunąć link za pośrednictwem usługi Azure Resource Explorer.

1. W przeglądarce przejdź do <a href="https://resources.azure.com" target="_blank">usługi Azure Resource Explorer (https://resources.azure.com)</a>. Upewnij się, że logowanie się przy użyciu tych samych poświadczeń platformy Azure.

   ![Eksplorator zasobów Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. W polu wyszukiwania wprowadź nazwę aplikacji logiki, a następnie znajdź i wybierz swoją aplikację logiki.

   ![Znajdź i wybierz aplikację logiki](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Na pasku tytułu explorer wybierz **odczytu/zapisu**.

   ![Włącz tryb "Odczytu/zapisu"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Na **danych** kartę, wybrać **Edytuj**.

   ![Na karcie "Dane" Wybierz "Edit"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. W edytorze, Znajdź `integrationAccount` właściwość integracji dla konta, a następnie usuń tę właściwość, która ma następujący format:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Na przykład:

   ![Znajdź definicji właściwości "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Na **danych** kartę, wybrać **umieścić** Aby zapisać zmiany. 

   ![Wybierz pozycję "Put", aby zapisać zmiany](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. W witrynie Azure portal w obszarze aplikację logiki **ustawienia przepływu pracy**, sprawdź, czy **konta integracji** właściwość pojawi się pusta.

   ![Sprawdź, czy konto integracji nie jest połączony](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Przenieść konta integracji

Konta integracji można przenieść do innej platformy Azure subskrypcji lub grupy zasobów.

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wpisz "konta integracji" jako filtr, a następnie wybierz pozycję **kont integracji**.

   ![Znajdź swoje konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. W obszarze **kont integracji**, wybierz konto integracji, które chcesz przenieść. Na integracji usługi menu konta, w obszarze **ustawienia**, wybierz **właściwości**.

   ![W obszarze "Ustawienia" wybierz pozycję "Właściwości"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Zmień grupę zasobów platformy Azure lub subskrypcji na koncie integracji.

   ![Wybierz pozycję "Zmień grupę zasobów" lub "Zmień subskrypcję"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Gdy skończysz, pamiętaj o zaktualizowaniu wszystkie skrypty za pomocą nowych identyfikatorów zasobów dla artefaktów.  

## <a name="delete-integration-account"></a>Usuń konto integracji

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wpisz "konta integracji" jako filtr, a następnie wybierz pozycję **kont integracji**.

   ![Znajdź swoje konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. W obszarze **kont integracji**, wybierz konto integracji, które chcesz usunąć. W menu konta integracji wybierz **Przegląd**, następnie wybierz **Usuń**. 

   ![Wybierz konto integracji. Na stronie "Przegląd" Wybierz opcję "Usuń"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Aby upewnić się, że chcesz usunąć swoje konto integracji, wybierz opcję **tak**.

   ![Aby potwierdzić zamiar usunięcia, wybierz pozycję "Tak"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Kolejne kroki

* [Utwórz partnerów handlowych](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Tworzenie umów](../logic-apps/logic-apps-enterprise-integration-agreements.md)
