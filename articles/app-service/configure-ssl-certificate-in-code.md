---
title: Używanie certyfikatu SSL w kodzie aplikacji — Azure App Service | Microsoft Docs
description: Dowiedz się, jak używać certyfikatów klienta do łączenia się z zasobami zdalnymi, które ich wymagają.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513694"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Użyj certyfikatu protokołu SSL w kodzie aplikacji w Azure App Service

Kod aplikacji App Service może pełnić rolę klienta i uzyskać dostęp do usługi zewnętrznej wymagającej uwierzytelniania przy użyciu certyfikatu. W tym przewodniku opisano sposób używania certyfikatów publicznych lub prywatnych w kodzie aplikacji.

Takie podejście do korzystania z certyfikatów w kodzie korzysta z funkcji SSL w App Service, co wymaga, aby aplikacja była w warstwie **podstawowa** lub wyższa. Alternatywnie możesz [dołączyć plik certyfikatu do repozytorium aplikacji](#load-certificate-from-file), ale nie jest to zalecane rozwiązanie dla certyfikatów prywatnych.

Gdy zezwolisz App Service na zarządzanie certyfikatami SSL, możesz osobno zarządzać certyfikatami i kodem aplikacji oraz chronić poufne dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Dodawanie certyfikatu do aplikacji](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Znajdowanie odcisku palca

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services** >  **\<App-Name >** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**, a następnie wybierz pozycję **certyfikaty kluczy prywatnych (pfx)** lub **Certyfikaty klucza publicznego (CER)** .

Znajdź certyfikat, którego chcesz użyć, i Skopiuj odcisk palca.

![Skopiuj odcisk palca certyfikatu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>Załaduj certyfikat

Aby użyć certyfikatu w kodzie aplikacji, Dodaj jego odcisk palca do ustawienia aplikacji `WEBSITE_LOAD_CERTIFICATES`, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Aby udostępnić wszystkie certyfikaty, ustaw wartość na `*`.

> [!NOTE]
> To ustawienie powoduje umieszczenie określonych certyfikatów w [bieżącym magazynie User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) w przypadku większości warstw cenowych, ale w warstwie **izolowanej** (tj. aplikacja działa w [App Service Environment](environment/intro.md)) umieszcza certyfikaty w [lokalnym Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) zachować.
>

Skonfigurowane certyfikaty są teraz gotowe do użycia przez Twój kod.

## <a name="load-the-certificate-in-code"></a>Załaduj certyfikat w kodzie

Po uzyskaniu dostępu do certyfikatu można uzyskać do niego C# dostęp w kodzie za pomocą odcisku palca certyfikatu. Poniższy kod ładuje certyfikat z odciskiem palca `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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
## <a name="load-certificate-from-file"></a>Załaduj certyfikat z pliku

Jeśli zachodzi potrzeba załadowania pliku certyfikatu z katalogu aplikacji, lepszym rozwiązaniem jest przekazanie go przy użyciu [FTPS](deploy-ftp.md) zamiast [git](deploy-local-git.md), na przykład. Należy zachować poufne dane, takie jak certyfikat prywatny, z kontroli źródła.

Mimo że ładujesz plik bezpośrednio w kodzie .NET, biblioteka nadal weryfikuje, czy bieżący profil użytkownika jest załadowany. Aby załadować bieżący profil użytkownika, należy ustawić `WEBSITE_LOAD_USER_PROFILE` ustawienia aplikacji za pomocą następującego polecenia w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Po ustawieniu tego ustawienia Poniższy C# przykład ładuje certyfikat o nazwie `mycert.pfx` z katalogu `certs` repozytorium aplikacji.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>Więcej zasobów

* [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania SSL](configure-ssl-bindings.md)
* [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Wymuś protokół TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Często zadawane pytania: certyfikaty App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
