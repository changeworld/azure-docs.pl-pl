---
title: Użyj certyfikatu klienta SSL w kodzie aplikacji — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać certyfikatów klienta, nawiązywania połączenia z zasobami zdalnymi, które tego wymagają.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 763aadc50a8760b4265dbfc21e9278f909b68433
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851123"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service

Ten poradnik pokazuje jak użyć jednego z certyfikatów SSL, które zostały przekazane lub zaimportowane do aplikacji usługi app Service w kodzie aplikacji. Przykładem przypadek użycia jest, że Twoja aplikacja uzyskuje dostęp do usługi zewnętrznej, która wymaga uwierzytelniania certyfikatu. 

Takie podejście do korzystania z certyfikatów SSL w kodzie, korzysta z protokołu SSL funkcji w usłudze App Service, co wymaga aplikacji w **podstawowe** warstwy lub nowszej. Alternatywą jest dołączenie pliku certyfikatu w katalogu aplikacji i załadować je bezpośrednio (zobacz [alternatywnych: obciążenia certyfikat jako plik](#file)). Jednak ta alternatywa pozwala ukryć klucz prywatny w certyfikacie z kodu aplikacji lub dewelopera. Ponadto jeśli kod aplikacji znajduje się w repozytorium "open source", utrzymywanie certyfikatu z kluczem prywatnym w repozytorium nie jest opcją.

Jeśli umożliwisz Zarządzaj certyfikatami SSL w usłudze App Service, możesz oddzielnie zachować certyfikaty i kodu aplikacji i chronić poufne dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten poradnik:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Mapuj niestandardową nazwę DNS na swoją aplikację internetową](app-service-web-tutorial-custom-domain.md)
- [Przekaż certyfikat SSL](app-service-web-tutorial-custom-ssl.md) lub [Importowanie certyfikatu usługi App Service](web-sites-purchase-ssl-web-site.md) do aplikacji sieci web


## <a name="load-your-certificates"></a>Ładowanie certyfikatów

Aby korzystać z certyfikatu, który jest przekazywane do lub zaimportować do usługi App Service, najpierw go udostępnić w kodzie aplikacji. W tym z `WEBSITE_LOAD_CERTIFICATES` ustawienia aplikacji.

W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, otwórz stronę aplikacji sieci web.

Na lewym pasku nawigacyjnym kliknij **certyfikaty SSL**.

![Przekazano certyfikat](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Wszystkie przekazane i importowanie certyfikatów SSL dla tej aplikacji sieci web są wyświetlane w tym miejscu przy użyciu ich odciski palców. Skopiuj odcisk palca certyfikatu, którego chcesz użyć.

Na lewym pasku nawigacyjnym kliknij **ustawienia aplikacji**.

Dodawanie aplikacji nosi nazwę `WEBSITE_LOAD_CERTIFICATES` i ustawić jej wartość na odcisk palca certyfikatu. Aby udostępnić wiele certyfikatów, należy użyć wartości rozdzielanych przecinkami odcisku palca. Aby udostępnić wszystkie certyfikaty, ustaw wartość `*`. Take należy pamiętać, że spowoduje to umieszczenie certyfikatu w `CurrentUser\My` przechowywania.

![Skonfiguruj ustawienia aplikacji](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Po skończeniu kliknij przycisk **Zapisz**.

Skonfigurowany certyfikat jest teraz gotowa do użycia w kodzie.

## <a name="use-certificate-in-c-code"></a>Użyj certyfikatu w kodzie języka C#

Gdy certyfikat jest dostępny, można do niego dostęp w kodzie języka C# przez odcisk palca certyfikatu. Poniższy kod ładuje certyfikatu z odciskiem palca `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>Alternatywa: załadować certyfikat jako plik

W tej sekcji pokazano, jak i załaduj plik certyfikatu, który znajduje się w katalogu aplikacji. 

W poniższym przykładzie C# ładuje certyfikatu o nazwie `mycert.pfx` z `certs` katalogu repozytorium aplikacji.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

