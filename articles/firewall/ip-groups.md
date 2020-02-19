---
title: Grupy IP w zaporze platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444487"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Grupy IP (wersja zapoznawcza) w zaporze platformy Azure

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi w następujący sposób:

- Jako adres źródłowy w regułach DNAT
- Jako adres źródłowy lub docelowy w regułach sieci
- Jako adres źródłowy w regułach aplikacji


Grupa adresów IP może mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

Grupy adresów IP mogą być ponownie używane w regułach DNAT, sieci i aplikacji zapory platformy Azure dla wielu zapór w różnych regionach i subskrypcjach na platformie Azure. Nazwy grup muszą być unikatowe. Grupę adresów IP można skonfigurować w Azure Portal, w interfejsie wiersza polecenia platformy Azure lub interfejsu API REST. Przykładowy szablon jest dostarczany, aby pomóc Ci rozpocząć pracę.

## <a name="sample-format"></a>Przykładowy format

Następujące przykłady formatu adresu IPv4 są prawidłowe do użycia w grupach adresów IP:

- Pojedynczy adres: 10.0.0.0
- Notacja CIDR: 10.1.0.0/32
- Zakres adresów: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Tworzenie grupy adresów IP

Grupę adresów IP można utworzyć przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub API REST. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy adresów IP (wersja zapoznawcza)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Przeglądaj grupy adresów IP
1. Na pasku wyszukiwania Azure Portal wpisz **grupy adresów IP** i wybierz je. Zobaczysz listę grup adresów IP lub możesz wybrać pozycję **Dodaj** , aby utworzyć nową grupę adresów IP.
2. Wybierz grupę adresów IP, aby otworzyć stronę przegląd. Można edytować, dodawać lub usuwać adresy IP lub grupy IP.

   ![Przegląd grup adresów IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Zarządzanie grupą adresów IP

Widoczne są wszystkie adresy IP należące do grupy IP oraz skojarzone z nimi reguły lub zasoby. Aby usunąć grupę adresów IP, należy najpierw odłączyć grupę adresów IP od zasobu, który go używa.

1. Aby wyświetlić lub edytować adresy IP, wybierz opcję **adresy IP** w obszarze **Ustawienia** w okienku po lewej stronie.
2. Aby dodać jeden lub wiele adresów IP, wybierz pozycję **Dodaj adresy IP**. Spowoduje to otwarcie strony **przeciągnij lub Przeglądaj** w celu przekazania lub można wprowadzić adres ręcznie.
3.  Wybranie wielokropka ( **...** ) po prawej stronie do edytowania lub usuwania adresów IP. Aby edytować lub usunąć wiele adresów IP, zaznacz pola i wybierz pozycję **Edytuj** lub **Usuń** u góry.
4. Na koniec można wyeksportować plik w formacie CSV.

> [!NOTE]
> W przypadku usunięcia wszystkich adresów IP w grupie IP, gdy jest ona nadal używana w regule, ta reguła zostanie pominięta.


## <a name="use-an-ip-group"></a>Użyj grupy adresów IP

Teraz można wybrać **grupę adresów** IP jako **typ źródłowy** lub **docelowy** dla adresów IP podczas tworzenia reguł DNAT, aplikacji lub sieci platformy Azure.

> [!NOTE]
> Grupy adresów IP nie są obsługiwane w zasadach zapory. Jest ona obecnie obsługiwana tylko w przypadku tradycyjnych reguł zapory.

![Grupy IP w zaporze](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Dostępność w danym regionie

Grupy IP są obecnie dostępne w następujących regionach:

- Zachodnie stany USA
- Zachodnie stany USA 2
- Wschodnie stany USA
- Wschodnie stany USA 2
- Środkowe stany USA
- Północno-środkowe stany USA
- Zachodnio-środkowe stany USA
- Południowo-środkowe stany USA
- Kanada Środkowa
- Europa Północna
- Europa Zachodnia
- Francja Środkowa
- Południowe Zjednoczone Królestwo
- Australia Wschodnia
- Australia Środkowa
- Australia Południowo-Wschodnia

## <a name="related-azure-powershell-cmdlets"></a>Pokrewne polecenia cmdlet Azure PowerShell

Następujące polecenia cmdlet Azure PowerShell mogą służyć do tworzenia grup adresów IP i zarządzania nimi:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).