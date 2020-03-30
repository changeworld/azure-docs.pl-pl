---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183731"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurowanie projektu systemu iOS w programie Xamarin Studio
1. W programie Xamarin.Studio otwórz **info.plist**i zaktualizuj **identyfikator pakietu** o identyfikator pakietu, który został utworzony wcześniej za pomocą nowego identyfikatora aplikacji.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Przewiń w dół do **trybu tła**. Wybierz pole **Włącz tryby tła** i pole **Powiadomienia zdalne.**

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Kliknij dwukrotnie projekt w Panelu rozwiązania, aby otworzyć **aplet Opcje projektu**.
4. W obszarze **Kompilacja**wybierz pozycję **Podpisywanie pakietu**systemu iOS i wybierz odpowiedni profil tożsamości i inicjowania obsługi administracyjnej, który właśnie skonfigurowano dla tego projektu.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Gwarantuje to, że projekt używa nowego profilu do podpisywania kodu. Aby zapoznać się z oficjalną dokumentacją inicjowania obsługi administracyjnej urządzeń platformy Xamarin, zobacz [Inicjowanie obsługi administracyjnej urządzeń platformy Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurowanie projektu systemu iOS w programie Visual Studio
1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt, a następnie kliknij polecenie **Właściwości**.
2. Na stronach właściwości kliknij kartę Aplikacji systemu **iOS** i zaktualizuj **identyfikator** o identyfikator utworzony wcześniej.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Na karcie **Podpisywanie pakietu systemu iOS** wybierz odpowiedni profil tożsamości i inicjowania obsługi administracyjnej, który został właśnie skonfigurowany dla tego projektu.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Gwarantuje to, że projekt używa nowego profilu do podpisywania kodu. Aby zapoznać się z oficjalną dokumentacją inicjowania obsługi administracyjnej urządzeń platformy Xamarin, zobacz [Inicjowanie obsługi administracyjnej urządzeń platformy Xamarin].
4. Kliknij dwukrotnie info.plist, aby go otworzyć, a następnie włącz **zdalnenotyfikacje** w **trybach tła**.

[Inicjowanie obsługi administracyjnej urządzeń platformy Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
