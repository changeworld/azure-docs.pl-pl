---
title: 'Wdrażanie aplikacji usług: Azure stosu | Dokumentacja firmy Microsoft'
description: Szczegółowe wskazówki dotyczące wdrażania usługi aplikacji Azure stosu
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
ms.date: 05/22/2018
ms.author: anwestg
ms.openlocfilehash: f44e6e917058306e37b9eb99819afda76a742389
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604271"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Dodawanie dostawcy zasobów usługi aplikacji Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

> [!IMPORTANT]
> Zastosowanie aktualizacji 1804 systemu Azure stosu zintegrowane lub wdrożyć najnowszy zestaw deweloperski stosu Azure przed wdrożeniem usługi Azure App Service 1.2.
>
>

Jako operator chmury Azure stosu można zapewnić użytkownikom możliwość tworzenia sieci web i aplikacji interfejsu API. Aby to zrobić, należy najpierw dodać [dostawcy zasobów usługi aplikacji](azure-stack-app-service-overview.md) do wdrożenia stosu Azure zgodnie z opisem w tym artykule. Po zainstalowaniu dostawcy zasobów usługi aplikacji, można dołączyć ją w Twojej oferty i planów. Użytkownicy mogą następnie subskrybować pobrać usługi i rozpocząć tworzenie aplikacji.

> [!IMPORTANT]
> Przed uruchomieniem Instalatora, upewnij się, że zostały wykonane wskazówek dotyczących [przed rozpoczęciem pracy](azure-stack-app-service-before-you-get-started.md).
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Uruchom Instalatora dostawcy zasobów usługi aplikacji

Instalowanie dostawcy zasobów usługi aplikacji w środowisku Azure stosu może potrwać co najmniej godzinę zależy od liczby wystąpień roli wybrany do wdrożenia. W trakcie tego procesu zostanie Instalatora:

* Tworzenie kontenera obiektów blob w określonym koncie magazynu Azure stosu.
* Tworzenie strefy DNS i wpisy dla aplikacji usługi.
* Rejestrowanie dostawcy zasobów usługi aplikacji.
* Zarejestruj elementy galerii aplikacji usługi.

Aby wdrożyć dostawcy zasobów usługi aplikacji, wykonaj następujące kroki:

1. Uruchom appservice.exe jako administrator z komputera, który może osiągnąć Azure stosu Azure Resource Management punktu końcowego administratora.

2. Kliknij przycisk **wdrażania usługi aplikacji lub uaktualnienia do najnowszej wersji**.

    ![Instalator usługi aplikacji][1]

3. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie kliknij przycisk **dalej**.

4. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie kliknij przycisk **dalej**.

5. Upewnij się, że informacje o konfiguracji chmury usługi aplikacji jest prawidłowa. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure stosu Development Kit, można zaakceptować wartości domyślne w tym miejscu. Jednak jeśli dostosowany opcji w przypadku wdrożenia stosu Azure, lub są wdrażane na zintegrowany system, należy edytować wartości w tym oknie, aby odzwierciedlić który. Na przykład jeśli używasz mycloud.com sufiks domeny punktu końcowego Azure stosu dzierżawy usługi Azure Resource Manager musi zmienić do zarządzania. &lt;region&gt;. mycloud.com. Po upewnieniu się informacje, kliknij przycisk **dalej**.

    ![Instalator usługi aplikacji][2]

6. Na następnej stronie:
    1. Kliknij przycisk **Connect** znajdujący się obok **subskrypcji platformy Azure stosu** pole.
        * Jeśli używasz usługi Azure Active Directory (Azure AD), wprowadź konto administratora usługi Azure AD i hasło podane podczas wdrażania usługi Azure stosu. Kliknij przycisk **Zaloguj**.
        * Jeśli używasz programu Active Directory Federation Services (AD FS), podaj konto administratora. Na przykład cloudadmin@azurestack.local. Wprowadź hasło, a następnie kliknij przycisk **logowania**.
    2. W **subskrypcji platformy Azure stosu** wybierz opcję **domyślny dostawca subskrypcji**.
    
    > [!NOTE]
    > Usługi aplikacji można wdrożyć tylko do **domyślny dostawca subskrypcji** w tej chwili.  W przyszłej aktualizacji usługi aplikacji — zostanie wdrożona na nową subskrypcję pomiaru wprowadzone w 1804 stosu Azure i wszystkie istniejące wdrożenia będą migrowane do tej nowej subskrypcji również.
    >
    >
    
    3. W **lokalizacji stosu Azure** wybierz lokalizację, do której odnosi się do regionu jest wdrażany do. Na przykład wybierz **lokalnego** Jeśli wdrażanie Azure stosu Development Kit.

    ![Instalator usługi aplikacji][3]

7. Masz teraz możliwość wdrożenia do istniejącej sieci wirtualnej, zgodnie z konfiguracją kroków [tutaj](azure-stack-app-service-before-you-get-started.md#virtual-network), lub zezwolić Instalator usługi aplikacji do tworzenia sieci wirtualnej i skojarzonych podsieci.
    1. Wybierz **Utwórz sieć wirtualną przy użyciu ustawień domyślnych**, zaakceptuj ustawienia domyślne i kliknij przycisk **dalej**, lub;
    2. Wybierz **użyć istniejącej sieci wirtualnej i podsieci**.
        1. Wybierz **grupy zasobów** zawierający sieci wirtualnej.
        2. Wybierz poprawny **sieci wirtualnej** nazwę chcesz wdrożyć w;
        3. Wybierz poprawny **podsieci** wartości dla każdej podsieci roli wymagane;
        4. Kliknij przycisk **Dalej**

    ![Instalator usługi aplikacji][4]

8. Wprowadź informacje dotyczące udziału plików, a następnie kliknij przycisk **dalej**. Adres udziału plików korzystać w pełni kwalifikowana nazwa domeny lub adres IP serwera plików. Na przykład \\\appservicefileserver.local.cloudapp.azurestack.external\websites, lub \\\10.0.0.1\websites.

   > [!NOTE]
   > Instalator podejmuje próbę Testuj łączność z udziałem plików przed kontynuowaniem.  Jednak jeśli zostało wybrane do wdrożenia w ramach istniejącej sieci wirtualnej, Instalator może nie można nawiązać połączenia z udziałem plików i ostrzeżenia pytaniem, czy chcesz kontynuować.  Sprawdź informacje dotyczące udziału plików i Kontynuuj, jeśli są poprawne.
   >
   >

   ![Instalator usługi aplikacji][7]

9. Na następnej stronie:
    1. W **identyfikator aplikacji tożsamości** wprowadź identyfikator GUID dla aplikacji, używane dla tożsamości (z usługi Azure AD).
    2. W **pliku certyfikatu tożsamości aplikacji** wprowadź (lub przejdź do) lokalizację pliku certyfikatu.
    3. W **hasło certyfikatu tożsamości aplikacji** wprowadź hasło dla certyfikatu. To hasło jest wprowadzone należy wziąć pod uwagę, gdy skrypt jest używany do tworzenia certyfikatów.
    4. W **plik certyfikatu głównego usługi Azure Resource Manager** wprowadź (lub przejdź do) lokalizację pliku certyfikatu.
    5. Kliknij przycisk **Dalej**.

    ![Instalator usługi aplikacji][9]

10. Dla każdego z trzech pól pliku certyfikatu, kliknij przycisk **Przeglądaj** i przejdź do pliku odpowiedni certyfikat. Podaj hasło dla każdego certyfikatu. Te certyfikaty są tymi, które zostały utworzone w [tworzenia wymaganych certyfikatów krok](azure-stack-app-service-before-you-get-started.md#get-certificates). Kliknij przycisk **dalej** po wprowadzeniu wszystkich informacji.

    | Box | Przykład nazwy pliku certyfikatu |
    | --- | --- |
    | **Plik certyfikatu SSL domyślne usługi aplikacji** | \_.appservice.local.AzureStack.external.pfx |
    | **Plik certyfikatu SSL usługi aplikacji interfejsu API** | api.appservice.local.AzureStack.external.pfx |
    | **Plik certyfikatu SSL wydawcy usługi aplikacji** | ftp.appservice.local.AzureStack.external.pfx |

    Jeśli sufiks domeny różnych są używane podczas tworzenia certyfikatów, nie używaj nazwy pliku certyfikatu *lokalnego. AzureStack.external*. Zamiast tego należy używać Twoich informacji domeny niestandardowej.

    ![Instalator usługi aplikacji][10]

11. Podaj szczegóły programu SQL Server dla wystąpienia serwera, używane do obsługi baz danych dostawcy zasobów usługi aplikacji, a następnie kliknij przycisk **dalej**. Instalator sprawdza właściwości połączenia SQL.

    > [!NOTE]
    > Instalator podejmuje próbę przetestowanie łączności z serwerem SQl Server przed kontynuowaniem.  Jednak jeśli zostało wybrane do wdrożenia w ramach istniejącej sieci wirtualnej, Instalator może nie móc połączyć się z serwerem SQL i ostrzeżenia pytaniem, czy chcesz kontynuować.  Sprawdź informacje dotyczące programu SQL Server i Kontynuuj, jeśli są poprawne.
    >
    >

    ![Instalator usługi aplikacji][11]

12. Przejrzyj opcje jednostki SKU i wystąpienia roli. Minimalna liczba wystąpień i minimalna jednostki SKU dla każdej roli we wdrożeniu ASDK umieścić wartości domyślne. Aby ułatwić planowanie rozmieszczenia udostępniane jest podsumowanie wymagań vCPU i pamięci. Po dokonaniu wyboru kliknij przycisk **dalej**.

    > [!NOTE]
    > W przypadku wdrożeń produkcyjnych, postępując zgodnie ze wskazówkami w [zaplanować role serwera usługi Azure App Service w stosie Azure wydajność](azure-stack-app-service-capacity-planning.md).
    >
    >

    | Rola | Minimalna wystąpień | Minimalna jednostki SKU | Uwagi |
    | --- | --- | --- | --- |
    | Kontroler | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Zarządza i obsługuje kondycji chmury usługi aplikacji. |
    | Zarządzanie | 1 | Standard_A2 - (2 Vcpu, 3584 MB) | Zarządza punktów końcowych aplikacji usługi Azure Resource Manager i interfejsu API, rozszerzenia portalu (administratora, dzierżawcy, funkcje portalu) i usługi danych. Do obsługi trybu failover, zwiększyć zalecane wystąpień na 2. |
    | Wydawca | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publikowanie zawartości za pośrednictwem sieci web i FTP wdrożenia. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Kieruje żądania do aplikacji usługi aplikacji. |
    | Udostępnionego procesu roboczego | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hosty aplikacji sieci web lub aplikacji interfejsu API i usługi Azure Functions. Można dodać więcej wystąpień. Uprawnienia operatora można zdefiniować Twojej oferty i wybierz warstwę wszystkie jednostki SKU. Warstw musi mieć co najmniej jeden vCPU. |

    ![Instalator usługi aplikacji][13]

    > [!NOTE]
    > **Podstawowy Windows Server 2016 nie jest obrazem obsługiwanych platform do użycia z usługi Azure App Service na stosie Azure.  Nie należy używać obrazów oceny wdrożeń produkcyjnych.**

13. W **wybierz obrazu platformy** wybierz obraz maszyny wirtualnej wdrażania systemu Windows Server 2016 z obrazów dostępne w dostawcy zasobów obliczeniowych w chmurze usługi aplikacji. Kliknij przycisk **Dalej**.

14. Na następnej stronie:
     1. Wprowadź hasło i nazwa użytkownika administratora maszyny wirtualnej w roli procesu roboczego.
     2. Wprowadź nazwę użytkownika administratora maszyny wirtualnej inne role i hasło.
     3. Kliknij przycisk **Dalej**.

    ![Instalator usługi aplikacji][15]    

15. Na stronie Podsumowanie:
    1. Sprawdź wybrane opcje wprowadzone. Aby wprowadzić zmiany, należy użyć **Wstecz** przycisków, aby przejść do poprzedniej strony.
    2. Jeśli konfiguracje są poprawne, zaznacz pole wyboru.
    3. Aby uruchomić wdrożenie, kliknij przycisk **dalej**.

    ![Instalator usługi aplikacji][16]

16. Na następnej stronie:
    1. Śledź postęp instalacji. Usługi aplikacji Azure stosu trwa około 60 minut do wdrożenia na podstawie wybranych domyślne.
    2. Po pomyślnym zakończeniu Instalatora, kliknij przycisk **zakończenia**.

    ![Instalator usługi aplikacji][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Sprawdź poprawność usługi aplikacji Azure stosu instalacji

1. W portalu administracyjnym stosu Azure, przejdź do **Administracja - App Service**.

2. W przeglądzie w stanie, sprawdź, który **stan** pokazuje **wszystkie role są gotowe**.

    ![Zarządzanie usługami aplikacji](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej i wewnętrzny adres IP do conenct do plików użytkownika, należy dodać reguły zabezpieczeń dla ruchu wychodzącego, włączanie ruchu SMB między podsieci procesu roboczego i serwerze plików.  Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
> * Źródło: wszystkie
> * Zakres portów źródłowych: *
> * Miejsce docelowe: Adresy IP
> * Docelowy zakres adresów IP: zakres adresów IP dla sieci plików
> * Zakres portów docelowych: 445
> * Protokół: TCP
> * Akcja: Zezwalaj
> * Priorytet: 700
> * Nazwa: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Przetestuj aplikację usługi Azure stosu

Po wdrażanie i rejestrowanie dostawcy zasobów usługi aplikacji, przetestować go, aby upewnić się, że użytkownicy będą mogli wdrażać sieci web i aplikacji API apps.

> [!NOTE]
> Należy utworzyć ofertę, która ma przestrzeń nazw Microsoft.Web w planie. Następnie należy mieć subskrypcję dzierżawy, która subskrybuje tej oferty. Aby uzyskać więcej informacji, zobacz [Utwórz oferty](azure-stack-create-offer.md) i [Utwórz plan](azure-stack-create-plan.md).
>
Możesz *musi* mających subskrypcję dzierżawy do tworzenia aplikacji korzystających z usługi aplikacji Azure stosu. Tylko możliwości, które administrator usługi można wykonać w portalu administracyjnym dotyczących administracji dostawcy zasobów usługi App Service. Te funkcje obejmują dodanie pojemności, konfigurowanie źródła wdrożenia i dodawanie warstwy procesu roboczego i jednostki SKU.
>
Do tworzenia sieci web, interfejsu API i Azure funkcje aplikacji, należy użyć portalu dzierżawcy i ma subskrypcję dzierżawy.

1. W portalu Azure stosu dzierżawy kliknij **nowy** > **sieci Web i mobilność** > **aplikacji sieci Web**.

2. Na **aplikacji sieci Web** bloku, wpisz nazwę w **aplikacji sieci Web** pole.

3. W obszarze **grupy zasobów**, kliknij przycisk **nowy**. Wpisz nazwę w **grupy zasobów** pole.

4. Kliknij pozycję **Plan/lokalizacja usługi App Service** > **Utwórz nowy**.

5. Na **planu usługi aplikacji** bloku, wpisz nazwę w **planu usługi aplikacji** pole.

6. Kliknij przycisk **warstwa cenowa** > **Shared wolne** lub **Shared Shared** > **wybierz**  >   **OK** > **utworzyć**.

7. W obszarze minuty kafelków dla nowej aplikacji sieci web pojawi się na pulpicie nawigacyjnym. Kliknij Kafelek.

8. Na **aplikacji sieci Web** bloku, kliknij przycisk **Przeglądaj** można wyświetlić domyślną witrynę sieci Web dla tej aplikacji.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Wdrażanie witryny WordPress, DNN lub Django (opcjonalnie)

1. W portalu Azure stosu dzierżawy kliknij **+**, przejdź do portalu Azure Marketplace, wdrożyć witrynę sieci Web Django i poczekaj na pomyślne zakończenie. Platforma sieci web Django korzysta z bazy danych w oparciu o system plików. Nie wymaga żadnych dostawców dodatkowych zasobów, takich jak SQL lub MySQL.

2. Jeśli wdrożono również dostawcy zasobów MySQL, można wdrażać witryny sieci Web WordPress z poziomu portalu Marketplace. Gdy zostanie wyświetlony monit o parametry bazy danych, wprowadź nazwę użytkownika jako *User1@Server1*, z nazwą użytkownika i nazwę serwera.

3. Jeśli wdrożono również dostawcy zasobów programu SQL Server, można wdrożyć DNN witryny sieci Web z witryny Marketplace. Gdy zostanie wyświetlony monit o parametry bazy danych, wybierz bazę danych na komputerze z uruchomionym programem SQL Server, połączoną z dostawcą zasobów.

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować innych [platforma jako usługa (PaaS) usługi](azure-stack-tools-paas-services.md).

- [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Dostawca zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md)

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
