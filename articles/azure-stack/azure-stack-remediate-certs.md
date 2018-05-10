---
title: Korygowanie problemów z certyfikatami stosu Azure | Dokumentacja firmy Microsoft
description: Narzędzie sprawdzania gotowości stosu Azure umożliwia przeglądanie i Korygowanie problemów z certyfikatami.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Rozwiązania typowych problemów z certyfikatów PKI stosu Azure
Informacje przedstawione w tym artykule ułatwia zrozumienie i rozwiązywania typowych problemów dla certyfikatów PKI stosu Azure. Można wykryć problemy, gdy narzędzie sprawdzania gotowości stosu Azure do [sprawdzania poprawności certyfikatów PKI stosu Azure](azure-stack-validate-pki-certs.md). Narzędzie sprawdza, sprawdź, czy certyfikaty spełniają wymagania infrastruktury kluczy publicznych wdrożenia stosu Azure i obrotu klucz tajny stosu Azure i rejestruje wyniki [pliku report.json](azure-stack-validation-report.md).  

## <a name="read-pfx"></a>Przeczytaj PFX
**Ostrzeżenie** — hasło chroni tylko prywatne informacje w certyfikacie.  
**Korygowanie** — zaleca się eksportować pliki PFX z opcjonalne ustawienie **Włącz prywatność certyfikatu**.  

**Błąd** — nieprawidłowy plik PFX.  
**Korygowanie** -ponownie wyeksportuj certyfikat, wykonując kroki w [przygotowanie Azure stosu certyfikatówpki wdrożenia](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algorytm podpisu
**Błąd** — algorytm podpisywania jest algorytm SHA1.    
**Korygowanie** — wykonaj czynności w stos Azure certyfikaty podpisywania generowania żądania można ponownie wygenerować żądania (Podpisania certyfikatu) z podpisu algorytm SHA256. Następnie prześlij ponownie plik CSR, aby ponowne wystawienie certyfikatów urzędu certyfikacji.

## <a name="private-key"></a>Klucz prywatny
**Błąd** — klucz prywatny nie istnieje lub nie zawiera atrybut komputera lokalnego.  
**Korygowanie** — z komputera, który wygenerował CSR, ponownie wyeksportuj certyfikat przy użyciu certyfikatów przygotowanie PKI stosu Azure dla wdrożenia. Kroki te obejmują eksportowanie z magazynu certyfikatów komputera lokalnego.

## <a name="certificate-chain"></a>Łańcuch certyfikatów
**Błąd** — łańcuch certyfikatów jest niekompletny.  
**Korygowanie** -certyfikaty powinny zawierać łańcuch certyfikatów ukończone.  Ponownie wyeksportuj certyfikat, wykonując kroki w [przygotowanie Azure stosu certyfikatówpki wdrożenia](azure-stack-prepare-pki-certs.md) i wybierz opcję **Jeśli to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji.**

## <a name="dns-names"></a>Nazwy DNS
**Błąd** -DNSNameList certyfikatu nie zawiera nazwę punktu końcowego usługi Azure stosu lub nieprawidłowy symboli wieloznacznych.  Symbol wieloznaczny dopasowań są prawidłowe tylko dla przestrzeni nazw lewej nazwy DNS. Na przykład _*. region.domain.com_ jest prawidłowa tylko dla *portal.region.domain.com*, a nie _*. table.region.domain.com_.  
**Korygowanie** -wykonaj kroki w stos Azure certyfikaty podpisywania generowania żądania można ponownie wygenerować CSR o poprawne nazwy DNS do obsługi stosu Azure punktów końcowych. Prześlij ponownie plik CSR, aby urząd certyfikacji, a następnie postępuj zgodnie z instrukcjami [przygotowanie Azure stosu certyfikatówpki wdrożenia](azure-stack-prepare-pki-certs.md) można wyeksportować certyfikat z komputera, który wygenerował obsługi klienta.  

## <a name="key-usage"></a>Użycie klucza
**Błąd** — użycie klucza nie ma podpisu cyfrowego lub brak szyfrowanie klucza, orEnhanced użycie klucza uwierzytelniania serwera lub uwierzytelniania klienta.  
**Korygowanie** -wykonaj kroki w [stosu Azure certyfikaty podpisywania generowania żądania](azure-stack-get-pki-certs.md) można ponownie wygenerować CSR z prawidłowe atrybuty użycie klucza.  Prześlij ponownie plik CSR, aby urząd certyfikacji i upewnij się, że szablon certyfikatu nie jest zastępowanie użycie klucza w żądaniu.

## <a name="key-size"></a>Rozmiar klucza
**Błąd** — rozmiar klucza jest mniejszy niż 2048    
**Korygowanie** — wykonaj czynności w [stosu Azure certyfikaty podpisywania generowania żądania](azure-stack-get-pki-certs.md) ponownie wygenerować CSR z poprawną długość klucza (2048), a następnie prześlij ponownie plik CSR, aby urząd certyfikacji.

## <a name="chain-order"></a>Kolejnością łańcucha
**Błąd** -kolejność łańcucha certyfikatów jest niepoprawna.  
**Korygowanie** -ponownie wyeksportuj certyfikat, wykonując kroki w [przygotowanie Azure stosu certyfikatówpki wdrożenia](azure-stack-prepare-pki-certs.md) i wybierz opcję **Jeśli to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji.** Upewnij się, że tylko certyfikatu liścia został wybrany do eksportu. 

## <a name="other-certificates"></a>Innych certyfikatów
**Błąd** — pakiet PFX zawiera certyfikaty, które nie są certyfikatu liścia lub częścią łańcucha certyfikatów.  
**Korygowanie** -ponownie wyeksportuj certyfikat, wykonując kroki w [przygotowanie Azure stosu certyfikatówpki wdrożenia](azure-stack-prepare-pki-certs.md)i wybierz opcję **Jeśli to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji.** Upewnij się, że tylko certyfikatu liścia został wybrany do eksportu.

## <a name="fix-common-packaging-issues"></a>Rozwiązać typowe problemy pakowania
AzsReadinessChecker mogą być importowane i następnie wyeksportować plik PFX, aby rozwiązać typowe problemy tworzenia pakietów, w tym: 
 - *Klucz prywatny* brakuje atrybutu komputera lokalnego.
 - *Łańcuch certyfikatów* są niekompletne lub nieprawidłowe. (Komputer lokalny musi zawierać łańcucha certyfikatów, jeśli nie ma pakietu PFX.) 
 - *Inne certyfikaty*.
Jednak AzsReadinessChecker nie może pomóc, jeśli chcesz wygenerować nowy CSR, a następnie wydaj ponownie certyfikatu. 

### <a name="prerequisites"></a>Wymagania wstępne
Miejsce na komputerze, w którym działa narzędzie musi mieć następujące wymagania wstępne: 
 - Windows 10 lub Windows Server 2016 z połączeniem internetowym.
 - PowerShell 5.1 lub nowszy. Aby sprawdzić swoją wersję, uruchom następujące polecenia programu PowerShell, a następnie przejrzyj *głównych* wersji i *pomocnicza* wersji.

   > `$PSVersionTable.PSVersion`
 - Skonfiguruj [programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md). 
 - Pobierz najnowszą wersję [modułu sprawdzania gotowości pakietu Microsoft Azure stosu](https://aka.ms/AzsReadinessChecker) narzędzia.

### <a name="import-and-export-an-existing-pfx-file"></a>Importowanie i eksportowanie istniejący plik PFX
1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz programu PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby ustawić hasła do pliku PFX. Zastąp *PFXpassword* z rzeczywistego hasła. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby wyeksportować nowy plik PFX.
   - Dla *- PfxPath*, określ ścieżkę do pracy z pliku PFX.  W poniższym przykładzie jest ścieżka *.\certificates\ssl.pfx*.
   - Aby uzyskać *- ExportPFXPath*, określ lokalizację i nazwę pliku PFX eksportu.  W poniższym przykładzie jest ścieżka *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Gdy narzędzie ukończy, przejrzyj dane wyjściowe w przypadku powodzenia: ![wyników](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o zabezpieczeniach stosu Azure](azure-stack-rotate-secrets.md)