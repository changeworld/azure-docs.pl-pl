---
title: Rozwiązywanie problemów z Windows problemy dotyczące aktywacji maszyny wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów dotyczących aktywacji maszyny wirtualnej Windows na platformie Azure zapewnia kroki rozwiązywania problemów
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 46f52cb0478b47f8f6b45356815bc4c74e7cc800
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724120"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Rozwiązywanie problemów aktywacji maszyny wirtualnej Windows Azure

Jeśli masz problemy podczas aktywacji maszyny wirtualnej Windows Azure (VM), który jest tworzony na podstawie niestandardowego obrazu, można użyć informacji podanych w tym dokumencie, aby rozwiązać ten problem. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Omówienie punktów końcowych usługi KMS z usługi Azure na potrzeby aktywacji produktu Windows Azure Virtual Machines

Platforma Azure używa różnych punktów końcowych dla aktywacji usługi KMS w zależności od regionu chmury, w którym znajduje się maszyna wirtualna. Korzystając z tego przewodnika rozwiązywania problemów, należy użyć odpowiednich punktów końcowych usługi KMS, która ma zastosowanie do regionu.

* Regiony w chmurze publicznej platformy Azure: kms.core.windows.net:1688
* Azure China 21Vianet national cloud regions: kms.core.chinacloudapi.cn:1688
* Regiony chmur krajowych platformy Azure (Niemcy): kms.core.cloudapi.de:1688
* Chmury krajowe regionach Administracja USA —: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Objaw

Podczas próby aktywowania maszyny Wirtualnej z systemem Windows Azure, otrzymasz komunikat o błędzie komunikat jest podobny do poniższego przykładu:

**Błąd: 0xC004F074, który LicensingService oprogramowania zgłosił, że nie można aktywować komputera. Można się skontaktować z nie ManagementService kluczami (KMS). Można znaleźć w dzienniku zdarzeń aplikacji, aby uzyskać dodatkowe informacje.**

## <a name="cause"></a>Przyczyna

Ogólnie rzecz biorąc problemy dotyczące aktywacji maszyny Wirtualnej platformy Azure wystąpić, jeśli maszyna wirtualna Windows nie jest skonfigurowany przy użyciu odpowiedni klucz instalacji klienta usługi KMS lub maszyna wirtualna Windows ma problem z połączeniem z usługą Azure usługi zarządzania Kluczami (kms.core.windows.net, będzie używać portu 1688). 

## <a name="solution"></a>Rozwiązanie

>[!NOTE]
>Jeśli używasz sieci VPN lokacja lokacja i wymuszonego tunelowania, zobacz [Azure Użyj niestandardowych tras, aby umożliwić aktywacji usługi KMS przy użyciu wymuszonego tunelowania](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Jeśli używasz usługi ExpressRoute i masz opublikowanych trasy domyślnej, zobacz [maszyny Wirtualnej platformy Azure może się nie powieść aktywacji za pośrednictwem usługi ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Krok 1 Skonfiguruj odpowiedni klucz instalacji klienta usługi KMS

Dla maszyny Wirtualnej, który jest tworzony na podstawie niestandardowego obrazu należy skonfigurować odpowiedni klucz instalacji klienta usługi KMS dla maszyny Wirtualnej.

1. Uruchom **slmgr.vbs/DLV** w wierszu polecenia z podwyższonym poziomem uprawnień. Sprawdź wartość opisu w danych wyjściowych, a następnie sprawdzić, czy został utworzony z sieci sprzedaży (RETAIL channel) lub z nośnika licencji zbiorczej (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Jeśli po wpisaniu **slmgr.vbs /dlv** zostanie wyświetlony kanał RETAIL, uruchom następujące polecenia, aby ustawić [klucz konfiguracji klienta usługi KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) dla używanej wersji systemu Windows Server i wymusić ponowienie próby aktywacji: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Na przykład dla systemu Windows Server 2016 Datacenter, możesz uruchomić następujące polecenie:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2, sprawdź łączność między maszyny Wirtualnej i usługi zarządzania Kluczami w usłudze Azure service

1. Pobierz i Wyodrębnij [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) narzędzie do folderu lokalnego na maszynie Wirtualnej, która nie uaktywni się. 

2. Przejdź do ekranu startowego, wyszukiwanie w programie Windows PowerShell, kliknij prawym przyciskiem myszy środowiska Windows PowerShell i wybrać polecenie Uruchom jako administrator.

3. Upewnij się, że maszyna wirtualna jest skonfigurowana do używania właściwego serwera usługi Azure KMS. Aby to zrobić, uruchom następujące polecenie:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Polecenie powinno zwrócić następujący wynik: Pomyślnie ustawiono kms.core.windows.net:1688 nazwę maszyny usługi zarządzania kluczami.

4. Sprawdź przy użyciu Psping, że masz połączenie z serwerem usługi zarządzania Kluczami. Przejdź do folderu, w którym wyodrębniono pobrany plik Pstools.zip, a następnie uruchom:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   W drugim od końca wierszu danych wyjściowych upewnij się, że widzisz: Wysłane = 4, odebrane = 4, utracone = 0 (0% straty).

   Utracono jest większa od 0 (zero), maszyna wirtualna ma połączenie z serwerem usługi zarządzania Kluczami. W takiej sytuacji Jeśli maszyna wirtualna znajduje się w sieci wirtualnej i jest niestandardowego serwera DNS określona, musisz upewnić się, że serwer DNS jest w stanie rozpoznać kms.core.windows.net. Lub zmienić serwer DNS, który jest rozpoznawany kms.core.windows.net.

   Należy zauważyć, że po usunięciu wszystkich serwerów DNS z sieci wirtualnej maszyny wirtualne używają wewnętrzna usługi DNS platformy Azure. Ta usługa może rozpoznać kms.core.windows.net.
  
    Upewnij się, że wychodzącego ruchu sieciowego do punktu końcowego usługi KMS, za pomocą portu 1688 nie jest blokowany przez zaporę na maszynie wirtualnej również.

5. Po zweryfikowaniu pomyślne łączności kms.core.windows.net uruchom następujące polecenie w wierszu programu Windows PowerShell z podwyższonym poziomem uprawnień tej. To polecenie podejmuje próbę aktywacji wiele razy.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Pomyślna aktywacja zwraca informacje podobne do następujących:
    
    **Aktywowanie Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678)...  Produkt został aktywowany pomyślnie.**

## <a name="faq"></a>Często zadawane pytania 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Utworzono systemu Windows Server 2016 w witrynie Azure Marketplace. Należy skonfigurować klucz usługi KMS do aktywacji systemu Windows Server 2016? 

 
Nie. Obraz w witrynie Azure Marketplace ma odpowiedni klucz instalacji klienta usługi KMS, na już skonfigurowane. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Windows activation działa tak samo niezależnie od tego, jeśli maszyna wirtualna używa usługi Azure hybrydowego użycia korzyści (HUB) czy nie? 

 
Tak. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Co się stanie, jeśli upłynie okres aktywacji Windows? 

 
Gdy upłynął okres prolongaty i Windows nie nadal jest aktywny, Windows Server 2008 R2 i nowszych wersjach systemu Windows zostanie wyświetlone dodatkowe powiadomienia dotyczące aktywacji. Czarne pozostaje tapetę pulpitu i Windows Update zainstaluje, zabezpieczeń i tylko aktualizacje krytyczne, ale nie opcjonalne aktualizacje. W sekcji powiadomienia w dolnej części [warunki licencjonowania](https://technet.microsoft.com/library/ff793403.aspx) strony.   

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
