---
title: Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager z programem Visual Studio Mobile Center | Dokumentacja firmy Microsoft
description: Skonfiguruj aplikację mobilną utworzony przy użyciu Centrum Mobile programu Visual Studio do wysłania do usługi Traffic Manager rzeczywiste pomiary użytkownika
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 893e84b07b365fb0b534e0ddc021b2249c4174cf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181014"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Jak należy wysyłać do Menedżera ruchu z Centrum programu Visual Studio Mobile rzeczywiste pomiary użytkownika

Można skonfigurować aplikację mobilną utworzony przy użyciu Centrum Mobile programu Visual Studio do wysłania do usługi Traffic Manager rzeczywiste pomiary użytkownika przez wykonanie kroków:

>[!NOTE]
> Obecnie wysyłanie rzeczywiste pomiary użytkownika do Menedżera ruchu jest obsługiwana tylko dla systemu Android.

Aby skonfigurować rzeczywiste pomiary użytkownika, należy uzyskać klucz i Instrumentacja aplikacji za pomocą rumu pakietu.

## <a name="step-1-obtain-a-key"></a>Krok 1: Uzyskanie klucza
    
Pomiary zająć i wysyłane do Menedżera ruchu z aplikacji klienta są identyfikowane przez usługę przy użyciu unikatowych parametrów, nazywany kluczem rzeczywiste pomiary użytkownika (RUM). Możesz otrzymać klucz rumu przy użyciu portalu Azure, interfejsu API REST lub przy użyciu programu PowerShell / interfejsy interfejsu wiersza polecenia.

Aby uzyskać klucz RUM przy użyciu portalu Azure, korzystając z następującej procedury:
   1. W przeglądarce Zaloguj się do portalu Azure. Jeśli nie masz już konto, możesz zarejestrować się bezpłatnej wersji próbnej jeden miesiąc.
   2. Na pasku wyszukiwania portalu, wyszukaj nazwę profilu Menedżera ruchu, który chcesz zmodyfikować, a następnie kliknij profil Menedżera ruchu w wynikach który wyświetlone.
   3. Na stronie profilu usługi Traffic Manager, kliknij przycisk **rzeczywiste pomiary użytkownika** w obszarze **ustawienia**.
   4. Kliknij przycisk **Wygeneruj klucz** można utworzyć nowego klucza RUM.
        
   ![Generowanie klucza rzeczywiste pomiary użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: Generowanie klucza rzeczywiste pomiary użytkownika**

   5.   Strona wyświetla klucz RUM wygenerowany i fragment kodu JavaScript, który ma być osadzone w kodzie strony HTML.
 
   ![Kod JavaScript dla klucza rzeczywiste pomiary użytkownika](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Rysunek 2: Klucz pomiarów rzeczywistego użytkownika i pomiar JavaScript**
 
   6. Kliknij przycisk **kopiowania** przycisk, aby skopiować klucz RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: Instrumentacja aplikacji przy użyciu rumu pakietu Mobile Center SDK

Jeśli jesteś nowym użytkownikiem programu Visual Studio Mobile Center, odwiedź witrynę jego [witryny sieci Web](https://mobile.azure.com). Aby uzyskać szczegółowe informacje dotyczące integracji zestawu SDK, zobacz [wprowadzenie do zestawu SDK systemu Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Aby użyć rzeczywiste pomiary użytkownika, wykonaj następującą procedurę:

1.  Dodaj zestaw SDK do projektu

    W wersji zapoznawczej ATM RUM SDK Musisz jawnie odwoływać repozytorium pakietów.

    W Twojej **app/build.gradle** pliku Dodaj następujące wiersze:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    W Twojej **app/build.gradle** pliku Dodaj następujące wiersze:

    ```groovy
    dependencies {   
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Uruchom zestaw SDK

    Otwórz klasą działania głównego aplikacji i dodaj następujące instrukcje importu:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Wyszukaj `onCreate` wywołania zwrotnego, w tym samym pliku i Dodaj następujący kod:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [rzeczywiste pomiary użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się [działania Menedżera ruchu](traffic-manager-overview.md)
- Dowiedz się więcej o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Zarejestruj się](https://mobile.azure.com) dla Centrum Mobile
- Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez Menedżera ruchu
- Dowiedz się, jak [Tworzenie profilu Menedżera ruchu](traffic-manager-create-profile.md)

