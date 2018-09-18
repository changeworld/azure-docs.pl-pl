---
title: Tworzenie wysoko dostępnych aplikacji usługi Azure Storage na magazyn strefowo nadmiarowy (ZRS) | Dokumentacja firmy Microsoft
description: Magazyn strefowo nadmiarowy (ZRS) oferuje prosty sposób tworzyć aplikacje o wysokiej dostępności. Magazyn ZRS zapewnia ochronę przed awariami sprzętu w centrum danych i przed pewnych regionalnej awarii.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 0a4ebefd89414fa62ca69f2f9cc726f471694bee
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733620"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Magazyn strefowo nadmiarowy (ZRS): wysoko dostępnych aplikacji usługi Azure Storage
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Zakres pomocy technicznej i dostępność regionalna
Magazyn ZRS obecnie obsługuje typy kont ogólnego przeznaczenia standard w wersji 2. Aby uzyskać więcej informacji na temat typów kont magazynu, zobacz [Przegląd konta usługi Azure storage](storage-account-overview.md).

Magazyn ZRS jest dostępny dla blokowych obiektów blob, -disk stronicowe obiekty BLOB, pliki, tabele i kolejki.

Magazyn ZRS jest ogólnie dostępna w następujących regionach:

- Wschodnie stany USA
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

Magazyn ZRS nie może chronić dane przed regionalnej awarii, w którym trwale dotyczy wiele stref. Zamiast tego Magazyn ZRS zapewnia odporność danych staje się tymczasowo niedostępne. W celu ochrony przed regionalnej awarii firma Microsoft zaleca używanie magazyn geograficznie nadmiarowy (GRS). Aby uzyskać więcej informacji na temat GRS, zobacz [magazyn geograficznie nadmiarowy (GRS): replikacji między regionami dla usługi Azure Storage](storage-redundancy-grs.md).

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
> Firma Microsoft zostanie wycofana i migracji konta klasycznego magazynu ZRS na 31 marca 2021. Więcej szczegółów, zapewnia się do klasycznego magazynu ZRS klientów przed wycofywania. 
>
> Gdy stanie się ZRS [jest ogólnie dostępna](#support-coverage-and-regional-availability) w regionie, klienci nie będzie można utworzyć konta klasycznego magazynu ZRS w portalu, w tym regionie. Za pomocą programu Microsoft PowerShell i wiersza polecenia platformy Azure do utworzenia konta klasycznego magazynu ZRS jest obsługiwana, dopóki klasyczny magazyn ZRS jest przestarzała.

Klasyczny magazyn ZRS asynchronicznie replikuje dane między centrami danych w jednym lub dwóch regionach. Replika może być niedostępna, chyba że firma Microsoft zainicjuje przejście w tryb failover do regionu pomocniczego. Nie można przekonwertować konta klasycznego magazynu ZRS na konto magazynu LRS lub GRS ani odwrotnie, a ponadto konto klasycznego magazynu ZRS nie ma metryk ani funkcji rejestrowania.

Klasyczny magazyn ZRS jest dostępna tylko dla **blokowe obiekty BLOB** w ogólnego przeznaczenia w wersji 1 (GPv1) konta magazynu. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Przegląd konta usługi Azure storage](storage-account-overview.md).

Nie można przekonwertować konta klasycznego magazynu ZRS, do lub z magazynu LRS, GRS lub RA-GRS. Konta klasycznego magazynu ZRS nie obsługują także metryk i rejestrowania.

Z konta magazynu LRS, klasycznego magazynu ZRS, GRS lub RA-GRS lub ręcznego przeprowadzania migracji danych z konta magazynu ZRS, użyj narzędzia AzCopy, Eksplorator usługi Azure Storage, programu Azure PowerShell lub wiersza polecenia platformy Azure. Możesz także tworzyć własne rozwiązanie migracji przy użyciu jednego z biblioteki klienta usługi Azure Storage.

## <a name="see-also"></a>Zobacz także
- [Replikacja usługi Azure Storage](storage-redundancy.md)
- [Magazyn lokalnie nadmiarowy (LRS): nadmiarowość danych niedrogiej usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn geograficznie nadmiarowy (GRS): replikacji między regionami dla usługi Azure Storage](storage-redundancy-grs.md)