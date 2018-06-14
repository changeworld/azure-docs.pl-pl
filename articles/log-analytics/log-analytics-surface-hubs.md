---
title: Monitorowanie urządzenia Surface Hub z Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie Surface Hub umożliwia śledzenie kondycji z urządzeń Surface Hub i zrozumieć, jak są one używane.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 525b3ced979834a956f91ef8c6f647b659ca21f1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2018
ms.locfileid: "29179229"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorowanie urządzeń Surface Hub z analizy dzienników śledzenia ich kondycję

![Surface Hub symbol](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

W tym artykule opisano, jak używasz rozwiązania Surface Hub w analizy dzienników do monitorowania urządzeń Microsoft Surface Hub. Analiza dzienników pomaga śledzić kondycji z urządzeń Surface Hub dobrze zrozumieć, jak są one używane.

Każdy Surface Hub został zainstalowany program Microsoft Monitoring Agent. Jego przez agenta, czy użytkownik może wysyłać dane z Twojego Surface Hub analizy dzienników. Pliki dziennika są odczytywane z urządzeń Surface Hub i są następnie są wysyłane do analizy dzienników. Zagadnienia, takie jak serwery w trybie offline, Kalendarz nie synchronizuje lub jeśli nie może zalogować się do konta urządzeń Skype są wyświetlane na pulpicie nawigacyjnym Surface Hub w analizy dzienników. Przy użyciu danych na pulpicie nawigacyjnym, można zidentyfikować urządzenia nie są uruchomione lub są inne problemy i potencjalnie zastosować poprawki dotyczące wykrytych problemów.

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązania. Aby zarządzać z urządzeń Surface Hub w analizy dzienników, będą potrzebne następujące czynności:

* A [subskrypcji analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/) poziomu, która będzie obsługiwać liczbę urządzeń, które chcesz monitorować. Cennik usługi Analiza dzienników zmienia się w zależności od liczby urządzeń zarejestrowanych i ilość danych go procesów. Należy to uwzględnić podczas planowania wdrożenia Surface Hub.

Następnie spowoduje dodanie istniejący obszar roboczy analizy dzienników lub Utwórz nową. Szczegółowe instrukcje do przy użyciu jednej z metod jest w [wprowadzenie do analizy dzienników](log-analytics-get-started.md). Po skonfigurowaniu obszaru roboczego analizy dzienników istnieją dwa sposoby zarejestrować swoje urządzenia Surface Hub:

* Automatycznie za pomocą usługi Intune
* Ręcznie za pomocą **ustawienia** urządzenia Surface Hub.

## <a name="set-up-monitoring"></a>Konfigurowanie monitorowania
Można monitorować kondycję i działanie Centrum powierzchni przy użyciu analizy dzienników. Surface Hub można zarejestrować za pomocą usługi Intune lub lokalnie przy użyciu **ustawienia** na Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Urządzenia Surface Hub nawiązania połączenia analizy dzienników za pośrednictwem usługi Intune
Wymagany identyfikator i klucz obszaru roboczego dla obszaru roboczego analizy dzienników, która ma zarządzać z urządzeń Surface Hub. W portalu Azure, można uzyskać z ustawień obszaru roboczego.

Usługa Intune jest produktem firmy Microsoft, która umożliwia centralne zarządzanie ustawieniami konfiguracji analizy dzienników, które są stosowane do jednej lub kilku urządzeń. Wykonaj następujące kroki, aby skonfigurować urządzenia za pomocą usługi Intune:

1. Zaloguj się do usługi Intune.
2. Przejdź do **ustawienia** > **połączone źródła**.
3. Utwórz lub Edytuj zasady na podstawie szablonu Surface Hub.
4. Przejdź do sekcji OMS (Azure Operational Insights), zasad, a następnie dodaj analizy dzienników *identyfikator obszaru roboczego* i *klucz obszaru roboczego* z zasadami.
5. Zapisać zasady.
6. Skojarzyć zasady z odpowiedniej grupy urządzeń.

   ![Zasady usługi Intune](./media/log-analytics-surface-hubs/intune.png)

Następnie usługa Intune przeprowadza synchronizację ustawień analizy dzienników z urządzeń w grupie docelowej zarejestrowanie ich w obszarze roboczym analizy dzienników.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Łączenie urządzeń Surface Hub do analizy dzienników przy użyciu ustawień aplikacji
Wymagany identyfikator i klucz obszaru roboczego dla obszaru roboczego analizy dzienników, która ma zarządzać z urządzeń Surface Hub. Możesz uzyskać te ustawienia dla obszaru roboczego analizy dzienników w portalu Azure.

Jeśli nie używasz usługi Intune do zarządzania używanym środowiskiem, można zarejestrować ręcznie za pomocą urządzenia **ustawienia** na każdym Surface Hub:

1. Z Centrum powierzchni, otwórz **ustawienia**.
2. Wprowadź poświadczenia administratora urządzenia, po wyświetleniu monitu.
3. Kliknij przycisk **to urządzenie**i w obszarze **monitorowanie**, kliknij przycisk **Konfigurowanie ustawień OMS**.
4. Wybierz **Włącz monitorowanie**.
5. W oknie dialogowym Ustawienia OMS wpisz analizy dzienników **identyfikator obszaru roboczego** i wpisz **klucz obszaru roboczego**.  
   ![Ustawienia](./media/log-analytics-surface-hubs/settings.png)
6. Kliknij przycisk **OK** w celu ukończenia konfiguracji.

Pojawia się potwierdzenie informujący o tym, czy konfiguracja została pomyślnie zastosowana do urządzenia. Jeśli tak jest, pojawi się komunikat z informacją, że agent połączył się z analizy dzienników. Urządzenie następnie rozpoczyna wysyłanie danych do analizy dzienników, w którym można wyświetlać i działa na nim.

## <a name="monitor-surface-hubs"></a>Monitor urządzenia Surface Hub.
Monitorowanie sieci urządzeń Surface Hub przy użyciu analizy dzienników jest podobne jak w przypadku monitorowania zarejestrowanych urządzeń.

1. Zaloguj się do Portalu Azure.
2. Przejdź do obszaru roboczego analizy dzienników i wybierz **omówienie**.
2. Kliknij Kafelek Surface Hub.
3. Kondycja urządzenia jest wyświetlana.

   ![Surface Hub pulpitu nawigacyjnego](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Można utworzyć [alerty](log-analytics-alerts.md) oparte na istniejących lub niestandardowych dziennik wyszukiwania. Przy użyciu danych, które zbiera Log Analytics z z urządzeń Surface Hub, możesz wyszukać problemy i alert na warunki, które definiują urządzeń.

## <a name="next-steps"></a>Kolejne kroki
* Użyj [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane Surface Hub.
* Utwórz [alerty](log-analytics-alerts.md) do powiadamiania, gdy występują problemy z Twojej urządzeń Surface Hub.
