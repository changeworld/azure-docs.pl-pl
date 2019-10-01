---
title: Dodawanie magazynu do pamięci podręcznej platformy Azure HPC (wersja zapoznawcza)
description: Jak zdefiniować cele magazynu, aby pamięć podręczna platformy Azure HPC mogła używać lokalnego systemu plików NFS lub kontenerów obiektów blob platformy Azure do przechowywania długoterminowych plików
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: 302d727ede9604d11972eaa8f46a3e27f204858f
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710041"
---
# <a name="add-storage-targets"></a>Dodaj cele magazynu

*Cele magazynu* są magazynem zaplecza dla plików, do których dostęp jest uzyskiwany za pomocą wystąpienia pamięci podręcznej platformy Azure HPC. Można dodać magazyn systemu plików NFS (taki jak lokalny system sprzętowy) lub zapisać dane w usłudze Azure Blob.

Można zdefiniować do dziesięciu różnych miejsc docelowych magazynu dla jednej pamięci podręcznej. Pamięć podręczna przedstawia wszystkie cele magazynu w jednej zagregowanej przestrzeni nazw.

Należy pamiętać, że eksporty magazynu muszą być dostępne z sieci wirtualnej pamięci podręcznej. W przypadku lokalnego magazynu sprzętu może być konieczne skonfigurowanie serwera DNS, który może rozpoznawać nazwy hostów dla dostępu do magazynu NFS. Więcej informacji można znaleźć w temacie [dostęp do usługi DNS](hpc-cache-prereqs.md#dns-access).

Dodaj elementy docelowe magazynu po utworzeniu pamięci podręcznej. Procedura jest nieco inna w zależności od tego, czy dodawana jest usługa Azure Blob Storage, czy eksport systemu plików NFS. Poniżej znajdują się szczegółowe informacje dotyczące każdego z nich.

## <a name="open-the-storage-targets-page"></a>Otwórz stronę miejsc docelowych magazynu

W Azure Portal Otwórz wystąpienie pamięci podręcznej, a następnie kliknij pozycję **cele magazynu** na lewym pasku bocznym. Na stronie miejsce docelowe magazynu są wyświetlane wszystkie istniejące elementy docelowe i nadające się do dodania łącze.

![zrzut ekranu przedstawiający link miejsca do magazynowania na pasku bocznym w obszarze Konfigurowanie nagłówka, który jest między ustawieniami nagłówków kategorii i monitorowaniem](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Dodaj nowy element docelowy usługi Azure Blob Storage

Nowy obiekt docelowy magazynu obiektów BLOB wymaga pustego kontenera obiektów blob lub kontenera, który został wypełniony danymi w formacie systemu plików w chmurze usługi Azure HPC. Przeczytaj więcej na temat wstępnego ładowania kontenera obiektów BLOB w temacie [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

Aby zdefiniować kontener obiektów blob platformy Azure, wprowadź te informacje.

![zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu, wypełniony informacjami o nowym obiekcie docelowym usługi Azure Blob Storage](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles -->

* **Nazwa docelowego magazynu** — Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.
* **Typ docelowy** — wybierz **obiekt BLOB**.
* **Konto magazynu** — wybierz konto z kontenerem, którego ma dotyczyć odwołanie.

  Musisz autoryzować wystąpienie pamięci podręcznej, aby uzyskać dostęp do konta magazynu, zgodnie z opisem w temacie [Dodawanie ról dostępu](#add-the-access-control-roles-to-your-account).
* **Kontener magazynu** — wybierz kontener obiektów BLOB dla tego obiektu docelowego.

* **Ścieżka do wirtualnej przestrzeni nazw** — Ustaw ścieżkę pliku dla klienta dla tego miejsca docelowego magazynu. Przeczytaj temat [Konfigurowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) , aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

Po zakończeniu kliknij przycisk **OK** , aby dodać miejsce docelowe magazynu.

### <a name="add-the-access-control-roles-to-your-account"></a>Dodaj role kontroli dostępu do swojego konta

Pamięć podręczna Azure HPC używa [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) do autoryzacji aplikacji pamięci podręcznej w celu uzyskania dostępu do konta magazynu dla obiektów docelowych usługi Azure Blob Storage.

Właściciel konta magazynu musi jawnie dodać rolę współautor danych [współautor konta magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) i [obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) dla użytkownika "StorageCache Resource Provider".

Można to zrobić wcześniej lub przez kliknięcie linku na stronie, w którym można dodać obiekt docelowy magazynu obiektów BLOB.

Procedura dodawania ról RBAC:

1. Otwórz stronę **kontroli dostępu (IAM)** dla konta magazynu. (Link na stronie **Dodaj miejsce docelowe magazynu** automatycznie otwiera Tę stronę dla wybranego konta).

1. Kliknij **+** w górnej części strony, a następnie wybierz pozycję **Dodaj przypisanie roli**.

1. Wybierz z listy rolę "Współautor konta magazynu".

1. W polu **Przypisz dostęp do** pozostaw wartość domyślną wybrana ("użytkownik, Grupa lub nazwa główna usługi").  

1. W polu **Wybierz** Wyszukaj ciąg "storagecache".  Ten ciąg powinien być zgodny z jednym podmiotem zabezpieczeń o nazwie "dostawca zasobów pamięci podręcznej HPC". Kliknij ten podmiot zabezpieczeń, aby go wybrać.

1. Kliknij przycisk **Zapisz** , aby dodać przypisanie roli do konta magazynu.

1. Powtórz ten proces, aby przypisać rolę "Współautor danych obiektu blob magazynu".  

![zrzut ekranu przedstawiający graficzny interfejs użytkownika dodawania przypisania roli](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Dodaj nowy element docelowy magazynu NFS

Miejsce docelowe magazynu NFS zawiera kilka dodatkowych pól, w których można określić, jak uzyskać dostęp do eksportu magazynu i jak efektywnie buforować swoje dane. Ponadto można utworzyć wiele ścieżek przestrzeni nazw z jednego hosta NFS, jeśli jest dostępny więcej niż jeden eksport.

![Zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu z zdefiniowanym elementem docelowym NFS](media/hpc-cache-add-nfs-target.png)

Podaj te informacje dla miejsca docelowego magazynu z kopią zapasową NFS:

* **Nazwa docelowego magazynu** — Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.

* **Typ docelowy** — wybierz system **plików NFS**.

* **Nazwa hosta** — wprowadź adres IP lub w pełni kwalifikowaną nazwę domeny dla systemu magazynu NFS. (Użyj nazwy domeny tylko wtedy, gdy pamięć podręczna ma dostęp do serwera DNS, który może rozpoznać nazwę).

* **Model użycia** — wybierz jeden z profili buforowania danych opartych na przepływie pracy, zgodnie z opisem w temacie [Wybierz model użycia poniżej](#choose-a-usage-model).

### <a name="nfs-namespace-paths"></a>Ścieżki przestrzeni nazw NFS

Obiekt docelowy magazynu NFS może mieć wiele ścieżek wirtualnych, o ile każda ścieżka reprezentuje inny eksport lub podkatalog w tym samym systemie magazynu.

Utwórz wszystkie ścieżki z jednego miejsca docelowego magazynu.
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

Wypełnij te wartości dla każdej ścieżki przestrzeni nazw:

* **Ścieżka do wirtualnej przestrzeni nazw** — Ustaw ścieżkę pliku dla klienta dla tego miejsca docelowego magazynu. Przeczytaj temat [Konfigurowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) , aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

<!--  The virtual path should start with a slash ``/``. -->

* **Ścieżka eksportowania NFS** — wprowadź ścieżkę do eksportu systemu plików NFS.

* **Ścieżka podkatalogu** — Jeśli chcesz zainstalować określony podkatalog eksportu, wprowadź go tutaj. Jeśli nie, pozostaw to pole puste.

Po zakończeniu kliknij przycisk **OK** , aby dodać miejsce docelowe magazynu.

### <a name="choose-a-usage-model"></a>Wybierz model użycia
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Podczas tworzenia miejsca docelowego magazynu, które wskazuje system magazynu NFS, należy wybrać *model użycia* dla tego celu. Ten model określa, w jaki sposób dane są buforowane.

* Odczytaj duże — Jeśli korzystasz głównie z pamięci podręcznej w celu przyspieszenia dostępu do odczytu danych, wybierz tę opcję.

* Odczyt/zapis — Jeśli klienci używają pamięci podręcznej do odczytu i zapisu, wybierz tę opcję.

* Klienci pomijają pamięć podręczną — wybierz tę opcję, jeśli klienci zapisują dane bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obiektów docelowych magazynu należy wziąć pod uwagę jedno z następujących zadań:

* [Instalowanie pamięci podręcznej platformy Azure HPC](hpc-cache-mount.md)
* [Przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md)
