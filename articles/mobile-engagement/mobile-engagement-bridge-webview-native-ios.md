---
title: Mostek iOS widoku sieci Web z natywnego Mobile Engagement iOS SDK
description: Opisuje sposób tworzenia mostka między systemem obsługi języka Javascript i natywnego Mobile Engagement iOS SDK widoku sieci Web
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 07b2b8be80c090ce533f31cc28910f3ce7b91f73
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Mostek iOS widoku sieci Web z natywnego Mobile Engagement iOS SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Mostek systemu android](mobile-engagement-bridge-webview-native-android.md)
> * [iOS Bridge](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Niektóre aplikacje mobilne są zaprojektowane jako aplikacji hybrydowych, gdy aplikacja jest utworzony przy użyciu opracowywania aplikacji systemu iOS natywnego języka Objective-C, ale niektóre lub nawet wszystkich ekranów są renderowane w widoku sieci Web z systemem iOS. Możesz nadal korzystać z usługi Mobile Engagement iOS SDK w ramach tych aplikacji i w tym samouczku opisano sposób Przejdź o tej czynności. 

Istnieją dwie metody, można to osiągnąć, chociaż są nieudokumentowanej:

* Najpierw jeden opisane w tym [łącze](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) który obejmuje rejestrowanie `UIWebViewDelegate` w widoku sieci web i catch i natychmiast Anuluj zmiany lokalizacji wykonywane w języku Javascript. 
* Drugi jedną opiera się na tym [sesji WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), podejście, które jest bardziej czytelny niż pierwszy i firma Microsoft przestrzega tego przewodnika. Należy pamiętać, że ta metoda działa tylko w systemie iOS7 i nowszych. 

Dla systemu iOS zestawiania próbki, wykonaj następujące czynności:

1. Po pierwsze, należy się upewnić, że przeszły naszych [Wprowadzenie — samouczek](mobile-engagement-ios-get-started.md) integracja Mobile Engagement iOS SDK w aplikacji hybrydowych. Opcjonalnie można również włączyć, testu w następujący sposób rejestrowania, dzięki czemu można wyświetlić metody zestawu SDK, jak wyzwalana je z widoku sieci Web. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Teraz upewnij się, że aplikacji hybrydowych ma ekran z widoku sieci Web na nim. Można dodać go do `Main.storyboard` aplikacji. 
3. Skojarz ten widok sieci Web z Twojej **ViewController** klikając i przeciągając widoku sieci Web z scenę kontrolera widoku w celu `ViewController.h` ekranu umieszczenia go edytować tylko poniżej `@interface` wiersza. 
4. Po wykonaniu tej czynności okno dialogowe będzie wyskakujące monitowania o nazwę. Podaj nazwę jako **webView**. Twoje `ViewController.h` pliku powinna wyglądać następująco:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Firma Microsoft będzie aktualizować `ViewController.m` pliku później, ale najpierw utworzymy pliku mostek, który tworzy otokę przez niektóre typowe Mobile Engagement iOS SDK metody. Utwórz nowy plik nagłówka o nazwie **EngagementJsExports.h** używający `JSExport` mechanizm opisane w powyższym formularzu [sesji](https://developer.apple.com/videos/play/wwdc2013/615) do udostępnienia metody natywnej dla systemu iOS. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Następnie utworzymy drugiej części pliku mostek. Utwórz plik o nazwie **EngagementJsExports.m** zawierający implementację tworzenia otoki rzeczywiste przez wywołanie usługi Mobile Engagement iOS SDK metody. Należy także zauważyć, że firma Microsoft podczas analizowania `extras` przekazywany z poziomu języka javascript widoku sieci Web i który do umieszczania `NSMutableDictionary` obiekt przekazywany z wywołań metody Engagement SDK.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Teraz możemy wróć do **ViewController.m** i zaktualizować go następującym kodem: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Należy zwrócić uwagę następujące kwestie dotyczące **ViewController.m** pliku:
   
   * W `loadWebView` metody, możemy ładowania lokalnego pliku o nazwie **LocalPage.html** którego kod Zapoznamy się obok. 
   * W `webViewDidFinishLoad` metody, możemy są dane `JsContext` i kojarzenie z nim naszej klasy otoki. Umożliwi to wywołanie naszych otoki metod SDK przy użyciu dojścia **EngagementJs** z widoku sieci Web. 
9. Utwórz plik o nazwie **LocalPage.html** następującym kodem:
   
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
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Należy uwzględnić następujące kwestie dotyczące pliku w formacie HTML powyżej:
    
    * Zawiera zestaw wejściowy pól, w którym można podać dane mają być używane jako nazwy dla zdarzeń, zadania, błąd, AppInfo. Po kliknięciu przycisku Dalej, aby go połączenie jest nawiązywane w przypadku Javascript, który ostatecznie wywołuje metody z pliku mostek do przekazania to wywołanie usługi Mobile Engagement iOS SDK. 
    * Firma Microsoft są znakowanie na niektórych statycznych dodatkowe informacje do zdarzeń, zadania i nawet błędów, aby zademonstrować, jak można to zrobić. Te dodatkowe informacje są wysyłane jako JSON ciąg, który w `EngagementJsExports.m` pliku, jest analizowana i przekazywane wraz ze zdarzeń, zadań, błędy wysyłania. 
    * Zadanie Mobile Engagement zostało rozpoczęte o nazwie określonej w polu wejściowym, uruchom na 10 sekund i zamknąć. 
    * Usługa Mobile Engagement appinfo lub znacznik jest przekazywany z "customer_name" jako statyczny klucz i wartość wprowadzona w danych wejściowych jako wartość tagu. 
11. Uruchom aplikację i pojawi się poniżej. Teraz niektóre nazwy zdarzenie testowe, podobnie jak poniżej i kliknij przycisk **wysyłania** obok niej. 
    
     ![][1]
12. Teraz, jeśli przejdziesz do **Monitor** kartę aplikacji i wyglądu w obszarze **zdarzenia -> Szczegóły**, zobaczysz tego zdarzenia wyświetlane wraz ze statycznego aplikacji — informacje, które firma Microsoft wysyłania. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
