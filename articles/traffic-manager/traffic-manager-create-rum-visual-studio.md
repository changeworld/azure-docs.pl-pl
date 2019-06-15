---
title: Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager za pomocą programu Visual Studio Mobile Center | Dokumentacja firmy Microsoft
description: Konfigurowanie aplikacji mobilnych opracowanych za pomocą programu Visual Studio Mobile Center wysyłać pomiary dotyczące prawdziwych użytkowników do usługi Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 95207cea5988a22b0b3caa23be39b481f9fd687b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071255"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Jak wysyłać pomiary dotyczące prawdziwych użytkowników do usługi Traffic Manager za pomocą programu Visual Studio Mobile Center

Można skonfigurować aplikację mobilną opracowanych za pomocą programu Visual Studio Mobile Center, aby wysyłanie pomiarów rzeczywistego użytkownika do usługi Traffic Manager, wykonując następujące kroki:

>[!NOTE]
> Obecnie wysyłanie pomiarów rzeczywistego użytkownika do usługi Traffic manager jest obsługiwana tylko dla systemu Android.

Aby skonfigurować pomiarów rzeczywistego użytkownika, należy uzyskać klucz i instrumentacji aplikacji za pomocą pakietu uruchamianie.

## <a name="step-1-obtain-a-key"></a>Krok 1: Uzyskiwanie klucza
    
Pomiary, pobrać i wysyłane do usługi Traffic Manager z aplikacji klienckiej są identyfikowane przez usługę za pomocą unikatowy ciąg, nazywany kluczem rzeczywistego użytkownika pomiarów (RUM). Możesz uzyskać klucz uruchamianie przy użyciu witryny Azure portal, interfejsu API REST lub przy użyciu programu PowerShell / interfejsu wiersza polecenia, interfejsy.

Aby uzyskać klucz RUM przy użyciu witryny Azure portal, korzystając z następującej procedury:
1. Z poziomu przeglądarki Zaloguj się do witryny Azure portal. Jeśli nie masz jeszcze konta, możesz zarejestrować się w bezpłatnej wersji próbnej jednego miesiąca.
2. Na pasku wyszukiwania portalu Wyszukaj nazwę profilu usługi Traffic Manager, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w taki sposób, w wynikach, wyświetlana.
3. Na stronie profilu usługi Traffic Manager kliknij **pomiarów rzeczywistego użytkownika** w obszarze **ustawienia**.
4. Kliknij przycisk **Wygeneruj klucz** można utworzyć nowego klucza (rum).
        
   ![Wygeneruj klucz pomiarów rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: Rzeczywiste pomiary dotyczące prawdziwych użytkowników generowania kluczy**

5. Zostanie wyświetlona strona klucza (rum), który jest generowany i wstawki kodu JavaScript, który ma być osadzone w kodzie strony HTML.
 
   ![Kod JavaScript dla klucza pomiarów rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Rysunek 2: Klucz pomiarów rzeczywistego użytkownika oraz kod JavaScript pomiaru**
 
6. Kliknij przycisk **kopiowania** przycisk, aby skopiować klucz (rum). 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: Instrumentacja aplikacji przy użyciu uruchamianie pakiet zestawu SDK usługi Mobile Center

Jeśli jesteś nowym użytkownikiem programu Visual Studio Mobile Center, odwiedź stronę jego [witryny sieci Web](https://mobile.azure.com). Aby uzyskać szczegółowe instrukcje dotyczące integracji zestawu SDK, zobacz [wprowadzenie do zestawu SDK systemu Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Aby użyć pomiarów rzeczywistego użytkownika, należy wykonać poniższą procedurę:

1.  Dodaj zestaw SDK do projektu

    W trakcie okresu zapoznawczego ATM RUM SDK należy jawnie odwoływać się do repozytorium pakietów.

    W swojej **app/build.gradle** pliku Dodaj następujące wiersze:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    W swojej **app/build.gradle** pliku Dodaj następujące wiersze:

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
- Dowiedz się więcej o [pomiarów rzeczywistego użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się, [jak działa usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Zarejestruj](https://mobile.azure.com) for Mobile Center
- Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez usługę Traffic Manager
- Dowiedz się, jak [Tworzenie profilu usługi Traffic Manager](traffic-manager-create-profile.md)

