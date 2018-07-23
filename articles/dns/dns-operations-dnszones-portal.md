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
ms.openlocfilehash: ca9d03cb14e79b23ccc2021e0a31650eb9bbd95b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171242"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Jak zarządzać strefami systemu DNS w witrynie Azure portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Program PowerShell](dns-operations-dnszones.md)
> * [Interfejs wiersza polecenia platformy Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](dns-operations-dnszones-cli.md)

W tym artykule pokazano, jak zarządzać strefami DNS przy użyciu witryny Azure portal. Można również zarządzać stref DNS przy użyciu dla wielu platform [wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md) lub Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Logowanie się do witryny Azure Portal
2. W menu Centrum kliknij pozycję **Utwórz zasób > Sieć >** a następnie kliknij przycisk **strefy DNS** aby otworzyć blok tworzenie strefy DNS.

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

W witrynie Azure portal przejdź do **więcej usług** > **sieć** > **stref DNS**. Każda strefa DNS jest to własnych zasobów, informacje, takie jak liczba zestawów rekordów i serwery nazw są wyświetlane z poziomu tego widoku. Kolumna **serwery nazw** nie jest w domyślnym widoku, aby ją dodać, kliknij przycisk **kolumn**, wybierz opcję **serwery nazw** i kliknij przycisk **gotowe**.

![Wyświetlanie listy stref DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Przejdź do strefy DNS w portalu. Na **strefy DNS** bloku kliknij **Usuń strefę**. Monit, aby potwierdzić, że chce usunąć strefy DNS. Usuwanie strefy DNS powoduje również usunięcie wszystkich rekordów, które są zawarte w strefie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak pracować z Twojej strefy i rekordów DNS, odwiedzając [Rozpoczynanie pracy z usługą Azure DNS przy użyciu witryny Azure portal](dns-getstarted-portal.md).