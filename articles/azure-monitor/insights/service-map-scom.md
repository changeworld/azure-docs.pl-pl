---
title: Integracja mapy usługi Azure Monitor dla maszyn wirtualnych z programem Operations Manager | Dokumenty firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych automatycznie odnajduje składniki aplikacji w systemach Windows i Linux i mapuje komunikację między usługami. W tym artykule omówiono użycie funkcji Mapa do automatycznego tworzenia diagramów aplikacji rozproszonych w programie Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663457"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integracja programu System Center Operations Manager z funkcją mapy usługi Azure Monitor dla maszyn wirtualnych

W usłudze Azure Monitor dla maszyn wirtualnych można wyświetlać odnalezione składniki aplikacji na maszynach wirtualnych systemu Windows i Linux, które są uruchamiane na platformie Azure lub w Twoim środowisku. Dzięki tej integracji między funkcją Map i Programem Operacyjnym Centrum systemu można automatycznie tworzyć diagramy aplikacji rozproszonych w programie Operations Manager, które są oparte na mapach zależności dynamicznych w usłudze Azure Monitor dla maszyn wirtualnych. W tym artykule opisano sposób konfigurowania grupy zarządzania programu Operations Manager programu System Center do obsługi tej funkcji.

>[!NOTE]
>Jeśli mapa usługi została już wdrożona, możesz wyświetlić mapy w usłudze Azure Monitor dla maszyn wirtualnych, która zawiera dodatkowe funkcje monitorowania kondycji i wydajności maszyny wirtualnej. Funkcja Mapy usługi Azure Monitor dla maszyn wirtualnych ma zastąpić autonomiczne rozwiązanie mapy usług. Aby dowiedzieć się więcej, zobacz [Azure Monitor dla maszyn wirtualnych omówienie](vminsights-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Grupa zarządzania programem Operations Manager programu System Center (2012 R2 lub nowsza).
* Obszar roboczy usługi Log Analytics skonfigurowany do obsługi usługi Azure Monitor dla maszyn wirtualnych.
* Co najmniej jedna maszyna wirtualna systemu Windows i Linux lub komputery fizyczne monitorowane przez program Operations Manager i wysyłające dane do obszaru roboczego usługi Log Analytics. Serwery systemu Linux, które zgłaszają się do grupy zarządzania programu Operations Manager, muszą być skonfigurowane do bezpośredniego łączenia się z usługą Azure Monitor. Aby uzyskać więcej informacji, zapoznaj się z omówieniem w aplikacji [Collect log data with the Log Analytics agent](../platform/log-analytics-agent.md).
* Podmiot usługi z dostępem do subskrypcji platformy Azure, który jest skojarzony z obszarem roboczym usługi Log Analytics. Aby uzyskać więcej informacji, przejdź do [tematu Tworzenie jednostki usługi](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalowanie pakietu administracyjnego Mapy usług

Integrację między programem Operations Manager a funkcją Mapa można włączyć, importując pakiet pakietu administracyjnego Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Pakiet pakietu administracyjnego można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). Pakiet zawiera następujące pakiety administracyjne:

* Widoki aplikacji mapy usług firmy Microsoft
* Mapa usługi programu Microsoft System Center Wewnętrzna
* Zastąpienia map usługi programu Microsoft System Center
* Mapa usług programu Microsoft System Center

## <a name="configure-integration"></a>Konfigurowanie integracji

Po zainstalowaniu pakietu administracyjnego Mapy usług nowy węzeł, **Mapa usługi,** jest wyświetlany w obszarze **Pakiet zarządzania operacjami** w okienku **Administracja** konsoli Operations Manager.

>[!NOTE]
>[Pakiet Operations Management Suite był zbiorem usług,](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) które obejmowały usługę Log Analytics, która jest teraz częścią [usługi Azure Monitor.](../overview.md)

Aby skonfigurować platformę Azure Monitor dla integracji map maszyn wirtualnych, wykonaj następujące czynności:

1. Aby otworzyć kreatora konfiguracji, w okienku **Przegląd mapy usług** kliknij pozycję Dodaj obszar **roboczy**.  

    ![Okienko Przegląd mapy usług](media/service-map-scom/scom-configuration.png)

2. W oknie **Konfiguracja połączenia** wprowadź nazwę dzierżawy lub identyfikator, identyfikator aplikacji (znany również jako nazwa użytkownika lub identyfikator klienta) i hasło jednostki usługi, a następnie kliknij przycisk **Dalej**. Aby uzyskać więcej informacji, przejdź do tworzenie jednostki usługi.

    ![Okno Konfiguracja połączenia](media/service-map-scom/scom-config-spn.png)

3. W oknie **Wybór subskrypcji** wybierz subskrypcję platformy Azure, grupę zasobów platformy Azure (tę zawierającą obszar roboczy usługi Log Analytics) i obszar roboczy Usługi Log Analytics, a następnie kliknij przycisk **Dalej**.

    ![Obszar roboczy konfiguracji programu Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. W oknie **Wybór grupy maszyn** można wybrać grupy maszyn mapy usług, które mają zostać zsynchronizowane z programem Operations Manager. Kliknij **pozycję Dodaj/Usuń grupy maszyn,** wybierz grupy z listy **dostępnych grup maszyn**i kliknij przycisk **Dodaj**.  Po zakończeniu wybierania grup kliknij przycisk **Ok,** aby zakończyć.

    ![Grupy maszyn konfiguracyjnych programu Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. W oknie **Wybór serwera** należy skonfigurować grupę serwerów map usług z serwerami, które mają być synchronizowane między programem Operations Manager a funkcją Mapa. Kliknij **pozycję Dodaj/Usuń serwery**.

    Aby integracja tworzyła diagram aplikacji rozproszonej dla serwera, serwer musi być:

   * Monitorowane przez menedżera operacyjnego
   * Skonfigurowano do raportowania do obszaru roboczego usługi Log Analytics skonfigurowany za pomocą usługi Azure Monitor dla maszyn wirtualnych
   * Wymienione w grupie Serwery map usług

     ![Grupa konfiguracji programu Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcjonalnie: wybierz pulę zasobów wszystkich serwerów zarządzania, aby komunikować się z usługą Log Analytics, a następnie kliknij pozycję **Dodaj obszar roboczy**.

    ![Pula zasobów konfiguracji programu Operations Manager](media/service-map-scom/scom-config-pool.png)

    Może upłynąć minuta, aby skonfigurować i zarejestrować obszar roboczy usługi Log Analytics. Po skonfigurowaniu program Operations Manager inicjuje pierwszą synchronizację map.

    ![Pula zasobów konfiguracji programu Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorowanie integracji

Po podłączeniu obszaru roboczego usługi Log Analytics w okienku **Monitorowanie** konsoli Operacje programu Operations Manager wyświetlany jest nowy folder, Mapa usługi.

![Okienko Monitorowanie programu Operations Manager](media/service-map-scom/scom-monitoring.png)

Folder Mapy usług ma cztery węzły:

* **Aktywne alerty:** Wyświetla listę wszystkich aktywnych alertów dotyczących komunikacji między programem Operations Manager a usługą Azure Monitor.  

  >[!NOTE]
  >Te alerty nie są alertami usługi Log Analytics zsynchronizowanymi z programem Operations Manager, są generowane w grupie zarządzania na podstawie przepływów pracy zdefiniowanych w pakiecie administracyjnym mapy usług.

* **Serwery:** Wyświetla listę monitorowanych serwerów skonfigurowanych do synchronizacji z funkcji mapowania usługi Azure Monitor dla maszyn wirtualnych.

    ![Okienko Serwery monitorowania programu Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Widoki zależności grupy maszyn:** wyświetla listę wszystkich grup maszyn, które są synchronizowane z funkcji Mapa. Można kliknąć dowolną grupę, aby wyświetlić jej diagram aplikacji rozproszonej.

    ![Diagram aplikacji rozproszonych programu Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Widoki zależności serwera:** Wyświetla listę wszystkich serwerów, które są synchronizowane z funkcji Mapa. Można kliknąć dowolny serwer, aby wyświetlić jego diagram aplikacji rozproszonej.

    ![Diagram aplikacji rozproszonych programu Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Edytowanie lub usuwanie obszaru roboczego

Skonfigurowany obszar roboczy można edytować lub usuwać za pomocą okienka **Przegląd mapy usług** (okienko**administracyjne** >**mapa usługi**pakietu Operations Management **Suite** > ).

>[!NOTE]
>[Pakiet Operations Management Suite był zbiorem usług,](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) które obejmowały usługę Log Analytics, która jest teraz częścią [usługi Azure Monitor.](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md)

W bieżącej wersji można skonfigurować tylko jeden obszar roboczy usługi Log Analytics.

![Okienko Edytuj obszar roboczy programu Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurowanie reguł i zastępowania

Reguła *Microsoft.SystemCenter.ServiceMapImport.Rule*, okresowo pobiera informacje z usługi Azure Monitor dla funkcji mapy maszyn wirtualnych. Aby zmodyfikować interwał synchronizacji, można zastąpić regułę i zmodyfikować wartość parametru **IntervalMinutes**.

![Okno Właściwości programu Operations Manager zastępuje](media/service-map-scom/scom-overrides.png)

* **Włączone**: Włączanie lub wyłączanie aktualizacji automatycznych.
* **IntervalMinutes**: Określa czas między aktualizacjami. Domyślny interwał to jedna godzina. Jeśli chcesz częściej synchronizować mapy, możesz zmienić tę wartość.
* **Limit czasu:** Określa czas przed przesuniem czasu żądania.
* **TimeWindowMinutes**: Określa przedział czasu wykonywania zapytań o dane. Wartość domyślna to 60 minut, co jest maksymalnym dozwolonym interwałem.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Obecny projekt przedstawia następujące problemy i ograniczenia:

* Można połączyć się tylko z jednym obszarem roboczym usługi Log Analytics.
* Chociaż serwery można dodawać do grupy serwerów map usług ręcznie za pośrednictwem **okienka Tworzenie,** mapy tych serwerów nie są natychmiast synchronizowane. Zostaną one zsynchronizowane z usługi Azure Monitor dla maszyn wirtualnych map funkcji podczas następnego cyklu synchronizacji.
* Jeśli wniesiesz jakiekolwiek zmiany do diagramów aplikacji rozproszonych utworzonych przez pakiet administracyjny, te zmiany zostaną prawdopodobnie zastąpione przy następnej synchronizacji z usługą Azure Monitor dla maszyn wirtualnych.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby uzyskać oficjalną dokumentację platformy Azure dotyczącą tworzenia jednostki usługi, zobacz:

* [Tworzenie jednostki usługi przy użyciu programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Tworzenie jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Tworzenie jednostki usługi przy użyciu portalu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Opinia
Czy masz jakieś opinie na temat integracji z funkcją map usługi Azure Monitor dla maszyn wirtualnych lub tej dokumentacji? Odwiedź naszą [stronę Głos użytkownika,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)gdzie możesz zaproponować funkcje lub zagłosować na istniejące sugestie.
