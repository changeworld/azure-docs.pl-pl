---
title: Rozwiązywanie problemów aktywacji maszyny wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera opis kroków Rozwiązywanie problemów dotyczących ustalania problemy dotyczące aktywacji maszyny wirtualnej systemu Windows na platformie Azure
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
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 11e90a79f45e54f3842d103d290c17254d0e75fc
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071497"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Rozwiązywanie problemów aktywacji maszyny wirtualnej systemu Windows Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Jeśli masz problemy podczas aktywacji maszyny wirtualnej systemu Windows Azure (VM), która jest tworzona na podstawie obrazu niestandardowego, można użyć informacje zawarte w tym dokumencie, aby rozwiązać ten problem. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Punkty końcowe usługi KMS Azure Opis aktywacji produktu Windows maszyn wirtualnych Azure
Platforma Azure korzysta różnych punktów końcowych do aktywacji usługi KMS w zależności od regionu chmury, gdzie znajduje się maszyna wirtualna. Podczas korzystania z tego przewodnika dotyczące rozwiązywania problemów, należy użyć odpowiedniego punktu końcowego usługi KMS, który ma zastosowanie do tego regionu.

* Regiony w chmurze publicznej systemu Azure: kms.core.windows.net:1688
* Regiony platformy Azure national cloud Chin: kms.core.chinacloudapi.cn:1688
* Regiony platformy Azure national cloud Niemcy: kms.core.cloudapi.de:1688
* Regiony platformy Azure national cloud nam wersji dla instytucji rządowych: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Objaw

Podczas próby aktywowania maszyny Wirtualnej systemu Windows Azure, komunikat o błędzie komunikat podobny do poniższego polecenia:

**Błąd: 0xC004F074, który LicensingService oprogramowania zgłosiła, że nie można aktywować komputera. Można się skontaktować z nie ManagementService kluczami (KMS). Sprawdź dziennik zdarzeń aplikacji, aby uzyskać dodatkowe informacje.**

## <a name="cause"></a>Przyczyna
Ogólnie rzecz biorąc problemy dotyczące aktywacji maszyny Wirtualnej Azure wystąpić, jeśli nie skonfigurowano maszyny Wirtualnej systemu Windows przy użyciu odpowiedni klucz instalacji klienta usługi KMS lub maszyny Wirtualnej systemu Windows ma problem z połączeniem z usługą Azure usługi zarządzania Kluczami (kms.core.windows.net, port 1668). 

## <a name="solution"></a>Rozwiązanie

>[!NOTE]
>Jeśli używasz sieci VPN lokacja lokacja i wymuszone tunelowanie, zobacz [tras niestandardowych Azure używana do włączenia aktywacji usługi KMS z wymuszone tunelowanie](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Jeśli używasz usługi ExpressRoute i konieczne jest opublikowane trasy domyślnej, zobacz [maszyny Wirtualnej platformy Azure może zakończyć się niepowodzeniem do aktywowania przez ExpressRoute](https://blogs.technet.microsoft.com/jpaztech/2016/05/16/azure-vm-may-fail-to-activate-over-expressroute/).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Krok 1: Konfigurowanie odpowiedni klucz instalacji klienta usługi KMS (dla systemu Windows Server 2016 i Windows Server 2012 R2)

Dla maszyny Wirtualnej jest tworzona na podstawie niestandardowego obrazu systemu Windows Server 2016 lub Windows Server 2012 R2 należy skonfigurować odpowiedni klucz instalacji klienta usługi KMS dla maszyny Wirtualnej.

Ten krok nie ma zastosowania do systemu Windows 2012 lub Windows 2008 R2. Używa funkcji automatyzacji Aktywacja maszyny wirtualnej (AVMA), która jest obsługiwana tylko przez system Windows Server 2016 i Windows Server 2012 R2.

1. Uruchom **slmgr.vbs/DLV** w wierszu polecenia z podwyższonym poziomem uprawnień. Sprawdź wartość opisu w danych wyjściowych, a następnie określ, czy został utworzony z detaliczne (kanał sprzedaży DETALICZNEJ) lub z nośnika licencji zbiorczej (VOLUME_KMSCLIENT):
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Jeśli **slmgr.vbs/DLV** przedstawia kanał sprzedaży DETALICZNEJ, uruchom następujące polecenia, aby ustawić [klucz instalacji klienta usługi KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) dla wersji systemu Windows Server używana i wymusić, aby ponowić próbę aktywacji: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Na przykład dla systemu Windows Server 2016 centrum danych, uruchom następujące polecenie:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2 Sprawdź łączność między usługę maszyny Wirtualnej i Azure usługi zarządzania Kluczami

1. Pobierać i wyodrębniać [narzędzia Psping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) narzędzia do folderu lokalnego na maszynie wirtualnej, która nie uaktywni się. 

2. Przejdź do menu Start, wyszukiwania w programie Windows PowerShell, kliknij prawym przyciskiem myszy środowiska Windows PowerShell i wybierz polecenie Uruchom jako administrator.

3. Upewnij się, że maszyna wirtualna jest skonfigurowany do korzystania z właściwym serwerem usługi KMS Azure. Aby to zrobić, uruchom następujące polecenie:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    Polecenie powinien zwrócić: Nazwa komputera usługi zarządzania kluczami ustawioną kms.core.windows.net:1688 pomyślnie.

4. Sprawdź przy użyciu narzędzia Psping, że masz połączenie z serwerem usługi KMS. Przejdź do folderu, w którym została rozpakowana pobierania Pstools.zip, a następnie uruchom następujące czynności:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Upewnij się, że jest wyświetlany w sekundy do ostatniego wiersza danych wyjściowych,: Wysłane = 4, odebrane = 4, utracone = 0 (0% straty).

  Utracone jest większa niż 0 (zero), maszyna wirtualna nie ma łączności z serwerem usługi KMS. W takiej sytuacji Jeśli maszyna wirtualna jest w sieci wirtualnej, a ma niestandardowy serwer DNS określona, należy się upewnić, że serwer DNS jest w stanie rozpoznać kms.core.windows.net. Można również zmienić serwer DNS, który jest rozpoznawany kms.core.windows.net.

  Należy zauważyć, że po usunięciu wszystkich serwerów DNS z sieci wirtualnej maszyn wirtualnych użyć wewnętrznego usługa DNS platformy Azure. Ta usługa może zostać rozwiązany kms.core.windows.net.
  
Sprawdź również, czy zapory gościa nie został skonfigurowany w taki sposób, który umożliwia zablokowanie prób aktywacji.

5. Po zweryfikowaniu pomyślne łączności kms.core.windows.net uruchom następujące polecenie w tym wierszu środowiska Windows PowerShell z podwyższonym poziomem uprawnień. To polecenie podejmuje aktywacji wiele razy.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Pomyślnej aktywacji zwraca informacje, które jest podobny do następującego:

**Aktywowanie Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678)... Produkt aktywowany pomyślnie.**

## <a name="faq"></a>Często zadawane pytania 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Windows Server 2016 utworzony z portalu Azure Marketplace. Należy skonfigurować klucz usługi KMS do aktywacji systemu Windows Server 2016? 
 
Nie. Obraz w portalu Azure Marketplace zawiera klucz instalacji odpowiedniego klienta usługi KMS już skonfigurowane. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Aktywacja systemu Windows działa tak samo niezależnie od Jeśli maszyna wirtualna używa Azure hybrydowego Użyj korzyści (HUB) lub nie? 
 
Tak. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Co się stanie, jeśli okres aktywacji systemu Windows? 
 
Gdy upłynął okres prolongaty i systemu Windows nadal nie włączono, Windows Server 2008 R2 i nowszych wersjach systemu Windows zostanie wyświetlone dodatkowe powiadomienia dotyczące aktywacji. Czarne pozostaje tapety pulpitu i usługi Windows Update zainstaluje zabezpieczeń i tylko aktualizacje krytyczne, ale nie opcjonalne aktualizacje. W sekcji powiadomienia w dolnej części [warunki licencyjne](http://technet.microsoft.com/library/ff793403.aspx) strony.   

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
