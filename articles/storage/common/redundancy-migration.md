---
title: Zmienianie sposobu replikowania konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak zmienić sposób replikowania danych na istniejącym koncie magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337074"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Zmienianie sposobu replikowania konta magazynu

Usługa Azure Storage zawsze przechowuje wiele kopii danych, dzięki czemu są chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętu, awariami sieci lub zasilania oraz masowymi klęskami żywiołowymi. Nadmiarowość gwarantuje, że twoje konto magazynu spełnia [umowę dotyczącą poziomu usług (SLA) dla usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) nawet w przypadku awarii.

Usługa Azure Storage oferuje następujące typy replikacji:

- Magazyn lokalnie nadmiarowy (LRS)
- Magazyn strefowo nadmiarowy (ZRS)
- Magazyn geograficznie nadmiarowy (GRS) lub magazyn geograficzny dostępu do odczytu (RA-GRS)
- Magazyn geograficznie nadmiarowy (GZRS) lub magazyn geograficzny dostępu do odczytu (RA-GZRS) (wersja zapoznawcza)

Aby uzyskać omówienie każdej z tych opcji, zobacz [Nadmiarowość usługi Azure Storage](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Przełączanie między typami replikacji

Konto magazynu można przełączyć z jednego typu replikacji na dowolny inny typ, ale niektóre scenariusze są bardziej proste niż inne. Jeśli chcesz dodać lub usunąć replikację geograficzną lub dostęp do odczytu do regionu pomocniczego, możesz zaktualizować ustawienie replikacji za pomocą portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Jeśli jednak chcesz zmienić sposób replikowania danych w regionie podstawowym, przechodząc z LRS do ZRS lub odwrotnie, należy przeprowadzić migrację ręczną.

Poniższa tabela zawiera omówienie sposobu przełączania się z każdego typu replikacji do innego:

| Przełączania | ... do LRS | ... do GRS/RA-GRS | ... do ZRS | ... do GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... od LRS</b> | Nie dotyczy | Zmiana ustawienia replikacji<sup>1</sup> za pomocą portalu Azure, programu PowerShell lub interfejsu wiersza polecenia | Wykonywanie migracji ręcznej <br /><br />Poproś o migrację na żywo | Wykonywanie migracji ręcznej <br /><br /> LUB <br /><br /> Najpierw przełącz się na GRS/RA-GRS, a następnie poproś o migrację na żywo<sup>1</sup> |
| <b>... od GRS/RA-GRS</b> | Zmiana ustawienia replikacji za pomocą portalu Azure, programu PowerShell lub interfejsu wiersza polecenia | Nie dotyczy | Wykonywanie migracji ręcznej <br /><br /> LUB <br /><br /> Najpierw przełącz się do lrs, a następnie poproś o migrację na żywo | Wykonywanie migracji ręcznej <br /><br /> Poproś o migrację na żywo |
| <b>... od ZRS</b> | Wykonywanie migracji ręcznej | Wykonywanie migracji ręcznej | Nie dotyczy | Zmiana ustawienia replikacji<sup>1</sup> za pomocą portalu Azure, programu PowerShell lub interfejsu wiersza polecenia |
| <b>... od GZRS/RA-GZRS</b> | Wykonywanie migracji ręcznej | Wykonywanie migracji ręcznej | Zmiana ustawienia replikacji za pomocą portalu Azure, programu PowerShell lub interfejsu wiersza polecenia | Nie dotyczy |

<sup>1</sup> Wiąże się z jednorazowym obciążeniem wychodzącym.

> [!CAUTION]
> Jeśli konto jest wykonywane w [pełnienia funkcji failover](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) dla konta (RA-)GRS lub (RA-)GZRS, jest skonfigurowany jako lokalnie nadmiarowy w nowym regionie podstawowym. Migracja na żywo do ZRS lub GZRS dla takich kont LRS nie jest obsługiwana. Konieczne będzie przeprowadzenie [migracji ręcznej](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs).

## <a name="change-the-replication-setting"></a>Zmienianie ustawienia replikacji

Za pomocą witryny Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure można zmienić ustawienie replikacji konta magazynu, o ile nie zmieniasz sposobu replikowania danych w regionie podstawowym. W przypadku migracji z LRS w regionie podstawowym do usługi ZRS w regionie podstawowym lub odwrotnie należy przeprowadzić [migrację ręczną](#perform-a-manual-migration-to-zrs) lub [migrację na żywo.](#request-a-live-migration-to-zrs)

Zmiana sposobu replikowania konta magazynu nie powoduje przestojów aplikacji.

# <a name="portal"></a>[Portal](#tab/portal)

Aby zmienić opcję nadmiarowości konta magazynu w witrynie Azure portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **Konfiguracja.**
1. Zaktualizuj ustawienie **Replikacja.**

![Zrzut ekranu przedstawiający sposób zmiany opcji replikacji w portalu](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby zmienić opcję nadmiarowości konta magazynu za pomocą programu PowerShell, należy wywołać `-SkuName` polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) i określić parametr:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zmienić opcję nadmiarowości dla konta magazynu za pomocą interfejsu wiersza `--sku` polecenia platformy Azure, wywołanie polecenia aktualizacji konta magazynu [az](/cli/azure/storage/account#az-storage-account-update) i określenie parametru:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Wykonywanie ręcznej migracji do usługi ZRS

Jeśli chcesz zmienić sposób replikowania danych na koncie magazynu w regionie podstawowym, przechodząc z LRS do ZRS lub odwrotnie, możesz zdecydować się na przeprowadzenie migracji ręcznej. Migracja ręczna zapewnia większą elastyczność niż migracja na żywo. Można kontrolować czas migracji ręcznej, więc użyj tej opcji, jeśli potrzebujesz migracji, aby zakończyć w określonym terminie.

Podczas ręcznej migracji z LRS do ZRS w regionie podstawowym lub odwrotnie, konto magazynu docelowego może być geograficznie nadmiarowe i można również skonfigurować dostęp do odczytu do regionu pomocniczego. Na przykład można migrować konto LRS do konta GZRS lub RA-GZRS w jednym kroku.

Migracja ręczna może spowodować przestoje aplikacji. Jeśli aplikacja wymaga wysokiej dostępności, firma Microsoft udostępnia również opcję migracji na żywo. Migracja na żywo to migracja w miejscu bez przestojów.

Dzięki migracji ręcznej dane z istniejącego konta magazynu można skopiować na nowe konto magazynu, które używa usługi ZRS w regionie podstawowym. Aby przeprowadzić migrację ręczną, można użyć jednej z następujących opcji:

- Kopiowanie danych przy użyciu istniejącego narzędzia, takiego jak AzCopy, jedna z bibliotek klienta usługi Azure Storage lub niezawodne narzędzie innej firmy.
- Jeśli znasz hadoop lub HDInsight, możesz dołączyć do klastra zarówno konto magazynu źródłowego, jak i konto magazynu docelowego. Następnie zrównoleglić proces kopiowania danych za pomocą narzędzia takiego jak DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Poproś o migrację na żywo do ZRS

Jeśli musisz przeprowadzić migrację konta magazynu z LRS lub GRS do usługi ZRS w regionie podstawowym bez przestojów aplikacji, możesz zażądać migracji na żywo od firmy Microsoft. Podczas migracji na żywo można uzyskać dostęp do danych na koncie magazynu i bez utraty trwałości lub dostępności. Usługa Azure Storage SLA jest obsługiwana podczas procesu migracji. Nie ma utraty danych związanych z migracją na żywo. Punkty końcowe usługi, klucze dostępu, sygnatury dostępu współdzielonego i inne opcje konta pozostają niezmienione po migracji.

ZRS obsługuje tylko konta ogólnego przeznaczenia w wersji 2, więc upewnij się, że uaktualnisz swoje konto magazynu przed przesłaniem żądania migracji na żywo do ZRS. Aby uzyskać więcej informacji, zobacz [Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md). Konto magazynu musi zawierać dane, które mają zostać zmigrowane za pośrednictwem migracji na żywo.

Migracja na żywo jest obsługiwana tylko dla kont magazynu, które używają replikacji LRS lub GRS. Jeśli twoje konto korzysta z RA-GRS, przed kontynuowaniem musisz najpierw zmienić typ replikacji konta na LRS lub GRS. Ten krok pośrednio usuwa pomocniczy punkt końcowy tylko do odczytu dostarczony przez RA-GRS przed migracją.

Mimo że firma Microsoft obsłuży Twoje żądanie migracji na żywo tak szybko, jak to możliwe, nie ma możliwości określenia, kiedy migracja na żywo zostanie ukończona. Jeśli chcesz, aby migracja Twoich danych została zakończona w określonym terminie, firma Microsoft zaleca przeprowadzenie zamiast tego migracji ręcznej. Ogólnie rzecz biorąc, im więcej danych zawiera Twoje konto, tym dłużej trwa ich migracja.

Migrację ręczną należy przeprowadzić, jeśli:

- Chcesz przeprowadzić migrację danych do konta magazynu ZRS, które znajduje się w regionie innym niż konto źródłowe.
- Twoje konto magazynu to konto blob strony premium lub konto blokowe obiektów blob.
- Chcesz przeprowadzić migrację danych z ZRS do LRS, GRS lub RA-GRS.
- Twoje konto magazynu zawiera dane w warstwie archiwum.

Możesz zażądać migracji na żywo za pośrednictwem [portalu pomocy technicznej platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). W portalu wybierz konto magazynu, które chcesz przekonwertować na ZRS.

1. Wybierz **nowe żądanie pomocy technicznej**
2. Uzupełnij podstawy na podstawie informacji o **koncie.** W sekcji **Usługa** wybierz pozycję **Zarządzanie kontem magazynu** i zasób, który chcesz przekonwertować na usługę ZRS.
3. Wybierz **pozycję Dalej**.
4. Określ następujące wartości w sekcji **Problem:**
    - **Ważność:** Pozostaw wartość domyślną w stanie rzeczywistym.
    - **Typ problemu:** Wybierz **migrację danych**.
    - **Kategoria**: Wybierz opcję **Migracja do ZRS**.
    - **Tytuł**: Wpisz opisowy tytuł, na przykład **migrację konta ZRS**.
    - **Szczegóły**: Wpisz dodatkowe szczegóły w polu **Szczegóły,** na przykład, chciałbym przeprowadzić migrację do ZRS z [LRS, GRS] w \_ \_ regionie.
5. Wybierz **pozycję Dalej**.
6. Sprawdź, czy informacje kontaktowe są poprawne na bloku **Informacje kontaktowe.**
7. Wybierz **pozycję Utwórz**.

Osoba udzielana pomocy technicznej skontaktuje się z Tobą i udzieli wszelkiej potrzebnej pomocy.

> [!NOTE]
> Migracja na żywo nie jest obecnie obsługiwana dla udziałów plików w uiszczonej klasy premium. Obecnie obsługiwane jest tylko ręczne kopiowanie lub przenoszenie danych.
>
> Konta magazynu GZRS nie obsługują obecnie warstwy archiwum. Zobacz [usługi Azure Blob storage: hot, cool i archiwum warstwy dostępu, aby](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) uzyskać więcej informacji.
>
> Dyski zarządzane są dostępne tylko dla LRS i nie można ich migrować do usługi ZRS. Migawki i obrazy dla standardowych dysków zarządzanych SSD można przechowywać w standardowej pamięci masowej na dysku twardym i [wybierać między opcjami LRS i ZRS.](https://azure.microsoft.com/pricing/details/managed-disks/) Aby uzyskać informacje na temat integracji z zestawami dostępności, zobacz [Wprowadzenie do dysków zarządzanych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Przełącz się z ZRS Classic

> [!IMPORTANT]
> 31 marca 2021 r. firma Microsoft wycofa i przeprowadzi migrację kont ZRS Classic. Więcej szczegółów zostanie dostarczonych klientom ZRS Classic przed wycofaniem.
>
> Po ZRS staje się ogólnie dostępne w danym regionie, klienci nie będą już mogli tworzyć kont ZRS Classic z witryny Azure portal w tym regionie. Tworzenie kont ZRS Classic za pomocą programu Microsoft PowerShell i platformy Azure CLI jest opcją do momentu przestarzałego wycofania z usługi ZRS Classic. Aby uzyskać informacje o tym, gdzie usługa ZRS jest dostępna, zobacz [Nadmiarowość usługi Azure Storage](storage-redundancy.md).

Asynchronicznie replikuje dane w centrach danych w obrębie jednego do dwóch regionów. Replikowane dane mogą nie być dostępne, chyba że firma Microsoft inicjuje przejście awaryjne do pomocniczego. Nie można przekonwertować konta ZRS Classic na lrs, GRS lub RA-GRS lub z niego. Konta ZRS Classic również nie obsługują metryk ani rejestrowania.

ZRS Classic jest dostępny tylko dla **bloków blob** na kontach magazynu ogólnego przeznaczenia V1 (GPv1). Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta magazynu platformy Azure](storage-account-overview.md).

Aby ręcznie przeprowadzić migrację danych konta ZRS do lub z konta LRS, GRS, RA-GRS lub ZRS Classic, użyj jednego z następujących narzędzi: AzCopy, Azure Storage Explorer, PowerShell lub Azure CLI. Można również utworzyć własne rozwiązanie migracji z jednej z bibliotek klienta usługi Azure Storage.

Konto magazynu ZRS Classic można również uaktualnić do usługi ZRS przy użyciu witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure w regionach, w których usługa ZRS jest dostępna.

# <a name="portal"></a>[Portal](#tab/portal)

Aby uaktualnić do usługi ZRS w witrynie Azure portal, przejdź do ustawień **konfiguracji** konta i wybierz **pozycję Uaktualnij:**

![Uaktualnij ZRS Classic do ZRS w portalu](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby uaktualnić do usługi ZRS za pomocą programu PowerShell, wywołanie następującego polecenia:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uaktualnić do usługi ZRS przy użyciu interfejsu wiersza polecenia platformy Azure, wywołanie następującego polecenia:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Koszty związane ze zmianą sposobu replikowania danych

Koszty związane ze zmianą sposobu replikowania danych zależą od ścieżki konwersji. Zamawianie od najmniej do najdroższych ofert nadmiarowości usługi Azure Storage obejmują LRS, ZRS, GRS, RA-GRS, GZRS i RA-GZRS.

Na przykład przejście *z* LRS do innego typu replikacji spowoduje naliczenie dodatkowych opłat, ponieważ przenosisz się do bardziej zaawansowanego poziomu nadmiarowości. Migracja *do* GRS lub RA-GRS spowoduje obciążenie przepustowością wychodzącą, ponieważ dane (w regionie podstawowym) są replikowane do zdalnego regionu pomocniczego. Opłata ta jest jednorazowa przy wstępnej konfiguracji. Po skopiowaniu danych nie ma żadnych dalszych opłat za migrację. Aby uzyskać szczegółowe informacje na temat opłat za przepustowość, zobacz [stronę Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Jeśli zmigrujesz konto magazynu z GRS do LRS, nie ma żadnych dodatkowych kosztów, ale zreplikowane dane zostaną usunięte z lokalizacji dodatkowej.

> [!IMPORTANT]
> Jeśli zmigrujesz konto magazynu z RA-GRS do GRS lub LRS, to konto jest rozliczane jako RA-GRS przez dodatkowe 30 dni po dacie, że został przekonwertowany.

## <a name="see-also"></a>Zobacz też

- [Nadmiarowość usługi Azure Storage](storage-redundancy.md)
- [Sprawdź właściwość Czas ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu pamięci masowej geograficznej z dostępem do odczytu](storage-designing-ha-apps-with-ragrs.md)
