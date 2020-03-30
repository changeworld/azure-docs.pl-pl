---
title: Grupy adresów IP w Zaporze platformy Azure
description: Grupy adresów IP umożliwiają grupowanie adresów IP i zarządzanie nimi dla reguł zapory platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444487"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Grupy IP (wersja zapoznawcza) w Zaporze platformy Azure

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Grupy IP umożliwiają grupowanie adresów IP i zarządzanie nimi w celu uzyskania następujących reguł zapory platformy Azure w następujący sposób:

- Jako adres źródłowy w regułach DNAT
- Jako adres źródłowy lub docelowy w regułach sieci
- Jako adres źródłowy w regułach aplikacji


Grupa IP może mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

Grupy IP mogą być ponownie używane w regułach DNAT zapory azure, sieci i aplikacji dla wielu zapór w różnych regionach i subskrypcjach na platformie Azure. Nazwy grup muszą być unikatowe. Grupę ADRESÓW IP można skonfigurować w witrynie Azure portal, interfejsie wiersza polecenia platformy Azure lub interfejsie API REST. Przykładowy szablon jest dostarczany, aby ułatwić rozpoczęcie pracy.

## <a name="sample-format"></a>Przykładowy format

Następujące przykłady formatu adresu IPv4 są prawidłowe do użycia w grupach IP:

- Pojedynczy adres: 10.0.0.0
- Notacja CIDR: 10.1.0.0/32
- Zakres adresów: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Tworzenie grupy adresów IP

Grupę IP można utworzyć przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy ADRESÓW IP (wersja zapoznawcza)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Przeglądanie grup adresów IP
1. Na pasku wyszukiwania portalu Azure wpisz **grupy ADRESÓW IP** i wybierz je. Możesz wyświetlić listę grup IP lub wybrać **dodaj,** aby utworzyć nową grupę IP.
2. Wybierz grupę IP, aby otworzyć stronę przeglądu. Można edytować, dodawać lub usuwać adresy IP lub grupy ADRESÓW IP.

   ![Omówienie grup IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Zarządzanie grupą adresów IP

Możesz zobaczyć wszystkie adresy IP w grupie IP oraz reguły lub zasoby, które są z nią skojarzone. Aby usunąć grupę IP, należy najpierw odłączyć grupę IP od zasobu, który jej używa.

1. Aby wyświetlić lub edytować adresy IP, wybierz pozycję **Adresy IP** w obszarze **Ustawienia** w lewym okienku.
2. Aby dodać jeden lub wiele adresów IP, wybierz **pozycję Dodaj adresy IP**. Spowoduje to otwarcie strony **Przeciągnij lub Przejrzyj** w celu przesłania lub możesz wprowadzić adres ręcznie.
3.  Wybranie elipsy (**...**) w prawo, aby edytować lub usunąć adresy IP. Aby edytować lub usunąć wiele adresów IP, zaznacz pola i wybierz pozycję **Edytuj** lub **Usuń** u góry.
4. Na koniec można wyeksportować plik w formacie CSV.

> [!NOTE]
> Jeśli usuniesz wszystkie adresy IP w grupie ADRESÓW IP, gdy jest ona nadal używana w regule, ta reguła zostanie pominięta.


## <a name="use-an-ip-group"></a>Korzystanie z grupy adresów IP

Podczas tworzenia reguł DNAT, aplikacji lub reguł sieciowych usługi Azure Firewall można teraz wybrać **grupę IP** jako **typ źródła** lub typ **miejsca docelowego** dla adresów IP.

> [!NOTE]
> Grupy IP nie są obsługiwane w zasadach zapory. Obecnie jest obsługiwany tylko z tradycyjnymi regułami zapory.

![Grupy adresów IP w zaporze](media/ip-groups/fw-ipgroup.png)

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

## <a name="related-azure-powershell-cmdlets"></a>Pokrewne polecenia cmdlet programu Azure PowerShell

Następujące polecenia cmdlet programu Azure PowerShell mogą służyć do tworzenia grup IP i zarządzania nimi:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Usuń-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Nowy-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [Nowy-AzFirewallZastosowanieRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Nowy-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć i skonfigurować Zaporę platformy Azure](tutorial-firewall-deploy-portal.md).