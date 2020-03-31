---
title: Zarządzanie zestawami rekordów i rekordami DNS za pomocą usługi Azure DNS
description: Usługa Azure DNS umożliwia zarządzanie zestawami rekordów i rekordami DNS podczas hostowania domeny.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 4012b32eb2684126b8dc64b9e86bf35a016e9fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936843"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure portal

W tym artykule pokazano, jak zarządzać zestawami rekordów i rekordami dla strefy DNS przy użyciu witryny Azure portal.

Ważne jest, aby zrozumieć różnicę między zestawami rekordów DNS a poszczególnymi rekordami DNS. Zestaw rekordów to zbiór rekordów w strefie o tej samej nazwie i tej samej typie. Aby uzyskać więcej informacji, zobacz [Tworzenie zestawów rekordów i rekordów DNS przy użyciu witryny Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Tworzenie nowego zestawu rekordów i rejestrowanie

Aby utworzyć zestaw rekordów w witrynie Azure portal, zobacz [Tworzenie rekordów DNS przy użyciu portalu Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Wyświetlanie zestawu rekordów

1. W witrynie Azure portal przejdź do bloku **strefy DNS.**
2. Wyszukaj zestaw rekordów i wybierz go. Spowoduje to otwarcie właściwości zestawu rekordów.

    ![Wyszukiwanie zestawu rekordów](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Dodawanie nowego rekordu do zestawu rekordów

Do dowolnego zestawu rekordów można dodać maksymalnie 20 rekordów. Zestaw rekordów nie może zawierać dwóch identycznych rekordów. Można utworzyć puste zestawy rekordów (z zerowymi rekordami), ale nie są wyświetlane na serwerach nazw DNS platformy Azure. Zestawy rekordów typu CNAME mogą zawierać co najwyżej jeden rekord.

1. W bloku **Właściwości zestawu rekordów** dla strefy DNS kliknij zestaw rekordów, do których chcesz dodać rekord.

    ![Wybieranie zestawu rekordów](./media/dns-operations-recordsets-portal/selectset500.png)

2. Określ właściwości zestawu rekordów, wypełniając pola.

    ![Dodawanie rekordu](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Kliknij **przycisk Zapisz** w górnej części bloku, aby zapisać ustawienia. Następnie zamknij ostrze.
4. W rogu zobaczysz, że rekord jest zapisywany.

    ![Zapisywanie zestawu rekordów](./media/dns-operations-recordsets-portal/saving150.png)

Po zapisaniu rekordu wartości w bloku **strefy DNS** będą odzwierciedlać nowy rekord.

## <a name="update-a-record"></a>Zaktualizowanie rekordu

Po zaktualizowaniu rekordu w istniejącym zestawie rekordów pola, które można zaktualizować, zależą od typu rekordu, z którym pracujesz.

1. Na bloku **Właściwości zestawu rekordów** dla zestawu rekordów wyszukaj rekord.
2. Zmodyfikuj rekord. Podczas modyfikowania rekordu można zmienić dostępne ustawienia rekordu. W poniższym przykładzie pole **adres IP** jest zaznaczone, a adres IP jest w trakcie modyfikowanie.

    ![Modyfikowanie rekordu](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Kliknij **przycisk Zapisz** w górnej części bloku, aby zapisać ustawienia. W prawym górnym rogu zobaczysz powiadomienie o zapisaniu rekordu.

    ![Zapisany zestaw rekordów](./media/dns-operations-recordsets-portal/saved150.png)

Po zapisaniu rekordu wartości rekordu ustawionego w bloku **strefy DNS** będą odzwierciedlać zaktualizowany rekord.

## <a name="remove-a-record-from-a-record-set"></a>Usuwanie rekordu z zestawu rekordów

Za pomocą witryny Azure Portal można usunąć rekordy z zestawu rekordów. Należy zauważyć, że usunięcie ostatniego rekordu z zestawu rekordów nie powoduje usunięcia zestawu rekordów.

1. Na bloku **Właściwości zestawu rekordów** dla zestawu rekordów wyszukaj rekord.
2. Kliknij rekord, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![Usuwanie rekordu](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Kliknij **przycisk Zapisz** w górnej części bloku, aby zapisać ustawienia.
4. Po usunięciu rekordu wartości rekordu w bloku **strefy DNS** będą odzwierciedlać usunięcie.

## <a name="delete-a-record-set"></a><a name="delete"></a>Usuwanie zestawu rekordów

1. Na bloku **Właściwości zestawu rekordów** dla zestawu rekordów kliknij pozycję **Usuń**.

    ![Usuwanie zestawu rekordów](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Zostanie wyświetlony komunikat z pytaniem, czy chcesz usunąć zestaw rekordów.
3. Sprawdź, czy nazwa jest zgodna z zestawem rekordów, który chcesz usunąć, a następnie kliknij przycisk **Tak**.
4. W bloku **strefy DNS** sprawdź, czy zestaw rekordów nie jest już widoczny.

## <a name="work-with-ns-and-soa-records"></a>Praca z rekordami NS i SOA

Rekordy NS i SOA, które są tworzone automatycznie, są zarządzane inaczej niż inne typy rekordów.

### <a name="modify-soa-records"></a>Modyfikowanie rekordów SOA

Nie można dodawać ani usuwać rekordów z automatycznie utworzonego rekordu\@SOA ustawionego na wierzchołku strefy (nazwa = " "). Można jednak zmodyfikować dowolny z parametrów w rekordzie SOA (z wyjątkiem "Host") i rekordu ustawionego TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modyfikowanie rekordów NS w wierzchołku strefy

Rekord NS ustawiony w wierzchołku strefy jest tworzony automatycznie z każdą strefą DNS. Zawiera nazwy serwerów nazw DNS platformy Azure przypisanych do strefy.

Do tego zestawu rekordów NS można dodać dodatkowe serwery nazw, aby obsługiwać domeny współ hostingowe z więcej niż jednym dostawcą DNS. Można również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw DNS platformy Azure.

Należy zauważyć, że dotyczy to tylko rekordu NS ustawionego w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) mogą być modyfikowane bez ograniczeń.

### <a name="delete-soa-or-ns-record-sets"></a>Usuwanie zestawów rekordów SOA lub NS

Nie można usunąć zestawów rekordów SOA i NS\@w wierzchołku strefy (nazwa = " "), które są tworzone automatycznie podczas tworzenia strefy. Są one usuwane automatycznie po usunięciu strefy.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat usługi Azure DNS, zobacz [omówienie usługi Azure DNS](dns-overview.md).
* Aby uzyskać więcej informacji na temat automatyzacji systemu DNS, zobacz [Tworzenie stref DNS i zestawów rekordów przy użyciu pliku .NET SDK](dns-sdk.md).
* Aby uzyskać więcej informacji na temat odwrotnych rekordów DNS, zobacz [Omówienie odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).
* Aby uzyskać więcej informacji na temat rekordów aliasu DNS platformy Azure, zobacz [omówienie rekordów aliasu DNS platformy Azure](dns-alias.md).
