---
title: Pomiary rzeczywistych użytkowników za pomocą programu Visual Studio Mobile Center — usługa Azure Traffic Manager
description: Skonfiguruj aplikację mobilną opracowaną przy użyciu programu Visual Studio Mobile Center w celu wysyłania pomiarów rzeczywistych użytkowników do Menedżera ruchu
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939184"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Jak wysyłać pomiary rzeczywistych użytkowników do menedżera ruchu za pomocą programu Visual Studio Mobile Center

Aplikację mobilną opracowaną za pomocą programu Visual Studio Mobile Center można skonfigurować w celu wysyłania pomiarów rzeczywistych użytkowników do usługi Traffic Manager, wykonując następujące czynności:

>[!NOTE]
> Obecnie wysyłanie pomiarów rzeczywistego użytkownika do Menedżera ruchu jest obsługiwane tylko dla systemu Android.

Aby skonfigurować pomiary rzeczywistego użytkownika, musisz uzyskać klucz i instrument aplikacji za pomocą pakietu RUM.

## <a name="step-1-obtain-a-key"></a>Krok 1: Uzyskaj klucz
    
Pomiary, które należy podjąć i wysłać do usługi Traffic Manager z aplikacji klienckiej są identyfikowane przez usługę przy użyciu unikatowego ciągu, zwanego kluczem pomiarów rzeczywistego użytkownika (RUM). Klucz RUM można uzyskać za pomocą witryny Azure portal, interfejsu API REST lub przy użyciu interfejsów programu PowerShell/CLI.

Aby uzyskać klucz RUM przy użyciu witryny Azure portal przy użyciu następującej procedury:
1. Z poziomu przeglądarki zaloguj się do witryny Azure Portal. Jeśli jeszcze nie masz konta, możesz skorzystać z bezpłatnej miesięcznej wersji próbnej.
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę profilu usługi Traffic Manager, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. Na stronie profilu Usługi Traffic Manager kliknij pozycję **Pomiary rzeczywistego użytkownika** w obszarze **Ustawienia**.
4. Kliknij **przycisk Generuj klucz,** aby utworzyć nowy klucz RUM.
        
   ![Klucz Generowanie pomiarów rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: Generowanie klucza pomiarów rzeczywistych użytkowników**

5. Na stronie zostanie wyświetlony wygenerowany klucz RUM oraz fragment kodu JavaScript, który musi zostać osadzony na stronie HTML.
 
   ![Kod JavaScript dla klucza pomiarów rzeczywistych użytkowników](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Rysunek 2: Klucz pomiarów rzeczywistych użytkowników i pomiar JavaScript**
 
6. Kliknij przycisk **Kopiuj,** aby skopiować klucz RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: Instrumentowanie aplikacji za pomocą pakietu RUM mobile center SDK

Jeśli jesteś nowym użytkownikem programu Visual Studio Mobile Center, odwiedź jego [witrynę sieci Web.](https://mobile.azure.com) Aby uzyskać szczegółowe instrukcje dotyczące integracji z zestawem SDK, zobacz [Wprowadzenie do sdk systemu Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Aby użyć pomiarów rzeczywistego użytkownika, wykonaj następującą procedurę:

1.  Dodawanie sdk do projektu

    Podczas podglądu zestawu SDK ATM RUM należy jawnie odwoływać się do repozytorium pakietów.

    W pliku **app/build.gradle** dodaj następujące wiersze:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    W pliku **app/build.gradle** dodaj następujące wiersze:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Uruchamianie sdk

    Otwórz główną klasę aktywności aplikacji i dodaj następujące instrukcje importu:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Poszukaj wywołania zwrotnego `onCreate` w tym samym pliku i dodaj następujący kod:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [pomiarach rzeczywistych użytkowników](traffic-manager-rum-overview.md)
- Dowiedz [się, jak działa Usługa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Zarejestruj się w](https://mobile.azure.com) Mobile Center
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez usługę Traffic Manager
- Dowiedz się, jak [utworzyć profil usługi Traffic Manager](traffic-manager-create-profile.md)

