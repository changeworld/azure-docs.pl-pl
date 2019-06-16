---
title: Zarządzanie zestawami rekordów DNS i rekordów za pomocą usługi Azure DNS
description: Usługa system DNS Azure umożliwia zarządzanie zestawami rekordów DNS i rekordów, gdy hosting domeny.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 891adfacde6e46b1d8fe8e2f6b5fb39c90ce27a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61296481"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Zarządzanie rekordami systemu DNS i zestawów rekordów przy użyciu witryny Azure portal

W tym artykule pokazano, jak zarządzać zestawy rekordów i rekordów dla strefy DNS przy użyciu witryny Azure portal.

Należy zrozumieć różnicę między zestawami rekordów DNS oraz pojedynczych rekordów DNS. Zestaw rekordów jest kolekcją rekordów w strefie, które mają taką samą nazwę i są tego samego typu. Aby uzyskać więcej informacji, zobacz [DNS utworzyć zestawy rekordów i rekordów przy użyciu witryny Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Utwórz nowy zestaw rekordów i rekordów

Aby utworzyć zestaw rekordów w witrynie Azure portal, zobacz [DNS utworzyć rekordy przy użyciu witryny Azure portal](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Wyświetlanie zestawu rekordów

1. W witrynie Azure portal przejdź do **strefy DNS** bloku.
2. Wyszukaj zestaw rekordów, a następnie wybierz ją. Spowoduje to otwarcie właściwości zestawu rekordów.

    ![Wyszukiwanie zestawu rekordów](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Dodaj nowy rekord do zestawu rekordów

Możesz dodać maksymalnie 20 rekordów do dowolnego zestawu rekordów. Zestaw rekordów nie może zawierać dwa identyczne rekordy. Pusty zestawów rekordów (z zerową rekordów) mogą być tworzone, ale nie pojawiają się na serwerach nazw usługi Azure DNS. Zestawy rekordów CNAME typu może zawierać co najwyżej jeden rekord.

1. Na **właściwości zestawu rekordów** bloku dla strefy DNS, kliknij przycisk, którą chcesz dodać rekord do zestawu rekordów.

    ![Wybierz zestaw rekordów](./media/dns-operations-recordsets-portal/selectset500.png)

2. Określ właściwości zestawu rekordów, wypełniając pola.

    ![Dodaj rekord](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Kliknij przycisk **Zapisz** w górnej części bloku Aby zapisać ustawienia. Następnie zamknij blok.
4. W górnym zostanie wyświetlony, zapisuje rekord.

    ![Zapisywanie zestawu rekordów](./media/dns-operations-recordsets-portal/saving150.png)

Po rekord został zapisany, wartości na **strefy DNS** bloku będzie odzwierciedlać nowy rekord.

## <a name="update-a-record"></a>Zaktualizuj rekord

Po zaktualizowaniu rekordu w istniejącym zestawie rekordów pola, które można zaktualizować zależą od typu rekordu pracujemy z użyciem.

1. Na **właściwości zestawu rekordów** bloku dla zestawu rekordów, Wyszukaj rekord.
2. Zmodyfikuj rekord. Po zmodyfikowaniu rekordu, możesz zmienić dostępne ustawienia dla rekordu. W poniższym przykładzie **adresu IP** pole jest zaznaczone, a adres IP jest w trakcie jego modyfikację.

    ![Modyfikowanie rekordu](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Kliknij przycisk **Zapisz** w górnej części bloku Aby zapisać ustawienia. W prawym górnym rogu zobaczysz powiadomienie, które rekord został zapisany.

    ![Zapisano zestaw rekordów](./media/dns-operations-recordsets-portal/saved150.png)

Po zapisaniu rekordu wartości w rekordzie, ustaw na **strefy DNS** bloku będzie odzwierciedlać zaktualizowanym rekordem.

## <a name="remove-a-record-from-a-record-set"></a>Usunięcie rekordu z zestawu rekordów

Witryna Azure portal służy do usuwania rekordów z zestawu rekordów. Należy pamiętać, że usunięcie ostatniego rekordu w zestawie rekordów nie powoduje usunięcia zestawu rekordów.

1. Na **właściwości zestawu rekordów** bloku dla zestawu rekordów, Wyszukaj rekord.
2. Kliknij rekord, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![Usunięcie rekordu](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Kliknij przycisk **Zapisz** w górnej części bloku Aby zapisać ustawienia.
4. Po usunięciu rekordu wartości rekordu na **strefy DNS** bloku będzie odzwierciedlać usunięcie.

## <a name="delete"></a>Usuwanie zestawu rekordów

1. Na **właściwości zestawu rekordów** bloku zestaw rekordów, kliknij przycisk **Usuń**.

    ![Usuwanie zestawu rekordów](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Pojawi się komunikat z pytaniem, czy chcesz usunąć zestaw rekordów.
3. Sprawdź zgodność nazwy zestawu rekordów, który chcesz usunąć, a następnie kliknij przycisk **tak**.
4. Na **strefy DNS** bloku, sprawdź, czy zestaw rekordów nie jest już widoczna.

## <a name="work-with-ns-and-soa-records"></a>Praca z rekordy NS i SOA

Rekordy NS i SOA, które są automatycznie tworzone są zarządzane w różny sposób z innych typów rekordów.

### <a name="modify-soa-records"></a>Modyfikowanie rekordów SOA

Nie można dodać lub usunąć rekordy z automatycznie utworzoną SOA zestaw rekordów w wierzchołku strefy (nazwa = "\@"). Jednak można modyfikować parametrów w ramach rekord SOA (z wyjątkiem "Host") i czas wygaśnięcia zestawu rekordów.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modyfikowanie rekordów NS w wierzchołku strefy

Zestaw w wierzchołku strefy rekordów NS jest tworzony automatycznie w każdej strefie DNS. Zawiera ona nazwy serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Możesz dodać dodatkowe serwery do tego rekordu NS skonfigurowane, do obsługi domeny hosting współpracujących z więcej niż jednego dostawcę DNS. Można również zmodyfikować czas wygaśnięcia i metadanych dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw usługi Azure DNS.

Należy zauważyć, że dotyczy to tylko zestaw w wierzchołku strefy rekordów NS. Inne zestawy rekordów NS w strefie (co umożliwia delegowanie strefy podrzędnej) można zmodyfikować bez ograniczeń.

### <a name="delete-soa-or-ns-record-sets"></a>Usuń zestawy rekordów SOA lub NS

Nie można usunąć SOA i zestawy rekordów NS w wierzchołku strefy (nazwa = "\@"), są tworzone automatycznie podczas tworzenia strefy. Są one usuwane automatycznie po usunięciu strefy.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat usługi Azure DNS, zobacz [Omówienie usługi Azure DNS](dns-overview.md).
* Aby uzyskać więcej informacji na temat automatyzowania DNS, zobacz [zestawów rekordów przy użyciu zestawu SDK platformy .NET i strefami DNS tworzenia](dns-sdk.md).
* Aby uzyskać więcej informacji na temat rekordami odwrotnego systemu DNS, zobacz [omówienie odwrotnego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).
* Aby uzyskać więcej informacji na temat rekordów aliasów systemu DNS platformy Azure, zobacz [Azure alias DNS rekordów — omówienie](dns-alias.md).
