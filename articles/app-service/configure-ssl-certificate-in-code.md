---
title: Używanie certyfikatu TLS/SSL w kodzie
description: Dowiedz się, jak używać certyfikatów klientów w kodzie. Uwierzytelnij się przy użyciu zasobów zdalnych za pomocą certyfikatu klienta lub uruchom z nimi zadania kryptograficzne.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811697"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Używanie certyfikatu TLS/SSL w kodzie w usłudze Azure App Service

W kodzie aplikacji można uzyskać dostęp do [certyfikatów publicznych lub prywatnych dodawanych do usługi App Service](configure-ssl-certificate.md). Kod aplikacji może działać jako klient i uzyskiwać dostęp do usługi zewnętrznej, która wymaga uwierzytelniania certyfikatów lub może być konieczne wykonywanie zadań kryptograficznych. W tym przewodniku pokazano, jak używać certyfikatów publicznych lub prywatnych w kodzie aplikacji.

Ta metoda korzystania z certyfikatów w kodzie korzysta z funkcji TLS w usłudze App Service, która wymaga, aby aplikacja była w warstwie **Podstawowa** lub nowsza. Jeśli aplikacja znajduje się w warstwie **Bezpłatna** lub **Udostępniona,** możesz [dołączyć plik certyfikatu do repozytorium aplikacji](#load-certificate-from-file).

Po umożliwieniu app service zarządzać certyfikatami TLS/SSL, można zachować certyfikaty i kod aplikacji oddzielnie i chronić poufne dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z tym przewodnikiem:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Dodawanie certyfikatu do aplikacji](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Znajdź odcisk palca

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>, z lewego menu wybierz pozycję **App Services** > **\<app-name>**.

Z lewej strony aplikacji wybierz **pozycję Ustawienia protokołu TLS/SSL**, a następnie wybierz pozycję **Certyfikaty klucza prywatnego (pfx)** lub **Certyfikaty kluczy publicznych (cer).**

Znajdź certyfikat, którego chcesz użyć, i skopiuj odcisk palca.

![Kopiowanie odcisku palca certyfikatu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Udostępnij certyfikat

Aby uzyskać dostęp do certyfikatu w kodzie `WEBSITE_LOAD_CERTIFICATES` aplikacji, dodaj jego odcisk palca do ustawienia aplikacji, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >aplikacji Cloud Shell:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Aby udostępnić wszystkie certyfikaty, `*`ustaw wartość na .

## <a name="load-certificate-in-windows-apps"></a>Ładowanie certyfikatu w aplikacjach systemu Windows

Ustawienie `WEBSITE_LOAD_CERTIFICATES` aplikacji sprawia, że określone certyfikaty są dostępne dla aplikacji hostowanego w systemie Windows w magazynie certyfikatów systemu Windows, a lokalizacja zależy od [warstwy cenowej:](overview-hosting-plans.md)

- **Warstwa izolowana** — w [obszarze Maszyna lokalna\Mój](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Wszystkie inne warstwy — w [obszarze Bieżący użytkownik\Mój](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

W kodzie języka C# dostęp do certyfikatu za pomocą odcisku palca certyfikatu. Poniższy kod ładuje certyfikat z `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`odciskiem palca .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

W kodzie Java uzyskujesz dostęp do certyfikatu ze sklepu "Windows-MY" przy użyciu pola Nazwa wspólna podmiotu (zobacz [Certyfikat klucza publicznego).](https://en.wikipedia.org/wiki/Public_key_certificate) Poniższy kod pokazuje, jak załadować certyfikat klucza prywatnego:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

W przypadku języków, które nie obsługują lub nie oferują niewystarczającej obsługi magazynu certyfikatów systemu Windows, zobacz [Ładowanie certyfikatu z pliku](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Ładowanie certyfikatu w aplikacjach systemu Linux

Ustawienia `WEBSITE_LOAD_CERTIFICATES` aplikacji sprawiają, że określone certyfikaty są dostępne dla aplikacji hostowanych w systemie Linux (w tym niestandardowych aplikacji kontenerowych) jako pliki. Pliki znajdują się w następujących katalogach:

- Certyfikaty `/var/ssl/private` prywatne `.p12` - ( pliki)
- Certyfikaty `/var/ssl/certs` publiczne `.der` - ( pliki)

Nazwy plików certyfikatów są odciskami palców certyfikatu. Poniższy kod języka C# pokazuje, jak załadować certyfikat publiczny w aplikacji systemu Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Aby zobaczyć, jak załadować certyfikat TLS/SSL z pliku w node.js, PHP, Python, Java lub Ruby, zobacz dokumentację odpowiedniego języka lub platformy internetowej.

## <a name="load-certificate-from-file"></a>Ładowanie certyfikatu z pliku

Jeśli chcesz załadować plik certyfikatu, który przesyłasz ręcznie, lepiej przesłać certyfikat za pomocą [FTPS](deploy-ftp.md) zamiast [Git](deploy-local-git.md), na przykład. Należy przechowywać poufne dane, takie jak prywatny certyfikat poza kontrolą źródła.

> [!NOTE]
> ASP.NET i ASP.NET Core w systemie Windows musi uzyskać dostęp do magazynu certyfikatów, nawet jeśli certyfikat jest ładowany z pliku. Aby załadować plik certyfikatu w aplikacji Windows .NET, załaduj bieżący profil użytkownika za pomocą następującego polecenia w <a target="_blank" href="https://shell.azure.com" >aplikacji Cloud Shell:</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Ta metoda korzystania z certyfikatów w kodzie korzysta z funkcji TLS w usłudze App Service, która wymaga, aby aplikacja była w warstwie **Podstawowa** lub nowsza.

Poniższy przykład języka C# ładuje certyfikat publiczny ze ścieżki względnej w aplikacji:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Aby zobaczyć, jak załadować certyfikat TLS/SSL z pliku w node.js, PHP, Python, Java lub Ruby, zobacz dokumentację odpowiedniego języka lub platformy internetowej.

## <a name="more-resources"></a>Więcej zasobów

* [Zabezpiecz niestandardową nazwę DNS za pomocą powiązania TLS/SSL w usłudze Azure App Service](configure-ssl-bindings.md)
* [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Często zadawane pytania: Certyfikaty usługi aplikacji](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
