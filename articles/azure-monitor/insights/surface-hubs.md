---
title: Monitoruj centra powierzchni za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: Użyj rozwiązania Surface Hub, aby śledzić kondycję centrów surface i zrozumieć, w jaki sposób są one używane.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7999735bf2d182b2811d01172adcfc89cba27dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662505"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitoruj usługi Surface Hubs za pomocą usługi Azure Monitor, aby śledzić ich kondycję

![Symbol piasty powierzchni](./media/surface-hubs/surface-hub-symbol.png)

W tym artykule opisano, jak można używać rozwiązania Surface Hub w usłudze Azure Monitor do monitorowania urządzeń Microsoft Surface Hub. Rozwiązanie pomaga śledzić kondycję centrów Surface Hubs, a także zrozumieć, w jaki sposób są one używane.

Każdy surface hub ma zainstalowany agent monitorowania firmy Microsoft. Jego za pośrednictwem agenta, który można wysyłać dane z urządzenia Surface Hub do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Pliki dziennika są odczytywane z usługi Surface Hubs, a następnie są wysyłane do usługi Azure Monitor. Problemy, takie jak serwery w trybie offline, kalendarz nie synchronizuje się lub jeśli konto urządzenia nie może zalogować się do skype są wyświetlane na pulpicie nawigacyjnym urządzenia Surface Hub w usłudze Azure Monitor. Korzystając z danych na pulpicie nawigacyjnym, można zidentyfikować urządzenia, które nie są uruchomione lub które mają inne problemy, i potencjalnie zastosować poprawki wykrytych problemów.

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie. Aby zarządzać urządzeniami Surface Hubs w usłudze Azure Monitor, potrzebne są następujące elementy:

* Poziom [subskrypcji usługi Log Analytics,](https://azure.microsoft.com/pricing/details/log-analytics/) który będzie obsługiwał liczbę urządzeń, które chcesz monitorować. Ceny usługi Log Analytics różnią się w zależności od liczby zarejestrowanych urządzeń i ilości przetwarzanych danych. Należy wziąć to pod uwagę podczas planowania wdrożenia urządzenia Surface Hub.

Następnie należy dodać istniejący obszar roboczy usługi Log Analytics lub utworzyć nowy. Szczegółowe instrukcje dotyczące korzystania z jednej z tych metod znajduje się w [obszarze roboczym Tworzenie usługi Log Analytics w witrynie Azure portal](../learn/quick-create-workspace.md). Po skonfigurowaniu obszaru roboczego usługi Log Analytics istnieją dwa sposoby rejestrowania urządzeń urządzenia Surface Hub:

* Automatycznie za pomocą usługi Intune
* Ręcznie za pomocą **ustawień** na urządzeniu Surface Hub.

## <a name="set-up-monitoring"></a>Konfigurowanie monitorowania
Kondycję i aktywność urządzenia Surface Hub można monitorować za pomocą usługi Azure Monitor. Urządzenie Surface Hub można zarejestrować za pomocą usługi Intune lub lokalnie przy użyciu **ustawień** w urządzeniu Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Łączenie centrów surface z monitorem platformy Azure za pośrednictwem usługi Intune
Do obszaru roboczego usługi Log Analytics, który będzie zarządzał urządzeniami Surface Hubs, potrzebny jest identyfikator obszaru roboczego i klucz obszaru roboczego. Można je uzyskać z ustawień obszaru roboczego w witrynie Azure portal.

Usługa Intune to produkt firmy Microsoft, który umożliwia centralne zarządzanie ustawieniami konfiguracji obszaru roboczego usługi Log Analytics, które są stosowane do jednego lub kilku urządzeń. Wykonaj następujące kroki, aby skonfigurować urządzenia za pomocą usługi Intune:

1. Zaloguj się do usługi Intune.
2. Przejdź do **ustawień** > **połączonych źródeł**.
3. Tworzenie lub edytowanie zasad na podstawie szablonu Urządzenia Surface Hub.
4. Przejdź do sekcji Usługi Azure Operational Insights zasad i dodaj *identyfikator obszaru roboczego* usługi Log Analytics i *klucz obszaru roboczego* do zasad.
5. Zapisz zasady.
6. Skojarz zasady z odpowiednią grupą urządzeń.

   ![Zasady usługi Intune](./media/surface-hubs/intune.png)

Usługa Intune synchronizuje następnie ustawienia usługi Log Analytics z urządzeniami w grupie docelowej, rejestrując je w obszarze roboczym usługi Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Łączenie centrów surface hubs z usługą Azure Monitor przy użyciu aplikacji Ustawienia
Do obszaru roboczego usługi Log Analytics, który będzie zarządzał urządzeniami Surface Hubs, potrzebny jest identyfikator obszaru roboczego i klucz obszaru roboczego. Można je uzyskać w ustawieniach obszaru roboczego usługi Log Analytics w witrynie Azure portal.

Jeśli nie używasz usługi Intune do zarządzania środowiskiem, możesz zarejestrować urządzenia ręcznie za pomocą **ustawień** na każdym urządzeniu Surface Hub:

1. W urządzeniu Surface Hub otwórz pozycję **Ustawienia**.
2. Po wyświetleniu monitu wprowadź poświadczenia administratora urządzenia.
3. Kliknij **pozycję To urządzenie**, a następnie w obszarze **Monitorowanie**kliknij pozycję **Konfiguruj ustawienia analizy dzienników**.
4. Wybierz **włącz monitorowanie**.
5. W oknie dialogowym Ustawienia usługi Log Analytics wpisz identyfikator **obszaru roboczego** usługi Log Analytics i wpisz **klawisz Workspace**.  
   ![Ustawienia](./media/surface-hubs/settings.png)
6. Kliknij **przycisk OK,** aby zakończyć konfigurację.

Pojawi się potwierdzenie informujące, czy konfiguracja została pomyślnie zastosowana do urządzenia. Jeśli tak było, zostanie wyświetlony komunikat informujący, że agent pomyślnie połączony z usługą Azure Monitor. Urządzenie następnie rozpoczyna wysyłanie danych do usługi Azure Monitor, gdzie można wyświetlać i działać na nim.

## <a name="monitor-surface-hubs"></a>Monitoruj koncentratory powierzchni
Monitorowanie urządzeń Surface Hubs przy użyciu usługi Azure Monitor przypomina monitorowanie innych zarejestrowanych urządzeń.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Po kliknięciu kafelka Surface Hub zostanie wyświetlony stan urządzenia.

   ![Pulpit nawigacyjny urządzenia Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Alerty [alerts](../platform/alerts-overview.md) można tworzyć na podstawie istniejących lub niestandardowych wyszukiwań w dzienniku. Korzystając z danych zbieranych przez usługę Azure Monitor z usługi Surface Hubs, można wyszukiwać problemy i ostrzegać o warunkach zdefiniowanych dla urządzeń.

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dziennika w usłudze Azure Monitor,](../log-query/log-query-overview.md) aby wyświetlić szczegółowe dane usługi Surface Hub.
* Tworzenie [alertów,](../platform/alerts-overview.md) aby powiadamiać o wystąpieniu problemów z urządzeniami Surface Hubs.
