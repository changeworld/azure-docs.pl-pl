---
title: Wdrażanie chmury hybrydowej, przy użyciu platformy Azure i usługi Azure Stack | Dokumentacja firmy Microsoft
description: Informacje o wdrażaniu chmury hybrydowej, przy użyciu platformy Azure i usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: e4da34be5b1e9eb2675f540fe4069cfd3dc1d056
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49410200"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Samouczek: Wdrażanie hybrydowe rozwiązanie w chmurze dzięki platformie Azure i usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

W tym samouczku dowiesz się, jak wdrożyć hybrydowe rozwiązanie w chmurze, która korzysta z chmury publicznej platformy Azure i w chmurze prywatnej usługi Azure Stack.

Za pomocą hybrydowe rozwiązanie w chmurze, możesz połączyć zgodności zalety chmury prywatnej dzięki możliwościom skalowania chmury publicznej. Ponadto deweloperzy można korzystać z zalet ekosystemu deweloperów firmy Microsoft i dotyczą ich umiejętności środowiskach chmurowych i lokalnych.

## <a name="overview-and-assumptions"></a>Omówienie i założenia

Można wykonać w tym samouczku, aby skonfigurować przepływ pracy, która pozwala deweloperom na wdrażanie aplikacji sieci web identyczne chmury publicznej i prywatnej chmury. Ta aplikacja będzie uzyskanie dostępu do sieci Routing-Internet, hostowanych w chmurze prywatnej. Tych aplikacji sieci web są monitorowane i kiedy ma największy wzrost ruchu, program modyfikuje rekordy DNS przekierowywanie ruchu do chmury publicznej. Gdy ruch spadnie do poziomu przed kolekcji, ruch jest kierowany do chmury prywatnej.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> - Wdróż serwer bazy danych programu SQL Server połączenia hybrydowego.
> - Łączenie aplikacji internetowej na platformie Azure w globalnej sieci hybrydowych.
> - Konfigurowanie systemu DNS do skalowania wielu chmur.
> - Skonfiguruj certyfikaty SSL służące do skalowania wielu chmur.
> - Konfigurowanie i wdrażanie aplikacji sieci web.
> - Tworzenie profilu usługi Traffic Manager i skonfiguruj go pod kątem skalowania wielu chmur.
> - Konfigurowanie usługi Application Insights, monitorowania i alertów w połączeniu z zwiększonego ruchu.
> - Skonfiguruj automatyczne ruchu przełączania się między globalnej platformy Azure i usługi Azure Stack.

### <a name="assumptions"></a>Założenia

Ten samouczek zakłada, że masz podstawową wiedzę na temat globalnej platformy Azure i usługi Azure Stack. Aby dowiedzieć się więcej, przed rozpoczęciem tego samouczka, należy przejrzeć następujące artykuły:

 - [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Kluczowe pojęcia usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

Ten samouczek zakłada również, że masz subskrypcję platformy Azure. Jeśli nie masz subskrypcji, możesz to zrobić [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, upewnij się, że spełniasz następujące wymagania:

- Azure Stack Development Kit (ASDK) lub subskrypcji w zintegrowany System usługi Azure Stack. Aby wdrożyć usługi Azure Stack Development Kit, postępuj zgodnie z instrukcjami [wdrażanie ASDK za pomocą Instalatora](../asdk/asdk-deploy.md).
- Instalacja usługi Azure Stack powinna mieć zainstalowane następujące oprogramowanie:
  - Usługa Azure App Service. Praca z operatorem usługi Azure Stack można wdrożyć i skonfigurować usługi Azure App Service w środowisku. Ten samouczek wymaga usługi App Service ma co najmniej jeden (1) dostępne dedykowanych procesów roboczych roli.
  - Obraz systemu Windows Server 2016
  - Ustawienia systemu Windows Server 2016 z obrazem programu Microsoft SQL Server
  - Odpowiednie plany i oferty
 - Nazwy domeny dla aplikacji sieci web. Jeśli nie masz nazwy domeny, możesz kupić go od dostawcy domeny, np. GoDaddy, Bluehost i InMotion informacji.
- Certyfikat SSL dla domeny z zaufanego urzędu certyfikacji takich jak LetsEncrypt.
- Aplikacja sieci web, który komunikuje się z bazą danych programu SQL Server i obsługuje usługę Application Insights. Możesz pobrać [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) przykładowej aplikacji z usługi GitHub.
- Sieć hybrydowa między siecią wirtualną platformy Azure i siecią wirtualną usługi Azure Stack. Aby uzyskać szczegółowe instrukcje, zobacz [skonfigurować łączność w chmurze hybrydowej z platformą Azure i usługi Azure Stack](azure-stack-solution-hybrid-connectivity.md).

- Hybrydowe ciągłej integracji/ciągłego wdrażania (CI/CD) potoku za pomocą agenta kompilacji prywatnych w usłudze Azure Stack. Aby uzyskać szczegółowe instrukcje, zobacz [skonfigurować tożsamość w chmurze hybrydowej z aplikacjami platformy Azure i usługi Azure Stack](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Wdrażanie serwera bazy danych programu SQL Server połączenia hybrydowego

1. Zaloguj się do portalu użytkownika usługi Azure Stack.

2. Na **pulpit nawigacyjny**, wybierz opcję **Marketplace**.

    ![Portal Marketplace Auzure stosu](media/azure-stack-solution-hybrid-cloud/image1.png)

3. W **Marketplace**, wybierz opcję **obliczenia**, a następnie wybierz **więcej**. W obszarze **więcej**, wybierz opcję **Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server** obrazu.

    ![Wybieranie obrazu maszyny wirtualnej](media/azure-stack-solution-hybrid-cloud/image2.png)

4. Na **Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server** wybierz **Utwórz**.

5. Na **podstawy > Konfigurowanie ustawień podstawowych**, podaj **nazwa** dla maszyny wirtualnej (VM), **nazwy użytkownika** dla administratora systemu SQL Server i **hasło** skojarzenia zabezpieczeń.  Z **subskrypcji** listy rozwijanej wybierz subskrypcję, której przeprowadzasz wdrożenie. Aby uzyskać **grupy zasobów**, użyj **wybierz istniejące** i umieścić maszynę Wirtualną w tej samej grupie zasobów co aplikacja sieci web usługi Azure Stack.

    ![Konfigurowanie ustawień podstawowych dla maszyny Wirtualnej](media/azure-stack-solution-hybrid-cloud/image3.png)

6. W obszarze **rozmiar**, wybierz rozmiar maszyny wirtualnej. W tym samouczku zalecamy A2_Standard lub DS2_V2_Standard.

7. W obszarze **Ustawienia > Konfiguruj funkcje opcjonalne**, skonfiguruj następujące ustawienia:

    - **Konto magazynu**. Utwórz nowe konto, jeśli potrzebna jest jedna.
    - **Sieć wirtualna**

      > [!Important]  
      > Upewnij się, że maszyna wirtualna serwera SQL został wdrożony w tej samej sieci wirtualnej jako bramy sieci VPN.

    - **Publiczny adres IP**. Można użyć ustawień domyślnych.
    - **Sieciowa grupa zabezpieczeń** (NSG). Tworzenie nowej sieciowej grupy zabezpieczeń.
    - **Monitorowanie i rozszerzenia**. Zachowaj ustawienia domyślne.
    - **Konto magazynu diagnostyki**. Utwórz nowe konto, jeśli potrzebna jest jedna.
    - Wybierz **OK** Aby zapisać konfigurację.

    ![Konfigurowanie funkcji opcjonalnych](media/azure-stack-solution-hybrid-cloud/image4.png)

1. W obszarze **ustawień programu SQL Server**, skonfiguruj następujące ustawienia:
   - Aby uzyskać **łączność z serwerem SQL**i wybierz opcję **publiczne (Internet)**.
   - Aby uzyskać **portu**, Zachowaj wartość domyślną, **1433**.
   - Aby uzyskać **uwierzytelniania programu SQL**, wybierz opcję **Włącz**.

     > [!Note]  
     > Po włączeniu uwierzytelniania SQL, jego powinny automatycznie wypełniać informacjami "SQLAdmin", który został skonfigurowany w **podstawy**.

   - Dla pozostałych ustawień pozostaw wartości domyślne. Kliknij przycisk **OK**.

    ![Skonfiguruj ustawienia programu SQL Server](media/azure-stack-solution-hybrid-cloud/image5.png)

9. Na **Podsumowanie**, sprawdź konfigurację maszyny wirtualnej, a następnie wybierz **OK** rozpocząć wdrażanie.

    ![Podsumowanie konfiguracji](media/azure-stack-solution-hybrid-cloud/image6.png)

10. Potrwa chwilę, aby utworzyć nową maszynę Wirtualną. Można wyświetlić stan maszyn wirtualnych w **maszyn wirtualnych**.

    ![Maszyny wirtualne](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Tworzenie aplikacji sieci web na platformie Azure i usługi Azure Stack

Azure App Service ułatwia uruchamiania i zarządzania nimi aplikacji sieci web. Ponieważ usługi Azure Stack jest spójny z platformą Azure, usługi App Service można uruchamiać w obu środowiskach. Użyjesz usługi App Service do hostowania aplikacji.

### <a name="create-web-apps"></a>Tworzenie aplikacji sieci web

1. Tworzenie aplikacji internetowej na platformie Azure, postępując zgodnie z instrukcjami wyświetlanymi w [Zarządzanie plan usługi App Service na platformie Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Upewnij się, że umieszczenie w aplikacji sieci web w tej samej subskrypcji i grupie zasobów jako sieci hybrydowych.

2. Powtórz poprzedni krok (1) w usłudze Azure Stack.

### <a name="add-route-for-azure-stack"></a>Dodaj trasę dla usługi Azure Stack

App Service w usłudze Azure Stack musi obsługiwać Routing z publicznej sieci Internet, aby umożliwić użytkownikom dostęp do Twoich aplikacji. Jeśli usługi Azure Stack jest dostępny z Internetu, zanotuj publicznego adresu IP lub adres URL dla aplikacji sieci web usługi Azure Stack.

Jeśli używasz ASDK [skonfigurować statyczne mapowanie translatora adresów Sieciowych](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) do udostępnienia usługi App Service na zewnątrz środowiska wirtualnego.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Łączenie aplikacji internetowej na platformie Azure do sieci hybrydowych

Aby zapewnić łączność między sieci web frontonu na platformie Azure i bazy danych SQL Server w usłudze Azure Stack w aplikacji sieci web połączyć sieć hybrydowa między platformą Azure i usługi Azure Stack. Aby włączyć łączność, musisz:

- Konfigurowanie połączenia punkt lokacja
- Konfigurowanie aplikacji internetowej
- Zmodyfikować bramę sieci lokalnej w usłudze Azure Stack.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Konfigurowanie sieci wirtualnej platformy Azure dla połączenia punkt lokacja

Brama sieci wirtualnej platformy Azure po stronie sieć hybrydowa muszą zezwalać na połączenia punkt lokacja, do integracji z usługą Azure App Service.

1. Na platformie Azure przejdź do strony bramy sieci wirtualnej. W obszarze **ustawienia**, wybierz opcję **Point-to-site configuration**.

    ![Opcja Point-to-site](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Wybierz **teraz skonfigurować** skonfigurować punkt lokacja.

    ![Rozpocznij Konfiguracja punktu do lokacji](media/azure-stack-solution-hybrid-cloud/image9.png)

3. Na **Point-to-site** konfiguracji wpisz zakresu prywatnych adresów IP, które mają być używane w **puli adresów**.

   > [!Note]  
   > Upewnij się, że zakres, który określisz nie pokrywa się z żadnym z zakresów adresów, już używana przez podsieci w globalnej platformy Azure lub usługi Azure Stack składniki sieci hybrydowych.

   W obszarze **Typ tunelu**, usuń zaznaczenie pola wyboru **IKEv2 sieci VPN**. Wybierz **Zapisz** można ukończyć konfigurowanie punkt lokacja.

   ![Ustawienia punktu do lokacji](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Integrowanie aplikacji w usłudze Azure App Service za pomocą sieci hybrydowych

1. Aby połączyć aplikację z siecią wirtualną platformy Azure, postępuj zgodnie z instrukcjami [włączenie integracji sieci wirtualnej](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration).

2. Przejdź do **ustawienia** dla planu usługi App Service, hostowanie aplikacji sieci web. W **ustawienia**, wybierz opcję **sieć**.

    ![Konfigurowanie sieci](media/azure-stack-solution-hybrid-cloud/image11.png)

3. W **Integracja z siecią Wirtualną**, wybierz opcję **kliknij tutaj, aby zarządzać**.

    ![Zarządzanie Integracja sieci Wirtualnej](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Wybierz sieć Wirtualną, którą chcesz skonfigurować. W obszarze **IP adresów kierowane do sieci Wirtualnej**, wprowadź zakres adresów IP dla sieci wirtualnej platformy Azure, sieci wirtualnej usługi Azure Stack i adresowe punkt lokacja. Wybierz **Zapisz** Aby zweryfikować i zapisać te ustawienia.

    ![Zakresy adresów IP do rozsyłania](media/azure-stack-solution-hybrid-cloud/image13.png)

Aby dowiedzieć się, jak usługa App Service integruje się z sieciami wirtualnymi platformy Azure, zobacz [Integrowanie aplikacji z usługą Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Konfigurowanie sieci wirtualnej w usłudze Azure Stack

Brama sieci lokalnej w sieci wirtualnej usługi Azure Stack musi być skonfigurowane do kierowania ruchu z zakresu adresów point-to-site usługi App Service.

1. W usłudze Azure Stack, przejdź do **bramy sieci lokalnej**. W obszarze **ustawienia**, wybierz opcję **konfiguracji**.

    ![Opcja konfiguracji bramy](media/azure-stack-solution-hybrid-cloud/image14.png)

2. W **przestrzeń adresowa**, wprowadź zakres adresów punkt lokacja dla bramy sieci wirtualnej w wybranych Azure.l **Zapisz** Aby to zweryfikować i zapisać tę konfigurację.

    ![Przestrzeń adresowa punkt lokacja](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>Konfigurowanie systemu DNS do skalowania wielu chmur

Konfigurując prawidłowo DNS dla aplikacji w chmurze między, użytkownicy mogą korzystać z globalnej platformy Azure i usługi Azure Stack wystąpienia aplikacji sieci web. Gdy obciążenie rośnie lub maleje, konfiguracji DNS na potrzeby tego samouczka umożliwia również usługi Azure Traffic Manager kierować ruchem.

Ten samouczek używa usługi Azure DNS do zarządzania usługi DNS. (Domeny usługi app Service nie będzie działać.)

### <a name="create-subdomains"></a>Tworzenia poddomen

Ponieważ usługi Traffic Manager opiera się na rekordy CNAME DNS, poddomeny, konieczne są prawidłowo kierowania ruchu do punktów końcowych. Aby uzyskać więcej informacji na temat mapowania domeny i rekordów DNS, zobacz [mapowanie domen za pomocą usługi Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Azure umożliwia dostęp do aplikacji sieci web punktu końcowego, utworzysz poddomeny przez użytkowników. W tym samouczku, można użyć **app.northwind.com**, ale należy dostosować tę wartość na podstawie własnej domeny.

Należy także utworzyć poddomeny przy użyciu rekordu A dla punktu końcowego usługi Azure Stack. Możesz użyć **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Konfigurowanie domeny niestandardowej na platformie Azure

1. Dodaj **app.northwind.com** nazwy hosta do aplikacji sieci web platformy Azure przy użyciu [mapowanie rekordu CNAME w usłudze Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Konfigurowanie domen niestandardowych w usłudze Azure Stack

1. Dodaj **azurestack.northwind.com** nazwę hosta aplikacji sieci web usługi Azure Stack przez [mapowanie rekordu A w usłudze Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Dla aplikacji usługi App Service, należy użyć adresu IP routingu internetowego.

2. Dodaj **app.northwind.com** nazwę hosta aplikacji sieci web usługi Azure Stack przez [mapowanie rekordu CNAME w usłudze Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Użyj nazwy hosta, skonfigurowanego w poprzednim kroku (1) jako element docelowy dla tego rekordu CNAME.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Konfigurowanie certyfikatów protokołu SSL do skalowania wielu chmur

Należy upewnić się, że dane poufne zbierane przez aplikację sieci web jest bezpieczne podczas przesyłania do i przechowywane w bazie danych SQL.

Należy skonfigurować usługi aplikacji sieci web platformy Azure i usługi Azure Stack używać certyfikatów protokołu SSL dla całego ruchu przychodzącego.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Dodawanie protokół SSL na platformę Azure i usługi Azure Stack

Aby dodać protokół SSL na platformie Azure:

1. Upewnij się, że certyfikat protokołu SSL, który można uzyskać nadaje się do domeny podrzędnej, który został utworzony. (Jest można używać certyfikatów z symbolami wieloznacznymi).

2. Na platformie Azure, postępuj zgodnie z instrukcjami **przygotowywanie aplikacji sieci web** i **wiązanie certyfikatu protokołu SSL** sekcje [powiązania istniejącego niestandardowego certyfikatu SSL w usłudze Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artykuły. Wybierz **oparte na SNI SSL** jako **typ SSL**.

3. Przekieruj cały ruch do portu HTTPS. Postępuj zgodnie z instrukcjami w **Wymuszanie protokołu HTTPS** części [powiązania istniejącego niestandardowego certyfikatu SSL w usłudze Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artykułu.

Aby dodać protokołu SSL do usługi Azure Stack:

- Powtórz kroki 1 – 3, używany dla platformy Azure.

## <a name="configure-and-deploy-the-web-application"></a>Konfigurowanie i wdrażanie aplikacji sieci web

Będzie Konfigurowanie kodu aplikacji do telemetrii raportu do wystąpienia usługi Application Insights jest poprawny, a następnie skonfigurować aplikacje sieci web przy użyciu parametrów połączenia na odpowiednie. Aby dowiedzieć się więcej na temat usługi Application Insights, zobacz [co to jest usługa Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Dodawanie usługi Application Insights

1. Otwórz aplikację sieci web w programie Microsoft Visual Studio.

2. [Dodaj usługę Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry) do projektu do przesyłania danych telemetrycznych, korzystającą z usługi Application Insights w celu tworzenia alertów, gdy ruch w sieci web zwiększa lub zmniejsza.

### <a name="configure-dynamic-connection-strings"></a>Konfigurowanie parametrów połączenia dynamicznego

Każde wystąpienie aplikacji sieci web będzie użyć innej metody, aby nawiązać połączenie z bazą danych SQL. Prywatny adres IP maszyny wirtualnej programu SQL Server (VM) korzysta z aplikacji na platformie Azure i aplikacji w usłudze Azure Stack używa publicznego adresu IP maszyny Wirtualnej programu SQL Server.

> [!Note]  
> W usłudze Azure Stack zintegrowany System publiczny adres IP nie należy routingu internetowego. W usłudze Azure Stack Development Kit (ASDK), routing poza ASDK jest publiczny adres IP.

Za pomocą usługi App Service zmienne środowiskowe do przekazania parametrów połączenia różnych do każdego wystąpienia aplikacji.

1. Otwórz aplikację w programie Visual Studio.

2. Otwórz plik Startup.cs i znajdź następujący blok kodu:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Zastąp następujący kod, który używa parametrów połączenia zdefiniowanego w pliku appsettings.json poprzedni blok kodu:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>Konfiguruj ustawienia aplikacji usługi App Service

1. Tworzenie parametrów połączenia dla platformy Azure i usługi Azure Stack. Ciągi powinny być takie same, z wyjątkiem adresów IP, które są używane.

2. Na platformie Azure i usługi Azure Stack, Dodaj parametry połączenia odpowiednie [jako ustawienie aplikacji](https://docs.microsoft.com/azure/app-service/web-sites-configure) w aplikacji sieci web przy użyciu `SQLCONNSTR\_` jako prefiksu nazwy.

3. **Zapisz** ustawień aplikacji sieci web i ponownie uruchom aplikację.

## <a name="enable-automatic-scaling-in-global-azure"></a>Włączanie automatycznego skalowania globalnej platformy Azure

Po utworzeniu aplikacji sieci web w środowisku usługi App Service uruchamia jedno wystąpienie. Można automatycznie skalować w poziomie można dodać wystąpień, aby zapewnić więcej zasobów obliczeniowych dla aplikacji. Można podobnie automatycznie skalowanie w pionie i zmniejszyć liczbę wystąpień wymagań aplikacji.

> [!Note]  
> Musisz mieć Plan usługi App Service do skonfigurowania skalowania w poziomie i skalowanie do wewnątrz. Jeśli nie masz planu, utworzyć przed rozpoczęciem następnych kroków.

### <a name="enable-automatic-scale-out"></a>Włącz automatyczne skalowanie w poziomie

1. Na platformie Azure, Znajdź plan usługi App Service dla witryn, aby skalować w poziomie, a następnie wybierz **skalowalnego w poziomie (plan usługi App Service)**.

    ![Skalowanie w poziomie](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Wybierz **włączyć Skalowanie automatyczne**.

    ![Włącz skalowanie automatyczne](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Wprowadź nazwę dla **Nazwa ustawienia skalowania automatycznego**. Aby uzyskać **domyślne** reguły skalowania automatycznego, wybierz opcję **skalowania na podstawie metryki**. Ustaw **limity wystąpień** do **Minimum: 1**, **maksymalna: 10**, i **domyślne: 1**.

    ![Konfigurowanie automatycznego skalowania](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Wybierz **+ Dodaj regułę**.

5. W **źródło metryki**, wybierz opcję **bieżącego zasobu**. Użyj następujące kryteria i akcje dla reguły.

**Kryteria**

1. W obszarze **Agregacja czasu** wybierz **średni**.

2. W obszarze **Nazwa metryki**, wybierz opcję **procent użycia procesora CPU**.

3. W obszarze **Operator**, wybierz opcję **większa**.

   - Ustaw **próg** do **50**.
   - Ustaw **czas trwania** do **10**.

**Akcja**

1. W obszarze **operacji**, wybierz opcję **Zwiększ liczbę o**.

2. Ustaw **liczba wystąpień** do **2**.

3. Ustaw **ochładzania** do **5**.

4. Wybierz pozycję **Dodaj**.

5. Wybierz **+ Dodaj regułę**.

6. W **źródło metryki**, wybierz opcję **bieżącego zasobu.**

   > [!Note]  
   > Bieżący zasób będzie zawierać nazwę planu usługi App Service/GUID i **typ zasobu** i **zasobów** list rozwijanych będą wyszarzone.

### <a name="enable-automatic-scale-in"></a>Włącz automatyczne skalowanie

Gdy zmniejsza ruch, aplikacji internetowej platformy Azure automatycznie zmniejszyć liczbę aktywnych wystąpień, aby ograniczyć koszty. Ta akcja jest łagodniej niż skalowanie w poziomie, aby zminimalizować wpływ na użytkowników aplikacji.

1. Przejdź do **domyślne** skalowania w poziomie warunku wybierz **+ Dodaj regułę**. Użyj następujące kryteria i akcje dla reguły.

**Kryteria**

1. W obszarze **Agregacja czasu** wybierz **średni**.

2. W obszarze **Nazwa metryki**, wybierz opcję **procent użycia procesora CPU**.

3. W obszarze **Operator**, wybierz opcję **mniej niż**.

   - Ustaw **próg** do **30**.
   - Ustaw **czas trwania** do **10**.

**Akcja**

1. W obszarze **operacji**, wybierz opcję **Zmniejsz liczbę o**.

   - Ustaw **liczba wystąpień** do **1**.
   - Ustaw **ochładzania** do **5**.

2. Wybierz pozycję **Dodaj**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Tworzenie profilu usługi Traffic Manager i konfigurowanie wielu chmur skalowania

Będzie Tworzenie profilu usługi Traffic Manager na platformie Azure, a następnie skonfiguruj punkty końcowe, aby umożliwić skalowanie wielu chmur.

### <a name="create-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

1. Wybierz **Utwórz zasób**
2. Wybierz **sieci**
3. Wybierz **profilu usługi Traffic Manager** i skonfiguruj następujące opcje:

   - W **nazwa**, wprowadź nazwę dla swojego profilu. Ta nazwa **musi** być unikatowy w strefy trafficmanager.net i służy do tworzenia nowej nazwy DNS (na przykład northwindstore.trafficmanager.net).
   - Aby uzyskać **Metoda routingu**, wybierz opcję **ważona**.
   - Aby uzyskać **subskrypcji**, wybierz subskrypcję, aby utworzyć profil w programie.
   - W **grupy zasobów**, Utwórz nową grupę zasobów dla tego profilu.
   - W obszarze **Lokalizacja grupy zasobów** wybierz lokalizację grupy zasobów. To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.

4. Wybierz pozycję **Utwórz**.

    ![Tworzenie profilu usługi Traffic Manager](media/azure-stack-solution-hybrid-cloud/image19.png)

 Po zakończeniu globalnego wdrażania profilu usługi Traffic Manager jest wyświetlana na listy zasobów dla grupy zasobów, w której został utworzony w obszarze.

### <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

1. Wyszukiwanie profilu usługi Traffic Manager, który został utworzony. (Jeśli przejście grupy zasobów dla tego profilu, wybierz profil).

2. W **profilu usługi Traffic Manager**w obszarze **ustawienia**, wybierz opcję **punktów końcowych**.

3. Wybierz pozycję **Dodaj**.

4. W **Dodaj punkt końcowy**, użyj następujących ustawień dla usługi Azure Stack:

   - Aby uzyskać **typu**, wybierz opcję **zewnętrzny punkt końcowy**.
   - Wprowadź **nazwa** dla tego punktu końcowego.
   - Aby uzyskać **w pełni kwalifikowana nazwa domeny (FQDN) lub adres IP** wprowadzić zewnętrzny adres URL aplikacji sieci web usługi Azure Stack.
   - Aby uzyskać **wagi**, Zachowaj wartość domyślną, **1**. Powoduje to wagi całego ruchu kierowanego do tego punktu końcowego, jeśli jest w dobrej kondycji.
   - Pozostaw **Dodaj jako wyłączone** niezaznaczone.

5. Wybierz **OK** można zapisać punktu końcowego usługi Azure Stack.

Następnie należy skonfigurować punkt końcowy platformy Azure.

1. Na **profilu usługi Traffic Manager**, wybierz opcję **punktów końcowych**.
2. Wybierz **+ Dodaj**.
3. Na **Dodaj punkt końcowy**, użyj następujących ustawień dla platformy Azure:

   - Aby uzyskać **typu**, wybierz opcję **punkt końcowy platformy Azure**.
   - Wprowadź **nazwa** dla tego punktu końcowego.
   - Aby uzyskać **typ zasobu docelowego**, wybierz opcję **usługi App Service**.
   - Aby uzyskać **zasób docelowy**, wybierz opcję **wybierz usługę aplikacji** umożliwia wyświetlenie listy aplikacji sieci Web w tej samej subskrypcji.
   - W obszarze **Zasoby** wybierz usługę aplikacji, którą chcesz dodać jako pierwszy punkt końcowy.
   - Aby uzyskać **wagi**, wybierz opcję **2**. Skutkuje to całego ruchu kierowanego do tego punktu końcowego, jeśli podstawowy punkt końcowy jest w złej kondycji lub reguły/alerty, które ponownie kieruje ruch po wyzwoleniu.
   - Pozostaw **Dodaj jako wyłączone** niezaznaczone.

4. Wybierz **OK** można zapisać punkt końcowy platformy Azure.

Po skonfigurowaniu obu punkty końcowe są wyświetlane w **profilu usługi Traffic Manager** po wybraniu **punktów końcowych**. W przykładzie na poniższym zrzucie ekranu przedstawiono dwa punkty końcowe z informacjami o stanie i konfiguracji dla każdej z nich.

![Punkty końcowe](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Konfigurowanie usługi Application Insights, monitorowania i alertów

Usługa Azure Application Insights umożliwia monitorowanie aplikacji i wysyłania alertów na podstawie warunków, które można skonfigurować. Oto kilka przykładów: aplikacja jest niedostępna, występują błędy lub są wyświetlane problemy z wydajnością.

Użyjesz metryk usługi Application Insights, aby utworzyć alerty. Podczas wyzwalania te alerty, aplikacji sieci Web wystąpienia przełącza z usługi Azure Stack na platformie Azure w celu skalowania w poziomie, a następnie z powrotem do usługi Azure stosu do skalowania do wewnątrz.

### <a name="create-an-alert-from-metrics"></a>Tworzenie alertu na podstawie metryki

Przejdź do grupy zasobów, w tym samouczku, a następnie wybierz wystąpienie usługi Application Insights, aby otworzyć **usługi Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Użyjesz tego widoku do utworzenia skalowania w poziomie alertu i skalowania w alercie.

### <a name="create-the-scale-out-alert"></a>Utwórz skalowania w poziomie alertu

1. W obszarze **Konfiguruj**, wybierz opcję **alerty (klasyczne)**.
2. Wybierz **Dodaj alert dotyczący metryki (wersja klasyczna)**.
3. W **Dodaj regułę**, skonfiguruj następujące opcje:

   - Dla **nazwa**, wprowadź **w chmurze na platformie Azure — seria**.
   - A **opis** jest opcjonalne.
   - W obszarze **źródła**, **Alert dotyczący**, wybierz opcję **metryki**.
   - W obszarze **kryteria**, wybierz swoją subskrypcję, grupy zasobów dla profilu usługi Traffic Manager, a nazwa profilu usługi Traffic Manager dla zasobu.

4. Aby uzyskać **metryki**, wybierz opcję **żądań**.
5. Aby uzyskać **warunek**, wybierz opcję **większa niż**.
6. Aby uzyskać **próg**, wprowadź **2**.
7. Aby uzyskać **okres**, wybierz opcję **w ciągu ostatnich 5 minut**.
8. W obszarze **powiadomienia**:
   - Zaznacz pole wyboru dla **właściciele, współautorzy i czytelnicy poczty E-mail**.
   - Wprowadź swój adres e-mail dla **email(s) dodatkowego administratora**.

9. Na pasku menu wybierz **Zapisz**.

### <a name="create-the-scale-in-alert"></a>Utwórz skali w alercie

1. W obszarze **Konfiguruj**, wybierz opcję **alerty (klasyczne)**.
2. Wybierz **Dodaj alert dotyczący metryki (wersja klasyczna)**.
3. W **Dodaj regułę**, skonfiguruj następujące opcje:

   - Aby uzyskać **nazwa**, wprowadź **skalowania do usługi Azure Stack**.
   - A **opis** jest opcjonalne.
   - W obszarze **źródła**, **Alert dotyczący**, wybierz opcję **metryki**.
   - W obszarze **kryteria**, wybierz swoją subskrypcję, grupy zasobów dla profilu usługi Traffic Manager, a nazwa profilu usługi Traffic Manager dla zasobu.

4. Aby uzyskać **metryki**, wybierz opcję **żądań**.
5. Aby uzyskać **warunek**, wybierz opcję **mniej niż**.
6. Aby uzyskać **próg**, wprowadź **2**.
7. Aby uzyskać **okres**, wybierz opcję **w ciągu ostatnich 5 minut**.
8. W obszarze **powiadomienia**:
   - Zaznacz pole wyboru dla **właściciele, współautorzy i czytelnicy poczty E-mail**.
   - Wprowadź swój adres e-mail dla **email(s) dodatkowego administratora**.

9. Na pasku menu wybierz **Zapisz**.

Poniższy zrzut ekranu przedstawia alerty dla skalowania w poziomie i skalowanie do wewnątrz.

   ![Alerty (klasyczne)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Przekierowywanie ruchu między platformą Azure i usługi Azure Stack

Można skonfigurować, ręczne lub automatyczne przełączanie zainteresowanie Twoją aplikacją sieci Web przełączanie między platformą Azure i usługi Azure Stack.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Konfigurowanie ręcznego przełączania między platformą Azure i usługi Azure Stack

Witryny sieci Web osiągnie progów, które można skonfigurować, otrzymasz alert. Wykonaj następujące kroki, aby ręcznie przekierować ruch na platformę Azure.

1. W witrynie Azure portal wybierz profilu usługi Traffic Manager.

    ![Punkty końcowe usługi Traffic Manager](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Wybierz **punktów końcowych**.
3. Wybierz **punkt końcowy platformy Azure**.
4. W obszarze **stan** , wybierz opcję **włączone**, a następnie wybierz pozycję **Zapisz**.

    ![Włącz punkt końcowy platformy Azure](media/azure-stack-solution-hybrid-cloud/image23.png)

5. Na **punktów końcowych** profilu usługi Traffic Manager można wybrać **zewnętrzny punkt końcowy**.
6. W obszarze **stan** , wybierz opcję **wyłączone**, a następnie wybierz pozycję **Zapisz**.

    ![Wyłączenie punktu końcowego usługi Azure Stack](media/azure-stack-solution-hybrid-cloud/image24.png)

Po skonfigurowaniu punktów końcowych aplikacji ruch jest przesyłany do aplikacji sieci web platformy Azure skalowalnego w poziomie zamiast aplikacji sieci web usługi Azure Stack.

 ![Punkty końcowe zmienione](media/azure-stack-solution-hybrid-cloud/image25.png)

Aby odwrócić przepływ ponownie w usłudze Azure Stack, należy użyć poprzednie kroki, aby:

- Włączanie punktu końcowego usługi Azure Stack
- Wyłącz punkt końcowy platformy Azure

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Konfigurowanie automatycznego przełączania między platformą Azure i usługi Azure Stack

Umożliwia również monitorowanie usługi Application Insights, jeśli aplikacja działa w [bezserwerowe](https://azure.microsoft.com/overview/serverless-computing/) środowiska udostępnianego przez usługę Azure Functions.

W tym scenariuszu można skonfigurować usługi Application Insights, aby użyć elementu webhook, który wywołuje aplikację funkcji. Ta aplikacja automatycznie włącza lub wyłącza punkt końcowy w odpowiedzi na alert.

Do konfigurowania przełączania automatyczne ruch, wykonaj następujące kroki jako wskazówki.

1. Utwórz aplikację funkcji platformy Azure.
2. Tworzenie funkcji wyzwalanej przez HTTP.
3. Importuj zestawy Azure SDK dla usługi Resource Manager, aplikacje sieci Web i usługi Traffic Manager.
4. Twórz kod, aby:

   - Uwierzytelnianie do swojej subskrypcji platformy Azure.
   - Użyj parametru, który włącza/wyłącza punkty końcowe usługi Traffic Manager do kierowania ruchu do platformy Azure lub usługi Azure Stack.

5. Zapisz swój kod i adres URL aplikacji funkcji, z odpowiednimi parametrami, aby dodać **elementu Webhook** sekcji Ustawienia reguły alertów usługi Application Insights.
6. Ruch jest przekierowywany automatycznie, gdy zostanie wyzwolony alert danych usługi Application Insights.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
