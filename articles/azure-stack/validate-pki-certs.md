---
title: Sprawdź poprawność certyfikatów infrastruktury kluczy publicznych stosu Azure stosu Azure zintegrowanych systemów wdrożenia | Dokumentacja firmy Microsoft
description: Opisuje sposób sprawdzania poprawności certyfikatów PKI stosu Azure stosu Azure zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: b38e3cc45d14645611c0cd804f2bfa66047810f0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Sprawdź poprawność certyfikatów PKI stosu Azure
Narzędzie sprawdzania certyfikatów stosu Azure opisane w tym artykule są dostarczane przez producenta OEM dołączonego pliku deploymentdata.json, aby sprawdzić, czy [wygenerowanych certyfikatów PKI](azure-stack-get-pki-certs.md) nadają się do przed wdrożeniem. Certyfikaty powinny być weryfikowane z wystarczająco dużo czasu na test i uzyskać certyfikaty ponownie w razie potrzeby. 

Narzędzie do sprawdzania certyfikatów (Certchecker) wykonuje następujące testy:

- **Przeczytaj PFX**. Sprawdza, czy prawidłowy plik PFX, prawidłowe hasło i wyświetli ostrzeżenie, jeśli informacje o publicznym nie jest chroniony przez hasło. 
- **Algorytm podpisu**. Sprawdza, czy algorytm podpisu nie jest SHA1 
- **Klucz prywatny**. Sprawdza klucz prywatny jest obecny i został wyeksportowany z atrybutem komputera lokalnego. 
- **Łańcuch certyfikatów**. Sprawdza, czy łańcuch certyfikatów znajduje się w tym niezmieniony certyfikaty z podpisem własnym. 
- **Nazwy DNS**. Sprawdza, czy odpowiednie nazwy DNS dla każdego punktu końcowego zawiera sieci SAN lub w przypadku obsługi występuje symbol wieloznaczny. 
- **Użycie klucza**. Sprawdza użycie klucza zawiera podpis cyfrowy i szyfrowanie klucza i rozszerzone użycie klucza zawiera uwierzytelnianie serwera i uwierzytelnianie klienta. 
- **Rozmiar klucza**. Sprawdza, czy rozmiar klucza to 2048 lub większa 
- **Kolejność łańcucha**. Sprawdza, czy kolejność innych tworzenie łańcucha certyfikatów jest poprawna. 
- **Inne certyfikaty**. Upewnij się, że żadne inne certyfikaty nie zostały opakowane w formacie PFX inne niż liścia odpowiedniego certyfikatu i jego łańcucha. 
- **Brak profilu**. Sprawdza, czy nowy użytkownik mogą ładować danych PFX bez załadowane, profil użytkownika mimicking zachowania kont gMSA podczas obsługi certyfikatu.   

> [!IMPORTANT]
> Plik PFX certyfikatu PKI i hasła powinny być traktowane jako poufne informacje.

## <a name="prerequisites"></a>Wymagania wstępne
System powinna spełniać następujące wymagania wstępne, przed sprawdzaniem poprawności certyfikatów PKI dla wdrożenia usługi Azure stosu:
- CertChecker (w PartnerToolKit w obszarze \utils\certchecker)
- Certyfikaty SSL wyeksportowane po [instrukcje dotyczące przygotowań](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 lub Windows Server 2016

## <a name="perform-certificate-validation"></a>Sprawdzania poprawności certyfikatu
Do przygotowania i sprawdzania poprawności certyfikatów PKI stosu Azure, wykonaj następujące kroki: 

1. Wyodrębnij zawartość pliku <partnerToolkit>\utils\certchecker do nowego katalogu, na przykład **c:\certchecker**.

2. Otwórz program PowerShell jako Administrator i zmień katalog na folder certchecker:

  ```powershell
  cd c:\certchecker
  ```
 
3. Utwórz strukturę katalogów dla certyfikatów, uruchamiając następujące polecenia programu PowerShell:

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Jeśli dostawca tożsamości wdrożenia stosu Azure jest usługą Azure AD, Usuń **usług AD FS** i **wykres** katalogów. 

4. Umieść Twoje certyfikaty w katalogach odpowiednie na przykład utworzone w poprzednim kroku: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - i tak dalej... 

5. Kopiuj **deploymentdata.json** do **c:\certchecker** katalogu.

6. W oknie programu PowerShell, uruchom następujące polecenia: 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. Dane wyjściowe powinny zawierać OK dla wszystkich certyfikatów i wszystkie atrybuty zaznaczone: 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Znane problemy 
**Objaw**: Certchecker kończy się przedwcześnie i pojawi się następujący błąd: 
> Niepowodzenie

> Szczegóły: Nie można uruchomić to polecenie z powodu błędu: Nazwa katalogu jest nieprawidłowa. 

**Przyczyna**: na przykład uruchamiając certchecker.ps1 z folderem restrykcyjne, c:\temp lub % temp % 

**Rozdzielczość**: Przenieś do nowego katalogu, na przykład C:\CertChecker narzędzia certchecker 


**Objaw**: Certchecker wyświetla ostrzeżenie dotyczące korzystania z wersji Pre-1803 (jak pokazano w przykładzie powyżej w kroku 7):

> [!WARNING]
> Struktura 1803 wstępnie świadectwa. Folder struktury 1803 stosu Azure i brzmi: ACSTable ACSBlob, ACSQueue, zamiast folderu ACS. Więcej informacji znajduje się w dokumentacji wdrożeniowej.

**Przyczyna**: wykryto CertChecker korzystanie z pojedynczym folderem ACS jest poprawny w przypadku wdrożeń przed 1803. Dla wersji stosu Azure 1803 i powyżej wdrożeń struktury folderów zmiany ACSBlob ACSTable, ACSQueue. Certchecker już ma zostać zaktualizowane do obsługi tej funkcji.

**Rozdzielczość**: Jeśli wdrażane 1802, nie jest wymagana żadna akcja. Jeśli wdrażane 1803 powyżej, Zamień ACS ACSTable, ACSQueue, ACSBlob i skopiuj certyfikaty usług ACS do tych folderów.

**Objaw**: testy są pomijane.

**Przyczyna**: CertChecker pomija niektórych testów, jeśli zależność nie zostały spełnione:
- Inne certyfikaty są pomijane, jeśli łańcuch certyfikatów nie powiodło się.
- Brak profilu jest pomijane, jeśli:
  - Zasady zabezpieczeń poprzez ograniczenie możliwości utworzenie tymczasowego użytkownika i uruchom program powershell jako ten użytkownik nie istnieje.
  - Klucz prywatny sprawdzenie nie powiedzie się.

**Rozdzielczość**: postępuj zgodnie ze wskazówkami narzędzia w sekcji szczegółów w obszarze każdego certyfikatu zestaw testów.


## <a name="prepare-deployment-script-certificates"></a>Przygotowywanie wdrożenia certyfikatów skryptu 
Jako ostatni krok wszystkie certyfikaty, które zostały przygotowane muszą być umieszczone w odpowiednich katalogów na hoście wdrożenia. Na hoście wdrożenia Utwórz folder o nazwie. Certyfikaty ** i miejsce wyeksportowanego certyfikatu pliki w odpowiednie podfoldery określone w [wymagane certyfikaty](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) sekcji:

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> Certyfikaty oznaczone gwiazdką * są potrzebne tylko wtedy, gdy usługi AD FS jest używana jako magazynu tożsamości.


## <a name="next-steps"></a>Kolejne kroki
[Integracja tożsamości centrum danych](azure-stack-integrate-identity.md)