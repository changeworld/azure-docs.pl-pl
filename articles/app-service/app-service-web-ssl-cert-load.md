---
title: Używanie certyfikatu SSL klienta w kodzie aplikacji — Azure App Service | Microsoft Docs
description: Dowiedz się, jak używać certyfikatów klienta do łączenia się z zasobami zdalnymi, które ich wymagają.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c8c270681794621b2a12671d4bcf350cd6cc4d8
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981111"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Użyj certyfikatu protokołu SSL w kodzie aplikacji w Azure App Service

W tym przewodniku opisano sposób używania certyfikatów publicznych lub prywatnych w kodzie aplikacji. Przykładem przypadku użycia jest to, że aplikacja uzyskuje dostęp do zewnętrznej usługi, która wymaga uwierzytelniania certyfikatu.

Takie podejście do korzystania z certyfikatów w kodzie korzysta z funkcji SSL w App Service, co wymaga, aby aplikacja była w warstwie **podstawowa** lub wyższa. Alternatywnie możesz [dołączyć plik certyfikatu do repozytorium aplikacji](#load-certificate-from-file), ale nie jest to zalecane rozwiązanie dla certyfikatów prywatnych.

Gdy zezwolisz App Service na zarządzanie certyfikatami SSL, możesz osobno zarządzać certyfikatami i kodem aplikacji oraz chronić poufne dane.

## <a name="upload-a-private-certificate"></a>Przekaż certyfikat prywatny

Przed przekazaniem certyfikatu prywatnego upewnij się, że [spełnia on wszystkie wymagania](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), z tą różnicą, że nie trzeba go konfigurować na potrzeby uwierzytelniania serwera.

Gdy wszystko będzie gotowe do przekazania, uruchom następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Skopiuj odcisk palca certyfikatu i sprawdź, czy [certyfikat jest dostępny](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Przekaż certyfikat publiczny

Certyfikaty publiczne są obsługiwane w formacie *CER* . Aby przekazać certyfikat publiczny, <a href="https://portal.azure.com" target="_blank">Azure Portal</a>i przejdź do swojej aplikacji.

Kliknij pozycję **Ustawienia protokołu SSL** > **Certyfikaty publiczne (. cer)**  > **Przekaż certyfikat publiczny** od lewego nawigowania w aplikacji.

W polu **Nazwa**wpisz nazwę certyfikatu. W polu **plik certyfikatu CER**wybierz plik CER.

Kliknij przycisk **Przekaż**.

![Przekaż certyfikat publiczny](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Po przekazaniu certyfikatu Skopiuj odcisk palca certyfikatu i sprawdź, czy [certyfikat jest dostępny](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importowanie certyfikatu App Service

Zobacz [kupowanie i Konfigurowanie certyfikatu SSL dla Azure App Service](web-sites-purchase-ssl-web-site.md).

Po zaimportowaniu certyfikatu Skopiuj odcisk palca certyfikatu i sprawdź, czy [certyfikat jest dostępny](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Udostępnianie certyfikatu

Aby użyć przekazanego lub zaimportowanego certyfikatu w kodzie aplikacji, zmień jego odcisk palca na ustawienie aplikacji `WEBSITE_LOAD_CERTIFICATES`, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Aby udostępnić wszystkie certyfikaty, ustaw wartość na `*`.

> [!NOTE]
> To ustawienie powoduje umieszczenie określonych certyfikatów w [bieżącym magazynie User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) w przypadku większości warstw cenowych, ale jeśli aplikacja jest uruchomiona w warstwie **izolowanej** (tj. aplikacja działa w [App Service Environment](environment/intro.md)), może być konieczne zaewidencjonowanie [lokalnego Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) .
>

![Konfiguruj ustawienie aplikacji](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Po zakończeniu kliknij przycisk **Zapisz**.

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

Mimo że ładujesz plik bezpośrednio w kodzie .NET, biblioteka nadal weryfikuje, czy bieżący profil użytkownika jest załadowany. Aby załadować bieżący profil użytkownika, ustaw dla ustawienia aplikacji `WEBSITE_LOAD_USER_PROFILE` następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

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
