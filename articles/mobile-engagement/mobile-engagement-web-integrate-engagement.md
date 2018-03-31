---
title: Integracja z usługą Azure Mobile Engagement Web SDK | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK usługi Azure Mobile Engagement sieci Web
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: bccfbdfe9d99900f58d5dbfa44183146c79b9c88
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrowanie usługi Azure Mobile Engagement w aplikacji sieci web
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Procedury przedstawione w tym artykule opisano Najprostszym sposobem, aby aktywować analizy i funkcji monitorowania w usłudze Azure Mobile Engagement w aplikacji sieci web.

Wykonaj kroki, aby aktywować raporty dziennika, które są wymagane do obliczenia wszystkie statystyki dotyczące użytkowników, sesji, działania, awarii (Crash) i technicals. Statystyki zależne od aplikacji, takich jak zdarzenia, błędy i zadań należy ręcznie uaktywnić raporty dziennika przy użyciu interfejsu API Azure Mobile Engagement. Aby uzyskać więcej informacji, Dowiedz się [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji sieci web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Wprowadzenie
[Pobierz sieci Web usługi Azure Mobile Engagement SDK](http://aka.ms/P7b453).
Zestaw SDK usługi Mobile Engagement w sieci Web jest dostarczana jako pojedynczy plik JavaScript azure-engagement.js, które mają zostać uwzględnione w każdej strony z witryny lub aplikacji sieci web.

> [!IMPORTANT]
> Przed uruchomieniem tego skryptu, należy uruchomić skrypt lub fragment kodu napisanego można skonfigurować usługi Mobile Engagement dla aplikacji.
> 
> 

## <a name="browser-compatibility"></a>Zgodność przeglądarek
Zestaw SDK usługi Mobile Engagement w sieci Web używa natywnego JSON, kodowania i dekodowania oprócz żądania AJAX między domenami (oparte na specyfikacji W3C CORS). Jest on zgodny z poniższych przeglądarek:

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Konfigurowanie usługi Mobile Engagement
Napisać skrypt, który tworzy globalnym `azureEngagement` obiektu JavaScript, jak w poniższym przykładzie. Ponieważ witryna może być wielokrotność stron, w tym przykładzie założono, że ten skrypt znajduje się na każdej stronie. W tym przykładzie nosi nazwę obiektu JavaScript `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

`connectionString` Wartość dla aplikacji jest wyświetlana w portalu Azure.

> [!NOTE]
> `appVersionName` i `appVersionCode` są opcjonalne. Jednak zaleca się je skonfigurować tak, aby analiza może przetworzyć informacji o wersji.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Uwzględnić skrypty Mobile Engagement na swoich stronach
Dodaj usługi Mobile Engagement skryptów do stron w jednym z następujących sposobów:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Lub to:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Po załadowaniu skryptu zestaw SDK usługi Mobile Engagement w sieci Web tworzy **engagement** alias dostępu do interfejsów API zestawu SDK. Nie można użyć tego aliasu, aby zdefiniować konfigurację zestawu SDK. Ten alias jest używany jako odwołanie w niniejszej dokumentacji.

Należy pamiętać, że jeśli domyślny alias powoduje konflikt z innym zmiennej globalnej ze strony użytkownika, można zmienić go w konfiguracji w następujący sposób przed załadowaniem zestaw SDK usługi Mobile Engagement w sieci Web:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Podstawowe raportowanie
Podstawowym raportowaniem w usłudze Mobile Engagement obejmuje statystyki poziomu sesji, takie jak statystyki dotyczące użytkowników, sesji, działaniami i awariami.

### <a name="session-tracking"></a>Śledzenie sesji
Sesja usługi Mobile Engagement jest podzielony na sekwencję działań, każdy identyfikowane przez nazwę.

W klasycznej witryny zaleca się zadeklarować różnych działań na każdej stronie witryny. Witryny sieci Web lub aplikacji sieci web w którym nie ulega zmianie bieżącej strony można śledzić aktywność na mniejszą skalę, takich jak wewnątrz strony.

W obu przypadkach, aby uruchomić lub zmienić bieżącego użytkownika, wywołaj `engagement.agent.startActivity` funkcji. Na przykład:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Serwer usługi Mobile Engagement kończy się automatycznie otwartych sesji w ciągu trzech minut po zamknięciu strony aplikacji.

Alternatywnie można zakończyć sesję ręcznie przez wywołanie metody `engagement.agent.endActivity`. To ustawienie bieżącego użytkownika do "Bezczynny."  Sesja zostanie zakończona później 10 sekund, chyba że nowe wywołanie w celu `engagement.agent.startActivity` wznawia sesję.

10-sekundowe opóźnienie można skonfigurować w obiekcie globalnej w następujący sposób:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> Nie można użyć `engagement.agent.endActivity` w `onunload` wywołania zwrotnego, ponieważ na tym etapie nie można wprowadzić wywołania AJAX.
> 
> 

## <a name="advanced-reporting"></a>Zaawansowane raportowanie
Opcjonalnie Jeśli chcesz zgłosić zadań, błędy i zdarzenia specyficzne dla aplikacji, należy użyć interfejsu API Mobile Engagement. Możesz uzyskać dostępu do interfejsu API programu Mobile Engagement za pomocą `engagement.agent` obiektu.

Są dostępne wszystkie funkcje zaawansowane w usłudze Mobile Engagement w interfejsie API Mobile Engagement. Interfejs API została szczegółowo opisana w artykule [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji sieci web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Dostosuj adresy URL używany dla wywołań AJAX
Można dostosować adresów URL używanych przez zestaw SDK usługi Mobile Engagement w sieci Web. Na przykład do ponownego zdefiniowania URL dziennika (zestaw SDK punktu końcowego rejestrowania), mogą zastąpić konfigurację następująco:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Jeśli adres URL funkcji zwraca ciąg, który rozpoczyna się od `/`, `//`, `http://`, lub `https://`, nie jest używany domyślny schemat. Domyślnie `https://` schemat jest używany dla tych adresów URL. Jeśli chcesz dostosować domyślny schemat, należy zastąpić konfigurację, następująco:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
