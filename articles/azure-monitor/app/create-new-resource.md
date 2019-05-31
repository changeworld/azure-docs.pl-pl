---
title: Utwórz nowy zasób usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Ręcznie skonfiguruj monitorowanie usługi Application Insights dla nowej aplikacji na żywo.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 712004a1ae8a2a72854b7b2332449a019c0820c3
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256255"
---
# <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights
Usługa Azure Application Insights zawiera dane dotyczące Twojej aplikacji w systemie Microsoft Azure *zasobów*. Tworzenie nowego zasobu znajduje się w związku z tym [konfiguracji usługi Application Insights na potrzeby monitorowania nowej aplikacji][start]. W wielu przypadkach tworzenia zasobu może odbywać się automatycznie IDE. Ale w niektórych przypadkach możesz Utwórz ręcznie zasób — na przykład mieć osobne zasoby do rozwoju i produkcji kompilacji aplikacji.

Po utworzeniu zasobu możesz pobrać jego klucza Instrumentacji i używać, aby skonfigurować zestaw SDK w aplikacji. Klucz zasobu łączy dane telemetryczne do zasobu.

## <a name="sign-up-to-microsoft-azure"></a>Zarejestruj się w systemie Microsoft Azure
Jeśli nie mam [Microsoft account, Uzyskaj je teraz](https://live.com). (Jeśli używasz usług, takich jak Outlook.com, OneDrive, Windows Phone i XBox Live, masz już konto Microsoft.)

Należy również subskrypcji [Microsoft Azure](https://azure.com). Jeśli Twój zespół lub organizacja mają subskrypcję platformy Azure, właściciel może Cię dodać do niego przy użyciu swojego identyfikatora Windows Live. Opłaty są naliczane tylko za rzeczywiste użycie. Domyślny plan basic umożliwia pewna ilość użycia eksperymentalnych bezpłatnie.

Jeśli masz dostęp do subskrypcji, zaloguj się do usługi Application Insights w [ https://portal.azure.com ](https://portal.azure.com)i użyj identyfikatora Live ID, aby zalogować się.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights
W [portal.azure.com](https://portal.azure.com), Dodaj zasób usługi Application Insights:

![Kliknij kolejno polecenia Nowy, Application Insights](./media/create-new-resource/01-new.png)

* **Typ aplikacji** dotyczy, zostanie wyświetlony w bloku przeglądu i właściwości, które są dostępne w [Eksplorator metryk][metrics]. Jeśli nie widać danego typu aplikacji, wybierz opcję ogólne.
* **Subskrypcja** jest Twoje konto płatności na platformie Azure.
* **Grupa zasobów** jest wygoda do zarządzania właściwościami, takich jak kontrola dostępu. Jeśli utworzono już innych zasobów platformy Azure, można umieścić tego nowego zasobu w tej samej grupie.
* **Lokalizacja** jest, w którym przechowujemy Twoje dane.
* **Przypnij do pulpitu nawigacyjnego** umieszcza kafelka szybkiego dostępu do zasobu na stronie głównej usługi Azure. Zalecane.

Po utworzeniu aplikacji, zostanie otwarty nowy blok. Ten blok jest tam, gdzie zobaczysz danych użycia i wydajności dotyczące Twojej aplikacji. 

Aby wrócić do niego następnym razem, gdy logujesz się do platformy Azure, poszukaj kafelka szybki start aplikacji na początku tablicy (główną). Lub kliknij przycisk Przeglądaj, aby ją znaleźć.

## <a name="copy-the-instrumentation-key"></a>Skopiuj klucz Instrumentacji
Klucz Instrumentacji identyfikuje zasób, który został utworzony. Możesz go potrzebować, aby zapewnić do zestawu SDK.

![Kliknij Essentials, kliknij przycisk klucz instrumentacji, CTRL + C](./media/create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Zainstaluj zestaw SDK w aplikacji
Zainstaluj zestaw SDK usługi Application Insights w swojej aplikacji. Ten krok zależy od intensywnie typu aplikacji. 

Użyj klucza Instrumentacji do skonfigurowania [zestawu SDK, który można zainstalować w aplikacji][start].

Zestaw SDK zawiera standardowe moduły, które wysyłają dane telemetryczne bez konieczności pisania kodu. Śledzenie działań użytkownika lub diagnozować problemy z bardziej szczegółowo [za pomocą interfejsu API] [ api] do wysyłania własnej telemetrii.

## <a name="monitor"></a>Zobacz dane telemetryczne
Zamknij blok szybkiego startu, aby wrócić do bloku aplikacji w witrynie Azure portal.

Kliknij pole wyszukiwania, aby zobaczyć [wyszukiwaniu diagnostycznym][diagnostic], gdzie pierwszy zdarzenia są wyświetlane. 

Jeśli oczekujesz większej ilości danych, kliknij przycisk **Odśwież** po kilku sekundach.

## <a name="creating-a-resource-automatically"></a>Automatyczne tworzenie zasobu
Można napisać [skrypt programu PowerShell](../../azure-monitor/app/powershell.md) automatycznego tworzenia zasobu.

## <a name="next-steps"></a>Kolejne kroki
* [Wyszukiwanie diagnostyczne](../../azure-monitor/app/diagnostic-search.md)
* [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md)
* [Pisanie zapytań analitycznych](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md

