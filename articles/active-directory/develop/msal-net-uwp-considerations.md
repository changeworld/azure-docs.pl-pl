---
title: PLATFORMY UWP — zagadnienia (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o określonych kwestiach dotyczących używania platforma uniwersalna systemu Windows z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 4803b2bda63ef0e14137aaafe95a422089e7f671
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083659"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Zagadnienia dotyczące platforma uniwersalna systemu Windows z MSAL.NET
W systemie platformy UWP istnieje kilka kwestii, które należy wziąć pod uwagę podczas korzystania z MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Właściwość UseCorporateNetwork
Na platformie WinRT `PublicClientApplication` ma następującą właściwość logiczną ``UseCorporateNetwork``. Ta właściwość umożliwia aplikacjom Win 8.1 i platformy UWP korzystanie z zintegrowanego uwierzytelniania systemu Windows (i w związku z tym Logowanie jednokrotne przy użyciu systemu operacyjnego użytkownika), jeśli użytkownik jest zalogowany przy użyciu konta w federacyjnej dzierżawie usługi Azure AD. Po ustawieniu tej właściwości MSAL.NET wykorzystuje WAB (Web Authentication Broker).

> [!IMPORTANT]
> Ustawienie tej właściwości na true zakłada, że deweloper aplikacji włączył zintegrowane uwierzytelnianie systemu Windows (IWA) w aplikacji. Dla tego:
> - W ``Package.appxmanifest`` aplikacji platformy UWP na karcie **możliwości** Włącz następujące możliwości:
>   - Uwierzytelnianie w przedsiębiorstwie
>   - Sieci prywatne (serwer & klienta)
>   - Udostępniony certyfikat użytkownika

IWA nie jest domyślnie włączona, ponieważ aplikacje żądające możliwości uwierzytelniania przedsiębiorstwa lub certyfikatów użytkowników udostępnionych wymagają, aby wyższy poziom weryfikacji został zaakceptowany do sklepu Windows, a nie wszyscy deweloperzy mogą chcieć wykonać wyższą poziom weryfikacji.

Bazowa implementacja na platformie platformy UWP (WAB) nie działa prawidłowo w scenariuszach przedsiębiorstwa, w których włączono dostęp warunkowy. Objawem jest to, że użytkownik próbuje zalogować się przy użyciu systemu Windows Hello i zaproponować wybranie certyfikatu, ale:

- nie znaleziono certyfikatu dla numeru PIN,
- lub użytkownik wybiera go, ale nigdy nie otrzymuje monitu o podanie numeru PIN.

Obejście polega na użyciu alternatywnej metody (nazwy użytkownika/hasła i uwierzytelniania telefonu), ale środowisko nie jest dobre.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Niektórzy klienci zgłosili w niektórych środowiskach korporacyjnych następujące błędy logowania:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

wiedząc, że mają połączenie z Internetem i który współpracuje z siecią publiczną.

Obejście to upewnienie się, że sieć WAB (podstawowy składnik systemu Windows) umożliwia korzystanie z sieci prywatnej. Można to zrobić, ustawiając klucz rejestru:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Aby uzyskać szczegółowe informacje, zobacz [brokera uwierzytelniania w sieci Web — programu Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Następne kroki
Więcej szczegółowych informacji znajduje się w następujących przykładach:

Sample | Platforma | Opis 
|------ | -------- | -----------|
|[Active-Directory-dotnet-Native-platformy UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | Platforma UWP | Platforma uniwersalna systemu Windows aplikacji klienckiej używającej msal.net, która uzyskuje dostęp do Microsoft Graph w celu uwierzytelniania użytkowników za pomocą punktu końcowego usługi Azure AD v 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platformy UWP | Prosta aplikacja interfejsu Xamarin Forms, która przedstawia, jak używać MSAL do uwierzytelniania kont MSA i Azure AD za pośrednictwem punktu końcowego usługi AAD w wersji 2.0 oraz uzyskiwać dostęp do Microsoft Graph przy użyciu tokenu z wynikiem. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
