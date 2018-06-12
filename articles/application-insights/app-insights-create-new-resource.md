---
title: Utwórz nowy zasób usługi Application Insights dla platformy Azure | Dokumentacja firmy Microsoft
description: Ręcznie skonfiguruj monitorowanie usługi Application Insights dla nowej aplikacji na żywo.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 59bb8564613e9a0cebda00c2c847283ff218b882
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294721"
---
# <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights
Azure Application Insights przedstawia dane dotyczące Twojej aplikacji w systemie Microsoft Azure *zasobów*. Tworzenie nowego zasobu w związku z tym jest częścią [konfigurowaniu usługi Application Insights do monitorowania nowej aplikacji][start]. W wielu przypadkach tworzenie zasobu można automatycznie IDE. Ale w niektórych przypadkach należy Utwórz ręcznie zasób — na przykład mieć osobne zasobów dla rozwoju i produkcji kompilacje aplikacji.

Po utworzeniu zasobu uzyskać jego klucza Instrumentacji i używać, aby skonfigurować zestaw SDK w aplikacji. Klucz zasobu łączy dane telemetryczne do zasobu.

## <a name="sign-up-to-microsoft-azure"></a>Zaloguj do systemu Microsoft Azure
Jeśli nie mam [Microsoft konto, Uzyskaj je teraz](http://live.com). (Jeśli korzystasz z usług takich jak Outlook.com, OneDrive, Windows Phone lub XBox Live, masz już konto Microsoft.)

Należy również subskrypcji [Microsoft Azure](http://azure.com). Jeśli zespół lub organizacja ma subskrypcję platformy Azure, właściciel można do niej dodać, przy użyciu swojego identyfikatora Windows Live. Tylko są naliczane opłaty za można użyć. Podstawowy plan domyślny umożliwia pewnego wykorzystanie eksperymentalne bezpłatnie.

Jeśli masz dostęp do subskrypcji, zaloguj się do usługi Application Insights w [ http://portal.azure.com ](https://portal.azure.com)i używać swojego identyfikatora Live ID do logowania.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights
W [portal.azure.com](https://portal.azure.com), Dodaj zasób usługi Application Insights:

![Kliknij kolejno polecenia Nowy, Application Insights](./media/app-insights-create-new-resource/01-new.png)

* **Typ aplikacji** wpływa na informacje wyświetlane na bloku omówienie i właściwości, które są dostępne w [Eksploratora metryk][metrics]. Jeśli nie widzisz typ aplikacji, wybierz pozycję Ogólne.
* **Subskrypcja** Twojego konta płatności na platformie Azure.
* **Grupa zasobów** jest wygodne właściwości zarządzania, takich jak kontrola dostępu. Jeśli utworzono już innych zasobów platformy Azure, można umieścić tego nowego zasobu w tej samej grupie.
* **Lokalizacja** jest, gdzie możemy przechowywanie danych.
* **Przypnij do pulpitu nawigacyjnego** umieszcza kafelka szybkiego dostępu dla zasobu na stronie głównej Azure. Zalecane.

Po utworzeniu aplikacji, zostanie otwarty nowy blok. Ten blok jest, gdzie zobaczyć dane wydajności i użycia dotyczące Twojej aplikacji. 

Aby wrócić do niej następnym zalogowaniu się do platformy Azure, poszukaj aplikacji szybki start kafelka na tablicy start (ekranu). Lub kliknij przycisk Przeglądaj, aby go znaleźć.

## <a name="copy-the-instrumentation-key"></a>Skopiuj klucz Instrumentacji
Klucz Instrumentacji identyfikatorem zasobu, który został utworzony. Należy go ma zostać przypisany do zestawu SDK.

![Kliknij Essentials, kliknij przycisk klucza Instrumentacji klawisze CTRL + C](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Zainstaluj zestaw SDK w aplikacji
Zainstaluj zestaw SDK usługi Application Insights w aplikacji. Ten krok stopniu zależne od typu aplikacji. 

Umożliwia skonfigurowanie klucza Instrumentacji [instalowanej aplikacji zestawu SDK][start].

Zestaw SDK zawiera standardowe moduły, które wysłać dane telemetryczne bez konieczności pisania kodu. Śledzenie działań użytkownika lub diagnozowanie problemów bardziej szczegółowo [za pomocą interfejsu API] [ api] do wysyłania własnych danych telemetrycznych.

## <a name="monitor"></a>Zobacz dane telemetryczne
Zamknij bloku szybki start, aby powrócić do bloku Twojej aplikacji w portalu Azure.

Kliknij pole wyszukiwania, aby zobaczyć [diagnostycznych wyszukiwania][diagnostic], gdy pojawi się pierwsze zdarzenia. 

Jeśli spodziewasz większej ilości danych, kliknij przycisk **Odśwież** po kilku sekundach.

## <a name="creating-a-resource-automatically"></a>Automatyczne tworzenie zasobu
Można napisać [skrypt programu PowerShell](app-insights-powershell.md) automatyczne tworzenie zasobu.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie pulpitu nawigacyjnego](app-insights-dashboards.md)
* [Wyszukiwanie diagnostyczne](app-insights-diagnostic-search.md)
* [Eksplorowanie metryk](app-insights-metrics-explorer.md)
* [Pisanie zapytań analitycznych](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

