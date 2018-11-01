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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: eeecf37a6cc7a0f86662f002b6f0efab5ef8c35c
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417467"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Rozwiązywanie problemów aktywacji maszyny wirtualnej Windows Azure

Jeśli masz problemy podczas aktywacji maszyny wirtualnej Windows Azure (VM), który jest tworzony na podstawie niestandardowego obrazu, można użyć informacji podanych w tym dokumencie, aby rozwiązać ten problem. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Omówienie punktów końcowych usługi KMS z usługi Azure na potrzeby aktywacji produktu Windows Azure Virtual Machines
Platforma Azure używa różnych punktów końcowych dla aktywacji usługi KMS w zależności od regionu chmury, w którym znajduje się maszyna wirtualna. Korzystając z tego przewodnika rozwiązywania problemów, należy użyć odpowiednich punktów końcowych usługi KMS, która ma zastosowanie do regionu.

* Regiony w chmurze publicznej platformy Azure: kms.core.windows.net:1688
* Regiony chmur krajowych platformy Azure (Chiny): kms.core.chinacloudapi.cn:1688
* Regiony chmur krajowych platformy Azure (Niemcy): kms.core.cloudapi.de:1688
* Chmury krajowe regionach Administracja USA —: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Objaw

Podczas próby aktywowania maszyny Wirtualnej z systemem Windows Azure, otrzymasz komunikat o błędzie komunikat jest podobny do poniższego przykładu:

**Błąd: 0xC004F074, który LicensingService oprogramowania zgłosił, że nie można aktywować komputera. Można się skontaktować z nie ManagementService kluczami (KMS). Można znaleźć w dzienniku zdarzeń aplikacji, aby uzyskać dodatkowe informacje.**

## <a name="cause"></a>Przyczyna
Ogólnie rzecz biorąc problemy dotyczące aktywacji maszyny Wirtualnej platformy Azure wystąpić, jeśli maszyna wirtualna Windows nie jest skonfigurowany przy użyciu odpowiedni klucz instalacji klienta usługi KMS lub maszyna wirtualna Windows ma problem z połączeniem z usługą Azure usługi zarządzania Kluczami (kms.core.windows.net, port 1668). 

## <a name="solution"></a>Rozwiązanie

>[!NOTE]
>Jeśli używasz sieci VPN lokacja lokacja i wymuszonego tunelowania, zobacz [Azure Użyj niestandardowych tras, aby umożliwić aktywacji usługi KMS przy użyciu wymuszonego tunelowania](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Jeśli używasz usługi ExpressRoute i masz opublikowanych trasy domyślnej, zobacz [maszyny Wirtualnej platformy Azure może się nie powieść aktywacji za pośrednictwem usługi ExpressRoute](http://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Krok 1 Konfigurowanie odpowiedni klucz instalacji klienta usługi KMS (dla systemu Windows Server 2016 i Windows Server 2012 R2)

Dla maszyny Wirtualnej, który jest tworzony na podstawie niestandardowego obrazu systemu Windows Server 2016 lub Windows Server 2012 R2 należy skonfigurować odpowiedni klucz instalacji klienta usługi KMS dla maszyny Wirtualnej.

Ten krok nie ma zastosowania do Windows 2012 lub Windows 2008 R2. Używa ona funkcji automatyzacji Aktywacja maszyny wirtualnej (AVMA), która jest obsługiwana tylko przez system Windows Server 2016 i Windows Server 2012 R2.

1. Uruchom **slmgr.vbs/DLV** w wierszu polecenia z podwyższonym poziomem uprawnień. Sprawdź wartość opisu w danych wyjściowych, a następnie sprawdzić, czy został utworzony z sieci sprzedaży (RETAIL channel) lub z nośnika licencji zbiorczej (VOLUME_KMSCLIENT):
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Jeśli **slmgr.vbs/DLV** przedstawia kanał sprzedaży DETALICZNEJ, uruchom następujące polecenia, aby ustawić [klucz konfiguracji klienta usługi KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) dla wersji systemu Windows Server używana i wymusić, aby ponowić próbę aktywacji: 

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

3. Upewnij się, że maszyna wirtualna jest skonfigurowany do używania z właściwym serwerem usługi KMS z usługi Azure. Aby to zrobić, uruchom następujące polecenie:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    Polecenie powinna zwrócić: Nazwa maszyny usługi zarządzania kluczami pomyślnie ustawiono na kms.core.windows.net:1688.

4. Sprawdź przy użyciu Psping, że masz połączenie z serwerem usługi zarządzania Kluczami. Przejdź do folderu, w którym została rozpakowana pobierania Pstools.zip, a następnie uruchom następujące czynności:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Upewnij się, zostanie wyświetlony w danych wyjściowych w wierszu na sekundę do ostatniego: Wysłane = 4, odebrane = 4, utracone = 0 (0% straty).

  Utracono jest większa od 0 (zero), maszyna wirtualna ma połączenie z serwerem usługi zarządzania Kluczami. W takiej sytuacji Jeśli maszyna wirtualna znajduje się w sieci wirtualnej i jest niestandardowego serwera DNS określona, musisz upewnić się, że serwer DNS jest w stanie rozpoznać kms.core.windows.net. Lub zmienić serwer DNS, który jest rozpoznawany kms.core.windows.net.

  Należy zauważyć, że po usunięciu wszystkich serwerów DNS z sieci wirtualnej maszyny wirtualne używają wewnętrzna usługi DNS platformy Azure. Ta usługa może rozpoznać kms.core.windows.net.
  
Sprawdź także, czy Zapora gościa nie został skonfigurowany w taki sposób, który może zablokować próby aktywacji.

5. Po zweryfikowaniu pomyślne łączności kms.core.windows.net uruchom następujące polecenie w wierszu programu Windows PowerShell z podwyższonym poziomem uprawnień tej. To polecenie próbuje aktywacji wiele razy.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Aktywacja powiodła zwraca informacje, podobny do następującego:

**Aktywowanie Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678)... Produkt został aktywowany pomyślnie.**

## <a name="faq"></a>Często zadawane pytania 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Utworzono systemu Windows Server 2016 w witrynie Azure Marketplace. Należy skonfigurować klucz usługi KMS do aktywacji systemu Windows Server 2016? 
 
Nie. Obraz w witrynie Azure Marketplace ma odpowiedni klucz instalacji klienta usługi KMS, na już skonfigurowane. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Windows activation działa tak samo niezależnie od tego, jeśli maszyna wirtualna używa usługi Azure hybrydowego użycia korzyści (HUB) czy nie? 
 
Tak. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Co się stanie, jeśli upłynie okres aktywacji Windows? 
 
Gdy upłynął okres prolongaty i Windows nie nadal jest aktywny, Windows Server 2008 R2 i nowszych wersjach systemu Windows zostanie wyświetlone dodatkowe powiadomienia dotyczące aktywacji. Czarne pozostaje tapetę pulpitu i Windows Update zainstaluje, zabezpieczeń i tylko aktualizacje krytyczne, ale nie opcjonalne aktualizacje. W sekcji powiadomienia w dolnej części [warunki licencjonowania](http://technet.microsoft.com/library/ff793403.aspx) strony.   

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
