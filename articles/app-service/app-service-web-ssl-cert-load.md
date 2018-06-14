---
title: Użyj przekazano certyfikat SSL w kodzie aplikacji w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: ''
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
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2017
ms.locfileid: "26047700"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Użyj certyfikatu SSL w kodzie aplikacji w usłudze Azure App Service

Ten przewodnik przedstawia sposób użycia jednej z certyfikatów SSL, masz przekazany lub zaimportować do aplikację usługi aplikacji w kodzie aplikacji. Przykład przypadek użycia to, czy aplikacja uzyskuje dostęp do zewnętrznej usługi, który wymaga uwierzytelniania certyfikatu. 

Takie podejście do korzystania z certyfikatów SSL w kodzie, korzysta z protokołu SSL funkcji w usłudze App Service, która wymaga aplikacji w **podstawowe** warstwy lub nowszej. Alternatywą jest obejmują plik certyfikatu w katalogu aplikacji i załaduj go bezpośrednio (zobacz [alternatywne: obciążenia certyfikat jako plik](#file)). Jednak ta alternatywa nie zezwala na ukrywanie klucz prywatny w certyfikacie z kodu aplikacji lub dewelopera. Ponadto jeśli kod aplikacji znajduje się w repozytorium typu open source, utrzymywanie certyfikatu z kluczem prywatnym w repozytorium nie jest opcją.

Jeśli umożliwisz Zarządzaj certyfikatami SSL usługi aplikacji można osobno Obsługa certyfikatów i kod aplikacji i ochronie poufnych danych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik:

- [Utwórz aplikację usługi aplikacji](/azure/app-service/)
- [Mapowanie niestandardową nazwę DNS do aplikacji sieci web](app-service-web-tutorial-custom-domain.md)
- [Przekaż certyfikat SSL](app-service-web-tutorial-custom-ssl.md) lub [zaimportuj certyfikat usługi aplikacji](web-sites-purchase-ssl-web-site.md) do aplikacji sieci web


## <a name="load-your-certificates"></a>Ładowanie certyfikaty

Aby korzystać z certyfikatu, który jest przekazywane do lub zaimportować do usługi aplikacji, najpierw go udostępnić w kodzie aplikacji. W tym z `WEBSITE_LOAD_CERTIFICATES` ustawienia aplikacji.

W <a href="https://portal.azure.com" target="_blank">portalu Azure</a>, otwórz stronę sieci web w aplikacji.

W obszarze nawigacji po lewej stronie, kliknij przycisk **certyfikaty SSL**.

![Przekazany certyfikat](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Wszystkie przekazane i importowanie certyfikatów SSL dla tej aplikacji sieci web są wyświetlane tutaj z ich odciski palców. Skopiuj odcisk palca certyfikatu, którego chcesz użyć.

W obszarze nawigacji po lewej stronie, kliknij przycisk **ustawienia aplikacji**.

Dodaj aplikację nosi nazwę `WEBSITE_LOAD_CERTIFICATES` i ustaw dla niego wartość odcisku palca certyfikatu. Aby udostępnić wielu certyfikatów, należy użyć wartości rozdzielanych przecinkami odcisk palca. Aby udostępnić wszystkie certyfikaty, ustaw wartość na `*`. 

![Skonfiguruj ustawienia aplikacji](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Gdy skończysz, kliknij przycisk **zapisać**.

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

W tej sekcji przedstawiono sposób i załaduj plik certyfikatu, który znajduje się w katalogu aplikacji. 

W poniższym przykładzie C# ładuje certyfikat nazywany `mycert.pfx` z `certs` katalogu repozytorium aplikacji.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

