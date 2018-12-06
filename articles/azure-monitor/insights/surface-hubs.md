---
title: Monitoruj urządzenia Surface Hub przy użyciu usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie Surface Hub umożliwia śledzenie kondycji usługi urządzeń Surface Hub i zrozumieć, jak są one używane.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: a2cedb95469f97b564aaad2baa0ff7c784d67bf8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960995"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorowanie urządzeń Surface Hub z usługą Log Analytics, aby śledzić ich kondycję

![Urządzenia Surface Hub symboli](./media/surface-hubs/surface-hub-symbol.png)

W tym artykule opisano, jak używać rozwiązania Surface Hub w usłudze Log Analytics do monitorowania urządzeń Microsoft Surface Hub. Log Analytics ułatwia śledzenie kondycji usługi urządzeń Surface Hub dobrze zrozumieć, jak są one używane.

Każdego urządzenia Surface Hub ma zainstalowany program Microsoft Monitoring Agent. Jej za pomocą agenta, że w przypadku wysłania danych z Twojego urządzenia Surface Hub do usługi Log Analytics. Pliki dziennika są odczytywane z urządzeń Surface Hub i są następnie są wysyłane do usługi Log Analytics. Zagadnienia, takie jak serwery, jest w trybie offline, Kalendarz nie synchronizuje lub jeśli konta urządzeń nie może zalogować się do usługi Skype są wyświetlane na pulpicie nawigacyjnym urządzenia Surface Hub w usłudze Log Analytics. Przy użyciu danych na pulpicie nawigacyjnym, możesz zidentyfikować urządzenia nie są uruchomione lub są inne problemy oraz potencjalnie zastosować poprawki dotyczące wykrytych problemów.

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie. Aby można było zarządzać swoje urządzeń Surface Hub w usłudze Log Analytics, będą potrzebne następujące czynności:

* A [subskrypcja usługi Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) poziom, który będzie obsługiwać liczbę urządzeń, które chcesz monitorować. Cennik usługi log Analytics różni się w zależności od tego, ile urządzeń są rejestrowane, a także jak dużo danych go procesów. Należy wziąć pod uwagę podczas planowania wdrożenia urządzenia Surface Hub.

Następnie spowoduje dodanie do istniejącego obszaru roboczego usługi Log Analytics lub Utwórz nową. Szczegółowe instrukcje dotyczące przy użyciu jednej z metod wynosi [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../../azure-monitor/learn/quick-create-workspace.md). Po skonfigurowaniu obszaru roboczego usługi Log Analytics, istnieją dwa sposoby rejestrowania urządzeń Surface Hub:

* Automatycznie za pomocą usługi Intune
* Ręcznie za pomocą **ustawienia** na urządzeniu Surface Hub.

## <a name="set-up-monitoring"></a>Konfigurowanie monitorowania
Można monitorować kondycję i działanie usługi przy użyciu usługi Log Analytics na urządzeniu Surface Hub. Możesz zarejestrować urządzenia Surface Hub przy użyciu usługi Intune lub lokalnie, używając **ustawienia** na urządzeniu Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Łączenie urządzenia Surface Hub do usługi Log Analytics za pośrednictwem usługi Intune
Identyfikator obszaru roboczego i klucz obszaru roboczego należy dla obszaru roboczego usługi Log Analytics, która ma zarządzać usługi urządzeń Surface Hub. Możesz uzyskać te z ustawienia obszaru roboczego w witrynie Azure portal.

Usługa Intune jest produktem firmy Microsoft, która umożliwia centralne zarządzanie ustawienia konfiguracji usługi Log Analytics, które są stosowane do co najmniej jednej z Twoich urządzeń. Wykonaj następujące kroki, aby skonfigurować urządzenia za pomocą usługi Intune:

1. Zaloguj się do usługi Intune.
2. Przejdź do **ustawienia** > **połączonych źródeł**.
3. Utwórz lub Edytuj zasady na podstawie szablonu Surface Hub.
4. Przejdź do sekcji usługi Azure Operational Insights, zasad i Dodaj usługi Log Analytics *identyfikator obszaru roboczego* i *klucz obszaru roboczego* do zasad.
5. Zapisz zasady.
6. Skojarzyć zasady z odpowiedniej grupy urządzeń.

   ![Zasady usługi Intune](./media/surface-hubs/intune.png)

Usługa Intune następnie synchronizuje ustawienia usługi Log Analytics za pomocą urządzeń w grupie docelowej rejestrujesz je w obszarze roboczym usługi Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Łączenie urządzeń Surface Hub do usługi Log Analytics przy użyciu aplikacji ustawienia
Identyfikator obszaru roboczego i klucz obszaru roboczego należy dla obszaru roboczego usługi Log Analytics, która ma zarządzać usługi urządzeń Surface Hub. Możesz uzyskać te elementy w ustawieniach obszaru roboczego usługi Log Analytics w witrynie Azure portal.

Jeśli nie używasz usługi Intune do zarządzania środowiskiem, możesz zarejestrować urządzenia ręcznie za pomocą **ustawienia** na każdym urządzeniu Surface Hub:

1. Na urządzeniu Surface Hub Otwórz **ustawienia**.
2. Wprowadź poświadczenia administratora urządzenia, po wyświetleniu monitu.
3. Kliknij przycisk **to urządzenie**, a w obszarze **monitorowanie**, kliknij przycisk **skonfigurować ustawienia usługi Analiza dzienników**.
4. Wybierz **Włącz monitorowanie**.
5. W oknie dialogowym Ustawienia usługi Log Analytics, wpisz usługi Log Analytics **identyfikator obszaru roboczego** i wpisz **klucz obszaru roboczego**.  
   ![Ustawienia](./media/surface-hubs/settings.png)
6. Kliknij przycisk **OK** aby zakończyć konfigurację.

Zostanie wyświetlone potwierdzenie, informujące o tym, czy konfiguracja została pomyślnie zastosowana do urządzenia. Jeśli tak jest, pojawi się komunikat z informacją, że agent pomyślnie nawiązano połączenie z usługą Log Analytics. Urządzenie zostanie następnie uruchomione wysyłania danych do usługi Log Analytics, gdzie można wyświetlać i działa na nim.

## <a name="monitor-surface-hubs"></a>Monitor urządzenia Surface Hub
Monitorowanie usługi urządzeń Surface Hub przy użyciu usługi Log Analytics jest podobne do monitorowania innymi zarejestrowanymi urządzeniami.

1. Zaloguj się do Portalu Azure.
2. Przejdź do obszaru roboczego usługi Log Analytics i wybierz **Przegląd**.
2. Kliknij Kafelek urządzenia Surface Hub.
3. Kondycja urządzenia jest wyświetlana.

   ![Pulpicie nawigacyjnym urządzenia Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Możesz utworzyć [alerty](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) oparte na istniejące lub niestandardowych wyszukiwań w dziennikach. Korzystanie z danych, które usługa Log Analytics zbiera z usługi urządzeń Surface Hub, możesz wyszukać problemów i powiązane alerty warunki, które definiują dla urządzeń.

## <a name="next-steps"></a>Kolejne kroki
* Użyj [przeszukiwanie dzienników w usłudze Log Analytics](../../azure-monitor/log-query/log-query-overview.md) Aby przeglądać szczegółowe dane na urządzeniu Surface Hub.
* Tworzenie [alerty](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) być powiadamiana wystąpić problemy z usługi urządzeń Surface Hub.
