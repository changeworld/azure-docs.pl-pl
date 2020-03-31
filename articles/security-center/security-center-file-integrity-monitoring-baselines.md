---
title: Monitorowanie integralności plików w usłudze Azure Security Center
description: Dowiedz się, jak porównać linie bazowe z monitorowaniem integralności plików w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664407"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porównanie planów bazowych przy użyciu monitorowania integralności plików (FIM)

Monitorowanie integralności plików (FIM) informuje o wystąpieniu zmian w obszarach wrażliwych w zasobach, dzięki czemu można badać i rozwiązywać nieautoryzowane działania. FIM monitoruje pliki systemu Windows, rejestry systemu Windows i pliki systemu Linux.

W tym temacie wyjaśniono, jak włączyć FIM w plikach i rejestrach. Aby uzyskać więcej informacji na temat fim, zobacz [Monitorowanie integralności plików w usłudze Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Dlaczego warto skorzystać z FIM?

System operacyjny, aplikacje i skojarzone konfiguracje kontrolują zachowanie i stan zabezpieczeń zasobów. W związku z tym osoby atakujące kierować pliki, które kontrolują zasoby, w celu wyprzedzenia systemu operacyjnego zasobu i/lub wykonywania działań bez wykrycia.

W rzeczywistości wiele norm zgodności z przepisami, takich jak PCI-DSS & ISO 17799, wymaga wdrożenia kontroli FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Włączanie wbudowanych kontroli rejestru cyklicznego

Domyślne ustawienia gałęzi rejestru FIM zapewniają wygodny sposób monitorowania zmian cyklicznych we wspólnych obszarach zabezpieczeń.  Na przykład przeciwnik może skonfigurować skrypt do wykonania w LOCAL_SYSTEM kontekście, konfigurując wykonanie podczas uruchamiania lub zamykania systemu.  Aby monitorować zmiany tego typu, włącz wbudowaną kontrolę.  

![Rejestr](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Kontrole cykliczne mają zastosowanie tylko do zalecanych gałęzi zabezpieczeń, a nie do niestandardowych ścieżek rejestru.  

## <a name="adding-a-custom-registry-check"></a>Dodawanie niestandardowego sprawdzania rejestru

Linie bazowe FIM rozpoczynają się od zidentyfikowania cech znanego stanu dobrego dla systemu operacyjnego i aplikacji pomocniczej.  W tym przykładzie skupimy się na konfiguracjach zasad haseł dla systemu Windows Server 2008 i nowszych.


|Policy Name (Nazwa zasad)                 | Ustawienie rejestru|
|---------------------------------------|-------------|
|Kontroler domeny: odrzucaj zmiany hasła konta komputera| MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Członek domeny: szyfruj lub podpisuj cyfrowo dane bezpiecznego kanału — zawsze|MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Członek domeny: szyfruj cyfrowo dane bezpiecznego kanału — gdy to możliwe|MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Członek domeny: podpisuj cyfrowo dane bezpiecznego kanału — gdy to możliwe|MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Członek domeny: wyłącz zmiany hasła konta komputera|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Członek domeny: maksymalny wiek hasła konta komputera|MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Członek domeny: wymagaj silnego klucza sesji (system Windows 2000 lub nowszy)|MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpieczenia sieci: Ograniczanie uwierzytelniania NTLM: NTLM w tej domenie|MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpieczenia sieciowe: Ograniczanie ruchu NTLM: Dodaj wyjątki dla serwerów z tej domeny|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpieczenia sieci: Ograniczanie ntlm: inspekcja uwierzytelniania NTLM w tej domenie|MASZYNA\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Aby dowiedzieć się więcej o ustawieniach rejestru obsługiwanych przez różne wersje systemu operacyjnego, zapoznaj się z [numerem arkusza kalkulacyjnego Ustawienia zasad grupy](https://www.microsoft.com/download/confirmation.aspx?id=25250).

*Aby skonfigurować fim do monitorowania linii bazowych rejestru:*

1. W oknie **Dodawanie rejestru systemu Windows do śledzenia zmian** w polu **tekstowym Klucz rejestru systemu Windows** wprowadź klucz rejestru.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Włączanie fim w rejestrze](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Śledzenie zmian w plikach systemu Windows

1. W oknie **Dodawanie pliku systemu Windows do śledzenia zmian** w polu tekstowym Wprowadź **ścieżkę** wprowadź folder zawierający pliki, które chcesz śledzić. W przykładzie na poniższym rysunku **contoso aplikacji sieci Web** znajduje się w D:\ w strukturze folderów **ContosWebApp.**  
1. Utwórz niestandardowy wpis pliku systemu Windows, podając nazwę klasy ustawień, włączając rekursję i określając górny folder z przyrostkiem z symbolami wieloznaczowymi (*).

    ![Włączanie fim w pliku](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Pobieranie danych zmian

Dane monitorowania integralności plików znajdują się w zestawie tabeli Azure Log Analytics / ConfigurationChange.  

 1. Ustaw zakres czasu, aby pobrać podsumowanie zmian według zasobów.
W poniższym przykładzie pobieramy wszystkie zmiany w ciągu ostatnich czternastu dni w kategoriach rejestru i plików:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Aby wyświetlić szczegóły zmian w rejestrze:

    1. Usuń **pliki** z klauzuli **where,** 
    1. Usuń wiersz podsumowania i zastąp go klauzulą zamawiania:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Raporty można eksportować do pliku CSV w celu archiwizacji i/lub kierować do raportu usługi Power BI.  

![Dane FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)