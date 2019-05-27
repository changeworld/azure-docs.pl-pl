---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/25/2018
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 894dd5ea7270390780813b647fe7a8b4c0f173bd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66139960"
---
1. Kliknij przycisk **App Services**, wybierz zaplecze funkcji Mobile Apps, wybierz pozycję **Szybki start**, a następnie wybierz platformę klienta (iOS, Android, Xamarin, Cordova).

    ![Witryna Azure Portal z wyróżnioną pozycją Mobile Apps — szybki start][quickstart]

1. Jeśli nie skonfigurowano połączenia z bazą danych, utwórz je w następujący sposób:

    ![Witryna Azure Portal z opcją połączenia funkcji Mobile Apps z bazą danych][connect]

    a. Utwórz nową bazę danych SQL i serwer. Może być konieczne pozostawienie wartości domyślnej MS_TableConnectionString w polu nazwy parametrów połączenia w celu wykonania kroku 3 poniżej.

    ![Witryna Azure Portal z opcją tworzenia nowej bazy danych i serwera funkcji Mobile Apps][server]

    b. Poczekaj na pomyślne utworzenie połączenia danych.

    ![Powiadomienie witryny Azure Portal o pomyślnym utworzeniu połączenia danych][notification]

    c. Połączenie danych musi się powieść.

    ![Powiadomienie witryny Azure Portal „Masz już połączenie danych”][already-connection]

1. W obszarze **2. Utwórz tabelę interfejsu API** wybierz dla języka Node.js opcję **Język zaplecza**.

1. Zaakceptuj potwierdzenie i wybierz pozycję **Utwórz tabelę TodoItem**.
    Ta akcja spowoduje utworzenie nowej tabeli elementów do wykonania w bazie danych.

    >[!IMPORTANT]
    > Przełączenie istniejącego zaplecza na język Node.js spowoduje zastąpienie całej zawartości. Aby w zamian utworzyć zaplecze .NET, zobacz [Work with the .NET back-end server SDK for Mobile Apps (Praca z zestawem SDK serwera zaplecza .NET SDK dla funkcji Mobile Apps)][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
