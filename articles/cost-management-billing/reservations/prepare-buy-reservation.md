---
title: Przygotowanie do zakupu rezerwacji platformy Azure
description: Uzyskaj informacje na temat ważnych kwestii przed zakupem rezerwacji platformy Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235647"
---
# <a name="prepare-to-buy-a-reservation"></a>Przygotowanie do zakupu rezerwacji

Rezerwacje platformy Azure pomagają zaoszczędzić pieniądze dzięki rocznym lub 3-letnim planom dla wielu zasobów platformy Azure. Przed zobowiązaniem się do zakupu rezerwacji zapoznaj się z poniższymi sekcjami, aby przygotować się do zakupu.

## <a name="who-can-buy-a-reservation"></a>Kto może kupić rezerwację

Aby kupić plan, musisz mieć rolę właściciela subskrypcji w subskrypcji Enterprise (MS-AZR-0017P lub MS-AZR-0148P), subskrypcji z płatnością zgodnie z rzeczywistym użyciem (MS-AZR-0003P lub MS-AZR-0023P) albo subskrypcji w ramach umowy klienta firmy Microsoft. Dostawy rozwiązań w chmurze mogą użyć witryny Azure Portal lub [Centrum partnerskiego](/partner-center/azure-reservations), aby zakupić rezerwacje platformy Azure.

Klienci korzystający z umowy Enterprise Agreement (EA) mogą ograniczyć możliwość zakupów tylko do administratorów EA, wyłączając opcję **Dodaj wystąpienia zarezerwowane** w portalu EA. Administrator EA musi być właścicielem co najmniej jednej subskrypcji EA, aby mieć możliwość zakupu rezerwacji. Ta opcja jest przydatna w przypadku przedsiębiorstw, które chcą kupować rezerwacje dla różnych centrów kosztów za pośrednictwem centralnego zespołu. Po zakupie centralne zespoły mogą dodawać do rezerwacji właścicieli centrów kosztów. Właściciele mogą następnie ograniczyć rezerwację do swoich subskrypcji. Centralny zespół nie musi mieć dostępu właściciela subskrypcji do obszaru, w którym zakupiono rezerwację.

Rabat na rezerwację dotyczy tylko zasobów skojarzonych z subskrypcjami zakupionymi w ramach umowy Enterprise, subskrypcji dostawcy usług w chmurze, umowy klienta firmy Microsoft i planów indywidualnych ze stawkami płatności zgodnie z rzeczywistym użyciem.

## <a name="scope-reservations"></a>Określanie zakresu rezerwacji

Zakres rezerwacji można ograniczyć do subskrypcji lub grup zasobów. Ustawienie zakresu rezerwacji określa, gdzie wystąpią oszczędności z rezerwacji. W przypadku określenia zakresu rezerwacji jako grupa zasobów rabaty na rezerwację dotyczą tylko tej grupy zasobów — a nie całej subskrypcji.

### <a name="reservation-scoping-options"></a>Opcje określania zakresu rezerwacji

Masz trzy opcje zakresu rezerwacji do wyboru w zależności od potrzeb:

- **Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
- **Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
- **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja. W przypadku klientów z umową klienta firmy Microsoft zakres rozliczeniowy to profil rozliczeniowy. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.

Podczas stosowania rabatów na rezerwację w stosunku do użycia platforma Azure przetwarza rezerwacje w następującej kolejności:

1. Rezerwacje, których zakres jest ograniczony do grupy zasobów
2. Rezerwacje pojedynczego zakresu
3. Rezerwacje zakresu udostępnionego

Jedna grupa zasobów może uzyskać rabaty na rezerwację z wielu rezerwacji, w zależności od sposobu określania zakresu rezerwacji.

Zakres można zaktualizować w dowolnym momencie po zakupie rezerwacji. W tym celu należy przejść do rezerwacji, kliknąć pozycję **Konfiguracja** i ponownie określić zakres. Zmiana zakresu rezerwacji nie jest transakcją handlową. Okres rezerwacji nie jest zmieniany. Aby uzyskać więcej informacji o aktualizowaniu zakresu, [zobacz Aktualizowanie zakresu po zakupie rezerwacji.](manage-reserved-vm-instance.md#change-the-reservation-scope)

![Przykład pokazujący zmianę zakresu rezerwacji](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Zakup rezerwacji

Rezerwacje można kupić w witrynie Azure Portal, interfejsach API, programie PowerShell i interfejsie wiersza polecenia. Przeczytaj następujące artykuły, które Cię dotyczą, gdy wszystko będzie gotowe do zakupu rezerwacji:

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Disk Storage](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Plany oprogramowania](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Maszyny wirtualne](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie rezerwacjami zasobów platformy Azure](manage-reserved-vm-instance.md)
