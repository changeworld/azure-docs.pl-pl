---
title: Wdrażanie aplikacji sieci web przy użyciu szablonu — Azure Cosmos DB
description: Dowiedz się, jak wdrożyć konto usługi Azure Cosmos DB, Azure App Service Web Apps i przykładowej aplikacji sieci web przy użyciu szablonu usługi Azure Resource Manager.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: feab221c24034fe29df420b4f9eb6d84e06a90b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719340"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Wdrażanie usługi Azure Cosmos DB i Azure App Service Web Apps przy użyciu szablonu usługi Azure Resource Manager
W tym samouczku pokazano, jak szablon usługi Azure Resource Manager umożliwia wdrażanie i integrowanie [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) aplikacji sieci web i przykładowej aplikacji sieci web.

Za pomocą szablonów usługi Azure Resource Manager, można łatwo automatyzować wdrożenia i konfiguracji zasobów platformy Azure.  W tym samouczku pokazano, jak wdrożyć aplikację sieci web i automatycznie skonfigurować parametry połączenia konta usługi Azure Cosmos DB.

Po ukończeniu tego samouczka można odpowiedzieć na następujące pytania:  

* Jak używać szablonu usługi Azure Resource Manager do wdrażania i integrowanie konta usługi Azure Cosmos DB i aplikacji sieci web w usłudze Azure App Service?
* Jak używać szablonu usługi Azure Resource Manager do wdrażanie i integrowanie konta usługi Azure Cosmos DB, aplikacji sieci web w aplikacji sieci Web usługi App Service i aplikację Webdeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Wymagania wstępne
> [!TIP]
> Podczas tego samouczka nie zakłada się, musisz mieć doświadczenia, przy użyciu szablonów usługi Azure Resource Manager lub JSON, powinien, którą chcesz zmodyfikować odwołania szablonów lub opcje wdrażania, następnie wiedzę na temat każdego z tych obszarów jest wymagana.
> 
> 

Przed wykonaniem instrukcji zawartych w ramach tego samouczka, upewnij się, że masz subskrypcję platformy Azure. Azure to platforma z subskrypcji.  Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [opcje zakupu](https://azure.microsoft.com/pricing/purchase-options/), [oferty dla subskrybentów](https://azure.microsoft.com/pricing/member-offers/), lub [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Krok 1: Pobierz pliki szablonów
Zacznijmy od pobierania plików szablonów, które wymaga tego samouczka.

1. Pobierz [utworzenia konta usługi Azure Cosmos DB, aplikacje sieci Web i wdrażanie przykładowej aplikacji pokaz](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) szablonu do folderu lokalnego (na przykład C:\Azure Cosmos DBTemplates). Ten szablon wdraża konto usługi Azure Cosmos DB, aplikacja sieci web usługi App Service i aplikację sieci web.  Również automatycznie konfiguruje aplikacji sieci web, które umożliwiają połączenie z kontem usługi Azure Cosmos DB.
2. Pobierz [utworzyć konto usługi Azure Cosmos DB i przykładowej aplikacji sieci Web](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) szablonu do folderu lokalnego (na przykład C:\Azure Cosmos DBTemplates). Ten szablon wdraża konto usługi Azure Cosmos DB aplikacja sieci web usługi App Service i modyfikuje ustawienia aplikacji witryny można łatwo powierzchni informacje o połączeniu usługi Azure Cosmos DB, ale nie ma aplikacji sieci web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Krok 2: Wdrażanie usługi Azure Cosmos DB, aplikacji sieci web usługi App Service i przykładowej aplikacji w wersji demonstracyjnej
Teraz Zajmijmy się wdrożeniem pierwszego szablonu.

> [!TIP]
> Szablon nie sprawdza poprawności nazwy aplikacji sieci web i nazwy konta usługi Azure Cosmos DB, w następujący szablon czy) prawidłowe i (b) dostępne.  Zdecydowanie zaleca się, że możesz sprawdzić dostępności nazwy, która ma zostać podać przed przesłaniem wdrożenia.
> 
> 

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com), kliknij przycisk Nowy i wyszukaj frazę "Wdrożenie szablonu".
    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment1.png)
2. Wybierz element wdrożenia szablonu, a następnie kliknij przycisk **Utwórz** ![zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment2.png)
3. Kliknij przycisk **Edytuj szablon**, a następnie wklej zawartość pliku szablonu DocDBWebsiteTodo.json i kliknij przycisk **Zapisz**.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment3.png)
4. Kliknij przycisk **Edytuj parametry**, podaj wartości dla każdego z parametrów obowiązkowych i kliknij przycisk **OK**.  Parametry są następujące:
   
   1. SITEname: Określa nazwę aplikacji sieci Web App Service i służy do konstruowania adresu URL, który jest używany w celu uzyskania dostępu do aplikacji sieci Web (na przykład jeśli określisz wartość "mydemodocdbwebapp", adres URL, do którego uzyskujesz dostęp do aplikacji sieci Web, jest `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME: Określa nazwę planu hostingu usługi App Service, aby utworzyć.
   3. Lokalizacja: Określa lokalizacji platformy Azure, w której chcesz utworzyć zasobów aplikacji sieci web i usługi Azure Cosmos DB.
   4. DATABASEACCOUNTNAME: Określa nazwę konta usługi Azure Cosmos DB do utworzenia.   
      
      ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment4.png)
5. Wybierz istniejącą grupę zasobów lub podaj nazwę, aby utworzyć nową grupę zasobów, a następnie wybierz lokalizację dla grupy zasobów.

    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment5.png)
6. Kliknij przycisk **Przejrzyj postanowienia prawne**, **zakupu**, a następnie kliknij przycisk **Utwórz** aby rozpocząć wdrażanie.  Wybierz **Przypnij do pulpitu nawigacyjnego** więc wynikowy wdrożenia jest widoczny na stronie głównej portalu Azure.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment6.png)
7. Po zakończeniu wdrożenia zostanie otwarte okienko grupy zasobów.
   ![Zrzut ekranu przedstawiający okienko grupy zasobów](./media/create-website/TemplateDeployment7.png)  
8. Korzystanie z aplikacji, przejdź do adresu URL aplikacji sieci web (w powyższym przykładzie adres URL będzie mieć http://mydemodocdbwebapp.azurewebsites.net).  Zostanie wyświetlone następującej aplikacji sieci web:
   
   ![Przykładowa aplikacja Todo](./media/create-website/image2.png)
9. Przejdź dalej i Utwórz kilka zadań w aplikacji sieci web, a następnie wróć do okienka grupy zasobów w witrynie Azure portal. Kliknij zasób konta usługi Azure Cosmos DB na liście zasobów, a następnie kliknij przycisk **Eksplorator danych**.
10. Uruchom zapytanie domyślne "Wybierz * FROM c" i sprawdź wyniki.  Należy zauważyć, że zapytanie pobierze reprezentacji JSON wykonania, utworzony w kroku 7 powyżej.  Możesz eksperymentować z zapytań; na przykład, spróbuj uruchomić SELECT * FROM c WHERE c.isComplete = true, aby zwrócić wszystkie elementy zadań do wykonania, które zostały oznaczone jako ukończone.
11. Możesz eksplorować środowisko pracy w portalu usługi Azure Cosmos DB lub zmodyfikować przykładowej aplikacji Todo.  Gdy wszystko będzie gotowe, zajmijmy się wdrożeniem inny szablon.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Krok 3: Wdrażanie przykładowej aplikacji sieci web i konta dokumentu
Teraz Zajmijmy się wdrożeniem drugiego szablonu.  Ten szablon jest przydatny do pokazania, jak użytkownik może wprowadzać informacje o połączeniu usługi Azure Cosmos DB, takie jak punkt końcowy konta i klucz główny w aplikacji internetowej jako ustawienia aplikacji lub niestandardowe parametry połączenia. Na przykład być może masz własną aplikację sieci web, który chcesz wdrożyć przy użyciu konta usługi Azure Cosmos DB oraz korzystać z informacji o połączeniu, automatycznie wypełniane podczas wdrażania.

> [!TIP]
> Szablon nie zweryfikować, czy nazwa aplikacji sieci web i nazwę konta usługi Azure Cosmos DB wprowadzono poniżej są) prawidłowe i (b) dostępne.  Zdecydowanie zaleca się, że możesz sprawdzić dostępności nazwy, która ma zostać podać przed przesłaniem wdrożenia.
> 
> 

1. W [witryny Azure Portal](https://portal.azure.com), kliknij przycisk Nowy i wyszukaj frazę "Wdrożenie szablonu".
    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment1.png)
2. Wybierz element wdrożenia szablonu, a następnie kliknij przycisk **Utwórz** ![zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment2.png)
3. Kliknij przycisk **Edytuj szablon**, a następnie wklej zawartość pliku szablonu DocDBWebSite.json i kliknij przycisk **Zapisz**.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment3.png)
4. Kliknij przycisk **Edytuj parametry**, podaj wartości dla każdego z parametrów obowiązkowych i kliknij przycisk **OK**.  Parametry są następujące:
   
   1. Nazwa witryny: Określa nazwę aplikacji sieci web usługi App Service i jest używany do tworzenia adresu URL używanego do dostępu do aplikacji sieci web, (na przykład, jeśli określisz "mydemodocdbwebapp", a następnie za pomocą którego można uzyskać dostęp do aplikacji sieci web adres URL jest mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Określa nazwę planu hostingu usługi App Service, aby utworzyć.
   3. Lokalizacja: Określa lokalizacji platformy Azure, w której chcesz utworzyć zasobów aplikacji sieci web i usługi Azure Cosmos DB.
   4. DATABASEACCOUNTNAME: Określa nazwę konta usługi Azure Cosmos DB do utworzenia.   
      
      ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment4.png)
5. Wybierz istniejącą grupę zasobów lub podaj nazwę, aby utworzyć nową grupę zasobów, a następnie wybierz lokalizację dla grupy zasobów.

    ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment5.png)
6. Kliknij przycisk **Przejrzyj postanowienia prawne**, **zakupu**, a następnie kliknij przycisk **Utwórz** aby rozpocząć wdrażanie.  Wybierz **Przypnij do pulpitu nawigacyjnego** więc wynikowy wdrożenia jest widoczny na stronie głównej portalu Azure.
   ![Zrzut ekranu przedstawiający interfejs użytkownika wdrażania szablonu](./media/create-website/TemplateDeployment6.png)
7. Po zakończeniu wdrożenia zostanie otwarte okienko grupy zasobów.
   ![Zrzut ekranu przedstawiający okienko grupy zasobów](./media/create-website/TemplateDeployment7.png)  
8. Kliknij zasób aplikacji sieci Web na liście zasobów, a następnie kliknij przycisk **ustawienia aplikacji** ![zrzut ekranu przedstawiający grupy zasobów](./media/create-website/TemplateDeployment9.png)  
9. Należy pamiętać o tym, jak istnieją ustawienia aplikacji dla punktu końcowego usługi Azure Cosmos DB, a każdy z klucz główny usługi Azure Cosmos DB.

    ![Zrzut ekranu przedstawiający ustawienia aplikacji](./media/create-website/TemplateDeployment10.png)  
10. Możesz kontynuować poznawanie witryny Azure Portal lub użyj jednego z naszych usługi Azure Cosmos DB [przykłady](https://go.microsoft.com/fwlink/?LinkID=402386) Aby utworzyć aplikację usługi Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Następne kroki
Gratulacje! Udało Ci się wdrożyć usługi Azure Cosmos DB, aplikacji sieci web usługi App Service i przykładowej aplikacji sieci web przy użyciu szablonów usługi Azure Resource Manager.

* Aby dowiedzieć się więcej o usłudze Azure Cosmos DB, kliknij przycisk [tutaj](https://azure.microsoft.com/services/cosmos-db/).
* Aby dowiedzieć się więcej o usłudze Azure App Service Web apps, kliknij przycisk [tutaj](https://go.microsoft.com/fwlink/?LinkId=325362).
* Aby dowiedzieć się więcej na temat szablonów usługi Azure Resource Manager, kliknij przycisk [tutaj](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Co się zmieniło
* Przewodnik dotyczący przejścia od usługi Witryny sieci Web do usługi App Service można znaleźć w temacie [Azure App Service and Its Impact on Existing Azure Services](https://go.microsoft.com/fwlink/?LinkId=529714) (Usługa Azure App Service i jej wpływ na istniejące usługi platformy Azure).

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://go.microsoft.com/fwlink/?LinkId=523751) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych, bez zobowiązań.
> 
> 

