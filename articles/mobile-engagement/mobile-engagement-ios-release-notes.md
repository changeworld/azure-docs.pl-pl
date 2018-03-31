---
title: Azure Mobile Engagement iOS SDK informacje o wersji | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dla systemu iOS SDK dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 0268d65d2c0ea3cefeb8f06793838bc263e443bd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Informacje o wersji usługi Azure Mobile Engagement iOS SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 


## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Identyfikatory stałych wyczyszczone w tle.
* Stałe ostrzeżenia na 9 XCode o interfejsach API nie jest wywoływana w kolejki głównej.
* Stała sond Reach przeciek pamięci.
* Obsługę systemu iOS usunąć 6.X. Począwszy od tej wersji cel wdrożenia aplikacji musi mieć co najmniej system iOS 7.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Ulepszona dostarczania dziennika w tle.

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* Stałe powiadomienie nie których wykonano akcje na urządzeniach z systemem iOS 10.
* Zastąpić XCode 7.

## <a name="324-06302016"></a>3.2.4 (06/30/2016)
* Stałe agregacji między dzienniki techniczne i inne dzienniki.

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* Stałe usterki, gdzie dostarczanie opinii nie został zgłoszony podczas aplikacji znajduje się w tle.
* Zoptymalizowanych pod kątem wysyłania dzienników technicznych.

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* Usunięte usterki w anulowania żądania HTTP, która czasami prowadzi do awarii.

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* Stałe opóźnienie wyzwolenia nowe wystąpienie aplikacji przez powiadomienie z linków bezpośrednich

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* Włączone kodu bitowego w zestawie SDK, aby pracować z **Xcode 7**.
* Usunięte usterki związane z powiadomienia w aplikacji.
* Wprowadzone bardziej niezawodna w przypadku niskim poziomie naładowania baterii i inne scenariusze, takie powiadomienia w aplikacji.
* Usunąć dodatkowe konsoli dzienniki generowane przez 3 biblioteki strony.

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* Napraw błąd zgodności z systemem iOS 9 z biblioteką innych firm. Została ona powoduje awarii (Crash) podczas wysyłania sonduje wyników, informacje o aplikacji lub dodatkowe dane.

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* Usługa Mobile Engagement używa cichych powiadomień wypychanych.
* Obsługę systemu iOS usunąć 4.X. Począwszy od tej wersji cel wdrożenia aplikacji musi mieć co najmniej iOS 6.

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* Identyfikator urządzenia usługi Mobile Engagement dla urządzeń < iOS 6 teraz opiera się na identyfikator GUID generowany podczas instalacji.

## <a name="210-04242015"></a>2.1.0 (04/24/2015)
* Dodano Swift zgodności.
* Po kliknięciu przycisku na powiadomienie, akcji, którą adres URL jest teraz wykonywane prawo po otwarciu aplikacji.
* Dodano brakujący plik nagłówka w pakiecie SDK.
* Rozwiązano problem, gdy osoby zgłaszającej awarii usługi Mobile Engagement został wyłączony.

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Początkowa wersja usługi Azure Mobile Engagement
* Konfiguracja appId/sdkKey zastępuje konfigurację ciągu połączenia.
* Usunięto interfejs API do wysyłania i odbierania wiadomości XMPP dowolnego z dowolnego XMPP jednostek.
* Usunięto interfejs API do wysyłania i odbierania wiadomości między urządzeniami.
* Ulepszenia zabezpieczeń.
* Śledzenie SmartAd usunięte.
