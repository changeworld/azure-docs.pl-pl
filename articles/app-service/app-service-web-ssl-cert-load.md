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
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475095"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service

Ten poradnik pokazuje jak używać certyfikatów publicznych lub prywatnych w kodzie aplikacji. Przykładem przypadek użycia jest, że Twoja aplikacja uzyskuje dostęp do usługi zewnętrznej, która wymaga uwierzytelniania certyfikatu.

Takie podejście do korzystania z certyfikatów w kodzie, korzysta z protokołu SSL funkcji w usłudze App Service, co wymaga aplikacji w **podstawowe** warstwy lub nowszej. Możesz też [dołączony plik certyfikatu do repozytorium aplikacji](#load-certificate-from-file), ale nie jest zalecana praktyka certyfikatów prywatnych.

Jeśli umożliwisz Zarządzaj certyfikatami SSL w usłudze App Service, możesz oddzielnie zachować certyfikaty i kodu aplikacji i chronić poufne dane.

## <a name="upload-a-private-certificate"></a>Przekaż certyfikat prywatny

Przed przekazaniem certyfikatu prywatnego, upewnij się, [spełnia wszystkie wymagania](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), z tą różnicą, że nie musi być skonfigurowany do uwierzytelniania serwera.

Gdy wszystko będzie gotowe do przekazania, uruchom następujące polecenie <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Skopiuj odcisk palca certyfikatu i zobacz [udostępnić certyfikat](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Przekazywanie certyfikatu publicznego

Certyfikaty publiczne są obsługiwane w *cer* formatu. Można przekazać certyfikatu publicznego, <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, a następnie przejdź do swojej aplikacji.

Kliknij przycisk **ustawienia protokołu SSL** > **certyfikaty publiczne (.cer)**  > **przekazywanie certyfikatu publicznego** w lewym obszarze nawigacji aplikacji.

W **nazwa**, wpisz nazwę certyfikatu. W **plik certyfikatu CER**, wybierz swój plik CER.

Kliknij pozycję **Przekaż**.

![Przekazywanie certyfikatu publicznego](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Po przekazaniu tego certyfikatu, skopiuj odcisk palca certyfikatu i zobacz [udostępnić certyfikat](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importowanie certyfikatu usługi App Service

Zobacz [Kup i skonfiguruj certyfikat SSL dla usługi Azure App Service](web-sites-purchase-ssl-web-site.md).

Po zaimportowaniu certyfikatu skopiuj odcisk palca certyfikatu i zobacz [udostępnić certyfikat](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Udostępnienie certyfikatu

Aby użyć certyfikatu przekazane lub zaimportowane w kodzie aplikacji, udostępnić jego odcisk palca z `WEBSITE_LOAD_CERTIFICATES` ustawienia aplikacji, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Aby udostępnić certyfikaty użytkownika, ustaw wartość `*`.

> [!NOTE]
> To ustawienie powoduje określonego certyfikaty w [User\My bieżącego](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) magazynu dla większości warstw cenowych, ale **izolowany** warstwy (czyli aplikacja jest uruchamiana w [środowiska App Service Environment](environment/intro.md)), certyfikaty są umieszczane [lokalnego Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) przechowywania.
>

![Skonfiguruj ustawienia aplikacji](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Po skończeniu kliknij przycisk **Zapisz**.

Skonfigurowanych certyfikatów są teraz gotowe do użycia w kodzie.

## <a name="load-the-certificate-in-code"></a>Ładowanie certyfikatu w kodzie

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
## <a name="load-certificate-from-file"></a>Załaduj certyfikat z pliku

Jeśli potrzebujesz załadować plik certyfikatu z katalogu aplikacji, zaleca się, przekaż go przy użyciu [FTPS](deploy-ftp.md) zamiast [Git](deploy-local-git.md), na przykład. Zachowaj dane poufne, takie jak certyfikatu prywatnego wyewidencjonowane z kontroli źródła.

Mimo że w przypadku ładowania pliku bezpośrednio w kodzie .NET, biblioteki nadal sprawdza, jeśli bieżący profil użytkownika jest ładowany. Aby załadować profilu bieżącego użytkownika, ustaw `WEBSITE_LOAD_USER_PROFILE` ustawienie aplikacji przy użyciu następującego polecenia w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Gdy to ustawienie ma wartość, następujące C# przykład ładuje certyfikatu o nazwie `mycert.pfx` z `certs` katalogu repozytorium aplikacji.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
