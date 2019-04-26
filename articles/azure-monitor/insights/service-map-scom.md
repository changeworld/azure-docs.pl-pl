---
title: Mapa usługi integracji programu System Center Operations Manager | Dokumentacja firmy Microsoft
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. W tym artykule omówiono, przy użyciu mapy usługi, aby automatycznie utworzyć aplikację rozproszoną diagramy w programie Operations Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: magoedte
ms.openlocfilehash: 40e6d6ff6ea8748b525642e5507c80590b322b7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402624"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Mapa usługi integracji programu System Center Operations Manager

Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Mapa usługi umożliwia wyświetlenie Twoich serwerów w ten sposób możesz traktować je jako wzajemnie połączonych systemów dostarczających krytycznych usług. Usługa Service Map Pokazuje połączenia między serwerami, procesami i portami w dowolnej architekturze połączenia TCP, bez konieczności konfiguracji oprócz instalacji agenta. Aby uzyskać więcej informacji, zobacz [dokumentacji rozwiązania Service Map]( service-map.md).

Dzięki tej integracji między mapy usługi i System Center Operations Manager może automatycznie tworzyć diagramy aplikacji rozproszonych w programie Operations Manager, które są oparte na mapach dynamiczne zależności na mapie usługi.

## <a name="prerequisites"></a>Wymagania wstępne
* Grupy zarządzania programu Operations Manager (2012 R2 lub nowszym) zarządza zestawu serwerów.
* Obszar roboczy usługi Log Analytics za pomocą rozwiązania Service Map, włączone.
* Zestaw serwerów (co najmniej jeden), które są zarządzane przez menedżera operacji i wysyłania danych do rozwiązania Service Map. Serwery Windows i Linux są obsługiwane.
* Jednostki usługi z dostępem do subskrypcji platformy Azure, który jest skojarzony z obszarem roboczym usługi Log Analytics. Aby uzyskać więcej informacji, przejdź do [utworzyć nazwę główną usługi](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Zainstaluj pakiet administracyjny rozwiązania Service Map
Umożliwia integrację między programem Operations Manager i Service Map, importując Microsoft.SystemCenter.ServiceMap pakietu administracyjnego (Microsoft.SystemCenter.ServiceMap.mpb). Można pobrać pakietu administracyjnego z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Pakiet zawiera następujące pakiety administracyjne:
* Widoki aplikacji mapy usługi firmy Microsoft
* Microsoft System Center Service Map wewnętrznego
* Zastąpienia mapy usługi programu Microsoft System Center
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Konfigurowanie integracji rozwiązania Service Map
Po zainstalowaniu pakietu administracyjnego rozwiązania Service Map, nowy węzeł **rozwiązania Service Map**, jest wyświetlany w obszarze **pakietu Operations Management Suite** w **administracji** okienka.

>[!NOTE]
>[Pakiet Operations Management Suite została kolekcja usług](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) włączone, usługa Log Analytics, który jest obecnie częścią programu [usługi Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Aby skonfigurować integrację rozwiązania Service Map, wykonaj następujące czynności:

1. Aby otworzyć Kreatora konfiguracji w **omówienie mapy usługi** okienku kliknij **Dodaj obszar roboczy**.  

    ![Okienko omówienia mapy usługi](media/service-map-scom/scom-configuration.png)

2. W **Konfiguracja połączenia** okna, wprowadź nazwę dzierżawy lub identyfikator, identyfikator aplikacji (znany także jako nazwa użytkownika lub identyfikatora klienta) i hasło jednostki usługi, a następnie kliknij przycisk **dalej**. Aby uzyskać więcej informacji przejdź do utworzenia jednostki usługi.

    ![W oknie Konfiguracja połączenia](media/service-map-scom/scom-config-spn.png)

3. W **wybór subskrypcji** , wybierz subskrypcję platformy Azure, grupę zasobów platformy Azure (jedna, która zawiera obszar roboczy usługi Log Analytics) i obszar roboczy usługi Log Analytics, a następnie kliknij przycisk **dalej**.

    ![Obszar roboczy programu Operations Manager konfiguracji](media/service-map-scom/scom-config-workspace.png)

4. W **wybór grupy maszyny** okna, możesz wybrać grupy maszyny mapy usługi mają być synchronizowane z programem Operations Manager. Kliknij przycisk **grupy maszyn Dodaj/Usuń**, wybierz grupy z listy **dostępne grupy maszyn**i kliknij przycisk **Dodaj**.  Po wybraniu grup kliknij **Ok** na zakończenie.

    ![Grupy maszyn konfiguracji menedżera operacji](media/service-map-scom/scom-config-machine-groups.png)

5. W **wybór dotyczący serwera** okna, skonfigurowaniu grupy serwerów mapy usługi przy użyciu serwerów, które mają być synchronizowane między programem Operations Manager i Service Map. Kliknij przycisk **Dodaj/Usuń serwery**.   

    Integracji do tworzenia diagramu aplikacji rozproszonej dla serwera serwer musi mieć:

   * Zarządzane przez program Operations Manager
   * Zarządzane przez usługę mapy
   * Na liście grupy serwerów mapy usługi

     ![Grupa konfiguracji programu Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcjonalnie: Wybierz pulę zasobów serwera zarządzania do komunikowania się z usługą Log Analytics, a następnie kliknij przycisk **Dodaj obszar roboczy**.

    ![Pula zasobów konfiguracji menedżera operacji](media/service-map-scom/scom-config-pool.png)

    Może potrwać chwilę, aby skonfigurować i zarejestrować obszaru roboczego usługi Log Analytics. Po skonfigurowaniu programu Operations Manager inicjuje pierwsza synchronizacja mapy usługi.

    ![Pula zasobów konfiguracji menedżera operacji](media/service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>Mapa usługi monitora
Po połączeniu obszaru roboczego usługi Log Analytics nowego folderu, mapa usługi jest wyświetlana w **monitorowanie** okienku konsoli programu Operations Manager.

![W okienku Monitoring programu Operations Manager](media/service-map-scom/scom-monitoring.png)

Folder rozwiązania Service Map ma cztery węzły:
* **Aktywne alerty**: Wyświetla listę wszystkich aktywnych alertów dotyczących komunikacji między programem Operations Manager i Service Map.  Należy zauważyć, że te alerty nie są usługi Log Analytics alerty synchronizowany z usługą Operations Manager.

* **Serwery**: Wyświetla listę monitorowanych serwerów, które są skonfigurowane do synchronizacji z mapy usługi.

    ![W okienku monitorowanie serwerów programu Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Widoki zależności grupy na komputerze**: Wyświetla listę wszystkich grup maszyny, które są synchronizowane z mapy usługi. Możesz kliknąć dowolną grupę, aby wyświetlić jego diagramu aplikacji rozproszonej.

    ![Diagramu aplikacji rozproszonej programu Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Widoki zależności Server**: Wyświetla listę wszystkich serwerów, które są synchronizowane z mapy usługi. Kliknięcie dowolnego serwera, aby wyświetlić jego diagramu aplikacji rozproszonej.

    ![Diagramu aplikacji rozproszonej programu Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Edytowanie lub usuwanie obszaru roboczego
Można edytować lub usunąć skonfigurowany obszar roboczy za pomocą **omówienie mapy usługi** okienko (**administracji** okienko > **pakietu Operations Management Suite**  >  **Usługi mapy**).

>[!NOTE]
>[Pakiet Operations Management Suite została kolekcja usług](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) włączone, usługa Log Analytics, który jest obecnie częścią programu [usługi Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Teraz można skonfigurować tylko jeden obszar roboczy usługi Log Analytics.

![W okienku Menedżera operacji Edytuj obszar roboczy](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurowanie reguł i zastąpień
Reguła _Microsoft.SystemCenter.ServiceMapImport.Rule_, zostanie utworzona okresowo pobieranie informacji z mapy usługi. Aby zmienić czasów synchronizacji, można skonfigurować przesłonięcia reguły (**tworzenie** okienko > **reguły** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![W oknie właściwości zastępuje programu Operations Manager](media/service-map-scom/scom-overrides.png)

* **Włączone**: Włącz lub wyłącz automatyczne aktualizacje.
* **IntervalMinutes**: Resetowanie czasu między aktualizacjami. Domyślny interwał to jedna godzina. Jeśli chcesz zsynchronizować serwera mapy częściej, możesz zmienić wartość.
* **TimeoutSeconds**: Resetuj czas, zanim upłynie limit czasu żądania.
* **TimeWindowMinutes**: Resetuj przedział czasu na wykonywanie zapytań o dane. Domyślna to 60-minutowe okna. Maksymalna wartość dozwolona przez rozwiązania Service Map jest 60 minut.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Bieżący projekt przedstawia następujące problemy i ograniczenia:
* Tekst można połączyć tylko do jednego obszaru roboczego usługi Log Analytics.
* Chociaż możesz dodać serwery do ręcznie za pomocą grupy serwerów mapy usługi **tworzenie** okienku mapy dla tych serwerów nie są od razu synchronizowane.  Zostaną one zsynchronizowane z rozwiązania Service Map podczas następnego cyklu synchronizacji.
* Jeśli wprowadzisz zmiany do diagramów aplikacji rozproszonych, utworzony przez pakiet administracyjny, te zmiany prawdopodobnie zostaną zastąpione podczas następnej synchronizacji przy użyciu mapy usługi.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi
Aby uzyskać oficjalnej dokumentacji platformy Azure dotyczące tworzenia jednostki usługi zobacz:
* [Tworzenie jednostki usługi przy użyciu programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Tworzenie jednostki usługi przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Tworzenie jednostki usługi przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Opinia
Masz opinię dla nas o rozwiązania Service Map lub tej dokumentacji? Odwiedź nasze [stronę z opiniami użytkowników](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), gdzie możesz proponować funkcje lub głosować w sprawie propozycji istniejących.
