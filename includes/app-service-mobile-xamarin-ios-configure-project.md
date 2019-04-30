---
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097545"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurowanie projektu dla systemu iOS w programie Xamarin Studio
1. Xamarin.Studio, otwórz **Info.plist**i zaktualizuj **identyfikatora pakietu** o identyfikatorze pakietu utworzonego wcześniej przy użyciu swojego nowego identyfikatora aplikacji.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Przewiń w dół do **Background Modes**. Wybierz **Włącz tryby w tle** pole i **zdalne powiadomienia** pole.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Kliknij dwukrotnie projekt w panelu rozwiązania, aby otworzyć **opcje projektu**.
4. W obszarze **kompilacji**, wybierz **podpisywanie pakietu systemu iOS**, wybierz odpowiedni tożsamość i profil aprowizacji możesz po prostu zestaw zapasową dla tego projektu.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Daje to gwarancję, że projekt korzysta z nowego profilu do podpisywania kodu. Aprowizacji dokumentacji oficjalnego urządzeń platformy Xamarin dla [Inicjowanie obsługi administracyjnej urządzeniem programu Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurowanie projektu dla systemu iOS w programie Visual Studio
1. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt, a następnie kliknij przycisk **właściwości**.
2. Na stronach właściwości, kliknij przycisk **aplikacja dla systemu iOS** , a następnie zaktualizować **identyfikator** identyfikatorem, który został utworzony wcześniej.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. W **podpisywanie pakietu systemu iOS** kartę, zaznacz odpowiedniej tożsamości i inicjowania obsługi profilu, możesz po prostu skonfigurować dla tego projektu.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Daje to gwarancję, że projekt korzysta z nowego profilu do podpisywania kodu. Aprowizacji dokumentacji oficjalnego urządzeń platformy Xamarin dla [Inicjowanie obsługi administracyjnej urządzeniem programu Xamarin].
4. Kliknij dwukrotnie plik Info.plist, aby go otworzyć, a następnie włączyć **RemoteNotifications** w obszarze **Background Modes**.

[Inicjowanie obsługi administracyjnej urządzeniem programu Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/