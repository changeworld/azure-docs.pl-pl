---
title: Tworzenie i zarządzanie kontami integracji rozwiązań B2B - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie, link, przenoszenie i usuwanie konta integracji integracji przedsiębiorstwa i rozwiązaniami B2B usługi Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: SyntaxC4
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: ecfan; LADocs
ms.openlocfilehash: 8e31a84d4508075dcb7a1d7ad8a64fa8e142681d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Tworzenie i zarządzanie kontami integracji rozwiązań B2B w usłudze logic apps

Przed skompilowaniem [integracji przedsiębiorstwa i rozwiązań B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) z [Azure Logic Apps](../logic-apps/logic-apps-overview.md), najpierw musi mieć konto integracji, czyli gdzie możesz tworzyć, przechowywać i zarządzanie B2B artefaktów, takich jak handlowymi partnerów, umów, map, schematów, certyfikaty i tak dalej. Przed aplikację logiki można Praca z artefaktami na koncie integracji i używać łączniki B2B aplikacji logiki, takich jak sprawdzanie poprawności kodu XML, należy [połączenia Twojego konta integracji](#link-account) do aplikacji logiki. Do nawiązania połączenia, musi mieć obu integracji konta i logiki aplikacji *tego samego* lokalizacji platformy Azure lub regionu.

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* Utwórz konta integracji.
* Połącz Twoje konto integracji z aplikacji logiki.
* Przenieś konta integracji Azure innej grupy zasobów lub subskrypcji.
* Usuwanie konta integracji.

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

## <a name="create-integration-account"></a>Tworzenie konta integracji

1. Wybierz z menu głównego Azure **wszystkie usługi**. W polu wyszukiwania wprowadź "konta integracji" jako filtr, a następnie wybierz **konta integracji**.

   ![Znajdź konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. W obszarze **konta integracji**, wybierz **Dodaj**.

   ![Wybierz opcję "Dodaj", aby utworzyć konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Podaj informacje o Twoim koncie integracji: 

   ![Podaj szczegóły konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Właściwość | Wymagane | Przykładowa wartość | Opis | 
   |----------|----------|---------------|-------------|
   | Name (Nazwa) | Yes | Test integracji konta | Nazwa konta integracji. W tym przykładzie należy użyć określonej nazwy. | 
   | Subskrypcja | Yes | <*Nazwa w przypadku subskrypcji platformy Azure*> | Nazwa subskrypcji platformy Azure do użycia | 
   | Grupa zasobów | Yes | Test — integracja — konta-zarządcy zasobów | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) używane do organizowania powiązane zasoby. Na przykład utworzyć nową grupę zasobów o określonej nazwie. | 
   | Warstwa cenowa | Yes | Bezpłatna | Warstwa cenowa, którego chcesz używać. Na przykład wybierz **wolne**. | 
   | Lokalizacja | Yes | Zachodnie stany USA | Region miejsce przechowywania informacji o koncie integracji. Wybierz tej samej lokalizacji co aplikacja logiki lub tworzenie aplikacji logiki w tej samej lokalizacji co konto integracji. Na przykład | 
   | Log Analytics | Nie | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. | 
   ||||| 

4. Jeśli wszystko jest gotowe, wybierz **Przypnij do pulpitu nawigacyjnego**i wybierz polecenie **Utwórz**.

   Po Azure wdraża konta integracji w wybranej lokalizacji, która zazwyczaj kończy się w ciągu jednej minuty, Azure otwiera konta integracji.

   ![Azure otwiera konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Teraz Aby użyć konta integracji aplikacji logiki, należy połączyć konta integracji aplikacji logiki.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Łącze do aplikacji logiki

Aby udzielić dostępu aplikacji logiki do konta integracji, które zawiera Twoje artefaktów B2B, takich jak handlowymi partnerów, umów map i schematów, należy połączyć konto integracji aplikacji logiki. 

> [!NOTE]
> Integracja aplikacji logiki i konto musi istnieć w tym samym regionie.

1. W portalu Azure Znajdź i Otwórz aplikację logiki.

2. W menu aplikacji logiki w obszarze **ustawienia**, wybierz pozycję **ustawienia przepływu pracy**. W **wybierz konto integracji** listy, wybierz konto integracji, aby utworzyć link do aplikacji logiki.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Aby zakończyć połączenie, wybierz **zapisać**.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Pomyślnie połączonego konta integracji Azure zawiera komunikat potwierdzenia. 

   ![Azure potwierdza łącza powiodło się](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Aplikację logiki można teraz używać i wszystkie artefakty konta integracji oraz łączniki B2B, takich jak sprawdzanie poprawności kodu XML i pliku prostego kodowania lub dekodowania.  

## <a name="unlink-from-logic-app"></a>Odłącz od aplikacji logiki

Aby połączyć aplikację logiki z innego konta integracji lub nie są już używane konto integracji z aplikacji logiki, należy usunąć łącza za pomocą Eksploratora zasobów Azure.

1. W przeglądarce przejdź do <a href="https://resources.azure.com" target="_blank">Eksploratora zasobów Azure (https://resources.azure.com)</a>. Upewnij się, że logujesz się za pomocą tych samych poświadczeń platformy Azure.

   ![Eksplorator zasobów Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. W polu wyszukiwania wprowadź nazwę aplikacji logiki, a następnie znajdź i wybierz aplikację logiki.

   ![Znajdź i zaznacz pozycję aplikacji logiki](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Na pasku tytułu explorer wybierz **odczytu/zapisu**.

   ![Włącz tryb "Odczytu/zapisu"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Na **danych** , wybierz pozycję **Edytuj**.

   ![Na karcie "Dane" wybierz pozycję "Edytuj"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. W edytorze, Znajdź `integrationAccount` właściwości integracji konta, a następnie usunąć tej właściwości, która ma format to:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Na przykład:

   ![Znajdź definicji właściwości "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Na **danych** , wybierz pozycję **Put** Aby zapisać zmiany. 

   ![Wybierz pozycję "Put", aby zapisać zmiany](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. W portalu Azure, w obszarze aplikację logiki **ustawienia przepływu pracy**, sprawdź, czy **konta integracji** właściwości pojawi się pusta.

   ![Sprawdź konto integracji nie jest połączony](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Przenoszenie konta integracji

Konta integracji można przenieść do innego Azure subskrypcji lub grupy zasobów.

1. W menu głównym Azure wybierz **wszystkie usługi**. W polu wyszukiwania wprowadź "konta integracji" jako filtr, a następnie wybierz **konta integracji**.

   ![Znajdź konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. W obszarze **konta integracji**, wybierz konto integracji, które mają zostać przeniesione. Na integracją konta menu, w obszarze **ustawienia**, wybierz **właściwości**.

   ![W obszarze "Ustawienia" wybierz pozycję "Właściwości"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Zmiana grupy zasobów platformy Azure lub subskrypcji dla Twojego konta integracji.

   ![Wybierz opcję "Zmiana grupy zasobów" lub "Zmień subskrypcję"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Gdy wszystko będzie gotowe, upewnij się, zaktualizować wszystkie skrypty o nowy zasób identyfikatory sieci artefaktów.  

## <a name="delete-integration-account"></a>Usuwanie konta integracji

1. W menu głównym Azure wybierz **wszystkie usługi**. W polu wyszukiwania wprowadź "konta integracji" jako filtr, a następnie wybierz **konta integracji**.

   ![Znajdź konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. W obszarze **konta integracji**, wybierz konto integracji, które chcesz usunąć. W menu konta integracji wybierz **omówienie**, a następnie wybierz **usunąć**. 

   ![Wybierz konto integracji. Na stronie "Overview" Wybierz opcję "Delete"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Aby upewnić się, że chcesz usunąć konto integracji, wybierz **tak**.

   ![Aby potwierdzić zamiar usunięcia, wybierz pozycję "Tak"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Kolejne kroki

* [Utwórz partnerami handlowymi](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Tworzenie umów](../logic-apps/logic-apps-enterprise-integration-agreements.md)