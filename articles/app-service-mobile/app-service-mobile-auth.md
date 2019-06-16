---
title: Uwierzytelnianie i autoryzacja w usłudze Azure App Service dla aplikacji mobilnych | Dokumentacja firmy Microsoft
description: Przewodnik koncepcyjny oraz omówienie uwierzytelniania / autoryzacji funkcji Azure App Service dla aplikacji mobilnych
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859128"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service dla aplikacji mobilnych

W tym artykule opisano, jak uwierzytelnianie i autoryzacja działa podczas tworzenia natywnych aplikacji mobilnych z zapleczem usługi App Service. Usługa App Service udostępnia zintegrowane uwierzytelniania i autoryzacji, aby aplikacje mobilne można się zalogować użytkowników bez zmieniania kodu w usłudze App Service. Zapewnia prosty sposób ochrona aplikacji i Praca z danymi użytkownika. 

Ten artykuł koncentruje się na temat programowania aplikacji mobilnych. Aby szybko rozpocząć pracę z uwierzytelniania usługi App Service i autoryzacji dla aplikacji mobilnej, zobacz jeden z następujących samouczków [Dodawanie uwierzytelniania do aplikacji systemu iOS] [ iOS] (lub [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], lub [Cordova]). 

Aby uzyskać informacje na temat uwierzytelniania i autoryzacji w usłudze App Service, zobacz [uwierzytelnianie i autoryzacja w usłudze Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Uwierzytelnianie za pomocą dostawcy zestawu SDK

Po skonfigurowaniu wszystkiego w usłudze App Service można zmodyfikować klientów mobilnych, aby zalogować się przy użyciu usługi App Service. Istnieją dwie metody w tym miejscu:

* Użyj zestawu SDK, który dostawca tożsamości danego publikuje do ustalenia tożsamości, a następnie uzyskać dostęp do usługi App Service.
* Tak, aby zestaw SDK klienta funkcji Mobile Apps można logowania użytkowników, należy użyć jednego wiersza kodu.

> [!TIP]
> Większość aplikacji, należy użyć dostawcy SDK, aby uzyskać spójniejsze środowisko pracy podczas logowania, aby korzystać z pomocy technicznej w wyniku odświeżenia tokenu i uzyskać inne korzyści, które określa dostawcę.
> 
> 

Gdy używasz dostawcy SDK, użytkownicy mogą logować się do bardziej ściśle integruje się z systemu operacyjnego, który aplikacja jest uruchomiona na środowisko. Ta metoda oferuje również dostawcę tokenu i niektóre informacje użytkownika na komputerze klienckim, co pozwala na znacznie ułatwia korzystanie z interfejsów API programu graph i Dostosuj interfejs użytkownika. Od czasu do czasu w naszych blogach i forach go nazywa się "przepływ klienta" lub "skierowane do klientów przepływ", ponieważ kod na loguje się użytkownicy klienta i kod klienta ma dostęp do dostawcy tokenu.

Po uzyskaniu tokenu dostawca musi być wysyłane do usługi App Service do sprawdzania poprawności. Po usługi App Service sprawdza poprawność tokenu, usługa App Service utworzy nowy token usługi App Service, która jest zwracana do klienta. Zestaw SDK klienta funkcji Mobile Apps zawiera metody pomocnicze do zarządzania tym programu exchange i automatycznie dołączyć ten token do wszystkich żądań do aplikacji zaplecza. Deweloperzy można również przechowywać odwołania do tokenu dostawcy.

Aby uzyskać więcej informacji na temat przepływ uwierzytelniania, zobacz [przebieg uwierzytelniania w usłudze App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Uwierzytelnianie bez dostawcy zestawu SDK

Jeśli chcesz skonfigurować dostawcy SDK, możesz zezwolić funkcji Mobile Apps w usłudze Azure App Service, aby zalogować się za Ciebie. Zestaw SDK klienta funkcji Mobile Apps spowoduje otwarcie widoku sieci web dostawcy wybrane i loguje użytkownika. Od czasu do czasu w naszych blogach i forach, jest on nazywany "przepływ serwera" lub "przepływ skierowane do serwera" ponieważ serwer zarządza procesem, logujący się użytkownicy, i zestawu SDK klienta nigdy nie odbiera token dostawcy.

Kod, aby uruchomić ten przepływ znajduje się w samouczku uwierzytelniania dla każdej platformy. Na końcu przepływu zestaw SDK klienta ma tokenu usługi App Service, a token jest automatycznie dołączany do wszystkich żądań do serwera aplikacji zaplecza.

Aby uzyskać więcej informacji na temat przepływ uwierzytelniania, zobacz [przebieg uwierzytelniania w usłudze App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Więcej zasobów

Następujące samouczki pokazują, jak dodać uwierzytelnianie do klientów mobilnych za pomocą [skierowane do serwera usługi flow](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Dodawanie uwierzytelniania do aplikacji systemu iOS][iOS]
* [Dodawanie uwierzytelniania do aplikacji systemu Android][android]
* [Dodawanie uwierzytelniania do aplikacji Windows][Windows]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin.iOS][xamarin.ios]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin.Android][xamarin.android]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin.Forms][xamarin.forms]
* [Dodawanie uwierzytelniania do aplikacji Cordova][Cordova]

Użyj następujących zasobów, jeśli chcesz używać [skierowane do klientów usługi flow](../app-service/overview-authentication-authorization.md#authentication-flow) usługi Azure Active Directory:

* [Użycie biblioteki uwierzytelniania usługi Active Directory dla systemu iOS][ADAL-iOS]
* [Korzystanie z biblioteki uwierzytelniania usługi Active Directory dla systemu Android][ADAL-Android]
* [Korzystanie z biblioteki uwierzytelniania usługi Active Directory dla Windows i Xamarin][ADAL-dotnet]

Użyj następujących zasobów, jeśli chcesz używać [skierowane do klientów usługi flow](../app-service/overview-authentication-authorization.md#authentication-flow) za pomocą konta Facebook:

* [Używanie zestawu SDK usługi Facebook dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Użyj następujących zasobów, jeśli chcesz używać [skierowane do klientów usługi flow](../app-service/overview-authentication-authorization.md#authentication-flow) dla usługi Twitter:

* [Użyj usługi Twitter Service Fabric dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Użyj następujących zasobów, jeśli chcesz używać [skierowane do klientów usługi flow](../app-service/overview-authentication-authorization.md#authentication-flow) dla usług Google:

* [Użycie Google Sign-In SDK dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
