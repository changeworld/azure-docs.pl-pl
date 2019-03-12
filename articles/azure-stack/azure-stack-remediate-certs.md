---
title: Korygowanie problemów z certyfikatami dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Użyj sprawdzania gotowości stosu usługi Azure, aby przejrzeć i Korygowanie problemów z certyfikatami.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 009eb56621f7cd395c3d2eefb29b9fa624af888b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778199"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Rozwiązania typowych problemów dotyczących certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack

Informacje w tym artykule ułatwiają zrozumienie i rozwiązywanie typowych problemów dotyczących certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack. Można wykryć problemy, korzystając z narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack [weryfikują certyfikaty infrastruktury kluczy publicznych do usługi Azure Stack](azure-stack-validate-pki-certs.md). Narzędzie sprawdza, sprawdź, czy certyfikaty wymagań infrastruktury kluczy publicznych wdrożenia usługi Azure Stack i Azure Stack klucz tajny obrotu i loguje się wyniki [pliku report.json](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Szyfrowanie PFX

**Błąd** -PFX szyfrowania nie jest algorytm SHA1 TripleDES.

**Korygowanie** -wyeksportować plik PFX pliki z **TripleDES SHA1** szyfrowania. Jest to flaga domyślna dla wszystkich klientów systemu Windows 10 podczas eksportowania z certyfikatów w przystawce lub przy użyciu `Export-PFXCertificate`.

## <a name="read-pfx"></a>Przeczytaj PFX

**Ostrzeżenie** -hasło chroni tylko informacje prywatne w certyfikacie.  

**Korygowanie** — pliki wyeksportować plik PFX z opcjonalne ustawienie **Włącz prywatność certyfikatu**.  

**Błąd** — nieprawidłowy plik PFX.  

**Korygowanie** -ponownie wyeksportować certyfikat wykonując kroki w [przygotowywanie usługi Azure Stack infrastruktury kluczy publicznych certyfikatów dla wdrożenia](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algorytm podpisu

**Błąd** — algorytm podpisu jest algorytm SHA1.

**Korygowanie** — wykonaj kroki w usługi Azure Stack certyfikaty podpisywania generowania żądania ponownego generowania certyfikatu podpisywania żądania (CSR) przy użyciu algorytmu podpisu w SHA256. Następnie prześlij plik CSR, urząd certyfikacji do ponownego wystawienia dla certyfikatu.

## <a name="private-key"></a>Klucz prywatny

**Błąd** -klucz prywatny nie istnieje lub nie zawiera atrybut maszyny lokalnej.  

**Korygowanie** — z komputera, który wygenerował plik CSR, ponownie wyeksportować certyfikat wykonując kroki w [przygotowywanie usługi Azure Stack infrastruktury kluczy publicznych certyfikatów dla wdrożenia](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Te kroki obejmują eksportowanie z magazynu certyfikatów komputera lokalnego.

## <a name="certificate-chain"></a>Łańcuch certyfikatów

**Błąd** — łańcuch certyfikatów nie została ukończona.  

**Korygowanie** — certyfikaty powinny zawierać łańcucha certyfikatów ukończone. Ponownie Eksportuj certyfikat wykonując kroki w [przygotowywanie usługi Azure Stack infrastruktury kluczy publicznych certyfikatów dla wdrożenia](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) i wybierz opcję **, jeśli jest to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji.**

## <a name="dns-names"></a>Nazwy DNS

**Błąd** -DNSNameList certyfikat nie zawiera nazwę punktu końcowego usługi Azure Stack lub nieprawidłowy symboli wieloznacznych. Symbol wieloznaczny pasuje są prawidłowe tylko dla przestrzeni nazw najdalej po lewej stronie nazwy DNS. Na przykład _*. region.domain.com_ jest prawidłowy tylko dla *portal.region.domain.com*, a nie _*. table.region.domain.com_.

**Korygowanie** — wykonaj kroki w usługi Azure Stack certyfikaty podpisywania generowania żądania ponownie wygenerować żądania CSR poprawną nazwą DNS do obsługi punktów końcowych usługi Azure Stack. Prześlij ponownie plik CSR, urząd certyfikacji, a następnie wykonaj kroki opisane w [przygotowywanie usługi Azure Stack infrastruktury kluczy publicznych certyfikatów dla wdrożenia](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) Aby wyeksportować certyfikat z komputera, który wygenerował plik CSR.  

## <a name="key-usage"></a>Użycie klucza

**Błąd** — użycie klucza nie ma podpisu cyfrowego lub szyfrowanie klucza lub brak rozszerzonego użycia klucza uwierzytelniania serwera czy uwierzytelnianie klienta.  

**Korygowanie** — Użyj kroków w [usługi Azure Stack certyfikaty podpisywania generowania żądania](azure-stack-get-pki-certs.md) ponownie wygenerować plik CSR przy użyciu prawidłowe atrybuty użycie klucza. Prześlij ponownie plik CSR, urząd certyfikacji i upewnij się, że szablon certyfikatu nie jest zastąpi użycia klucza w żądaniu.

## <a name="key-size"></a>Rozmiar klucza

**Błąd** — rozmiar klucza jest mniejszy niż 2048.

**Korygowanie** — Użyj kroków w [usługi Azure Stack certyfikaty podpisywania generowania żądania](azure-stack-get-pki-certs.md) można ponownie wygenerować żądania CSR z odpowiednią długość klucza (2048), a następnie prześlij ponownie plik CSR, urząd certyfikacji.

## <a name="chain-order"></a>Kolejnością łańcucha

**Błąd** — kolejność łańcuch certyfikatów jest nieprawidłowy.  

**Korygowanie** -ponownie wyeksportować certyfikat wykonując kroki w [przygotowywanie usługi Azure Stack infrastruktury kluczy publicznych certyfikatów dla wdrożenia](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) i wybierz opcję **, jeśli jest to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji.** Upewnij się, że tylko certyfikat liścia został wybrany do eksportu.

## <a name="other-certificates"></a>Inne certyfikaty

**Błąd** -pakiet PFX zawiera certyfikaty, które nie są certyfikatu liścia lub część łańcucha certyfikatów.  

**Korygowanie** -ponownie wyeksportować certyfikat wykonując kroki w [przygotowywanie usługi Azure Stack infrastruktury kluczy publicznych certyfikatów dla wdrożenia](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)i wybierz opcję **, jeśli jest to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji.** Upewnij się, że tylko certyfikat liścia został wybrany do eksportu.

## <a name="fix-common-packaging-issues"></a>Rozwiązywanie typowych problemów dotyczących tworzenia pakietów

**AzsReadinessChecker** zawiera polecenia cmdlet pomocnika o nazwie `Repair-AzsPfxCertificate`, który może importować i następnie eksportu PFX plik Rozwiązywanie typowych problemów dotyczących tworzenia pakietów, w tym:

- *Szyfrowanie PFX* nie jest algorytm SHA1 TripleDES.
- *Klucz prywatny* brakuje lokalnego atrybutu maszyny.
- *Łańcuch certyfikatów* jest niekompletne lub jest on nieprawidłowy. Komputer lokalny musi zawierać łańcucha certyfikatów, jeśli pakiet PFX nie jest.
- *Inne certyfikaty*

`Repair-AzsPfxCertificate` nie będzie mogła pomóc, jeśli potrzebujesz wygenerować nowy plik CSR i ponownego wystawienia certyfikatu.

### <a name="prerequisites"></a>Wymagania wstępne

Miejsce na komputerze, na którym działa narzędzie musi mieć następujące wymagania wstępne:

- System Windows 10 lub Windows Server 2016 z łącznością z Internetem.
- Program PowerShell 5.1 lub nowszej. Aby sprawdzić swoją wersję, uruchom następujące polecenie cmdlet programu PowerShell, a następnie przejrzyj *głównych* i *pomocnicza* wersji:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Konfigurowanie [programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).
- Pobierz najnowszą wersję [Microsoft Azure Stack gotowości Checker](https://aka.ms/AzsReadinessChecker) narzędzia.

### <a name="import-and-export-an-existing-pfx-file"></a>Importowanie i eksportowanie istniejącego pliku PFX

1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz polecenia PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. W wierszu polecenia programu PowerShell uruchom następujące polecenie cmdlet, aby ustawić hasło PFX. Zastąp `PFXpassword` przy użyciu rzeczywistego hasła:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby wyeksportować nowy plik PFX:

   - Aby uzyskać `-PfxPath`, określ ścieżkę do pliku PFX, w którym pracujesz. W poniższym przykładzie jest ścieżka `.\certificates\ssl.pfx`.
   - Aby uzyskać `-ExportPFXPath`, określ lokalizację i nazwę pliku PFX do eksportu. W poniższym przykładzie jest ścieżka `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Gdy narzędzie ukończy, przejrzyj dane wyjściowe w celu osiągnięcia sukcesu:

   ```powershell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Kolejne kroki

- [Kliknij tutaj, aby dowiedzieć się więcej na temat zabezpieczeń usługi Azure Stack](azure-stack-rotate-secrets.md).
