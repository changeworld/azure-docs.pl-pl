---
title: Zarządzanie strefami DNS w usłudze Azure DNS — witryna Azure portal | Dokumentacja firmy Microsoft
description: Możesz zarządzać strefami DNS przy użyciu witryny Azure portal. W tym artykule opisano jak aktualizowanie, usuwanie i tworzenie strefy DNS na usługę Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: victorh
ms.openlocfilehash: d0a20de8738e8c7b2719a9de85d5fd16aa5778cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926342"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Jak zarządzać strefami systemu DNS w witrynie Azure portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Program PowerShell](dns-operations-dnszones.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md)

W tym artykule pokazano, jak zarządzać strefami DNS przy użyciu witryny Azure portal. Można również zarządzać stref DNS przy użyciu dla wielu platform [wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md) lub Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Logowanie się do witryny Azure Portal
2. W menu centralnym, przejdź do **Utwórz zasób > Sieć > strefa DNS** otworzyć **tworzenie strefy DNS** bloku.

    ![Strefa DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. W bloku **Tworzenie strefy DNS** wprowadź następujące wartości, a następnie kliknij pozycję **Utwórz**:


   | **Ustawienie** | **Wartość** | **Szczegóły** |
   |---|---|---|
   |**Nazwa**|contoso.com|Nazwa strefy DNS|
   |**Subskrypcja**|[Twoja subskrypcja]|Wybierz subskrypcję, aby utworzyć w jej ramach strefę DNS.|
   |**Grupa zasobów**|**Utwórz nową:** contosoDNSRG|Utwórz grupę zasobów. Nazwa grupy zasobów musi być unikatowa w obrębie wybranej subskrypcji. Aby dowiedzieć się więcej na temat grup zasobów, zapoznaj się z artykułem [Omówienie usługi Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Lokalizacja**|Zachodnie stany USA||

> [!NOTE]
> Ustawienie Grupa zasobów dotyczy lokalizacji grupy zasobów i nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

## <a name="list-dns-zones"></a>Wyświetlanie listy stref DNS

W witrynie Azure portal przejdź do **więcej usług** > **sieć** > **stref DNS**. Każdej strefy DNS jest swój własny zasób i informacje, takie jak liczba zestawów rekordów i serwery nazw są wyświetlane z poziomu tego widoku. Kolumna **serwery nazw** nie znajduje się w domyślnym widoku. Aby ją dodać, kliknij przycisk **kolumn**, wybierz opcję **serwery nazw**, a następnie kliknij przycisk **gotowe**.

![Wyświetlanie listy stref DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Przejdź do strefy DNS w portalu. Na **strefy DNS** bloku kliknij **Usuń strefę**. Następnie monit upewnij się, że chce usunąć strefy DNS. Usuwanie strefy DNS powoduje również usunięcie wszystkich rekordów, które są zawarte w strefie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak pracować z Twojej strefy i rekordów DNS, odwiedzając [Rozpoczynanie pracy z usługą Azure DNS przy użyciu witryny Azure portal](dns-getstarted-portal.md).