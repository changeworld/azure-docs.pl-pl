---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 86c301748b58e7642df9a738c70b4fe70be3310b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400012"
---
Magazyn strefowo nadmiarowy (ZRS) replikuje Twoje dane synchronicznie w trzech klastrach magazynu w jednym regionie. Każdy klaster magazynu są fizycznie oddzielone od innych i znajduje się w jego własnej strefie dostępności (AZ). Każda strefa dostępności i klaster magazynu ZRS, to jest autonomicznego, przy użyciu osobnych narzędzi i funkcji sieciowych.

Przechowywanie danych w ramach konta magazynu ZRS zapewnia będą mieć dostęp i Zarządzaj danymi w przypadku, gdy strefa jest niedostępna. Magazyn ZRS zapewnia doskonałą wydajność i małe opóźnienia. Magazyn ZRS zapewnia takie same [cele skalowalności](../articles/storage/common/storage-scalability-targets.md) jako [magazyn lokalnie nadmiarowy (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Magazyn ZRS należy wziąć pod uwagę scenariusze, które wymagają wysokiego poziomu spójności, silne trwałości i wysokiej dostępności, nawet w przypadku awarii lub klęski żywiołowej powoduje wyświetlenie centrum danych strefowych niedostępny. Magazyn ZRS zapewnia trwałość obiektów magazynu co najmniej 99,9999999999% (12 9) w danym roku.

Aby uzyskać więcej informacji o strefach dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Zakres pomocy technicznej i dostępność regionalna
Magazyn ZRS obsługuje obecnie standard [ogólnego przeznaczenia w wersji 2 (GPv2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts) typów kont. Magazyn ZRS jest dostępny dla blokowych obiektów blob, -disk stronicowe obiekty BLOB, pliki, tabele i kolejki. Ponadto wszystkie swoje [Storage Analytics](../articles/storage/common/storage-analytics.md) dzienniki i [metryk usługi Storage](../articles/storage/common/storage-enable-and-view-metrics.md)

Magazyn ZRS jest ogólnie dostępna w następujących regionach:

- Wschodnie stany USA 2
- Zachodnie stany USA 2
- Środkowe stany USA
- Europa Północna
- Europa Zachodnia
- Francja Środkowa
- Azja Południowo-Wschodnia

Firma Microsoft nadal włączyć ZRS w dodatkowych regionach platformy Azure. Sprawdź [aktualizacje usługi Azure](https://azure.microsoft.com/updates/) regularnie stronę informacji o nowych regionów.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co się stanie, gdy strefa jest niedostępna?

Dane pozostają odporne na błędy, jeśli strefa jest niedostępna. Firma Microsoft zaleca nadal postępuj zgodnie z rozwiązania w zakresie błędu przejściowego w obsłudze, takich jak zaimplementowanie zasad ponawiania prób przy użyciu wykładniczego wycofywania. Gdy strefa jest niedostępna, Azure zobowiązuje sieci aktualizacji, takich jak DNS repointing. Te aktualizacje mogą wpływać na aplikację, jeśli został ukończony, są uzyskiwania dostępu do danych.

Magazyn ZRS nie może chronić dane przed regionalnej awarii, w którym trwale dotyczy wiele stref. Zamiast tego Magazyn ZRS zapewnia odporność danych staje się tymczasowo niedostępne. W celu ochrony przed regionalnej awarii firma Microsoft zaleca używanie magazyn geograficznie nadmiarowy (GRS). Aby uzyskać więcej informacji na temat GRS, zobacz [magazyn geograficznie nadmiarowy (GRS): replikacji między regionami dla usługi Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Konwertowanie na replikacją ZRS
Obecnie służy witryny Azure portal lub interfejsu API dostawcy zasobów magazynu można zmienić typu nadmiarowości swojego konta, tak długo, jak w przypadku migracji z lub do magazynu LRS, GRS i RA-GRS. Za pomocą magazynu ZRS jednak migracja nie jest jako utrudnione, ponieważ spowodowałoby to przenoszenia danych fizycznych z sygnatury pojedynczy magazyn do wielu sygnatury w regionie. 

Masz dwie podstawowe opcje migracja do / z magazynu ZRS. Można ręcznie skopiować lub przenieść dane do nowego konta magazynu ZRS z nią istniejącego konta. Możesz również poprosić o migracji na żywo. Firma Microsoft zaleca, wykonać ręczną migrację, ponieważ nie ma żadnej gwarancji, aby podczas migracji na żywo zostanie ukończona. Trasy ręcznej migracji zapewnia większą elastyczność niż migracji na żywo nie, a Ty masz kontrolę w czasie migracji.

Aby wykonać ręczną migrację, masz różne opcje:
- Użyj istniejących narzędzi, takich jak narzędzia AzCopy, storage SDK, niezawodne narzędzia innych producentów, itp.
- Jeśli jesteś zaznajomiony z usługi Hadoop lub HDInsight, możesz dołączyć oba źródła i docelowego (ZRS) konta do klastra, a także umożliwia podobny do narzędzia DistCp stopniu zrównoleglenia proces kopiowania danych
- Tworzenie własnych narzędzi, wykorzystując jedną wersję zestaw SDK usługi storage

Jeśli jednak ręcznej migracji spowoduje przestój aplikacji i nie można w celu ochrony przed rozproszonymi, Twoje obowiązki w, firma Microsoft udostępnia opcję migracji na żywo. Migracja na żywo jest migracja w miejscu, który umożliwia kontynuowanie przy użyciu istniejącego konta magazynu, podczas migracji danych między sygnatury magazynu źródłowego i docelowego. Podczas migracji będą nadal mają ten sam poziom trwałości i umowy SLA zapewniającej dostępność, tak jak zwykle.

Migracja na żywo jednak występują pewne ograniczenia. Są one wymienione poniżej.

- Gdy firmy Microsoft będzie dotyczyć niezwłocznie żądania migracji na żywo, nie ma gwarancji co podczas migracji zostanie ukończone. Jeśli potrzebujesz danych, aby mieć ZRS w określonym czasie, należy wykonać ręczną migrację. Ogólnie rzecz biorąc, większej ilości danych ma ze swojego konta tym dłużej potrwa do migracji danych. 
- Migracja na żywo może wykonywać tylko z konta, przy użyciu replikacji LRS lub GRS. Jeśli Twoje konto używa RA-GRS, należy najpierw przeprowadzić migrację do jednego z następujących typów replikacji przed kontynuowaniem. W tym kroku pośrednie gwarantuje, że pomocniczego tylko do odczytu punktu końcowego udostępniającej RA-GRS zostanie usunięty przed migracją.
- Twoje konto musi zawierać dane.
- Obsługiwane są tylko migracji wewnątrz regionu. Jeśli chcesz przeprowadzić migrację danych do konta magazynu ZRS, znajdującego się w regionie, które są inne niż konto źródłowe, należy wykonać ręczną migrację.
- Obsługiwane są tylko typy kont magazynu w warstwie standardowa. Nie można migrować z konta usługi premium storage.

Żądania są kierowane za pośrednictwem portalu pomocy technicznej systemu Azure migracja na żywo. Z poziomu portalu wybierz pozycję konta magazynu, który chcesz przekonwertować ZRS.
1. Kliknij przycisk **nowe żądanie obsługi**
2. Sprawdź podstawy. Kliknij przycisk **Dalej**. 
3. Na **Problem** sekcji 
    - Pozostaw ważność jako-to.
    - Typ problemu = **migracji danych**
    - Kategoria = **migracja do magazynu ZRS w obrębie regionu**
    - Tytuł = **ZRS konta migracji** (lub coś opisu)
    - Szczegóły = mogę przeprowadzić migrację do magazynu ZRS z [LRS, GRS] w regionie ___. 
4. Kliknij przycisk **Dalej**.
5. Sprawdź poprawność bloku informacje kontaktowe informacje kontaktowe.
6. Kliknij przycisk **przesłać**.

Działu pomocy technicznej następnie Tobą skontaktuje. Osoba będzie dostępne zapewnić pomoc, której może wymagać. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Klasyczny magazyn ZRS: To starszy opcja w przypadku blokowych obiektów blob nadmiarowości
> [!NOTE]
> Konta klasycznego magazynu ZRS są planowane dla obsługi i wymagane migracji na 31 marca 2021. Firma Microsoft będzie wysyłać więcej szczegółów do klasycznego magazynu ZRS klientów przed wycofywania. Firma Microsoft zamierza zapewnić proces automatycznej migracji z klasycznego magazynu ZRS ZRS w przyszłości.

>[!NOTE]
> Gdy magazyn ZRS jest [jest ogólnie dostępna](#support-coverage-and-regional-availability) w regionie, nie będzie już mógł utworzyć konta klasycznego magazynu ZRS w portalu, w tym samym regionie. Jednak można nadal utworzyć jedną za pomocą innych metod, takich jak Microsoft PowerShell i interfejsu wiersza polecenia platformy Azure, oznacza to, aż klasyczny magazyn ZRS jest przestarzała.

Klasyczny magazyn ZRS asynchronicznie replikuje dane między centrami danych w jednym lub dwóch regionach. Replika może być niedostępna, chyba że firma Microsoft zainicjuje przejście w tryb failover do regionu pomocniczego. Klasyczny magazyn ZRS jest dostępna tylko dla **blokowe obiekty BLOB** w [ogólnego przeznaczenia w wersji 1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts) kont magazynu. Nie można przekonwertować konta klasycznego magazynu ZRS na konto magazynu LRS lub GRS ani odwrotnie, a ponadto konto klasycznego magazynu ZRS nie ma metryk ani funkcji rejestrowania.

Nie można przekonwertować konta klasycznego magazynu ZRS, do lub z magazynu LRS, GRS lub RA-GRS. Konta klasycznego magazynu ZRS nie obsługują także metryk i rejestrowania.

Z konta magazynu LRS, klasycznego magazynu ZRS, GRS lub RA-GRS lub ręcznego przeprowadzania migracji danych z konta magazynu ZRS, użyj narzędzia AzCopy, Eksplorator usługi Azure Storage, programu Azure PowerShell lub wiersza polecenia platformy Azure. Możesz także tworzyć własne rozwiązanie migracji przy użyciu jednego z biblioteki klienta usługi Azure Storage.
