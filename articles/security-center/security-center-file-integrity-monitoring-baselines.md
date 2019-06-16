---
title: Porównanie planów bazowych z File Integrity Monitoring w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak porównać odniesienia zawierające File Integrity Monitoring w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358441"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porównanie planów bazowych przy użyciu pliku Integrity Monitoring (FIM)

Plik Integrity Monitoring (FIM) informuje, po wystąpieniu zmian do obszarów poufnych zasobów, aby można było zbadać i rozwiązać nieautoryzowane działanie. FIM monitoruje pliki Windows, Windows rejestrów i pliki systemu Linux.

W tym temacie wyjaśniono, jak włączyć FIM do plików i rejestrów. Aby uzyskać więcej informacji na temat programu FIM, zobacz [File Integrity Monitoring w usłudze Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Dlaczego warto używać usługi FIM?

System operacyjny, aplikacje i konfiguracje skojarzone kontrolować stan zachowanie i zabezpieczeń zasobów. W związku z tym osoby atakujące wybierają pliki, które kontrolują zasobów, aby można było overtake zasobu systemu operacyjnego i/lub wykonać działania bez wykrycia.

W rzeczywistości wiele standardów zgodności z przepisami, takimi jak PCI-DSS ISO 17799 wymagać zaimplementowania formanty programu FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Włącz sprawdzanie rejestru wbudowanych cykliczne

Ustawienia domyślne gałęzi rejestru usługi FIM zapewniają wygodny sposób monitorowania cyklicznego zmiany wymuszające modyfikację typowe obszary zabezpieczeń.  Osoba atakująca może na przykład skonfigurować skrypt do wykonania w kontekście LOCAL_SYSTEM, konfigurując wykonywania podczas uruchamiania lub zamykania.  Aby monitorować zmian tego typu, należy włączyć wbudowane sprawdzanie.  

![Rejestr](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Cyklicznej kontroli dotyczą tylko gałęzie zabezpieczeń, a nie do ścieżki rejestru niestandardowych.  

## <a name="adding-a-custom-registry-check"></a>Dodawanie sprawdzania rejestru niestandardowe

Podstawy programu FIM uruchomić identyfikacji cechy znanego dobrego stanu systemu operacyjnego i obsługi aplikacji.  W tym przykładzie skupimy się na konfiguracji zasad haseł systemu Windows Server 2008 lub nowszy.


|Nazwa zasad                 | Ustawienia rejestru|
|---------------------------------------|-------------|
|Kontroler domeny: Odmówić zmiany hasła konta komputera.| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Członek domeny: Szyfruj cyfrowo dane bezpiecznego kanału (zawsze)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Członek domeny: Szyfruj cyfrowo dane bezpiecznego kanału (jeśli jest to możliwe)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Członek domeny: Podpisuj cyfrowo bezpiecznego kanału danych (jeśli jest to możliwe)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Członek domeny: Wyłącz zmiany hasła konta komputera|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Członek domeny: Wiek hasła konta komputera maksymalna|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Członek domeny: Wymagaj silnego klucza sesji (Windows 2000 lub nowszy)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpieczenia sieciowe: Ograniczanie ruchu NTLM:  Uwierzytelnianie NTLM w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpieczenia sieciowe: Ograniczanie ruchu NTLM: Dodaj wyjątki dla serwerów w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpieczenia sieciowe: Ograniczanie ruchu NTLM: Przeprowadź inspekcję uwierzytelniania NTLM w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Aby dowiedzieć się więcej na temat ustawień rejestru obsługiwane przez różne wersje systemu operacyjnego, dotyczą [ustawienia zasad grupy odwoływać się do arkusza kalkulacyjnego](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Aby skonfigurować FIM do monitorowania odniesienia rejestru:*

1. W **Dodawanie rejestru Windows do śledzenia zmian** okna w **klucza rejestru Windows** tekstu wprowadź klucz rejestru.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Włączanie usługi FIM na rejestr](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Śledzenie zmian w plikach Windows

1. W **Add Windows File do śledzenia zmian** okna w **wprowadź ścieżkę** tekstu wprowadź folder, który zawiera pliki, które mają być śledzone. W przykładzie na poniższej ilustracji **aplikacji sieci Web firmy Contoso** znajduje się w D:\ dysk w ramach **ContosWebApp** strukturę folderów.  
1. Utwórz niestandardowy wpis pliku Windows przez podanie nazwy klasy ustawienie, włączanie rekursji i Określanie folderu najwyższego poziomu za pomocą sufiksu symbolu wieloznacznego (*).

    ![Włączanie usługi FIM z pliku](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Trwa pobieranie zmian danych

File Integrity Monitoring, znajdują się szczegółowe dane w usłudze Azure Log Analytics / set Zmianakonfiguracji tabeli.  

 1. Ustaw zakres czasu, aby pobrać podsumowanie zmian przez zasób.
W poniższym przykładzie firma Microsoft pobierają wszystkie zmiany w ciągu ostatnich 14 dni w kategoriach plików i rejestru:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Aby wyświetlić szczegóły zmian w rejestrze:

    1. Usuń **pliki** z **gdzie** klauzuli 
    1. Usuń wiersz podsumowania i zastąp go klauzuli sortowania:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Raporty można eksportować do formatu CSV dla archiwizacji i/lub channeled do raportu usługi Power BI.  

![Dane programu FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)