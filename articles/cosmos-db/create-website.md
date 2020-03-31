---
title: Wdrażanie aplikacji sieci Web z szablonem — Usługa Azure Cosmos DB
description: Dowiedz się, jak wdrożyć konto usługi Azure Cosmos DB, aplikacje sieci Web usługi Azure App Service i przykładową aplikację sieci web przy użyciu szablonu usługi Azure Resource Manager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128374"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Wdrażanie aplikacji Azure Cosmos DB i Usługi Azure App Service w sieci Web przy użyciu szablonu usługi Azure Resource Manager
W tym samouczku pokazano, jak używać szablonu usługi Azure Resource Manager do wdrażania i integrowania [usługi Microsoft Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/)aplikacji sieci web [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) i przykładowej aplikacji sieci web.

Korzystając z szablonów usługi Azure Resource Manager, można łatwo zautomatyzować wdrażanie i konfigurację zasobów platformy Azure.  W tym samouczku pokazano, jak wdrożyć aplikację sieci web i automatycznie skonfigurować informacje o połączeniu konta usługi Azure Cosmos DB.

Po ukończeniu tego samouczka będziesz mógł odpowiedzieć na następujące pytania:  

* Jak używać szablonu usługi Azure Resource Manager do wdrażania i integrowania konta usługi Azure Cosmos DB i aplikacji sieci Web w usłudze Azure App Service?
* Jak używać szablonu usługi Azure Resource Manager do wdrażania i integrowania konta usługi Azure Cosmos DB, aplikacji sieci web w aplikacjach sieci Web usługi App Service i aplikacji Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Wymagania wstępne
> [!TIP]
> Chociaż ten samouczek nie zakłada wcześniejszego doświadczenia z szablonami usługi Azure Resource Manager lub JSON, jeśli chcesz zmodyfikować szablony lub opcje wdrażania, wymagana jest znajomość każdego z tych obszarów.
> 
> 

Przed postępuje zgodnie z instrukcjami w tym samouczku, upewnij się, że masz subskrypcji platformy Azure. Platforma Azure to platforma oparta na subskrypcji.  Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [Opcje zakupu,](https://azure.microsoft.com/pricing/purchase-options/) [Oferty członków](https://azure.microsoft.com/pricing/member-offers/)lub [Bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Krok 1: Pobierz pliki szablonów
Zacznijmy od pobrania plików szablonów, które wymagają tego samouczka.

1. Pobierz [konto usługi Azure Cosmos DB, aplikacje sieci Web i wdrożyć przykładowy szablon aplikacji demonstracyjnej](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) w folderze lokalnym (na przykład C:\Azure Cosmos DBTemplates). Ten szablon wdraża konto usługi Azure Cosmos DB, aplikację sieci web usługi App Service i aplikację sieci web.  Automatycznie konfiguruje również aplikację sieci web, aby połączyć się z kontem usługi Azure Cosmos DB.
2. Pobierz [przykładowe konto usługi Azure Cosmos DB i szablon przykładu aplikacji Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) do folderu lokalnego (na przykład C:\Azure Cosmos DBTemplates). Ten szablon wdraża konto usługi Azure Cosmos DB, aplikację sieci web usługi App Service i modyfikuje ustawienia aplikacji witryny, aby łatwo powierzchni informacji o połączeniu usługi Azure Cosmos DB, ale nie zawiera aplikacji sieci web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Krok 2: Wdrażanie konta usługi Azure Cosmos DB, aplikacji sieci web usługi App Service i przykładowej aplikacji demonstracyjnej
Teraz zastosujmy swój pierwszy szablon.

> [!TIP]
> Szablon nie sprawdza, czy nazwa aplikacji sieci Web i nazwa konta usługi Azure Cosmos DB wprowadzone w poniższym szablonie są a) prawidłowe i b) dostępne.  Zdecydowanie zaleca się sprawdzenie dostępności nazw, które mają być dostępne przed przesłaniem wdrożenia.
> 
> 

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com), kliknij przycisk Nowy i wyszukaj hasło "Wdrożenie szablonu".
    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment1.png)
2. Wybierz element wdrażania szablonu i kliknij pozycję **Utwórz** ![zrzut ekranu interfejsu użytkownika wdrożenia szablonu](./media/create-website/TemplateDeployment2.png)
3. Kliknij **pozycję Edytuj szablon**, wklej zawartość pliku szablonu DocDBWebsiteTodo.json, a następnie kliknij przycisk **Zapisz**.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment3.png)
4. Kliknij **pozycję Edytuj parametry**, podaj wartości dla każdego z parametrów obowiązkowych, a następnie kliknij przycisk **OK**.  Używane są następujące parametry:
   
   1. NAZWA WITRYNY: Określa nazwę aplikacji sieci Web usługi App Service i jest używana do konstruowania adresu URL używanego do uzyskiwania dostępu do aplikacji sieci web `mydemodocdbwebapp.azurewebsites.net`(na przykład, jeśli określisz "mydemodocdbwebapp", adres URL, pod którym uzyskujesz dostęp do aplikacji internetowej, jest ).
   2. HOSTINGPLANNAME: Określa nazwę planu hostingu usługi App Service do utworzenia.
   3. LOKALIZACJA: Określa lokalizację platformy Azure, w której mają być utworzone zasoby usługi Azure Cosmos DB i zasobów aplikacji sieci web.
   4. DATABASEACCOUNTNAME: Określa nazwę konta usługi Azure Cosmos DB do utworzenia.   
      
      ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment4.png)
5. Wybierz istniejącą grupę zasobów lub podaj nazwę, aby stworzyć nową grupę zasobów, a następnie wybierz lokalizację dla grupy zasobów.

    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment5.png)
6. Kliknij **pozycję Przejrzyj warunki prawne**, **Zakup**, a następnie kliknij przycisk **Utwórz,** aby rozpocząć wdrażanie.  Wybierz **pozycję Przypnij do pulpitu nawigacyjnego, aby** wynikowe wdrożenie było łatwo widoczne na stronie głównej witryny azure portal.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment6.png)
7. Po zakończeniu wdrażania zostanie otwarte okienko Grupy zasobów.
   ![Zrzut ekranu przedstawiający okienko grupy zasobów](./media/create-website/TemplateDeployment7.png)  
8. Aby korzystać z aplikacji, przejdź do adresu URL aplikacji internetowej `http://mydemodocdbwebapp.azurewebsites.net`(w powyższym przykładzie adres URL będzie ).  Zobaczysz następującą aplikację sieci web:
   
   ![Przykładowa aplikacja Todo](./media/create-website/image2.png)
9. Śmiało i utworzyć kilka zadań w aplikacji sieci web, a następnie powrócić do okienka grupy zasobów w witrynie Azure portal. Kliknij zasób konta usługi Azure Cosmos DB na liście Zasoby, a następnie kliknij pozycję **Eksplorator danych**.
10. Uruchom kwerendę domyślną "SELECT * FROM c" i sprawdź wyniki.  Należy zauważyć, że kwerenda ma pobrać reprezentację JSON elementów todo utworzone w kroku 7 powyżej.  Zachęcamy do eksperymentowania z zapytaniami; na przykład spróbuj uruchomić SELECT * FROM c GDZIE c.isComplete = true, aby zwrócić wszystkie elementy todo, które zostały oznaczone jako kompletne.
11. Zachęcamy do eksplorowania środowiska portalu usługi Azure Cosmos DB lub modyfikowania przykładowej aplikacji Todo.  Gdy będziesz gotowy, wdrudmy inny szablon.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Krok 3: Wdrażanie przykładu konta dokumentu i aplikacji sieci Web
Teraz zastosujmy drugi szablon.  Ten szablon jest przydatny do pokazania, jak można wstrzyknąć informacje o połączeniu usługi Azure Cosmos DB, takie jak punkt końcowy konta i klucz główny do aplikacji sieci web jako ustawienia aplikacji lub jako niestandardowy ciąg połączenia. Na przykład być może masz własną aplikację sieci web, którą chcesz wdrożyć przy za pomocą konta usługi Azure Cosmos DB i automatycznie wypełniać informacje o połączeniu podczas wdrażania.

> [!TIP]
> Szablon nie sprawdza, czy nazwa aplikacji sieci web i nazwa konta usługi Azure Cosmos DB wprowadzone poniżej są a) prawidłowe i b) dostępne.  Zdecydowanie zaleca się sprawdzenie dostępności nazw, które mają być dostępne przed przesłaniem wdrożenia.
> 
> 

1. W witrynie [Azure Portal](https://portal.azure.com)kliknij pozycję Nowy i wyszukaj hasło "Wdrożenie szablonu".
    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment1.png)
2. Wybierz element wdrażania szablonu i kliknij pozycję **Utwórz** ![zrzut ekranu interfejsu użytkownika wdrożenia szablonu](./media/create-website/TemplateDeployment2.png)
3. Kliknij **pozycję Edytuj szablon**, wklej zawartość pliku szablonu DocDBWebSite.json, a następnie kliknij przycisk **Zapisz**.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment3.png)
4. Kliknij **pozycję Edytuj parametry**, podaj wartości dla każdego z parametrów obowiązkowych, a następnie kliknij przycisk **OK**.  Używane są następujące parametry:
   
   1. NAZWA WITRYNY: Określa nazwę aplikacji sieci Web usługi App Service i jest używana do konstruowania adresu URL, który będzie używany do uzyskiwania dostępu do aplikacji sieci web (na przykład, jeśli określisz "mydemodocdbwebapp", adres URL, za pomocą którego uzyskujesz dostęp do aplikacji sieci web, jest mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Określa nazwę planu hostingu usługi App Service do utworzenia.
   3. LOKALIZACJA: Określa lokalizację platformy Azure, w której mają być utworzone zasoby usługi Azure Cosmos DB i zasobów aplikacji sieci web.
   4. DATABASEACCOUNTNAME: Określa nazwę konta usługi Azure Cosmos DB do utworzenia.   
      
      ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment4.png)
5. Wybierz istniejącą grupę zasobów lub podaj nazwę, aby stworzyć nową grupę zasobów, a następnie wybierz lokalizację dla grupy zasobów.

    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment5.png)
6. Kliknij **pozycję Przejrzyj warunki prawne**, **Zakup**, a następnie kliknij przycisk **Utwórz,** aby rozpocząć wdrażanie.  Wybierz **pozycję Przypnij do pulpitu nawigacyjnego, aby** wynikowe wdrożenie było łatwo widoczne na stronie głównej witryny azure portal.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment6.png)
7. Po zakończeniu wdrażania zostanie otwarte okienko Grupy zasobów.
   ![Zrzut ekranu przedstawiający okienko grupy zasobów](./media/create-website/TemplateDeployment7.png)  
8. Kliknij zasób aplikacji Sieci Web na liście Zasoby, a następnie kliknij pozycję **Ustawienia** ![aplikacji Zrzut ekranu grupy zasobów](./media/create-website/TemplateDeployment9.png)  
9. Należy zauważyć, jak istnieją ustawienia aplikacji dla punktu końcowego usługi Azure Cosmos DB i każdego klucza głównego usługi Azure Cosmos DB.

    ![Zrzut ekranu przedstawiający ustawienia aplikacji](./media/create-website/TemplateDeployment10.png)  
10. Możesz kontynuować eksplorowanie witryny Azure Portal lub śledzić jeden z naszych [przykładów](https://go.microsoft.com/fwlink/?LinkID=402386) usługi Azure Cosmos DB, aby utworzyć własną aplikację usługi Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Następne kroki
Gratulacje! Wdrożono usługę Azure Cosmos DB, aplikację sieci web usługi App Service i przykładową aplikację sieci web przy użyciu szablonów usługi Azure Resource Manager.

* Aby dowiedzieć się więcej o usłudze Azure Cosmos DB, kliknij [tutaj](https://azure.microsoft.com/services/cosmos-db/).
* Aby dowiedzieć się więcej o aplikacjach azure app service web, kliknij [tutaj](https://go.microsoft.com/fwlink/?LinkId=325362).
* Aby dowiedzieć się więcej o szablonach usługi Azure Resource Manager, kliknij [tutaj](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Co zostało zmienione
* Aby uzyskać przewodnik po zmianie z witryn sieci Web na usługę app service, zobacz: [Usługa azure app service i jej wpływ na istniejące usługi platformy Azure](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://go.microsoft.com/fwlink/?LinkId=523751) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 

