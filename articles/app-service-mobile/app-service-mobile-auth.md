---
title: Uwierzytelnianie i autoryzacja
description: Odwołanie koncepcyjne i omówienie funkcji uwierzytelniania / autoryzacji dla usługi Azure App Service, specjalnie dla aplikacji mobilnych.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459467"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service dla aplikacji mobilnych

W tym artykule opisano, jak działa uwierzytelnianie i autoryzacja podczas tworzenia natywnych aplikacji mobilnych z zapleczem usługi App Service. Usługa App Service zapewnia zintegrowane uwierzytelnianie i autoryzację, dzięki czemu aplikacje mobilne mogą logować się do użytkowników bez zmiany kodu w usłudze App Service. Zapewnia łatwy sposób ochrony aplikacji i pracy z danymi dla użytkownika. 

Ten artykuł koncentruje się na tworzeniu aplikacji mobilnych. Aby szybko rozpocząć pracę z uwierzytelnianiem i autoryzacją usługi App Service dla aplikacji mobilnej, zobacz jeden z poniższych samouczków [Dodawanie uwierzytelniania do aplikacji na iOS][iOS] (lub [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]lub [Cordova]). 

Aby uzyskać informacje na temat działania uwierzytelniania i autoryzacji w usłudze App Service, zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Uwierzytelnianie za pomocą sdk dostawcy

Po skonfigurowaniu wszystkiego w usłudze App Service można zmodyfikować klientów mobilnych, aby zalogować się za pomocą usługi App Service. Istnieją dwa podejścia tutaj:

* Użyj zestawu SDK, który publikuje dany dostawca tożsamości w celu ustalenia tożsamości, a następnie uzyskania dostępu do usługi App Service.
* Użyj jednego wiersza kodu, aby sdk klienta aplikacji mobilnych mógł logować się do użytkowników.

> [!TIP]
> Większość aplikacji należy użyć sdk dostawcy, aby uzyskać bardziej spójne środowisko podczas logowania użytkowników, aby użyć obsługi odświeżania tokenu i uzyskać inne korzyści, które dostawca określa.
> 
> 

Korzystając z sdk dostawcy, użytkownicy mogą zalogować się do środowiska, które integruje się bardziej szczelnie z systemem operacyjnym, na którym aplikacja jest uruchomiona. Ta metoda zapewnia również token dostawcy i niektóre informacje o użytkowniku na kliencie, co znacznie ułatwia korzystanie z interfejsów API wykresu i dostosować środowisko użytkownika. Od czasu do czasu na blogach i forach jest określany jako "przepływ klienta" lub "przepływ kierowany przez klienta", ponieważ kod na kliencie loguje się w użytkownikach, a kod klienta ma dostęp do tokenu dostawcy.

Po uzyskaniu tokenu dostawcy musi zostać wysłany do usługi App Service w celu weryfikacji. Po app service sprawdza poprawność tokenu, Usługa app service tworzy nowy token usługi app service, który jest zwracany do klienta. SDK klienta aplikacji mobilnych ma metody pomocnicze do zarządzania tą wymianą i automatycznie dołączyć token do wszystkich żądań do zaplecza aplikacji. Deweloperzy mogą również zachować odwołanie do tokenu dostawcy.

Aby uzyskać więcej informacji na temat przepływu uwierzytelniania, zobacz [Przepływ uwierzytelniania usługi App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Uwierzytelnianie bez sdk dostawcy

Jeśli nie chcesz skonfigurować zestawu SDK dostawcy, możesz zezwolić na logowanie się do aplikacji mobilnych usługi Azure App Service. SDK klienta aplikacji mobilnych otworzy widok sieci Web dla wybranego dostawcy i zaloguj się do użytkownika. Od czasu do czasu na blogach i forach jest nazywany "przepływ serwera" lub "przepływ kierowany na serwer", ponieważ serwer zarządza procesem, który loguje się do użytkowników, a sdk klienta nigdy nie odbiera tokenu dostawcy.

Kod do uruchomienia tego przepływu znajduje się w samouczku uwierzytelniania dla każdej platformy. Na końcu przepływu sdk klienta ma token usługi app service, a token jest automatycznie dołączany do wszystkich żądań do wewnętrznej bazy danych aplikacji.

Aby uzyskać więcej informacji na temat przepływu uwierzytelniania, zobacz [Przepływ uwierzytelniania usługi App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Więcej zasobów

Poniższe samouczki pokazują, jak dodać uwierzytelnianie do klientów mobilnych przy użyciu [przepływu skierowanego](../app-service/overview-authentication-authorization.md#authentication-flow)do serwera:

* [Dodawanie uwierzytelniania do aplikacji na iOS][iOS]
* [Dodawanie uwierzytelniania do aplikacji na Androida][Android]
* [Dodawanie uwierzytelniania do aplikacji systemu Windows][Windows]
* [Dodawanie uwierzytelniania do aplikacji Xamarin.iOS][Xamarin.iOS]
* [Dodawanie uwierzytelniania do aplikacji Xamarin.Android][Xamarin.Android]
* [Dodawanie uwierzytelniania do aplikacji Xamarin.Forms][Xamarin.Forms]
* [Dodawanie uwierzytelniania do aplikacji Cordova][Cordova]

Użyj następujących zasobów, jeśli chcesz użyć [przepływu kierowanego](../app-service/overview-authentication-authorization.md#authentication-flow) przez klienta dla usługi Azure Active Directory:

* [Korzystanie z biblioteki uwierzytelniania usługi Active Directory dla systemu iOS][ADAL-iOS]
* [Korzystanie z biblioteki uwierzytelniania usługi Active Directory dla systemu Android][ADAL-Android]
* [Korzystanie z biblioteki uwierzytelniania usługi Active Directory dla systemów Windows i platformy Xamarin][ADAL-dotnet]

Użyj następujących zasobów, jeśli chcesz użyć [przepływu kierowanego](../app-service/overview-authentication-authorization.md#authentication-flow) przez klienta na Facebooku:

* [Korzystanie z sdk Facebook dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Użyj następujących zasobów, jeśli chcesz użyć [przepływu kierowanego](../app-service/overview-authentication-authorization.md#authentication-flow) przez klienta dla Twittera:

* [Korzystanie z twitter fabric dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Użyj następujących zasobów, jeśli chcesz korzystać z [przepływu kierowanego](../app-service/overview-authentication-authorization.md#authentication-flow) przez klienta dla Google:

* [Korzystanie z sdk logowania google dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
