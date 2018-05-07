---
title: Zarządzanie zestawów rekordów DNS i rekordy z usługi Azure DNS | Dokumentacja firmy Microsoft
description: Usługa DNS platformy Azure umożliwia zarządzanie zestawami rekordów DNS i rekordy, gdy hosting domeny.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 18ed44a1-7bfe-454f-964e-922ad978264a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: da7d2118a0fb6bc5004856d994a01a932094ca45
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Zarządzanie rekordami DNS i zestawów rekordów przy użyciu portalu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Interfejs wiersza polecenia platformy Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](dns-operations-recordsets-cli.md)
> * [Program PowerShell](dns-operations-recordsets.md)

W tym artykule przedstawiono sposób zarządzania zestawy rekordów i rekordów dla strefy DNS przy użyciu portalu Azure.

Należy zrozumieć różnicę między zestawami rekordów DNS i poszczególnych rekordów DNS. Zestaw rekordów jest kolekcją rekordów w strefie, które mają taką samą nazwę i są tego samego typu. Aby uzyskać więcej informacji, zobacz [DNS Utwórz zestawów rekordów i rekordów przy użyciu portalu Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Tworzenie nowego zestawu rekordów i rekordu

Aby utworzyć rekord w portalu Azure, zobacz [rekordy DNS utworzyć przy użyciu portalu Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Wyświetl zestaw rekordów

1. W portalu Azure, przejdź do **strefy DNS** bloku.
2. Wyszukiwanie zestawu rekordów i zaznacz go. Spowoduje to otwarcie właściwości zestawu rekordów.

    ![Wyszukiwanie zestawu rekordów](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Dodaj nowy rekord do zestawu rekordów

Możesz dodać maksymalnie 20 rekordów do każdego zestawu rekordów. Zestaw rekordów nie może zawierać dwa identyczne rekordy. Pusty zestawów rekordów (zero rekordami) mogą być tworzone, ale nie są wyświetlane na serwery nazw usługi Azure DNS. Zestawy rekordów typu CNAME może zawierać co najwyżej jeden rekord.

1. Na **właściwości zestawu rekordów** bloku dla swojej strefy DNS kliknij zestaw rekordów, które chcesz dodać rekordu do.

    ![Wybierz zestaw rekordów](./media/dns-operations-recordsets-portal/selectset500.png)

2. Określ właściwości zestawu rekordów, wypełniając pola.

    ![Dodaj rekord](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Kliknij przycisk **zapisać** w górnej części bloku, aby zapisać ustawienia. Następnie zamknij bloku.
4. W rogu pojawi się zapisuje rekord.

    ![Zapisywanie zestawu rekordów](./media/dns-operations-recordsets-portal/saving150.png)

Po zapisaniu rekordu, wartości na **strefy DNS** bloku zostaną one zastosowane w nowym rekordzie.

## <a name="update-a-record"></a>Aktualizacja rekordu

Podczas aktualizacji rekordu w istniejącego zestawu rekordów, pola, które można zaktualizować zależą od typu rekordu, z którymi pracujesz.

1. Na **właściwości zestawu rekordów** bloku dla zestawu rekordów, Wyszukaj rekord.
2. Zmodyfikuj rekord. Po zmodyfikowaniu rekordu, można zmienić ustawienia dostępne dla rekordu. W poniższym przykładzie **adres IP** pole jest zaznaczone, a adres IP jest w trakcie jest modyfikowany.

    ![Zmodyfikuj rekord](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Kliknij przycisk **zapisać** w górnej części bloku, aby zapisać ustawienia. W prawym górnym rogu zostanie wyświetlone powiadomienie, które rekord został zapisany.

    ![Zapisano zestaw rekordów](./media/dns-operations-recordsets-portal/saved150.png)

Po zapisaniu rekordu ustawiać wartości rekordu **strefy DNS** bloku będzie odzwierciedlać zaktualizowany rekord.

## <a name="remove-a-record-from-a-record-set"></a>Usuwanie rekordu zestawu rekordów

Azure portal służy do usuwania rekordów z zestawu rekordów. Należy pamiętać, że usunięcie ostatniego rekordu z zestawu rekordów nie powoduje usunięcia zestawu rekordów.

1. Na **właściwości zestawu rekordów** bloku dla zestawu rekordów, Wyszukaj rekord.
2. Kliknij przycisk rekordu, który chcesz usunąć. Następnie wybierz **Usuń**.

    ![Usuwanie rekordu](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Kliknij przycisk **zapisać** w górnej części bloku, aby zapisać ustawienia.
4. Usunięcie rekordu wartości rekordu na **strefy DNS** bloku będzie odzwierciedlać usuwania.

## <a name="delete"></a>Usuń zestaw rekordów

1. Na **właściwości zestawu rekordów** bloku dla zestawu rekordów, kliknij przycisk **usunąć**.

    ![Usuń zestaw rekordów](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Pojawi się komunikat z pytaniem, czy chcesz usunąć zestaw rekordów.
3. Sprawdź, czy nazwa jest zgodna zestawu rekordów, który chcesz usunąć, a następnie kliknij przycisk **tak**.
4. Na **strefy DNS** bloku, sprawdź, czy zestaw rekordów nie jest już widoczna.

## <a name="work-with-ns-and-soa-records"></a>Praca z rekordów NS i SOA

Rekordy NS i SOA, które są automatycznie tworzone są zarządzane zależy od innych typów rekordów.

### <a name="modify-soa-records"></a>Modyfikowania rekordów SOA

Nie można dodać lub usunąć rekordy z automatycznie utworzonych SOA zestawu rekordów w wierzchołku strefy (nazwa = "@"). Jednak można modyfikować żadnego z parametrów w ramach rekord SOA (z wyjątkiem "Host") i czas wygaśnięcia zestawu rekordów.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modyfikowania rekordów NS w wierzchołku strefy

Zestaw w wierzchołku strefy rekordów NS jest tworzony automatycznie w każdej strefie DNS. Zawiera ona nazwy serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Możesz dodać dodatkowe serwery do tego rekordu NS ustawiona, do obsługi wspólnej hostingu domen z więcej niż jednego dostawcy usługi DNS. Można również zmodyfikować TTL i metadanych dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnione serwerów nazw usługi Azure DNS.

Należy pamiętać, że dotyczy to tylko zestawu w wierzchołku strefy rekordów NS. Innymi zestawami rekordów NS w strefie (tak jak delegowania strefy podrzędnej) można modyfikować bez ograniczeń.

### <a name="delete-soa-or-ns-record-sets"></a>Usuwanie zestawów rekordów SOA lub NS

Nie można usunąć SOA i zestawy rekordów NS w wierzchołku strefy (nazwa = "@") która są tworzone automatycznie po utworzeniu strefy. Są one usuwane automatycznie po usunięciu strefy.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji o usłudze Azure DNS, zobacz [Omówienie usługi Azure DNS](dns-overview.md).
* Aby uzyskać więcej informacji na temat automatyzacji DNS, zobacz [stref DNS tworzenia i zestawów rekordów przy użyciu zestawu .NET SDK](dns-sdk.md).
* Aby uzyskać więcej informacji na temat odwrotnej rekordy DNS, zobacz [omówienie wstecznego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).
