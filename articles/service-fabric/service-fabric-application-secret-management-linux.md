---
title: Konfigurowanie certyfikatu szyfrowania i szyfrowanie kluczy tajnych w klastrach systemu Linux usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować certyfikat szyfrowania do szyfrowania kluczy tajnych w klastrach systemu Linux.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126991"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie kluczy tajnych w klastrach systemu Linux
W tym artykule pokazano, jak skonfigurować certyfikat szyfrowania i użyć go do szyfrowania kluczy tajnych w klastrach systemu Linux. W przypadku klastrów Windows zobacz [Ustaw certyfikat szyfrowania w górę i szyfrowanie kluczy tajnych w klastrach Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Uzyskaj certyfikat Szyfrowanie danych
Certyfikat Szyfrowanie danych jest używane wyłącznie do szyfrowania i odszyfrowywania [parametry] [ parameters-link] w Settings.xml usługi i [zmienne środowiskowe] [ environment-variables-link] w ServiceManifest.xml usługi. Nie jest używany do uwierzytelniania lub podpisywania z zaszyfrowanego tekstu. Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Użycia klucza certyfikatu musi zawierać szyfrowanie danych (10), a nie może zawierać uwierzytelnianie serwera i uwierzytelnianie klienta.

  Na przykład poniższe polecenia może służyć do generowania wymaganego certyfikatu, przy użyciu biblioteki OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Zainstaluj certyfikat w klastrze
Certyfikat musi być zainstalowany na każdym węźle klastra w obszarze `/var/lib/sfcerts`. Konto użytkownika, pod którym jest uruchomiona (sfuser domyślnie) **powinien mieć dostęp do odczytu** zainstalowanego certyfikatu (oznacza to, `/var/lib/sfcerts/TestCert.pem` w bieżącym przykładzie).

## <a name="encrypt-secrets"></a>Szyfrowanie kluczy tajnych
Poniższy fragment kodu może służyć do szyfrowania klucza tajnego. Ten fragment kodu szyfruje wartości; robi **nie** zarejestrować zaszyfrowanego tekstu. **Należy użyć** jest zainstalowany w klastrze, aby wygenerować tekstu szyfrowanego wpisu tajnego wartości tego samego certyfikatu szyfrowania.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Ciąg zakodowany base-64 dane wyjściowe do encrypted.txt zawiera zarówno tekstu szyfrowanego wpisu tajnego oraz informacje dotyczące certyfikatu, który został użyty do zaszyfrowania. Aby sprawdzić jego ważności, odszyfrowuje go z protokołem OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [Określ zaszyfrowane klucze tajne w aplikacji.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
