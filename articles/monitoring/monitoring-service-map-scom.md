---
title: Mapa usług integracji z programem System Center Operations Manager | Dokumentacja firmy Microsoft
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. W tym artykule omówiono przy użyciu mapy usługi do automatycznego tworzenia diagramów aplikacji rozproszonej w programie Operations Manager.
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: 6fbc49584b040f952fdff147207864d2d1f6377e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Mapa usług integracji z programem System Center Operations Manager
  > [!NOTE]
  > Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
  > 
  
Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Mapa usług umożliwia wyświetlenie serwerów sposób traktować ich jako połączonych systemy, które dostarczają usług krytycznych. Mapa usługi pokazuje połączeń między serwerami, procesów i portów w dowolnej architekturze połączenia TCP, bez konieczności wykonywania konfiguracyjnych wymaganych oprócz instalację agenta. Aby uzyskać więcej informacji, zobacz [mapy usługi dokumentacji]( monitoring-service-map.md).

Dzięki tej integracji między mapy usługi i System Center Operations Manager może automatycznie tworzyć diagramy aplikacji rozproszonej w programie Operations Manager, które są oparte na mapach dynamiczne zależności mapy usług.

## <a name="prerequisites"></a>Wymagania wstępne
* Grupy zarządzania programu Operations Manager (2012 R2 lub nowszy) zarządza z zestawu serwerów.
* Obszar roboczy analizy dzienników przy włączonej funkcji mapy usługi.
* Zestaw serwerów (co najmniej jeden), które są zarządzane przez programu Operations Manager oraz wysyłania danych do mapy usługi. Serwery z systemem Windows i Linux są obsługiwane.
* Nazwy głównej usługi z dostępem do subskrypcji Azure, która jest skojarzona z obszaru roboczego analizy dzienników. Aby uzyskać więcej informacji, przejdź do [Tworzenie nazwy głównej usługi](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Zainstaluj pakiet administracyjny mapy usług
Należy włączyć integrację programu Operations Manager a Service Map importując Microsoft.SystemCenter.ServiceMap pakietu administracyjnego (Microsoft.SystemCenter.ServiceMap.mpb). Można pobrać pakietu administracyjnego z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Pakiet zawiera następujące pakiety administracyjne:
* Widoki aplikacji mapy usługi firmy Microsoft
* Wewnętrzny mapy usługi programu Microsoft System Center
* Zastąpienia mapy usługi programu Microsoft System Center
* Mapa usług programu Microsoft System Center

## <a name="configure-the-service-map-integration"></a>Konfigurowanie integracji mapy usług
Po zainstalowaniu pakietu administracyjnego mapy usług, nowy węzeł **mapy usługi**, jest wyświetlana w obszarze **Operations Management Suite** w **administracji** okienka. 

Aby skonfigurować integrację mapy usług, wykonaj następujące czynności:

1. Aby otworzyć Kreatora konfiguracji w **omówienie mapy usługi** okienku, kliknij przycisk **Dodawanie obszaru roboczego**.  

    ![W okienku omówienie mapy usług](media/monitoring-service-map/scom-configuration.png)

2. W **konfiguracji połączenia** okna, wprowadź nazwę dzierżawcy lub identyfikator, Identyfikatora aplikacji (znanej także jako nazwa użytkownika lub clientID) i hasło nazwy głównej usługi, a następnie kliknij przycisk **dalej**. Aby uzyskać więcej informacji, przejdź do [Tworzenie nazwy głównej usługi](#creating-a-service-principal).

    ![W oknie Konfiguracja połączenia](media/monitoring-service-map/scom-config-spn.png)

3. W **wyboru subskrypcji** okna, wybierz subskrypcję platformy Azure, grupy zasobów platformy Azure (jedna zawiera obszar roboczy analizy dzienników) i obszaru roboczego analizy dzienników, a następnie kliknij przycisk **dalej**.

    ![Obszar roboczy konfiguracji menedżera operacji](media/monitoring-service-map/scom-config-workspace.png)

4. W **wybranej grupy maszyny** okna, możesz wybrać grupy maszyny mapy usługi mają być synchronizowane z programem Operations Manager. Kliknij przycisk **grupami maszyn Dodaj lub usuń**, wybierz z listy grup **dostępnych grup maszyny**i kliknij przycisk **Dodaj**.  Po wybraniu grup kliknij przycisk **Ok** aby zakończyć.
    
    ![Grupy na komputerze konfiguracji menedżera operacji](media/monitoring-service-map/scom-config-machine-groups.png)
    
5. W **wybór serwera** okna, konfigurowania grupy serwerów mapy usług z serwerów, które mają być synchronizowane między programem Operations Manager i mapy usługi. Kliknij przycisk **Dodaj lub Usuń serwery**.   
    
    Integracji Tworzenie diagramu aplikacji rozproszonej dla serwera serwer musi być:

    * Zarządzane przez program Operations Manager
    * Zarządzane przez usługę mapy
    * Na liście grupy serwerów mapy usług

    ![Grupa konfiguracji programu Operations Manager](media/monitoring-service-map/scom-config-group.png)

6. Opcjonalnie: Wybierz pulę zasobów serwera zarządzania do komunikacji z analizy dzienników, a następnie kliknij przycisk **Dodaj obszar roboczy**.

    ![Pula zasobów konfiguracji menedżera operacji](media/monitoring-service-map/scom-config-pool.png)

    Może upłynąć kilka minut, konfigurowanie i rejestrowanie obszaru roboczego analizy dzienników. Po skonfigurowaniu programu Operations Manager inicjuje pierwszej synchronizacji mapy usługi.

    ![Pula zasobów konfiguracji menedżera operacji](media/monitoring-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Monitor mapy usług
Po podłączeniu obszaru roboczego analizy dzienników nowego folderu, mapy usług jest wyświetlana w **monitorowanie** okienku konsoli programu Operations Manager.

![W okienku Monitoring programu Operations Manager](media/monitoring-service-map/scom-monitoring.png)

Folder mapy usługi ma cztery węzły:
* **Aktywne alerty**: Wyświetla wszystkie aktywne alerty dotyczące komunikacji między programem Operations Manager i mapy usługi.  Należy pamiętać, że te alerty są analizy dzienników alerty są zsynchronizowane z programem Operations Manager. 

* **Serwery**: Wyświetla listę monitorowanych serwerów, które są skonfigurowane do synchronizacji z mapy usługi.

    ![W okienku monitorowanie serwerów programu Operations Manager](media/monitoring-service-map/scom-monitoring-servers.png)

* **Maszyny widoków zależności grup**: Wyświetla listę wszystkich grup maszyny, które są synchronizowane z mapy usługi. Możesz kliknąć dowolną grupę, aby wyświetlić jego diagram aplikacji rozproszonej.

    ![Diagram aplikacji rozproszonej programu Operations Manager](media/monitoring-service-map/scom-group-dad.png)

* **Widoki zależności serwera**: Wyświetla listę wszystkich serwerów, które są synchronizowane z mapy usługi. Możesz kliknąć dowolny serwer, aby wyświetlić jego diagram aplikacji rozproszonej.

    ![Diagram aplikacji rozproszonej programu Operations Manager](media/monitoring-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Edytowanie lub usuwanie obszaru roboczego
Można edytować lub usunąć obszar roboczy skonfigurowany za pomocą **omówienie mapy usługi** okienka (**administracji** okienko > **Operations Management Suite** > **mapy usługi**). Teraz można skonfigurować tylko jeden obszar roboczy analizy dzienników.

![W okienku obszaru roboczego Edytuj menedżera operacji](media/monitoring-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurowanie reguł i zastąpień
Reguła, _Microsoft.SystemCenter.ServiceMapImport.Rule_, tworzona jest okresowo pobieranie informacji z mapy usługi. Aby zmienić chronometrażu synchronizacji, można skonfigurować zastąpienia reguły (**tworzenie** okienko > **reguły** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Okno właściwości zastępuje programu Operations Manager](media/monitoring-service-map/scom-overrides.png)

* **Włączone**: Włącz lub Wyłącz Aktualizacje automatyczne. 
* **IntervalMinutes**: zresetować czas między aktualizacjami. Domyślny interwał to jedna godzina. Jeśli chcesz synchronizować częściej mapy serwera, można zmienić wartości.
* **TimeoutSeconds**: zresetować czas, zanim upłynie limit czasu żądania. 
* **TimeWindowMinutes**: resetowanie przedział czasu dla zapytań danych. Domyślna to 60-minutowe okna. Maksymalna wartość dozwolona przez usługę mapy wynosi 60 min.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Bieżący projekt przedstawia następujące problemy i ograniczenia:
* Będzie można połączyć wyłącznie do jednego obszaru roboczego analizy dzienników.
* Chociaż możesz dodać serwery do ręcznie za pomocą grupy serwerów mapy usług **tworzenie** okienku mapy dla tych serwerów nie zostały zsynchronizowane natychmiast.  Zostaną one zsynchronizowane z mapy usługi podczas następnego cyklu synchronizacji.
* Jeśli wprowadzisz zmiany do diagramów aplikacji rozproszonych utworzonych przez pakiet administracyjny, te zmiany zostaną zastąpione prawdopodobnie w następnej synchronizacji z mapy usługi.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi
Oficjalnej dokumentacji platformy Azure o tworzeniu usługę podmiotu zabezpieczeń zobacz:
* [Tworzenie nazwy głównej usługi za pomocą programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Tworzenie nazwy głównej usługi przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Tworzenie nazwy głównej usługi przy użyciu portalu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Opinia
Masz opinię, abyśmy o mapy usługi lub tej dokumentacji? Można znaleźć w naszych [strony User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), gdzie można sugeruje funkcje lub oddawać głosy na istniejące sugestie.
