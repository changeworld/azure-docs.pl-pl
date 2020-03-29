---
title: Konfigurowanie certyfikatu szyfrowania w klastrach systemu Linux
description: Dowiedz się, jak skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969028"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Linux
W tym artykule pokazano, jak skonfigurować certyfikat szyfrowania i używać go do szyfrowania wpisów tajnych w klastrach systemu Linux. W przypadku klastrów systemu Windows zobacz [Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Uzyskiwanie certyfikatu szyfrowania danych
Certyfikat szyfrowania danych jest używany wyłącznie do szyfrowania i odszyfrowywania [parametrów][parameters-link] w pliku Settings.xml usługi i [zmiennych środowiskowych][environment-variables-link] w pliku ServiceManifest.xml usługi. Nie jest używany do uwierzytelniania lub podpisywania tekstu szyfrowego. Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Użycie klucza certyfikatu musi zawierać szyfrowanie danych (10) i nie powinno zawierać uwierzytelniania serwera ani uwierzytelniania klienta.

  Na przykład do generowania wymaganego certyfikatu przy użyciu openssl można użyć następujących poleceń:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalowanie certyfikatu w klastrze
Certyfikat musi być zainstalowany w każdym `/var/lib/sfcerts`węźle w klastrze w obszarze . Konto użytkownika, na którym usługa jest uruchomiona (sfuser domyślnie) powinien mieć `/var/lib/sfcerts/TestCert.pem` dostęp do **odczytu** do zainstalowanego certyfikatu (czyli w bieżącym przykładzie).

## <a name="encrypt-secrets"></a>Szyfrowanie wpisów tajnych
Poniższy fragment kodu może służyć do szyfrowania klucza tajnego. Ten fragment kodu tylko szyfruje wartość; **nie** podpisuje tekstu szyfru. **Należy użyć** tego samego certyfikatu szyfrowania, który jest zainstalowany w klastrze do tworzenia szyfrów dla wartości tajnych.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Wynikowy base-64 zakodowane dane wyjściowe ciągu do encrypted.txt zawiera zarówno tajny szyfr, jak również informacje o certyfikacie, który został użyty do zaszyfrowania go. Jego ważność można zweryfikować, odszyfrowując je za pomocą openssl.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [określić zaszyfrowane wpisy tajne w aplikacji.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
