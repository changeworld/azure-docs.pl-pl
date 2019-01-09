---
title: Wdrażanie usługi App Service w środowisku offline w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki na temat wdrażania usługi App Service w środowisku bez połączenia usługi Azure Stack zabezpieczone przez usługi AD FS.
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
ms.date: 11/13/2018
ms.author: anwestg
ms.openlocfilehash: a3703133e5dc5f0525638562fab98f9416bcf26e
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104662"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Dodaj dostawcę zasobów usługi App Service do odłączonego środowiska usługi Azure Stack, zabezpieczone przez usługi AD FS

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!IMPORTANT]
> Dotyczą aktualizacji 1809 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem usługi Azure App Service 1.4.
>
>

Postępując zgodnie z instrukcjami w tym artykule, możesz zainstalować [dostawcy zasobów usługi App Service](azure-stack-app-service-overview.md) do środowiska usługi Azure Stack, który jest:

- nie jest połączony z Internetem
- zabezpieczone przez usługi Active Directory Federation Services (AD FS).

Aby dodać dostawcę zasobów usługi App Service do wdrożenia usługi Azure Stack w trybie offline, należy wykonać te zadania najwyższego poziomu:

1. Wykonaj [wstępnie wymagane kroki](azure-stack-app-service-before-you-get-started.md) (takich jak zakupienia certyfikatów, które może potrwać kilka dni, aby otrzymać).
2. [Pobierz i Wyodrębnij pliki instalacyjne i Pomocnika](azure-stack-app-service-before-you-get-started.md) maszyną połączony z Internetem.
3. Utwórz pakiet instalacyjny offline.
4. Uruchom plik Instalatora appservice.exe.

## <a name="create-an-offline-installation-package"></a>Utwórz pakiet instalacyjny offline

Aby wdrożyć usługi App Service w środowisku bez połączenia, należy najpierw utworzyć pakiet instalacyjny offline na komputerze, na którym jest połączony z Internetem.

1. Uruchom Instalatora AppService.exe na komputerze, na którym jest połączony z Internetem.

2. Kliknij przycisk **zaawansowane** > **Utwórz pakiet instalacyjny offline**.

    ![Instalator usługi App Service][1]

3. Instalator usługi App Service tworzy pakiet instalacyjny offline i zawiera ścieżkę do niego. Możesz kliknąć pozycję **Otwórz folder** aby otworzyć folder w Eksploratorze plików.

    ![Instalator usługi App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Skopiuj pliki Instalatora (AppService.exe) i pakiet instalacyjny offline do komputera-hosta usługi Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Ukończenie instalacji usługi App Service w usłudze Azure Stack w trybie offline

1. Uruchom appservice.exe jako administrator z komputera, który można osiągnąć punktu końcowego usługi Azure Stack administratora usługi Azure Resource Management.

2. Kliknij przycisk **zaawansowane** > **ukończenie instalacji w trybie offline**.

    ![Instalator usługi App Service][2]

3. Przejdź do lokalizacji pakietu instalacyjnego offline, została wcześniej utworzona, a następnie kliknij przycisk **dalej**.

    ![Instalator usługi App Service](media/azure-stack-app-service-deploy-offline/image04.png)

4. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie kliknij **dalej**.

5. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie kliknij **dalej**.

6. Upewnij się, że informacje o konfiguracji w chmurze usługi App Service jest poprawna. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure Stack Development Kit, możesz zaakceptować wartości domyślne w tym miejscu. Jednak jeśli dostosowano opcje po wdrożeniu usługi Azure Stack lub są wdrażane na zintegrowany system, należy edytować wartości w tym oknie, w celu odzwierciedlenia zmiany. Na przykład jeśli używasz mycloud.com sufiksu domeny, punkt końcowy usługi Azure Stack dzierżawy usługi Azure Resource Manager zmienić do zarządzania. <region>. mycloud.com. Po upewnieniu się informacje, kliknij przycisk **dalej**.

    ![Instalator usługi App Service][3]

7. Na następnej stronie:
    1. Kliknij przycisk **Connect** znajdujący się obok **usługi Azure Stack subskrypcje** pole.
        - Podaj konto administratora. Na przykład cloudadmin@azurestack.local. Wprowadź hasło, a następnie kliknij przycisk **Sign In**.
    2. W **usługi Azure Stack subskrypcje** wybierz opcję **domyślne subskrypcję dostawcy**.
    
    > [!NOTE]
    > Usługa App Service można wdrażać tylko w **domyślne subskrypcję dostawcy**.
    >
    
    3. W **lokalizacje usługi Azure Stack** wybierz lokalizację, do której odnosi się do regionu jest wdrażany na. Na przykład wybierz **lokalnego** Jeśli Twoje wdrożenie usługi Azure Stack Development Kit.
    4. Kliknij przycisk **Dalej**.

    ![Instalator usługi App Service][4]

8. Masz teraz możliwość wdrożenia do istniejącej sieci wirtualnej, zgodnie z konfiguracją kroki [tutaj](azure-stack-app-service-before-you-get-started.md#virtual-network), lub zezwolić Instalatora usługi App Service, aby utworzyć sieć wirtualną i skojarzone podsieci.
    1. Wybierz **Utwórz sieć wirtualną z ustawieniami domyślnymi**, zaakceptuj wartości domyślne, a następnie kliknij przycisk **dalej**, lub;
    2. Wybierz **użyć istniejącej sieci wirtualnej i podsieci**.
        1. Wybierz **grupy zasobów** zawierający sieci wirtualnej.
        2. Wybierz prawidłowy **sieci wirtualnej** nazwy, które chcesz wdrożyć w;
        3. Wybierz poprawnego **podsieci** wartości dla każdej z tych podsieci roli wymagane;
        4. Kliknij przycisk **Dalej**

    ![Instalator usługi App Service][5]

9. Wprowadź informacje dotyczące udziału plików, a następnie kliknij przycisk **dalej**. Adres udziału plików należy użyć w pełni kwalifikowaną nazwę domeny lub adres IP serwera plików. Na przykład \\\appservicefileserver.local.cloudapp.azurestack.external\websites, lub \\\10.0.0.1\websites

    > [!NOTE]
    > Instalator podejmuje próbę przetestuj łączność z udziałem plików przed kontynuowaniem.  Jednak jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej, Instalator nie może być możliwe nawiązanie połączenia z udziałem plików i wyświetla ostrzeżenie, pytaniem, czy chcesz kontynuować.  Sprawdź informacje dotyczące udziału plików, a następnie kontynuuj, jeśli są poprawne.
    >
    >

   ![Instalator usługi App Service][8]

10. Na następnej stronie:
    1. W **identyfikator aplikacji tożsamości** wprowadź identyfikator GUID dla aplikacji, używany w przypadku tożsamości (z usługi Azure AD).
    2. W **plik certyfikatu aplikacji tożsamości** wprowadź (lub przejdź do) lokalizację pliku certyfikatu.
    3. W **hasło certyfikatu aplikacji tożsamości** wprowadź hasło dla certyfikatu. Jest to hasło, które zostały wprowadzone Zanotuj, gdy skrypt jest używany do tworzenia certyfikatów.
    4. W **plik certyfikatu głównego usługi Azure Resource Manager** wprowadź (lub przejdź do) lokalizację pliku certyfikatu.
    5. Kliknij przycisk **Dalej**.

    ![Instalator usługi App Service][10]

11. Dla każdego z trzech pól pliku certyfikatu, kliknij przycisk **Przeglądaj** , a następnie przejdź do pliku odpowiedni certyfikat. Należy podać hasło dla każdego certyfikatu. Te certyfikaty są tymi, które zostały utworzone w [tworzenia wymaganych certyfikatów kroku](azure-stack-app-service-before-you-get-started.md#get-certificates). Kliknij przycisk **dalej** po wprowadzeniu wszystkich informacji.

    | Box | Przykładowe nazwy pliku certyfikatu |
    | --- | --- |
    | **Plik certyfikatu usługi App Service domyślnego protokołu SSL** | \_.appservice.local.AzureStack.external.pfx |
    | **Plik certyfikatu protokołu SSL interfejsu API usługi App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Plik certyfikatu protokołu SSL wydawcy usługi App** | ftp.appservice.local.AzureStack.external.pfx |

    Jeśli sufiks domeny różnych są używane podczas tworzenia certyfikatów, nie należy używać nazwy pliku certyfikatu *lokalnego. AzureStack.external*. Zamiast tego należy używać Twoich informacji domeny niestandardowej.

    ![Instalator usługi App Service][11]

12. Podaj szczegóły programu SQL Server dla wystąpienia serwera, używane do hostowania baz danych dostawcy zasobów usługi App Service, a następnie kliknij przycisk **dalej**. Instalator sprawdza właściwości połączenia SQL. Możesz **musi** wprowadź wewnętrzny adres ip lub w pełni kwalifikowaną nazwę domeny dla nazwy serwera SQL.

    > [!NOTE]
    > Instalator podejmuje próbę przetestowanie łączności z programem SQl Server przed kontynuowaniem.  Jednak jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej, Instalator nie może być możliwe nawiązanie połączenia z programem SQL Server i wyświetla ostrzeżenie z pytaniem, czy chcesz kontynuować.  Sprawdź informacje dotyczące programu SQL Server i Kontynuuj, jeśli są poprawne.
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
   
   ![Instalator usługi App Service][12]

13. Przejrzyj opcje jednostki SKU i wystąpienia roli. Wartości domyślne są wypełniane przy użyciu minimalnej liczby wystąpień i minimalne jednostki SKU dla każdej roli we wdrożeniu ASDK. Aby ułatwić planowanie wdrożenia usługi udostępniane jest podsumowanie wymagań dotyczących pamięci i procesorów wirtualnych. Po dokonaniu wyboru, kliknij przycisk **dalej**.

     > [!NOTE]
     > W przypadku wdrożeń produkcyjnych, postępuj zgodnie ze wskazówkami w [wydajność ról serwera usługi Azure App Service w usłudze Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Rola | Minimalna wystąpień | Minimalna jednostki SKU | Uwagi |
    | --- | --- | --- | --- |
    | Kontroler | 1 | Standard_A2 - (2 procesory vCPU, 3584 MB) | Zarządza i utrzymuje kondycję chmury usługi App Service. |
    | Zarządzanie | 1 | Standard_A2 - (2 procesorów wirtualnych Vcpu, 3584 MB) | Zarządza punktów końcowych aplikacji usługi Azure Resource Manager i interfejsu API, rozszerzenia portalu (administratora, dzierżawcy, portalu funkcji) i usługi danych. Do obsługi trybu failover, zwiększenie zalecanych wystąpień na 2. |
    | Wydawca | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publikuje zawartości za pośrednictwem protokołu FTP i web deployment. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Kieruje żądania do aplikacji usługi App Service. |
    | Udostępnionego procesu roboczego | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hosty w sieci web lub aplikacji interfejsu API i aplikacje usługi Azure Functions. Można dodać więcej wystąpień. Operator można zdefiniować oferty i wybierz dowolną warstwę SKU. Warstw musi mieć co najmniej jeden procesor wirtualny vCPU. |

    ![Instalator usługi App Service][14]

    > [!NOTE]
    > **Windows Server 2016 Core nie jest obrazem obsługiwanych platform do użycia z usługą Azure App Service w usłudze Azure Stack.  Nie należy używać obrazów ocenę dla wdrożeń produkcyjnych.  Usługa Azure App Service w usłudze Azure Stack wymaga, że Microsoft.NET 3.5.1 SP1 została aktywowana w obrazie używane w ramach wdrożenia.   Portal Marketplace zespolone systemu Windows Server 2016, obrazy nie mają włączenia tej funkcji, w związku z tym należy utworzyć i używać obrazu systemu Windows Server 2016 za pomocą wstępnie włączeniu tej opcji.**

14. W **wybierz obraz platformy** wybierz obraz maszyny wirtualnej usługi wdrażania systemu Windows Server 2016 od tych, które są dostępne w dostawcy zasobów obliczeniowych w chmurze usługi App Service. Kliknij przycisk **Dalej**.

15. Na następnej stronie:
     1. Wprowadź nazwę użytkownika administratora maszyn wirtualnych roli procesu roboczego i hasło.
     2. Wprowadź nazwę użytkownika administratora maszyny wirtualnej inne role i hasło.
     3. Kliknij przycisk **Dalej**.

    ![Instalator usługi App Service][16]

16. Na stronie podsumowania:
    1. Sprawdź wybrane opcje, które zostały wprowadzone. Aby wprowadzić zmiany, należy użyć **Wstecz** przycisków, aby przejść do poprzedniej strony.
    2. Jeśli konfiguracje są poprawne, zaznacz pole wyboru.
    3. Aby uruchomić wdrożenie, kliknij przycisk **dalej**.

    ![Instalator usługi App Service][17]

17. Na następnej stronie:
    1. Śledź postęp instalacji. Usługi App Service w usłudze Azure Stack trwa około 60 minut wdrożenia oparte na wartości domyślne.
    2. Po pomyślnym zakończeniu pracy Instalatora kliknij przycisk **zakończenia**.

    ![Instalator usługi App Service][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Weryfikowanie usługi App Service w usłudze Azure Stack instalacji

1. W portalu administracyjnym usługi Azure Stack, przejdź do **administrowanie — usługa App Service**.

2. W obszarze Przegląd, w obszarze Stan zaznacz, aby zobaczyć, że **stan** Wyświetla **wszystkie role są gotowe**.

    ![Zarządzanie App Service](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> Jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej i wewnętrznego adresu IP, aby nawiązać połączenie magazynowi, należy dodać regułę zabezpieczeń dla ruchu wychodzącego włączanie ruchu SMB między podsieci procesów roboczych i serwera plików.  Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
> * Źródło: Dowolne
> * Zakres portów źródłowych: *
> * Miejsce docelowe: Adresy IP
> * Docelowy zakres adresów IP: Zakres adresów IP dla magazynowi
> * Zakres portów docelowych: 445
> * Protokół: TCP
> * Akcja: Zezwalaj
> * Priorytet: 700
> * Nazwa: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Testowanie usługi App Service w usłudze Azure Stack

Po wdrażanie i rejestrowanie dostawcy zasobów usługi App Service, go przetestować, aby upewnić się, że użytkownicy mogą wdrażać sieci web i aplikacji API apps.

> [!NOTE]
> Należy utworzyć ofertę, który zawiera przestrzeń nazw Microsoft.Web w ramach planu. Następnie musisz mieć subskrypcję dzierżawy, która ją subskrybuje tej oferty. Aby uzyskać więcej informacji, zobacz [Utwórz ofertę](azure-stack-create-offer.md) i [Utwórz plan](azure-stack-create-plan.md).
>
> Możesz *musi* mają subskrypcję dzierżawy, aby utworzyć aplikacje, które używają usługi App Service w usłudze Azure Stack. Tylko możliwości, których administrator usługi można wykonać z poziomu portalu administracyjnego są związane z administracyjnej dostawcy zasobów usługi App Service. Te funkcje obejmują dodanie pojemności, konfigurowanie źródeł wdrożenia i dodanie warstwy procesu roboczego i jednostki SKU.
>
> Począwszy od trzeciego technical preview do utworzenia sieci web, interfejsów API i Azure Functions aplikacje, należy użyć portalu dzierżawcy i masz subskrypcję dzierżawy.

1. W portalu dzierżawcy usługi Azure Stack, kliknij przycisk **+ Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

2. Na **aplikacji sieci Web** bloku, wpisz nazwę w **aplikacji sieci Web** pole.

3. W obszarze **grupy zasobów**, kliknij przycisk **New**. Wpisz nazwę w **grupy zasobów** pole.

4. Kliknij pozycję **Plan/lokalizacja usługi App Service** > **Utwórz nowy**.

5. Na **planu usługi App Service** bloku, wpisz nazwę w **planu usługi App Service** pole.

6. Kliknij przycisk **warstwa cenowa** > **udostępnione bezpłatnie** lub **Shared współdzielona** > **wybierz**  >   **OK** > **tworzenie**.

7. W obszarze minuty dla nowej aplikacji sieci web pojawi się Kafelek na pulpicie nawigacyjnym. Kliknij Kafelek.

8. Na **aplikacji sieci Web** bloku kliknij **Przeglądaj** do wyświetlania domyślnej witryny sieci Web dla tej aplikacji.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Wdrażanie platformy WordPress, DNN lub Django witryny sieci Web (opcjonalnie)

1. W portalu dzierżawcy usługi Azure Stack, kliknij przycisk **+**, przejdź do portalu Azure Marketplace, wdrażanie witryny sieci Web Django i poczekaj na pomyślne zakończenie. Platforma sieci web Django korzysta z bazy danych opartych na systemie plików. Nie wymaga żadnych dostawców dodatkowych zasobów, takich jak SQL lub MySQL.

2. Jeśli wdrożono również dostawcy zasobów bazy danych MySQL, można wdrażać witryny sieci Web WordPress w witrynie Marketplace. Po wyświetleniu monitu dla bazy danych, parametrów, wprowadź nazwę użytkownika jako *User1@Server1*, nazwą użytkownika i nazwę serwera.

3. Jeśli wdrożono również dostawcę zasobów programu SQL Server, można wdrożyć DNN witryny sieci Web z witryny Marketplace. Po wyświetleniu monitu dla parametrów bazy danych, wybierz bazę danych na komputerze z uruchomionym programem SQL Server, która jest połączona z dostawcą zasobów.

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować inne [platformy jako usługi (PaaS)](azure-stack-tools-paas-services.md).

- [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
