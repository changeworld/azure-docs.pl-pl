---
title: Zarządzanie strefami DNS w usłudze Azure DNS — witryna Azure portal | Dokumenty firmy Microsoft
description: Strefy DNS można zarządzać za pomocą witryny Azure portal. W tym artykule opisano sposób aktualizowania, usuwania i tworzenia stref DNS w usłudze Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936782"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Jak zarządzać strefami DNS w witrynie Azure portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md)

W tym artykule pokazano, jak zarządzać strefami DNS przy użyciu witryny Azure portal. Strefy DNS można również zarządzać za pomocą wieloplatformowego interfejsu [wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md) lub programu Azure [PowerShell.](dns-operations-dnszones.md)

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Logowanie się do witryny Azure Portal
2. W menu Centrum przejdź do pozycji **Utwórz zasób > strefa DNS > Networking >,** aby otworzyć blok **Utwórz strefę DNS.**

    ![Strefa DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. W bloku **Tworzenie strefy DNS** wprowadź następujące wartości, a następnie kliknij pozycję **Utwórz**:


   | **Ustawienie** | **Wartość** | **Szczegóły** |
   |---|---|---|
   |**Nazwa**|contoso.com|Nazwa strefy DNS|
   |**Subskrypcja**|[Twoja subskrypcja]|Wybierz subskrypcję, aby utworzyć w jej ramach strefę DNS.|
   |**Grupa zasobów**|**Utwórz nową:** contosoDNSRG|Utwórz grupę zasobów. Nazwa grupy zasobów musi być unikatowa w obrębie wybranej subskrypcji. Aby dowiedzieć się więcej o grupach zasobów, przeczytaj artykuł [— omówienie Menedżera zasobów.](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)|
   |**Lokalizacja**|Zachodnie stany USA||

> [!NOTE]
> Ustawienie Grupa zasobów dotyczy lokalizacji grupy zasobów i nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

## <a name="list-dns-zones"></a>Wyświetlanie listy stref DNS

W witrynie Azure portal przejdź do **pozycji Więcej usług** > **Sieciowe** > **strefy DNS**. Każda strefa DNS jest własnym zasobem, a informacje, takie jak liczba zestawów rekordów i serwery nazw, są widoczne z tego widoku. Serwery **nazw** kolumn nie są wyświetlane w widoku domyślnym. Aby go dodać, kliknij pozycję **Kolumny**, wybierz pozycję **Serwery nazw**, a następnie kliknij przycisk **Gotowe**.

![wyświetlanie stref DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Przejdź do strefy DNS w portalu. W bloku **strefa DNS** kliknij pozycję **Usuń strefę**. Następnie zostanie wyświetlony monit o potwierdzenie, że chcesz usunąć strefę DNS. Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów zawartych w strefie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak pracować ze strefą DNS i rekordami, odwiedzając witrynę [Wprowadzenie do usługi Azure DNS przy użyciu witryny Azure portal](dns-getstarted-portal.md).