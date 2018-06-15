---
title: Zarządzanie strefami DNS w usłudze Azure DNS - portalu Azure | Dokumentacja firmy Microsoft
description: Możesz zarządzać stref DNS przy użyciu portalu Azure. W tym artykule opisano sposób aktualizowanie, usuwanie i tworzenie stref DNS w usłudze Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: kumud
ms.openlocfilehash: 3fbf59010e690ac022e4363eddebe1cfbba53d13
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779043"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Jak zarządzać stref DNS w portalu Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Program PowerShell](dns-operations-dnszones.md)
> * [Interfejs wiersza polecenia platformy Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](dns-operations-dnszones-cli.md)

W tym artykule przedstawiono sposób zarządzania stref DNS przy użyciu portalu Azure. Można również zarządzać stref DNS przy użyciu wielu platform [interfejsu wiersza polecenia Azure](dns-operations-dnszones-cli.md) lub platformy Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Logowanie się do witryny Azure Portal
2. W menu centralnym kliknij przycisk, a następnie kliknij przycisk **tworzenie zasobu > Sieć >** , a następnie kliknij przycisk **strefy DNS** aby otworzyć blok strefy DNS Utwórz.

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

W portalu Azure, przejdź do **więcej usług** > **sieci** > **stref DNS**. Każdej strefy DNS jest jest własnych zasobów, informacje, takie jak liczba zestawów rekordów i serwery nazw są widoczne w tym widoku. Kolumna **serwery nazw** nie znajduje się w widok domyślny, aby dodać go kliknij **kolumn**, wybierz pozycję **serwery nazw** i kliknij przycisk **gotowe**.

![Lista stref DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Przejdź do strefy DNS w portalu. Na **strefy DNS** bloku, kliknij przycisk **Usuń strefę**. Monit o potwierdzenie chcą usunąć strefę DNS. Usunięcie strefy DNS powoduje usunięcie wszystkich rekordów, które są zawarte w strefie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak pracować z strefy DNS i rekordy odwiedzając [wprowadzenie do usługi Azure DNS przy użyciu portalu Azure](dns-getstarted-portal.md).