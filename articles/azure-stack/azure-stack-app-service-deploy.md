---
title: 'Wdrażanie usługi App Services: Usługa Azure Stack | Dokumentacja firmy Microsoft'
description: Szczegółowe wskazówki dotyczące wdrażania usługi App Service w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: anwestg
ms.openlocfilehash: cd16bf400c5a5e5a07c7e2dc459d801e6fc810b9
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635377"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Dodaj dostawcę zasobów usługi App Service do usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Użyj się ze wskazówkami w tym artykule, aby wdrożyć usługi App Service w usłudze Azure Stack.

> [!IMPORTANT]  
> Zastosowanie aktualizacji 1809 do systemu Azure Stack zintegrowane, lub wdrożyć najnowszą usługi Azure Stack Development Kit (ASDK) przed wdrożeniem usługi Azure App Service 1.4.

Zapewnić użytkownikom możliwość tworzenia aplikacji interfejsu API sieci web i. Aby zezwolić użytkownikom na tworzenie tych aplikacji, należy:

 - Dodaj [dostawcy zasobów usługi App Service](azure-stack-app-service-overview.md) do wdrożenia usługi Azure Stack, korzystając z procedury opisanej w tym artykule.
 - Po zainstalowaniu dostawcy zasobów usługi App Service, można uwzględnić go w swojej oferty i plany. Użytkownicy mogą następnie subskrybować Uzyskaj usługi i zacznij tworzyć aplikacje.

> [!IMPORTANT]  
> Przed uruchomieniem Instalatora dostawcy zasobów, upewnij się, że wykonano wskazówki zawarte w [przed rozpoczęciem pracy](azure-stack-app-service-before-you-get-started.md).

## <a name="run-the-app-service-resource-provider-installer"></a>Uruchom Instalatora dostawcy zasobów usługi App Service

Instalowanie dostawcy zasobów usługi App Service ma co najmniej godzinę. Czas potrzebny jest zależny od roli liczbę wystąpień, możesz wdrożyć. Podczas wdrażania program Instalator uruchamia następujące zadania:

 - Utwórz kontener obiektów blob w ramach określonego konta magazynu usługi Azure Stack.
 - Tworzenie strefy DNS i wpisy dla usługi App Service.
 - Zarejestruj dostawcę zasobów usługi App Service.
 - Zarejestruj elementy galerii usługi App Service.

Aby wdrożyć dostawcy zasobów usługi App Service, wykonaj następujące kroki:

1. Uruchom appservice.exe jako administrator z komputera, na którym mogą uzyskiwać dostęp do usługi Azure Stack administratora Azure zasobu punktu końcowego zarządzania.

2. Wybierz **wdrażanie usługi App Service lub uaktualnienia do najnowszej wersji**.

    ![Instalator usługi App Service][1]

3. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie wybierz pozycję **dalej**.

4. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie wybierz pozycję **dalej**.

5. Upewnij się, że informacje o konfiguracji w chmurze usługi App Service jest poprawna. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure Stack Development Kit (ASDK), możesz zaakceptować wartości domyślne. Jednak jeśli dostosowano opcje po wdrożeniu ASDK lub są wdrażane na zintegrowanym systemie Azure Stack, musi Edytowanie wartości w tym oknie, w celu uwzględnienia różnic.

   Na przykład jeśli używasz mycloud.com sufiksu domeny, punkt końcowy usługi Azure Stack dzierżawy usługi Azure Resource Manager zmienić do zarządzania. &lt;region&gt;. mycloud.com. Przejrzyj te ustawienia, a następnie wybierz pozycję **dalej** Aby zapisać ustawienia.

   ![Instalator usługi App Service][2]

6. Na następnej stronie Instalatora usługi aplikacji wykonaj następujące kroki:

    a. Wybierz **Connect** obok **usługi Azure Stack subskrypcje**.

     - Jeśli używasz usługi Azure Active Directory (Azure AD), wprowadź konto administratora usługi Azure AD i hasło podane podczas wdrażania usługi Azure Stack. Wybierz **Zaloguj**.
     - Jeśli używasz usługi Active Directory Federation Services (AD FS) zapewniają konta administratora. Na przykład cloudadmin@azurestack.local. Wprowadź hasło, a następnie wybierz **Sign In**.

   b. W **usługi Azure Stack subskrypcje**, wybierz opcję **domyślne subskrypcję dostawcy**.

     > [!IMPORTANT]  
     > Usługa App Service **musi** można wdrożyć **domyślne subskrypcję dostawcy**.

   c. W **lokalizacje usługi Azure Stack**, wybierz lokalizację, do której odnosi się do regionu jest wdrażany na. Na przykład wybierz **lokalnego** Jeśli Twoje wdrożenie usługi Azure Stack Development Kit.

    ![Instalator usługi App Service][3]

7. Teraz można wdrożyć w istniejącej sieci wirtualnej, który został skonfigurowany [za pomocą tych kroków](azure-stack-app-service-before-you-get-started.md#virtual-network), lub pozwól, aby Instalator usługi App Service, Utwórz nową sieć wirtualną i podsieci. Tworzenie sieci wirtualnej, wykonaj następujące kroki:

   a. Wybierz **Utwórz sieć wirtualną z ustawieniami domyślnymi**, zaakceptuj wartości domyślne, a następnie wybierz **dalej**.

   b. Można także wybrać **użyć istniejącej sieci wirtualnej i podsieci**. Wykonaj następujące czynności:

     - Wybierz **grupy zasobów** zawierający sieci wirtualnej.
     - Wybierz **sieci wirtualnej** nazwę, którą chcesz wdrożyć.
     - Wybierz poprawnego **podsieci** wartości dla każdej z tych podsieci wymagana rola.
     - Wybierz opcję **Dalej**.

   ![Instalator usługi App Service][4]

8. Wprowadź informacje dotyczące udziału plików, a następnie wybierz pozycję **dalej**. Adres udziału plików należy użyć w pełni kwalifikowanej domeny nazwę (FQDN) lub adres IP serwera plików. Na przykład \\\appservicefileserver.local.cloudapp.azurestack.external\websites, lub \\\10.0.0.1\websites.

   >[!NOTE]
   >Instalator spróbuje ją przetestować łączność z udziałem plików przed kontynuowaniem. Jednak jeśli wdrażasz do istniejącej sieci wirtualnej, ten test łączności może się nie powieść. Otrzymuje ostrzeżenie i monit, aby kontynuować. Jeśli informacje o udziale plików jest poprawny, nadal wdrożenia.

   ![Instalator usługi App Service][7]

9. Na następnej stronie Instalatora usługi aplikacji wykonaj następujące kroki:

   a. W **identyfikator aplikacji tożsamości** wprowadź identyfikator GUID dla aplikacji, używany w przypadku tożsamości (z usługi Azure AD).

   b. W **plik certyfikatu aplikacji tożsamości** wprowadź (lub przejdź do) lokalizację pliku certyfikatu.

   c. W **hasło certyfikatu aplikacji tożsamości** wprowadź hasło dla certyfikatu. Jest to hasło, które zostały wprowadzone Zanotuj, gdy skrypt jest używany do tworzenia certyfikatów.

   d. W **plik certyfikatu głównego usługi Azure Resource Manager** wprowadź (lub przejdź do) lokalizację pliku certyfikatu.

   e. Wybierz opcję **Dalej**.

   ![Instalator usługi App Service][9]

10. Dla każdego z trzech pól pliku, wybierz **Przeglądaj** i przejdź do pliku odpowiedni certyfikat. Należy podać hasło dla każdego certyfikatu. Te certyfikaty są tymi, które zostały utworzone w [tworzenia wymaganych certyfikatów kroku](azure-stack-app-service-before-you-get-started.md#get-certificates). Wybierz **dalej** po wprowadzeniu wszystkich informacji.

    | Box | Przykładowe nazwy pliku certyfikatu |
    | --- | --- |
    | **Plik certyfikatu usługi App Service domyślnego protokołu SSL** | \_.appservice.local.AzureStack.external.pfx |
    | **Plik certyfikatu protokołu SSL interfejsu API usługi App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Plik certyfikatu protokołu SSL wydawcy usługi App** | ftp.appservice.local.AzureStack.external.pfx |

    Jeśli sufiks domeny różnych są używane podczas tworzenia certyfikatów, nie należy używać nazwy pliku certyfikatu *lokalnego. AzureStack.external*. Zamiast tego należy używać Twoich informacji domeny niestandardowej.

    ![Instalator usługi App Service][10]

11. Podaj szczegóły programu SQL Server dla wystąpienia serwera, używane do hostowania baz danych dostawcy zasobów usługi App Service, a następnie wybierz pozycję **dalej**. Instalator sprawdza właściwości połączenia SQL.

    > [!NOTE]
    > Instalator spróbuje ją przetestować łączność z serwerem SQL Server przed kontynuowaniem. Jednak jeśli wdrażasz do istniejącej sieci wirtualnej, ten test łączności może się nie powieść. Otrzymuje ostrzeżenie i monit, aby kontynuować. Jeśli informacje programu SQL Server jest poprawna, nadal wdrożenia.
    >
    > Usługi Azure App Service na usługi Azure Stack i nowszych wersjach 1.3 Instalator sprawdzi, czy serwer SQL jest zawierania bazy danych, włączone na poziomie serwera SQL.  Jeśli nie jest dostępne, zostanie wyświetlony monit z następującym wyjątkiem:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Zapoznaj się [informacje o wersji dla usługi Azure App Service w usłudze Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md) Aby uzyskać więcej informacji.

    ![Instalator usługi App Service][11]

12. Przejrzyj opcje jednostki SKU i wystąpienia roli. Minimalna liczba wystąpień i minimalne jednostki SKU dla każdej roli we wdrożeniu ASDK wypełnij wartości domyślne. Aby ułatwić planowanie wdrożenia usługi udostępniane jest podsumowanie wymagań dotyczących pamięci i procesorów wirtualnych. Po dokonaniu wyboru, wybierz **dalej**.

    >[!NOTE]
    >W przypadku wdrożeń produkcyjnych, postępując zgodnie ze wskazówkami w [wydajność ról serwera usługi Azure App Service w usłudze Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Rola | Minimalna wystąpień | Minimalna jednostki SKU | Uwagi |
    | --- | --- | --- | --- |
    | Kontroler | 1 | Standard_A2 - (2 procesory vCPU, 3584 MB) | Zarządza i utrzymuje kondycję chmury usługi App Service. |
    | Zarządzanie | 1 | Standard_A2 - (2 procesorów wirtualnych Vcpu, 3584 MB) | Zarządza punktów końcowych aplikacji usługi Azure Resource Manager i interfejsu API, rozszerzenia portalu (administratora, dzierżawcy, portalu funkcji) i usługi danych. Do obsługi trybu failover, zwiększenie zalecanych wystąpień na 2. |
    | Wydawca | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publikuje zawartości za pośrednictwem protokołu FTP i web deployment. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Kieruje żądania do aplikacji usługi App Service. |
    | Udostępnionego procesu roboczego | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hosty w sieci web lub aplikacji interfejsu API i aplikacje usługi Azure Functions. Można dodać więcej wystąpień. Operator można zdefiniować oferty i wybierz dowolną warstwę SKU. Warstw musi mieć co najmniej jeden procesor wirtualny vCPU. |

    ![Instalator usługi App Service][13]

    >[!NOTE]
    >**Windows Server 2016 Core nie jest obrazem obsługiwanych platform do użycia z usługą Azure App Service w usłudze Azure Stack.  Nie należy używać obrazów ocenę dla wdrożeń produkcyjnych.**

13. W **wybierz obraz platformy** wybierz obraz maszyny wirtualnej usługi wdrażania systemu Windows Server 2016 przy użyciu obrazów, które są dostępne w dostawcy zasobów obliczeniowych w chmurze usługi App Service. Wybierz opcję **Dalej**.

14. Na następnej stronie Instalatora usługi aplikacji wykonaj następujące kroki:

     a. Wprowadź nazwę użytkownika administratora maszyn wirtualnych roli procesu roboczego i hasło.

     b. Wprowadź nazwę użytkownika administratora maszyny wirtualnej inne role i hasło.

     c. Wybierz opcję **Dalej**.

    ![Instalator usługi App Service][15]

15. Na stronie podsumowania Instalatora usługi aplikacji wykonaj następujące kroki:

    a. Sprawdź wybrane opcje, które zostały wprowadzone. Aby wprowadzić zmiany, należy użyć **Wstecz** przycisków, aby przejść do poprzedniej strony.

    b. Jeśli konfiguracje są poprawne, zaznacz pole wyboru.

    c. Aby uruchomić wdrożenie, zaznacz **dalej**.

    ![Instalator usługi App Service][16]

16. Na następnej stronie Instalatora usługi aplikacji wykonaj następujące kroki:

    a. Śledź postęp instalacji. Usługi App Service w usłudze Azure Stack trwa około 60 minut wdrożenia oparte na wartości domyślne.

    b. Po pomyślnym zakończeniu pracy Instalatora wybierz **zakończenia**.

    ![Instalator usługi App Service][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Weryfikowanie usługi App Service w usłudze Azure Stack instalacji

1. W portalu administracyjnym usługi Azure Stack, przejdź do **administrowanie — usługa App Service**.

2. W obszarze Przegląd, w obszarze Stan zaznacz, aby zobaczyć, że **stan** Wyświetla **wszystkie role są gotowe**.

    ![Zarządzanie App Service](media/azure-stack-app-service-deploy/image12.png)

    Jeśli wdrażasz do istniejącej sieci wirtualnej i przy użyciu wewnętrznego adresu IP, aby nawiązać połączenie magazynowi, należy dodać regułę zabezpieczeń dla ruchu wychodzącego. Ta reguła umożliwia ruch SMB między podsieci procesów roboczych i serwera plików.  Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:

    - Źródło: wszystkie
    - Zakres portów źródłowych: *
    - Miejsce docelowe: Adresy IP
    - Docelowy zakres adresów IP: zakres adresów IP dla magazynowi
    - Zakres portów docelowych: 445
    - Protokół: TCP
    - Akcja: Zezwalaj
    - Priorytet: 700
    - Nazwa: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Testowanie usługi App Service w usłudze Azure Stack

Po wdrażanie i rejestrowanie dostawcy zasobów usługi App Service, go przetestować, aby upewnić się, że użytkownicy mogą wdrażać sieci web i aplikacji API apps.

>[!NOTE]
>Musisz utworzyć ofertę, która ma przestrzeń nazw Microsoft.Web w planie. Należy również subskrypcji dzierżawcy, która ją subskrybuje oferty. Aby uzyskać więcej informacji, zobacz [Utwórz ofertę](azure-stack-create-offer.md) i [Utwórz plan](azure-stack-create-plan.md).
>
>Możesz *musi* mają subskrypcję dzierżawy, aby utworzyć aplikacje, które używają usługi App Service w usłudze Azure Stack. Tylko zadania, które administratora usługi można wykonać w portalu administracyjnym są powiązane z zarządzaniem dostawcy zasobów usługi App Service. Obejmuje to dodanie pojemności, konfigurowanie źródeł wdrożenia i dodanie warstwy procesu roboczego i jednostki SKU.
>
>Do tworzenia sieci web, interfejsów API i Azure Functions aplikacje, należy użyć portalu dzierżawcy i masz subskrypcję dzierżawy.
>

Aby utworzyć test aplikacji sieci web, wykonaj następujące kroki:

1. W aplikacji portal użytkowników usługi Azure Stack, wybierz **+ Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

2. W obszarze **aplikacji sieci Web**, wprowadź nazwę w **aplikacji sieci Web**.

3. W obszarze **grupy zasobów**, wybierz opcję **New**. Wprowadź nazwę dla **grupy zasobów**.

4. Wybierz **plan usługi App Service/lokalizacja** > **Utwórz nowy**.

5. W obszarze **planu usługi App Service**, wprowadź nazwę dla **planu usługi App Service**.

6. Wybierz **warstwa cenowa** > **udostępnione bezpłatnie** lub **Shared współdzielona** > **wybierz**  >  **OK** > **tworzenie**.

7. Na pulpicie nawigacyjnym pojawi się Kafelek dla nowej aplikacji sieci web. Wybierz Kafelek.

8. Na **aplikacji sieci Web**, wybierz opcję **Przeglądaj** do wyświetlania domyślnej witryny sieci Web dla tej aplikacji.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Wdrażanie platformy WordPress, DNN lub Django witryny sieci Web (opcjonalnie)

1. W portalu dzierżawcy usługi Azure Stack, wybierz **+**, przejdź do portalu Azure Marketplace, wdrażanie witryny sieci Web Django i zaczekaj na zakończenie wdrożenia. Platforma sieci web Django korzysta z bazy danych opartych na systemie plików. Nie wymaga żadnych dostawców dodatkowych zasobów, takich jak SQL lub MySQL.

2. Jeśli wdrożono również dostawcy zasobów bazy danych MySQL, można wdrażać witryny sieci Web WordPress w witrynie Marketplace. Po wyświetleniu monitu dla bazy danych, parametrów, wprowadź nazwę użytkownika jako *User1@Server1*, nazwą użytkownika i nazwę serwera.

3. Jeśli wdrożono również dostawcę zasobów programu SQL Server, można wdrożyć DNN witryny sieci Web z witryny Marketplace. Po wyświetleniu monitu dla parametrów bazy danych, wybierz bazę danych na komputerze z uruchomionym programem SQL Server, która jest połączona z dostawcą zasobów.

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować inne [platformy jako usługi (PaaS)](azure-stack-tools-paas-services.md).

 - [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
 - [Dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
