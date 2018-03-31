---
title: Mostek Android widoku sieci Web z natywnego Mobile Engagement zestawu SDK systemu Android
description: Opisuje sposób tworzenia mostka między systemem obsługi języka Javascript i natywny zestaw Mobile Engagement Android SDK widoku sieci Web
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 717c0360042b8f2afd9c41f1ee97e64e95a76a2b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Mostek Android widoku sieci Web z natywnego Mobile Engagement zestawu SDK systemu Android
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Mostek systemu android](mobile-engagement-bridge-webview-native-android.md)
> * [iOS Bridge](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Niektóre aplikacje mobilne są zaprojektowane jako aplikacji hybrydowych, gdy aplikacja jest utworzony przy użyciu natywnych systemu Android, ale niektóre lub nawet wszystkich ekranów są renderowane w widoku sieci Web dla systemu Android. Może nadal używać zestaw Mobile Engagement Android SDK w ramach tych aplikacji i w tym samouczku opisano sposób Przejdź o tej czynności. Poniższy przykładowy kod jest oparta na dokumentację dla systemu Android [tutaj](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Opisuje, jak takie podejście udokumentowane może posłużyć do wykonania takie same dla zestaw Mobile Engagement Android SDK dla często używanych metod tak, aby Webview z aplikacji hybrydowych można także zainicjować żądań śledzenia zdarzeń, zadań, błędów, informacje o aplikacji podczas ich przez przekazanie w potoku naszych Zestaw SDK systemu android. 

1. Po pierwsze, należy się upewnić, że przeszły naszych [Wprowadzenie — samouczek](mobile-engagement-android-get-started.md) zintegrować zestaw Mobile Engagement Android SDK w aplikacji hybrydowych. Gdy to zrobisz, Twoje `OnCreate` metoda będzie wyglądać następująco.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Teraz upewnij się, że aplikacji hybrydowych ma ekran z widoku sieci Web na nim. Kod dla niego będzie podobny do następującego gdzie czy ładowanie lokalnego pliku **Sample.html** w widoku sieci Web w `onCreate` metodę ekranu. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Teraz Utwórz plik Mostek o nazwie **WebAppInterface** który tworzy otokę w niektóre często używane przy użyciu metody zestaw Mobile Engagement Android SDK `@JavascriptInterface` podejścia opisanego w [Android dokumentacji](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Gdy utworzono plik Mostek powyżej, musimy upewnij się, że jest on skojarzony z naszych widoku sieci Web. W tym celu należy edytować Twojej `SetWebview` metody, dzięki czemu wygląda następująco:
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. We fragmencie powyżej dzwoniliśmy `addJavascriptInterface` do skojarzenia klasy Nasze mostek z naszych widoku sieci Web, a także utworzony uchwyt o nazwie **EngagementJs** do wywołania metody z pliku mostek. 
6. Teraz utworzyć następującego pliku o nazwie **Sample.html** w projekcie w folderze o nazwie **zasoby** który jest ładowany do widoku sieci Web i gdzie możemy wywoływać metody z pliku mostek.
   
        <!doctype html>
        <html>
            <head>
                <style type='text/css'>
                    html { font-family:Helvetica; color:#222; }
                    h1 { color:steelblue; font-size:22px; margin-top:16px; }
                    h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
                </style>
   
                <script type="text/javascript">
   
                    window.onerror = function(err)
                    {
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. Należy uwzględnić następujące kwestie dotyczące pliku w formacie HTML powyżej:
   
   * Zawiera zestaw wejściowy pól, w którym można podać dane mają być używane jako nazwy dla zdarzeń, zadania, błąd, AppInfo. Po kliknięciu przycisku Dalej, aby go połączenie jest nawiązywane w przypadku Javascript, który ostatecznie wywołuje metody z pliku mostek do przekazania to wywołanie zestaw Mobile Engagement Android SDK. 
   * Firma Microsoft są znakowanie na niektórych statycznych dodatkowe informacje do zdarzeń, zadania i nawet błędów, aby zademonstrować, jak można to zrobić. Te dodatkowe informacje są wysyłane jako JSON ciąg, który w `WebAppInterface` pliku, jest analizowana i umieść w systemie Android `Bundle` i jest przekazywany wraz ze zdarzeń, zadań, błędy wysyłania. 
   * Zadanie Mobile Engagement zostało rozpoczęte o nazwie określonej w polu wejściowym, uruchom na 10 sekund i zamknąć. 
   * Usługa Mobile Engagement appinfo lub znacznik jest przekazywany z "customer_name" jako statyczny klucz i wartość wprowadzona w danych wejściowych jako wartość tagu. 
8. Uruchom aplikację i pojawi się poniżej. Teraz niektóre nazwy zdarzenie testowe, podobnie jak poniżej i kliknij przycisk **wysyłania** poniżej. 
   
    ![][1]
9. Teraz, jeśli przejdziesz do **Monitor** kartę aplikacji i wyglądu w obszarze **zdarzenia -> Szczegóły**, zobaczysz tego zdarzenia wyświetlane wraz ze statycznego aplikacji — informacje, które firma Microsoft wysyłania. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
